# How to generate USEARCH compatible SILVA138 fasta

### 1. generate taxon.map for taxon-utils

    silva-tk maps tax_slv_ssu_138.txt >taxon.map

Note: remove 'Unknown Family' and 'Incertae Sedis' internal node.

### 2. tanslate SILVA taxId to USEARCH compatible taxonomy.

    silva-tk  patch  taxmap_slv_ssu_ref_138.txt  > taxmap_slv_ssu_patch_138.txt

Note: remove 1:N species to genus map. vote strategy.


    tail -n +2  taxmap_slv_ssu_patch_138.txt  |      \
    tsv-utils collapse -d '.' -f1,2  -  |       \
    silva-utils translate  -c 4 taxon.map - |   \
    silva-tk fmt -                              \
    >tax_slv_ssu.taxonomy.txt

### 3. remove Eukaryota clade and format taxon names.

    cat tax_slv_ssu.taxonomy.txt | \
    grep -v "k:Eukaryota" |     \
    sed 's/ /_/g'  |            \
    sed 's/"//g'                \
    > SILVA_16S_V138.txt

### 4. generate USEARCH compatible fasta

    seqtk seq  -C SILVA_138_SSURef_tax_silva.fasta |            \
    fastx-utils  rna2dna - |                                 \
    atlas-utils  label -p ";tax=" -r  SILVA_16S_V138.txt  -  \
    > SILVA_16S_V138.fasta


### 5. generate summary

    fastx-utils  info  SILVA_16S_V138.fasta


    #sequence       base    min_len max_len avg_len GC(%)
    2051165 2887935206      900     4000    1407.95 54.12

Version: 138
