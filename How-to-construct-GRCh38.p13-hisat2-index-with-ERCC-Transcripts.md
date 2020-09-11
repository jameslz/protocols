# How to construct Homo sapiens GRCh38.p13 hisat2 index with ERCC, Transcripts

### download data

    wget https://tools.thermofisher.com/content/sfs/manuals/ERCC92.zip
    wget ftp://ftp.ensembl.org/pub/release-99/fasta/homo_sapiens/dna/Homo_sapiens.GRCh38.dna.primary_assembly.fa.gz
    wget ftp://ftp.ensembl.org/pub/release-99/gtf/homo_sapiens/Homo_sapiens.GRCh38.99.gtf.gz

### build index


    gunzip -c  Homo_sapiens.GRCh38.dna.primary_assembly.fa.gz  >genome.fa
    unzip ERCC92.zip
    cat ERCC92.fa >>genome.fa
    gunzip -c Homo_sapiens.GRCh38.99.gtf.gz > Homo_sapiens.GRCh38.99.gtf
    cat ERCC92.gtf  >>Homo_sapiens.GRCh38.99.gtf


    hisat2_extract_splice_sites.py Homo_sapiens.GRCh38.99.gtf  >genome.ss
    hisat2_extract_exons.py Homo_sapiens.GRCh38.99.gtf >genome.exon


    hisat2_extract_splice_sites.py  Homo_sapiens.GRCh38.99.gtf  >genome.ss
    hisat2_extract_exons.py  Homo_sapiens.GRCh38.99.gtf >genome.exon

    hisat2-build -p 64 genome.fa  --ss genome.ss --exon genome.exon grch38

### clean data
 
 
    rm ERCC92.zip Homo_sapiens.GRCh38.dna.primary_assembly.fa.gz Homo_sapiens.GRCh38.99.gtf.gz Homo_sapiens.GRCh38.99.gtf
    rm genome*

License: BY-NC-SA 4.0