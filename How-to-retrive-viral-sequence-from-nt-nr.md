# How to retrive viral sequence from nt/nr db.

### download data

    ascp -i /etc/asperaweb_id_dsa.openssh --overwrite=diff -QTr -l6000m anonftp@ftp.ncbi.nlm.nih.gov:/blast/db/FASTA/nt.gz .
    ascp -i /etc/asperaweb_id_dsa.openssh --overwrite=diff -QTr -l6000m anonftp@ftp.ncbi.nlm.nih.gov:/blast/db/FASTA/nr.gz .
    ascp -i /etc/asperaweb_id_dsa.openssh --overwrite=diff -QTr -l6000m https://ftp.ncbi.nih.gov/pub/taxonomy/taxdump.tar.gz .
    ascp -i /etc/asperaweb_id_dsa.openssh --overwrite=diff -QTr -l6000m https://ftp.ncbi.nih.gov/pub/taxonomy/new_taxdump/new_taxdump.tar.gz .
    ascp -i /etc/asperaweb_id_dsa.openssh --overwrite=diff -QTr -l6000m ftp://ftp.ncbi.nlm.nih.gov/blast/db/taxdb.tar.gz .
    ascp -i /etc/asperaweb_id_dsa.openssh --overwrite=diff -QTr -l6000m ftp://ftp.ncbi.nlm.nih.gov/blast/db/taxdb.tar.gz .
    ascp -i /etc/asperaweb_id_dsa.openssh --overwrite=diff -QTr -l6000m https://ftp.ncbi.nih.gov/pub/taxonomy/accession2taxid .

**accession2taxid:** 

nucl_wgs.accession2taxid.gz  TaxID mapping for live nucleotide sequence records of type WGS or TSA. 
nucl_gb.accession2taxid.gz   TaxID mapping for live nucleotide sequence records that are not EST, GSS, WGS or TSA.
prot.accession2taxid.gz      TaxID mapping for live protein sequence records.

### generate viral clade taxonid

    mkdir taxonomy &&  mv  new_taxdump.tar.gz  taxonomy && cd taxonomy
    taxon-db  taxonomy/nodes.dmp  taxonomy/names.dmp  >taxonomy/ncbi.map
    taxon-utils  clade  taxonomy/ncbi.map 10239  > virus.taxonID.txt

### viral sequence in nt

    cat  accession2taxid/nucl_wgs.accession2taxid.gz   \
         accession2taxid/nucl_gb.accession2taxid.gz    \
    |tsv-utils subset -c 3 - virus.taxonID.txt         \
    |cut -f2,3                                         \
    |taxon-utils translate -c2  taxonomy/ncbi.map  -   \
    >virus.nuc.accessId.txt


    cut -f1 virus.nuc.accessId.txt                     \
    |fastx-utils subseq nt.gz - >virus.nuc.fasta


    fastx-nr  virus.nuc.fasta  > virus.nuc.annotation.txt


### viral protein sequence in nr

    cat  accession2taxid/prot.accession2taxid.gz      \
         accession2taxid/pdb.accession2taxid.gz       \
    |tsv-utils subset -c 3 - virus.taxonID.txt        \
    |cut -f2,3                                        \
    |taxon-utils translate -c2  taxonomy/ncbi.map -   \
    >virus.prot.accessId.txt


    cut -f1 virus.prot.accessId.txt                   \
    |fastx-utils subseq  nr.gz - > virus.prot.fasta


    fastx-nr  virus.prot.fasta   > virus.prot.annotation.txt

### download data


    rsync --no-motd --files-from=manifest.txt rsync://ftp.ncbi.nlm.nih.gov/genomes/ . 2> rsync.err


### summary

     $ fastx-utils info  virus.nuc.fasta
     #sequence       base    min_len max_len avg_len GC(%)
     2606563 5469759374      9       2473870 2098.46 44.69


     $ wc -l virus.nuc.annotation.txt
     3106075 virus.nuc.annotation.txt


     $ fastx-utils info  virus.prot.fasta
     #sequence       base    min_len max_len avg_len GC(%)
     2808241 1023293983      6       13556   364.39  8.83
     
     $  wc -l  virus.prot.annotation.txt
     4688234 virus.prot.annotation.txt

### clean
 
    mkdir misc
    pigz  virus.nuc.accessId.txt  virus.prot.accessId.txt   virus.taxonID.txt
    mv virus.nuc.accessId.txt.gz  virus.prot.accessId.txt.gz  virus.taxonID.txt.gz misc

License: BY-NC-SA 4.0