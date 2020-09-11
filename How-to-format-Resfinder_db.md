# How to format Resfinder_db

### 0. prerequisite

tsv-utils
fastx-utils
taxon-utils
efetch-utils 
parallel

### 1. download gb

    cat   resfinder_db/*.fsa  | perl -ne 's/\s+\n/\n/; print' > resfinder_db.fasta

change: 18676889 to  NG_047469

    mkdir gb && cd gb
    cat  ../resfinder.txt  |  efetch-utils efetch  - | parallel -j 5
    cd  ..

### 2. map protein_id name to locus

    cat resfinder.txt | \
      perl -ane 'print qq{efetch-utils protein gb/$F[0].gb\n}' | \
      bash >resfinder.faa


    fastx-utils view -c resfinder.faa > resfinder.protein_id-locus.txt

### 3. get taxonomy information


    cat resfinder.txt | \
      perl -ane 'print qq{efetch-utils taxonomy gb/$F[0].gb\n}' | \
      bash > taxon.txt


    cat taxon.txt | \
      taxon-utils translate  -c 2  /biostack/database/taxonomy/taxon.map  -  | \
      tsv-utils definition  -c 2 /biostack/database/taxonomy/scientific_name.txt   - | \
      cut -f1,3,4  | \
      sort | uniq  \
      >resfinder.taxonomy.txt


    fastx-utils view  resfinder_db.fasta | \
      perl -F_ -ane  'chomp; if(/(N[CZ]_)/){ print qq{$_\t$F[0]\t$1$F[-1]} }else{print qq{$_\t$F[0]\t$F[-1]}}'  |\
      sort | uniq  \
      >resfinder.flat.txt


    fastx-utils view -s  resfinder_db.fasta |\
      sort | uniq  |\
      tsv-utils annotation  resfinder.flat.txt  - |\
      tsv-utils annotation -c4  resfinder.taxonomy.txt -  | \
      tsv-utils   annotation   resfinder.phenotypes.txt  - | \
      tsv-utils  add_headline  "#id\tsequence\tgene\taccession\tspecies\tlineage\tclass\tphenotype\tpmid\tmechanism of resistance\tnotes\tRequired_gene" -  \
      > resfinder_db.txt


License: BY-NC-SA 4.0
