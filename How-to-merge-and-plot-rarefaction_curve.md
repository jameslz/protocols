# How to merge samples within group and rat

### 0. prerequisite

   [atlas-utils](https://github.com/jameslz/atlas-utils)
   [rtk](https://rdrr.io/cran/rtk/man/rtk.html)

### 1. group_by: sum for samples in the same group.

    $ export PATH=$PWD:$PATH
    $ cat  zotu_table.txt | head -n 6
    

    #OTU ID A-1     A-2     B-1     B-2     C-1     C-2
    ZOTU_1  0       0       87      278     1829    3608
    ZOTU_2  223     447     1268    1583    52      69
    ZOTU_3  0       0       162     159     1116    2021
    ZOTU_4  0       0       99      50      1250    2172
    ZOTU_5  0       0       1       8       1216    2143


    $ cat mapping_file.txt | head -n 6


    #SampleID       Description
    A-1     A
    A-2     A
    B-1     B
    B-2     B
    C-1     C


    $ atlas-utils  group_by zotu_table.txt  mapping_file.txt  > zotu_table.merge.txt
    $ cat  zotu_table.merge.txt | head -n 6


    #OTU ID A       B       C
    ZOTU_1  0       365     5437
    ZOTU_2  670     2851    121
    ZOTU_3  0       321     3137
    ZOTU_4  0       149     3422
    ZOTU_5  0       9       3359


### 2. rtk: rarefaction
    

    $rtk-steps  500:500:50000


    500,1000,1500,2000,2500,3000,3500,4000,4500,5000,5500,6000,6500,7000,7500,8000,8500,9000,9500,10000,10500,11000,11500,12000,12500,13000,13500,14000,14500,15000,15500,16000,16500,17000,17500,18000,18500,19000,19500,20000,20500,21000,21500,22000,22500,23000,23500,24000,24500,25000,25500,26000,26500,27000,27500,28000,28500,29000,29500,30000,30500,31000,31500,32000,32500,33000,33500,34000,34500,35000,35500,36000,36500,37000,37500,38000,38500,39000,39500,40000,40500,41000,41500,42000,42500,43000,43500,44000,44500,45000,45500,46000,46500,47000,47500,48000,48500,49000,49500,50000


    $ mkdir rare
    $ rtk   memory -i  zotu_table.merge.txt  -o  rare/zotu. -ns  -t 40 -d 500,1000,1500,2000,2500,3000,3500,4000,4500,5000,5500,6000,6500,7000,7500,8000,8500,9000,9500,10000,10500,11000,11500,12000,12500,13000,13500,14000,14500,15000,15500,16000,16500,17000,17500,18000,18500,19000,19500,20000,20500,21000,21500,22000,22500,23000,23500,24000,24500,25000,25500,26000,26500,27000,27500,28000,28500,29000,29500,30000,30500,31000,31500,32000,32500,33000,33500,34000,34500,35000,35500,36000,36500,37000,37500,38000,38500,39000,39500,40000,40500,41000,41500,42000,42500,43000,43500,44000,44500,45000,45500,46000,46500,47000,47500,48000,48500,49000,49500,50000  -r 50
    $ ls rare


    zotu.alpha_chao1.tsv  zotu.alpha_eve.tsv  zotu.alpha_invsimpson.tsv  zotu.alpha_richness.tsv  zotu.alpha_shannon.tsv  zotu.alpha_simpson.tsv  
    zotu.global_diversity.tsv  zotu.median_alpha_diversity.tsv



### 3. visualization


    $ cat metadata.txt


    A       A
    B       B
    C       C
    
    $ Rscript  rarefaction_curve.R  -t T  rare/zotu.alpha_shannon.tsv  metadata.txt  zotu.alpha_shannon.pdf  shannon



![](./zotu.alpha_shannon.png)    



License: BY-NC-SA 4.0