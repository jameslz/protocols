# How to extract reads from specified clade in metagenome dataset with kraken2


### 0. prerequisite

- kraken
- tsv-utils
- taxon-utils
- fastx-utils

### 1. Kraken2 reads classify

```bash
kraken2 --paired --threads 40 --output  kraken2.txt --report  kreport2.txt --db  R1.fastq  R2.fastq ;
```

### 2. taxon binning

For geneus level bin, extract reads in "Clostridiisalibacter, Acetanaerobacterium" clade

```bash
grep -P "^C" kraken2.txt | cut -f2,3 | taxon-utils bin -l genus misc/taxon.map - > geneus.bin
tsv-utils subset -c 3 -k geneus.bin Clostridiisalibacter,Acetanaerobacterium | cut -f1 >reads.txt
```

### 3. extract reads

```bash
fastx-utils subseq R1.fastq reads.txt  >reads-1.fq
fastx-utils subseq R2.fastq reads.txt  >reads-2.fq
```


License: BY-NC-SA 4.0