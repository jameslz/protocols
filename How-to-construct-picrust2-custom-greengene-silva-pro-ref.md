# How to construct picrust2 custom reference sequence and tree files

### download data

use sepp-refs: https://github.com/smirarab/sepp-refs

    wget https://github.com/smirarab/sepp-refs/blob/master/gg/sepp-package.tar.bz?raw=true  -O  sepp-package.tar.bz
    wget https://github.com/smirarab/sepp-refs/blob/master/silva/sepp-package-silva-2.tar.bz?raw=true   -O  sepp-package-silva-2.tar.bz
    wget https://github.com/smirarab/sepp-refs/blob/master/silva/sepp-package-silva.tar.bz?raw=true   -O  sepp-package-silva.tar.bz


### gg_13_5 pro_ref

    tar xjvf  sepp-package.tar.bz
    cd sepp-package/ref
    mkdir -p gg_13_5/pro_ref
    hmmbuild  -n  reference  gg_13_5/pro_ref/pro_ref.hmm    gg_13_5_ssu_align_99_pfiltered.fasta
    mv gg_13_5_ssu_align_99_pfiltered.fasta  gg_13_5/pro_ref/pro_ref.fna
    gzip  gg_13_5/pro_ref/pro_ref.fna
    wget https://github.com/picrust/picrust2/blob/master/picrust2/default_files/prokaryotic/pro_ref/pro_ref.model -O gg_13_5/pro_ref/pro_ref.model
    cp  reference-gg-raxml-bl-rooted.tre   gg_13_5/pro_ref/pro_ref.tre
    tar czvf  gg_13_5.tar.gz  gg_13_5


### silva 128 pro_ref

    tar xjvf  sepp-package-silva.tar.bz
    tar xjvf  sepp-package-silva-2.tar.bz
    cd sepp-package-silva/ref
    mkdir  -p   silva_128/pro_ref
    hmmbuild  -n  reference  silva_128/pro_ref/pro_ref.hmm    99_otus_aligned_masked1977.fasta
    mv  99_otus_aligned_masked1977.fasta  silva_128/pro_ref/pro_ref.fna
    gzip  silva_128/pro_ref/pro_ref.fna
    mv  reference-99_otus_aligned_masked1977.fasta-rooted.tre   silva_128/pro_ref/pro_ref.tre
    wget https://github.com/picrust/picrust2/blob/master/picrust2/default_files/prokaryotic/pro_ref/pro_ref.model -O silva_128/pro_ref/pro_ref.model
    tar czvf  silva_128.tar.gz  silva_128


### note

silva original data:  https://www.arb-silva.de/fileadmin/silva_databases/qiime/Silva_128_release.tgz


### summary

picrust2 default_files pro_ref
 
    $ fastx-utils  info  default_files/pro_ref/pro_ref.fna.gz


    #sequence       base    min_len max_len avg_len
    20000   31640000        1582    1582    1582.00

gg_13_5 pro_ref

    $ fastx-utils info  gg_13_5/pro_ref/pro_ref.fna.gz


    #sequence       base    min_len max_len avg_len
    203452  261435820       1285    1285    1285.00



    $ fastx-utils info  silva_128/pro_ref/pro_ref.fna.gz


    #sequence       base    min_len max_len avg_len
    395440  928888560       2349    2349    2349.00


### note
 
    place_seqs.py   --ref_dir  silva_128/pro_ref   -p  64   -s   zotus.fasta  -o  silva.tre  --intermediate placement_working

use default parameters, picrust2 removes sequences aligned poorly to reference sequences. We can use --min_align parameter placed more distant sequences

### next step

1. build the lastest silva database
2. use GTDB for picrust2 KEGG abundance prediction.

