# How to generate Nematode ITS2 USEARCH compatible fasta

### 1. taxonomy

    tabtk cut -r -f3,2 Nematode_ITS2_1.0.0_taxonomy_nr.txt | \
    tail -n +2 |                                             \
    taxon-utils  translate -c 2  taxon.map - |               \
    cut -f1,3 | sed  's/ /_/g'                               \
    > Nematode_ITS2_1.0.0_NCBI.taxon.txt

### 2. annotation


    atlas-utils  label  -p ';tax='                          \
       Nematode_ITS2_1.0.0_NCBI.taxon.txt                 \
       Nematode_ITS2_1.0.0_seqs_nr.fasta                  \
    >Nematode_ITS2_1.0.0_usearch.fasta


version: Nematode_ITS2_1.0.0