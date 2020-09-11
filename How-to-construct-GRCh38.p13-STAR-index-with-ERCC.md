# How to construct Homo sapiens GRCh38.p13 STAR index with ERCC

### download data

    wget https://tools.thermofisher.com/content/sfs/manuals/ERCC92.zip
    wget ftp://ftp.ensembl.org/pub/release-99/fasta/homo_sapiens/dna/Homo_sapiens.GRCh38.dna.primary_assembly.fa.gz
    wget ftp://ftp.ensembl.org/pub/release-99/gtf/homo_sapiens/Homo_sapiens.GRCh38.99.gtf.gz

###  build index

    gunzip -c  Homo_sapiens.GRCh38.dna.primary_assembly.fa.gz  >genome.fa
    unzip ERCC92.zip
    cat ERCC92.fa >>genome.fa
    gunzip -c Homo_sapiens.GRCh38.99.gtf.gz > Homo_sapiens.GRCh38.99.gtf
    cat ERCC92.gtf  Homo_sapiens.GRCh38.99.gtf >genome.gtf


    STAR                                 \ 
        --runThreadN 64                  \
        --runMode genomeGenerate         \
        --genomeDir   .                  \
        --genomeFastaFiles  genome.fa    \
        --sjdbGTFfile       genome.gtf   \

### clean
    
    rm genome.fa genome.gtf

License: BY-NC-SA 4.0