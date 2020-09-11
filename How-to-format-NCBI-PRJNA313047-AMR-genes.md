# How to format NCBI PRJNA313047 AMR genes

### 0. prerequisite

tsv-utils
fastx-utils
taxon-utils
efetch-utils 
parallel

### 1. download gb/protein/cds/gff3
    
    https://www.ncbi.nlm.nih.gov/bioproject/PRJNA313047/

### 2. get taxonomy information
   
    grep -P "\tregion\t"  PRJNA313047.gff3 | \
        perl -ane '/taxon:(\d+);/; print qq{$F[0]\t$1\n}' \
        >taxon.txt


    cat taxon.txt | \
      taxon-utils translate  -c 2  /biostack/database/taxonomy/taxon.map  -  | \
      tsv-utils definition  -c 2 /biostack/database/taxonomy/scientific_name.txt   - | \
      cut -f1,3,4  | \
      sort | uniq  \
      >taxonomy.txt

### 3. parse gff3
   
    grep -P "\tregion\t"  PRJNA313047.gff3    | \
        perl -ne '/ID=(.+?);/; print $1."\n"' | \
        sed -e 's/\.\./-/'  | \
        perl -F: -ane  'print qq{$F[0]\t$_}' \
        >PRJNA313047.region.txt


      grep -P "\tCDS\t" PRJNA313047.gff3  | \
           perl -ne  's/ID=cds-(\S+?);Parent=.+/ID=$1/; print' |  \
           gffread  -g  ../db/CDS.fasta  -w PRJNA313047.ffn -y  PRJNA313047.faa -

### 4. build db

     grep -P "\tCDS\t"  PRJNA313047.gff3 | \
        perl  -ane '($n)=$_=~/Name=(\S\S+?);/; ($g)=$_=~/gene=(\w\S+?);/; print qq{$F[0]\t$g\t$n\n}' | \
        tsv-utils  placehold - \
       >flat.txt


     fastx-utils view  ../db/CDS.fasta  | \
        tsv-utils annotation  flat.txt  - | \
        tsv-utils annotation  -c 3  <(fastx-utils view  -s PRJNA313047.ffn ) - | \
        tsv-utils annotation  taxonomy.txt - | \
        tsv-utils   add_headline  "#id\tgene\tprotein_id\tsequence\tspecies\tlineage" -  \
        > PRJNA313047_db.txt

 
License: BY-NC-SA 4.0
