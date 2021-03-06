# How to format ARG-ANNOT DB

### 0. prerequisite

tsv-utils
fastx-utils
taxon-utils
efetch-utils 
parallel

### 1. download gb
   
   ** manual fix a lot of format and accession number error in ARG-ANNOT_NT_V6_July2019 file. **

    mkdir gb && cd gb


    fastx-utils view  ../ARG-ANNOT_NT_V6_July2019.txt |  \
       cut -f2,3,4 -d : | \
       efetch-utils region - | \
       parallel -j 5


    ls | sed 's/.gb//' > ../arg-annot.txt
    cd  ..


### 2. get taxonomy information


    cat arg-annot.txt | \
      perl -ane 'print qq{efetch-utils taxonomy gb/$F[0].gb\n}' | \
      bash > taxon.txt


    cat taxon.txt | \
      taxon-utils translate  -c 2  /biostack/database/taxonomy/taxon.map  -  | \
      tsv-utils definition  -c 2 /biostack/database/taxonomy/scientific_name.txt   - | \
      cut -f1,3,4  | \
      sort | uniq  | \
      perl -ne 's/\.\d+\t/\t/; print' \
      >arg-annot.taxonomy.txt


    fastx-utils view  ARG-ANNOT_NT_V6_July2019.txt | \
       perl -F: -ane 'chomp; print qq{$_\t$F[0]\t$F[1]\t$F[2]\n}'  \
       > arg-annot.flat.txt


    fastx-utils view -s  ARG-ANNOT_NT_V6_July2019.txt |\
      sort | uniq  |\
      tsv-utils annotation  arg-annot.flat.txt  - |\
      tsv-utils annotation -c4  arg-annot.taxonomy.txt -  | \
      tsv-utils  add_headline  "#id\tsequence\tgene\taccession\tlocation\tspecies\tlineage" -  \
      > arg-annot_db.txt


License: BY-NC-SA 4.0
