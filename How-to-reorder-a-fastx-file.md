# How to reorder a fasta0q file

### 0. prerequisite

fastx-utils https://github.com/jameslz/biostack-suits/releases

### 1. reorder a fasta/q file

File R014_R1.fq R014_R2.fq may be from parall KneadData pipeline.

	fastx-utils view  R014_R1.fq | fastx-utils reorder R014_R2.fq -  >R014_R2.reordered.fq


License: BY-NC-SA 4.0
