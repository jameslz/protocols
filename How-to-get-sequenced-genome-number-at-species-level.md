# How to get sequenced genome number at species level

### download data

    wget ftp://ftp.ncbi.nlm.nih.gov/genomes/ASSEMBLY_REPORTS/assembly_summary_genbank.txt
    wget ftp://ftp.ncbi.nlm.nih.gov/genomes/ASSEMBLY_REPORTS/assembly_summary_refseq.txt

### summary

    cat assembly_summary_genbank.txt          \
    | grep -P -v  "^#"                        \
    | cut -f1,6                               \
    | taxon-utils  translate  -c 2 ncbi.map - \
    | cut -f1,3                               \
    | tsv-utils  unpack -                     \
    | grep "s:"                               \
    | tsv-utils  bins -t 2 -s 1 -             \
    | sed 's/s://'                            \
    > genbank.bins.txt


    cat assembly_summary_refseq.txt           \
    | grep -P -v  "^#"                        \
    | cut -f1,6                               \
    | taxon-utils translate  -c 2 ncbi.map -  \
    | cut -f1,3                               \
    | tsv-utils  unpack -                     \
    | grep "s:"                               \
    | tsv-utils  bins -t 2 -s 1 -             \
    | sed 's/s://'                            \
    > refseq.bins.txt

### note

  ncbi.map can be generated using taxon-db: [ref][1]

  [1]: https://github.com/jameslz/protocols/blob/master/How-to-retrive-viral-sequence-from-nt-nr
