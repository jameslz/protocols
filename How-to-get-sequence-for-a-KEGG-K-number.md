# How to get sequence for a KEGG K number

For example, plant fatty acid synthesis gene fabF, KEGG K number K09458.

### 0. prerequisite

KEGG API https://www.kegg.jp/kegg/rest/keggapi.html
curl

###  1. get all genes with K number K09458

    curl -o K00500.txt  http://rest.kegg.jp/link/genes/K00500

###  2. down load amino acid sequence

2.1 one by one download


    cat  K00500.txt | perl -ane  'chomp; print qq{curl  http://rest.kegg.jp/get/$F[1]/aaseq >> K00500.faa\n}' | bash


2.2 batch download


    cut -f2  K00500.txt  | kegg-tk  aaseq  -  10   |bash  >> K00500.faa
    

License: BY-NC-SA 4.0
