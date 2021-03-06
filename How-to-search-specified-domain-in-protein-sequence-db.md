# How to search specified domain in protein sequence db uing hmmsearch

### 0. prerequisite

hmmsearch
hmm-utils
atlas-utils
seqtk
fastx-utils

### 1. download sequence db and get taxonomy

    wget  ftp://ftp.uniprot.org/pub/databases/uniprot/current_release/knowledgebase/complete/uniprot_sprot.fasta.gz
    gunzip uniprot_sprot.fasta.gz

     fastx-utils view -c  uniprot_sprot.fasta |\
         perl -ane '($ox) = $_ =~/OX=(\d+)/; print qq{$F[0]\t$ox\n}' | \
         taxon-utils translate  /biostack/database/taxonomy/taxon.map  - | \
         cut -f1,3  >uniprot_sprot.taxonomy
   
    hmm-utils  hmmsearch  nirB.domtblout | hmm-utils   domains - > nirB.swiss_prot

### 2. hmmsearch

    annotation nirB using nirB.hmm or K00362.hmm


    hmmsearch --cpu 40 -E 1e-5 --domE 1e-5 -o /dev/null --domtblout nirB.domtblout nirB.hmm uniprot_sprot.fasta

### 3. annotation using taxonomy

    cut -f1  nirB.swiss_prot | \
       grep -v "#" | \
       fastx-utils subseq  uniprot_sprot.fasta - | \
       seqtk seq -C  | \
       atlas-utils label  -p ";tax=" -r  uniprot_sprot.taxonomy - \
       >nirB.swiss_pror.usearch.ffa

License: BY-NC-SA 4.0