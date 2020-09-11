# How to convert gff3 format to genbank file

### 0. prerequisite

    GAG: https://github.com/genomeannotation/GAG
    tsv-utils
    fastx-utils

### 1. format gff3 format.

GFF3 specification: http://www.genome.ucsc.edu/FAQ/FAQformat.html#format3

    ctg1    AUGUSTUS    gene    1206    3193    .   -   .   ID=g7742
    ctg1    AUGUSTUS    mRNA    1206    3193    .   -   .   ID=g7742.t1;Parent=g7742
    ctg1    AUGUSTUS    exon    1206    1220    .   -   .   ID=g7742.t1:exon:0;Parent=g7742.t1
    ctg1    AUGUSTUS    exon    1298    1319    .   -   .   ID=g7742.t1:exon:0;Parent=g7742.t1
    ctg1    AUGUSTUS    exon    1378    1406    .   -   .   ID=g7742.t1:exon:0;Parent=g7742.t1
    ctg1    AUGUSTUS    exon    1473    1806    .   -   .   ID=g7742.t1:exon:0;Parent=g7742.t1
    ctg1    AUGUSTUS    exon    1874    2004    .   -   .   ID=g7742.t1:exon:0;Parent=g7742.t1
    ctg1    AUGUSTUS    exon    2056    2105    .   -   .   ID=g7742.t1:exon:0;Parent=g7742.t1
    ctg1    AUGUSTUS    exon    2254    2453    .   -   .   ID=g7742.t1:exon:0;Parent=g7742.t1
    ctg1    AUGUSTUS    exon    2515    2635    .   -   .   ID=g7742.t1:exon:0;Parent=g7742.t1
    ctg1    AUGUSTUS    exon    2700    2797    .   -   .   ID=g7742.t1:exon:0;Parent=g7742.t1
    ctg1    AUGUSTUS    exon    2863    2878    .   -   .   ID=g7742.t1:exon:0;Parent=g7742.t1
    ctg1    AUGUSTUS    exon    3013    3193    .   -   .   ID=g7742.t1:exon:0;Parent=g7742.t1
    ctg1    AUGUSTUS    CDS 1206    1220    .   -   0   ID=g7742.t1:cds:0;Parent=g7742.t1
    ctg1    AUGUSTUS    CDS 1298    1319    .   -   1   ID=g7742.t1:cds:0;Parent=g7742.t1
    ctg1    AUGUSTUS    CDS 1378    1406    .   -   0   ID=g7742.t1:cds:0;Parent=g7742.t1
    ctg1    AUGUSTUS    CDS 1473    1806    .   -   1   ID=g7742.t1:cds:0;Parent=g7742.t1
    ctg1    AUGUSTUS    CDS 1874    2004    .   -   0   ID=g7742.t1:cds:0;Parent=g7742.t1
    ctg1    AUGUSTUS    CDS 2056    2105    .   -   2   ID=g7742.t1:cds:0;Parent=g7742.t1
    ctg1    AUGUSTUS    CDS 2254    2453    .   -   1   ID=g7742.t1:cds:0;Parent=g7742.t1
    ctg1    AUGUSTUS    CDS 2515    2635    .   -   2   ID=g7742.t1:cds:0;Parent=g7742.t1
    ctg1    AUGUSTUS    CDS 2700    2797    .   -   1   ID=g7742.t1:cds:0;Parent=g7742.t1
    ctg1    AUGUSTUS    CDS 2863    2878    .   -   2   ID=g7742.t1:cds:0;Parent=g7742.t1
    ctg1    AUGUSTUS    CDS 3013    3193    .   -   0   ID=g7742.t1:cds:0;Parent=g7742.t1


### 2. Generates an NCBI .tbl file

The easy way:

    mkdir gag
    gag.py -g augustus.gff3 -f  genomes.fasta  -o gag

for big genome:

    cut -f1 augustus.gff3 | sort | uniq  >ctg.txt
    mkdir -p gag gff3 genomes
    cat  ctg.txt | \
     perl -ane  'print qq{tsv-utils subset -k  augustus.gff3  $F[0] >gff3/$F[0].gff3\n}' | \
     parallel -j 40


    cat  ctg.txt | \
     perl -ane  'print qq{fastx-utils fetch genomes.fasta  $F[0] >genomes/$F[0].fasta\n}' | \
     parallel -j 40


    cat  ctg.txt | \
      perl -ane  'print qq{gag.py -g gff3/$F[0].gff3 -f  genomes/$F[0].fasta  -o gag/$F[0];\n}' | \
      parallel -j 40


###  3. Generates an NCBI .tbl file

The easy way:

    cd gag
    tbl2asn  -i genome.fasta  -p . -V vb

for big genome:

    cd gag
    ls  | perl -ane  'print qq{cd $F[0];tbl2asn  -i genome.fasta  -p . -V vb; cd -;\n}' | bash
    ls  | perl -ane  'print qq{cat $F[0]/genome.gbf >>../augustus.gbk\n}' | bash


License: BY-NC-SA 4.0
