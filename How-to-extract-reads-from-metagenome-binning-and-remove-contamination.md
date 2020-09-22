# How to extract reads from metagenome binning and remove contamination


### 0. prerequisite

- kraken
- tsv-utils
- taxon-utils
- fastx-utils

### 1. Kraken2 contigs classify

```bash
kraken2 --threads 40 --output classify.txt --report kreport2.txt --db  /biostack/database/kraken2/kraken-db  bins.30.fa ;
```

### 2. taxon bin and remove contamination

such as: `class` level binning and remove `Bacteroidia` clade.

```bash
grep -P "^C" kraken2.txt | cut -f2,3 | taxon-utils bin -l class taxon.map - > class.bin
tsv-utils subset -c 3 -k class.bin Bacteroidia | cut -f1 >contigs.txt
```

### 3. extract reads

`C-8.bam`: align reads to assembled contigs with bowtie2. 

```bash
samtools view  -F4  C-8.bam  | tsv-utils subset  -c 3 - contigs.txt | cut -f1  > reads.txt
fastx-utils subseq R1.fastq reads.txt  >reads-1.fq
fastx-utils subseq R2.fastq reads.txt  >reads-2.fq
```

License: BY-NC-SA 4.0