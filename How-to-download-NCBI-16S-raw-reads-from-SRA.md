# How to download NCBI 16S raw reads from SRA

### 0. prerequisite

NCBI SRA Toolkit (https://ftp-trace.ncbi.nlm.nih.gov/sra/sdk/2.10.7/sratoolkit.2.10.7-centos_linux64.tar.gz)
GNU Parallel: https://www.gnu.org/software/parallel/
eBay's TSV Utilities: https://github.com/eBay/tsv-utils
sra-utils: https://github.com/jameslz/biostack-suits/blob/master/sra-utils

### 1 get the SRA run file from BioProject ID

BioProject Accession: PRJNA480312

1.1 search SRA database using PRJNA480312:

https://www.ncbi.nlm.nih.gov/sra/?term=PRJNA480312

1.2 Export the RunInfo file.

Send to -> File + RunInfo -> Create File -> SraRunInfo.csv

### 2 download SRA file using ascp or wget

2.1 csv to tsv

    csv2tsv SraRunInfo.csv >SraRunInfo.tsv

2.2 download sra

    mkdir PRJNA480312
    grep -P ^"SRR" SraRunInfo.tsv  | perl -ane  'print qq{wget $F[10] -O PRJNA480312/$F[0].sra\n}'  | parallel -j 20

### 3 convert to fastq

    mkdir raw_data
    sra-utils mapping_file SraRunInfo.tsv  0 |  sra-utils extract  PRJNA480312 -

License: BY-NC-SA 4.0
