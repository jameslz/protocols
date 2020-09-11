# How to format AMRFinder

### 0. prerequisite

tsv-utils
fastx-utils
taxon-utils
efetch-utils 
parallel
accession2taxid

### 1. download AMRFinderPlus database

   AMR_CDS
   ReferenceGeneCatalog.txt

### 2. flatten the AMR_CDS sequence

       fastx-utils  view  -cs  AMR_CDS | \
           perl -ane 'chomp; @t=split /\|/, $F[0];  print qq{$t[1]\t$t[2]\t$F[1]\t$F[2]\n}'  \
           >flat.txt

### 3. map taxonomy 

       cut -f3  flat.txt | \
           tsv-utils subset  -c 2  accession2taxid/nucl_gb.accession2taxid.gz  - \
           >> accession2taxid.txt


       cut -f3  flat.txt | \
           tsv-utils subset  -c 2  accession2taxid/nucl_wgs.accession2taxid.gz  - \
           >> accession2taxid.txt


        cut -f2,3  accession2taxid.txt | \
           taxon-utils translate  -c 2  /biostack/database/taxonomy/taxon.map  -  | \
           tsv-utils definition  -c 2 /biostack/database/taxonomy/scientific_name.txt   - | \
           cut -f1,3,4  | \
           sort | uniq  \
           >taxonomy.txt

### 4. reference gene catalog information

       tsv-utils  placehold ReferenceGeneCatalog.txt | \
          tsv-utils cut  -f10,2,6,7,8,9,4  -  \
          > GeneCatalog.txt

### 5. merge db


      tsv-utils annotation  GeneCatalog.txt  flat.txt | \
          tsv-utils annotation -c 2  taxonomy.txt - | \
          tsv-utils  add_headline  "#protein_id\tnucleotide_accession\tlocation\tsequence\tsymbol\ttype\tsubtype\tclass\tsubclass\tproduct_name\tspecies\tlineage" - | \
          grep -P "\tAMR\t" \
          >AMR-db.txt

License: BY-NC-SA 4.0
