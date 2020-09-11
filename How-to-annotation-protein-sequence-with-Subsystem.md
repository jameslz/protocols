# How to annotation protein sequence with Subsystem

### 0. prerequisite

- superfocus
- diamond
- tsv-utils

### 1. download data

    wget edwards.sdsu.edu/superfocus/downloads/db.zip
    wget https://codeload.github.com/metageni/SUPER-FOCUS/tar.gz/0.35

### 2. formatdb


    unzip db.zip
    mkdir misc
    cat clusters/100_clusters/*   | \
       fastx-utils view -  | \
       sed  's/__/\t/g' | \
       perl -ane  'print qq{$F[0]\t$F[1]\n}' \
       > misc/annotation.txt


    cat clusters/100_clusters/*   | \
       fastx-utils view  -s - | \
       sed  's/__/\t/g' | \
       perl -ane  'print qq{>$F[0]\n$F[-1]\n}' \
       > sequence.faa


     diamond makedb --in sequence.faa --db sequence.dmnd


     tsv-utils view            \
       -c SUPER-FOCUS-0.35/superfocus_app/db/database_PKs.txt  \
     >misc/subsystem.txt


### 3. annotation


    diamond  blastp --id  60           \
       --more-sensitive -e 0.00001     \
       -k 1  --query  test.faa         \
       --db sequence.dmnd --outfmt 6   \
       --threads 40                    \
       --out  test.align.txt


    tsv-utils definition               \
       -c 2 misc/annotation.txt test.align.txt  | \
       tsv-utils annotation           \
       -c 3  misc/subsystem.txt -     \
    >test.annotation.txt


### 4. summary


    tsv-utils bins            \
       -t  14  -s 1           \
       test.annotation.txt |  \
       grep -v "-"            \
    > level1.txt


    tsv-utils bins            \
       -t  15  -s 1           \
       test.annotation.txt |  \
       grep -v "-"            \
    > level2.txt


    tsv-utils bins            \
       -t  16  -s 1           \
       test.annotation.txt |  \
       grep -v "-"            \
    > level3.txt


License: BY-NC-SA 4.0
