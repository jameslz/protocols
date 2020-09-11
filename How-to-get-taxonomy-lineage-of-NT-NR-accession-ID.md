# How to get the taxonomy lineage of NT/NR accession ID

### 0. prerequisite

taxon-utils
NCBI taxonomy taxon.map
NCBI accession2taxid data

### 1. download NCBI 

    ascp-utils ftp ftp://ftp.ncbi.nlm.nih.gov/pub/taxonomy/accession2taxid/

All files in accession2taxid have four columns separated by a TAB character. 
The first line in each file is a header line:


    accession<TAB>accession.version<TAB>taxid<TAB>gi


### 2. mapping accession id

For a list of protein accession ids in accession.txt:

    WP_003412295.1
    WP_002221775.1
    WP_001865978.1
    WP_003119872.1
    WP_005654219.1
    WP_002209848.1
    WP_002215568.1
    WP_001201732.1
    WP_000767937.1
    WP_000909989.1


    tsv-utils subset -c2  accession2taxid/prot.accession2taxid.gz accession.txt | \
    cut -f2,3 | \
    taxon-utils translate -c 2  /biostack/database/taxonomy/taxon.map  -  \
    > accession.taxonomy.txt


License: BY-NC-SA 4.0
