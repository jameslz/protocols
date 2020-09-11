# How to format MEGARes DB

### 0. prerequisite

tsv-utils
fastx-utils

### 1. flatten sequence file

    fastx-utils  view  -s   megares_drugs_database_v2.00.fasta | \
       perl -ane  '/^(\S+?)\|/; print qq{$1\t$F[0]\t$F[1]\n}' \
       > megares_flat.txt

### 2. parse mapping file


    csv2tsv megares_full_annotations_v2.00.csv |\
        tsv-utils annotation  -c 2 -  \
        >megares_flat.tx


    csv2tsv megares_to_external_header_mappings_v2.00.csv | \
        tsv-utils  cut -f3,2,1 - |\
        grep -P "\tCARD"  | \
        perl -ane '@t=split /\|/, $F[1]; print qq{$F[0]\t$t[2]:$t[4]\t$t[-1]\t$F[-1]\n}' \
        >  megares_card.txt


    csv2tsv megares_to_external_header_mappings_v2.00.csv | \
       tsv-utils  cut -f3,2,1 - | \
       grep -P "\tARG-ANNOT"  |  \
       perl -ane '@t=split /:/, $F[1]; print qq{$F[0]\t$t[1]:$t[2]\t$t[0]\t$F[-1]\n}'  \
       > megares_arg-annot.txt


    csv2tsv megares_to_external_header_mappings_v2.00.csv | \
       tsv-utils  cut -f3,2,1 - | \
       grep -P "\tAMRFinder"  |  \
       perl -ane '@t=split /\|/, $F[1]; print qq{$F[0]\t$F[2]\t$t[6]\t$F[-1]\n}' \
       > megares_amrfinder.txt


    csv2tsv megares_to_external_header_mappings_v2.00.csv | \
       tsv-utils  cut -f3,2,1 - | \
       grep -P "\tResFinder" | \
       perl -ane '@t=split /_\d+_/, $F[1]; print qq{$F[0]\t$t[1]\t$t[0]\t$F[-1]\n}' \
       > megares_resfinder.txt


    csv2tsv megares_to_external_header_mappings_v2.00.csv | \
      tsv-utils  cut -f3,2,1 - | \
      grep -P "\tNCBI" | \
      perl -ne 's/\s+gi\|\d+\|\S+?\|/;/g; print' | \
      sed 's/;/\t/' | \
      perl -ane '$F[1] =~s/\|/:/g ; print qq{$F[0]\t$F[1]\t-\t$F[-1]\n}' \
      > megares_ncbi.txt


    csv2tsv megares_to_external_header_mappings_v2.00.csv | \
            tsv-utils  cut -f3,2,1 - | \
            grep -P "\tBacMet"  | \
            perl -ane '@t=split /:|\|/, $F[1]; print qq{$F[0]\t$t[1]\t$t[0]\t$F[-1]\n}'  | \
            sed  's/\.\./-/'  \
            > megares_BacMet.txt


     cat  megares_amrfinder.txt  megares_arg-annot.txt  megares_BacMet.txt  megares_card.txt  megares_resfinder.txt megares_ncbi.txt \
           >  megares_mapping.txt

### 3. build_db

          csv2tsv  megares_full_annotations_v2.00.csv |\
              tsv-utils annotation -c2  - megares_flat.txt  |\
              tsv-utils annotation -c2  megares_mapping.txt - |\
              tsv-utils  add_headline  "#id\theader\tsequence\ttype\tclass\tmechanism\tgroup\tlocation\tsymbol\tDB" -   \
              > megares_db.txt

License: BY-NC-SA 4.0
