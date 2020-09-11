# How to annotation OTU table using atlas-utils and USEARCH

### input data

`zotus.fa  zotu_table.raw.txt`

### annotation using UBC(USEARCH RDP Naive Bayesian Classifier algorithm implementation)

    usearch -nbc_tax  zotus.fa  -db  rdp_16s_v16_sp.fasta -strand both -tabbedout classify.txt -log  classify.log -threads 24

alternative: using sintax algorithm if memory problem

    usearch -sintax  zotus.fa  -db  rdp_16s_v16_sp.fasta -strand both -tabbedout classify.txt -log  classify.log -threads 24


### reorder OTU table

    fastx-utils view  zotus.fa | tsv-utils  reorder  zotu_table.raw.txt - >zotu_table.txt

### annotation ZOTU table

    cut -f1,4  classify.txt | atlas-utils  annotation -  zotu_table.txt  >zotu_table.annotation.txt


### summary:

Now, you have  1. reordered zotu_table.txt, 2.zotu_table.annotation.txt


License: BY-NC-SA 4.0