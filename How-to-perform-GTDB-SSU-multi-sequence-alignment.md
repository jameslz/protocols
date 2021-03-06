# How to perform GTDB SSU multi-sequence alignment

### download data

    wget https://data.ace.uq.edu.au/public/gtdb/data/releases/release89/89.0/ssu_r89.fna
    wget https://data.ace.uq.edu.au/public/gtdb/data/releases/release89/89.0/hq_mimag_genomes_r89.tsv

### multi/single copy 16S rRNA genes

    cut -f1,5 hq_mimag_genomes_r89.tsv | tsv-utils  nfilter -l  - 1.1   > ssu_r89.multi-copy.txt
    cut -f1,5 hq_mimag_genomes_r89.tsv | tsv-utils  nfilter - 1.0       > ssu_r89.single-copy.txt

### ssu name table

    fastx-utils view  ssu_r89.fna | perl -F~ -ane 'print qq{$F[0]\t$_}' > ssu_r89.names.txt


### pick one sequence from one genome

    mkdir centroids picks ssu
    cut -f1 ssu_r89.multi-copy.txt  \
    perl -ane  'print qq{tsv-utils subset -k  ssu_r89.names.txt  $F[0] \
    >ssu/$F[0].txt\n}' | parallel -j 40


    cut -f1 ssu_r89.multi-copy.txt \
    |perl -ane  'print qq{cut -f2 ssu/$F[0].txt \ \
    |seqtk subseq  ssu_r89.fna -  \
    |seqtk seq -C >ssu/$F[0].fasta\n}' \
    |parallel -j 40


    cut -f1 ssu_r89.multi-copy.txt  \
    |perl -ane  'print qq{usearch -cluster_fast ssu/$F[0].fasta  -id 0.9  -sort length -centroids centroids/$F[0].fasta\n}' \
    |parallel -j 40


    cut -f1 ssu_r89.multi-copy.txt \
    |perl -ane  'print qq{fastx-pick centroids/$F[0].fasta  \>picks/$F[0].fasta \n}' \
    |parallel -j 40


    cut -f1 ssu_r89.single-copy.txt \
    |perl -ane  'print qq{tsv-utils subset -k  ssu_r89.names.txt  $F[0] \>ssu/$F[0].txt\n}' \
    |parallel -j 40


    cut -f1 ssu_r89.single-copy.txt \
    |perl -ane  'print qq{cut -f2 ssu/$F[0].txt \| seqtk subseq  ssu_r89.fna - \| seqtk seq -C \>ssu/$F[0].fasta\n}' \
    |parallel -j 40


    cut -f1 ssu_r89.single-copy.txt \
    |perl -ane  'print qq{cp ssu/$F[0].fasta  picks/$F[0].fasta\n}' \
    |parallel -j 40


    cat  hq_mimag_genomes_r89.tsv \
    | tail -n +2  \
    | perl -ane 'print qq{cat  picks/$F[0].fasta \>\> hq_ssu_r89.fasta\n}' \
    | bash

### reformat ssu fasta


    fastx-utils view  hq_ssu_r89.fasta             \
    | perl -F~ -ane 'chomp; print qq{$_\t$F[0]\n}' \
    | fastx-utils replace  -  hq_ssu_r89.fasta     \
    | sed 's/RS_//g; s/GB_//g'                     \
    > gtdb_ssu_r89_90149.fasta


### seperator Bacteria and 
 

     grep "d__Bacteria"  hq_mimag_genomes_r89.tsv    \
     |cut -f1                                        \
     |sed 's/GB_//;s/RS_//'                          \
     |fastx-utils subseq  gtdb_ssu_r89_90149.fasta - \
     >gtdb_ssu_r89.bact.fasta


    grep "d__Archaea"  hq_mimag_genomes_r89.tsv    \
     |cut -f1                                       \
     |sed 's/GB_//;s/RS_//'                         \
     |fastx-utils subseq  gtdb_ssu_r89_90149.fasta - >gtdb_ssu_r89.arch.fasta


### perform alignment using ssu-align

     ssu-align  --dna  -f  --no-search   -n bacteria -f  gtdb_ssu_r89.bact.fasta  msa

### status

onging...

License: BY-NC-SA 4.0