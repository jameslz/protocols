# How to get Higher abundance matrix for KEGG Pathway Database from KO abundance matrix

### 0 prerequisite

tsv-utils
atlas-utils
kegg-kit


###  1. download kegg mapping files uing KEGG API

    curl http://rest.kegg.jp/list/ko       | sed 's/ko://'    >ko-definition.txt
    curl http://rest.kegg.jp/list/pathway  | sed 's/path://'  >pathway-definition.txt
    curl http://rest.kegg.jp/list/brite    | sed 's/br://'    >brite-definition.txt
    curl http://rest.kegg.jp/list/reaction | sed 's/rn://'    >reaction-definition.txt
    curl http://rest.kegg.jp/list/enzyme   | sed 's/ec://'    >enzyme-definition.txt
    curl http://rest.kegg.jp/list/module   | sed 's/md://'    >module-definition.txt


    curl http://rest.kegg.jp/link/pathway/ko  > ko-pathway-map.txt
    grep "path:map" ko-pathway-map.txt  | sed 's/ko://' | sed  's/path://' > ko-pathway.txt
    curl http://rest.kegg.jp/link/module/ko | sed 's/ko://' | sed  's/md://' > ko-module.txt
    curl http://rest.kegg.jp/link/reaction/ko | sed 's/ko://' | sed  's/rn://' > ko-reaction.txt
    curl http://rest.kegg.jp/link/enzyme/ko | sed 's/ko://' | sed  's/ec://' > ko-enzyme.txt
    curl http://rest.kegg.jp/link/brite/ko | sed 's/ko://' | sed  's/br://' > ko-brite.txt


    wget  "https://www.kegg.jp/kegg-bin/download_htext?htext=br08901.keg&format=htext&filedir=" -O  br08901.keg


    kegg-tk  keg2tab br08901.keg  | \
        tail -n+2 | \
        perl -ne 's/\t(\d+)\s+/\tmap$1\t/; print' \
        > br08901.txt


    tsv-utils bins -t2 -s1 ko-pathway.txt | \
      cut -f1,3 >pathway.ko.bin.txt


    grep -P ^"Metabolism" br08901.txt  |\
       tsv-utils cut -f2,3  - | \
       tsv-utils annotation -c 2 pathway.ko.bin.txt - | \
       grep -vP "\t-$" | \
       cut -f1,3 | tsv-utils unpack - | \
       tsv-utils cut -f2,1 - \
       >ko-catalog.txt


    tsv-utils cut -f2,1  br08901.txt  | \
      uniq   \
      > catalog-definition.txt

    rm  br08901.keg  br08901.txt  ko-pathway-map.txt


###  2. annotation using atlas utils


File:ko.txt
    #KO A-1 A-2 B-1 B-2 C-1 C-2
    K00001  0.000808980004315947    0.000819109861945028    0.000612948680922169    0.000635933480682086    0.000559136968670103    0.000542486474351325
    K00002  7.72038380845697e-06    7.22688422533568e-06    2.49766518305712e-05    1.57502121695051e-05    9.56550887982675e-05    9.858089243293e-05
    K00003  0.000587762479037045    0.000592135220229698    0.000519999878818898    0.000549497183764971    0.000666059282177164    0.000677436021110664


To get module level abudance, we can:


  atlas-utils  kann  ko-module.txt   ko.txt | \
      tsv-utils definition  -d " "  module-definition.txt  - \
      >module.txt


File:module.txt
    #catalog    A-1 A-2 B-1 B-2 C-1 C-2
    M00120 Coenzyme A biosynthesis, pantothenate => CoA 0.00164474  0.00163207  0.00150454  0.00148803  0.00191855  0.0019554
    M00121 Heme biosynthesis, plants and bacteria, glutamate => heme    0.00615696  0.00609589  0.00587453  0.00603027  0.00728818  0.00748394
    M00001 Glycolysis (Embden-Meyerhof pathway), glucose => pyruvate    0.00716742  0.00709134  0.00746521  0.00707085  0.00839727  0.00851415
    M00002 Glycolysis, core module involving three-carbon compounds 0.00428334  0.00426116  0.00478332  0.00460004  0.00532464  0.00542467
    M00003 Gluconeogenesis, oxaloacetate => fructose-6P 0.00544234  0.005411    0.00593274  0.00569337  0.00674525  0.00687093

License: BY-NC-SA 4.0
