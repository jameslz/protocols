# How to download Fungene data and construct USEARCH annotation db.

### 0. prerequisite

    atlas-utils
    tsv-utils
    fastx-utils
    taxon-utils
    parallel
    fun-curl
    fun-tk

### 1. download data

download nirB gene, hmmid:347, page number: 91

    mkdir -p nirB
    for i in {1..91};do echo  fun-curl 347 $i \>nirB/$i.txt;done | bash

### 2. parse nucleotide id and location

    cat  nirB/*   | fun-tk table - nuc   >nuc.txt
    cat  nirB/*   | fun-tk table - prot  >prot.txt

### 3. download genebank file from NCBI API.
    
    mkdir genbank
    fun-tk table efetch  nuc.txt > genbank/efetch.txt
    cd genbank
    cat efetch.txt | parallel -j 3
    rm efetch.txt && cd  ..

### 4. file format conversion and sequence parse

    ls  genbank | sed  's/.gb//' > list.txt
    perl -ane 'print qq{seqret genbank/$F[0].gb genbank/$F[0].fasta\n}'  list.txt | parallel -j 40
    perl -ane 'print qq{fun-tk protein  genbank/$F[0].gb  >genbank/$F[0].pep\n}' list.txt  | parallel -j 40
    perl -ane 'print qq{fun-tk taxonomy genbank/$F[0].gb  >genbank/$F[0].taxonomy\n}' list.txt  | parallel -j 40
    perl -ane 'print qq{bp_genbank2gff3.pl -out stdout genbank/$F[0].gb \| fun-tk  patch  -  \> genbank/$F[0].gff\n}' list.txt  | parallel -j 40
    rm  *.fa


    perl -ane 'print qq{samtools faidx genbank/$F[0].fasta\n}' list.txt  | parallel -j 40
    perl -ane 'print qq{gffread  -w genbank/$F[0].ffn -g  genbank/$F[0].fasta genbank/$F[0].gff\n}' list.txt  | parallel -j 40


    find . |grep ".pep" | perl -ane  'print qq{cat $F[0] >> nirB.pep\n}' | bash
    find . |grep ".taxonomy" | perl -ane  'print qq{cat $F[0] >> nirB.taxonomy\n}' | bash
    find . |grep ".ffn" | perl -ane  'print qq{cat $F[0] | seqtk seq -U >> nirB.fasta\n}' | bash


### 5. taxonomy

    cat nirB.taxonomy | \
       grep -P -v  "\t$" | \
       perl  -ane  's/\.\d+\t/\t/; print'  | \
       taxon-utils  translate  -c 2  /biostack/database/taxonomy/taxon.map  -  | \
       cut -f1,3  > db.taxonomy

### 6. format USEARCH db.

    fastx-utils subseq  nirB.faa prot.txt  | \
       atlas-utils  label -p ";tax=" -r db.taxonomy - \
       > nirB.USEAECH.faa
    fastx-utils subseq  nirB.ffn prot.txt  | \
       atlas-utils  label -p ";tax=" -r db.taxonomy -  \
       > nirB.USEAECH.ffn

License: BY-NC-SA 4.0