# How to download sra data from ena

### 0. prerequisite

[ascp-utils](https://github.com/jameslz/biostack-suits/blob/master/ascp-utils)

###  1. download meta data

For example: SRP116709

    https://www.ebi.ac.uk/ena/browser/view/SRP116709

download tsv report

    https://www.ebi.ac.uk/ena/portal/api/filereport?accession=PRJNA399742&result=read_run&fields=study_accession,sample_accession,experiment_accession,run_accession,tax_id,scientific_name,fastq_ftp,submitted_ftp,sra_ftp&format=tsv&download=true


###  2. download sra

    cut -f9  filereport_read_run_PRJNA399742_tsv.txt  | tail -n +2 |   ascp-utils ena - | bash


License: BY-NC-SA 4.0
