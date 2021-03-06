# How to get taxonomy information from CARD nucleotide fasta

### 0. prerequisite

tsv-utils
fastx-utils
taxon-utils
efetch-utils 
parallel

### 1. get gb files

    mkdir gb && cd gb
    

    fastx-utils view  nucleotide_fasta_protein_homolog_model.fasta | \
      efetch-utils  card - | \
      parallel -j 5


    ls  > ../card.txt
    cd ..

### 2. map protein_id name to locus

    cat card.txt | \
        perl -ane 'print qq{efetch-utils protein gb/$F[0]\n}' | \
        bash >card.faa


    fastx-utils view -c card.faa > card.protein_id-locus.txt

### 3. get taxonomy information

    cat card.txt | \
        perl -ane 'print qq{efetch-utils taxonomy gb/$F[0]\n}' | \
        bash > taxon.txt


    cat taxon.txt | \
        taxon-utils translate  -c 2  /biostack/database/taxonomy/taxon.map  -  | \
        tsv-utils definition  -c 2 /biostack/database/taxonomy/scientific_name.txt   - | \ 
        cut -f1,3,4  \
        >card.taxonomy.txt


    fastx-utils view -s nucleotide_fasta_protein_homolog_model.fasta  | \
        sed 's/|/\t/g' | \
        cut -f2,3,4,6,7 | \
        tsv-utils annotation card.taxonomy.txt - \
        >card.nucleotide.txt

License: BY-NC-SA 4.0
