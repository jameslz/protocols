# How to summarize taxonomy level using atlas-utils

### 0. prerequisite

atlas-utils
QIIME2 metadata.tsv 
QIIME2 feature-table.tsv

### 1. About data:

metadata.tsv

    Feature ID  Taxon Confidence
    #q2:types categorical categorical
    fa9f4f0810c07ba4234f810b967f8503  d__Bacteria; p__Campilobacterota; c__Campylobacteria; o__Campylobacterales; f__Arcobacteraceae; g__Pseudarcobacter  0.9914008841687375
    50be70bdfb77472d55195fc3f9255fab  d__Bacteria; p__Proteobacteria; c__Gammaproteobacteria; o__Oceanospirillales; f__Halomonadaceae; g__Halomonas 0.9998966503476293
    aafc8d239e064361659b02759eb9f3c2  d__Bacteria; p__Bacteroidota; c__Bacteroidia; o__Bacteroidales; f__Dysgonomonadaceae; g__Proteiniphilum 0.8230711359253776


feature-table.tsv

    #OTU ID A-1 A-2 B-1 B-2 C-1 C-2
    fa9f4f0810c07ba4234f810b967f8503  0 0 85  277 1821  3586
    50be70bdfb77472d55195fc3f9255fab  224 446 1265  1584  52  69
    aafc8d239e064361659b02759eb9f3c2  0 0 98  52  1246  2169
    0e672a8e5214425f86b8f730df839ff6  0 0 160 160 1109  2013
    6cda7252d7b4985d532e2b780dae46b5  0 0 0 11  1249  2145

### 2 annotation feature-table

    tail -n+3  metadata.tsv  | \
      cut -f1,2| \
      sed  's/__/:/g; s/; /,/g;' | \
      atlas-utils annotation  -  feature-table.tsv  \
    >feature-table.annotation.tsv


    atlas-utils  counts2freqs  feature-table.tsv  \
    > feature-table.freq.tsv


    tail -n+3  metadata.tsv  | \
      cut -f1,2| \
      sed  's/__/:/g; s/; /,/g;' | \
      atlas-utils annotation  -  feature-table.freq.tsv  \
    >feature-table.freq.annotation.tsv
    
### 3. taxonomy level summry

    atlas-utils  level  -l g   feature-table.freq.annotation.tsv  \
    >feature-table.freq.genus.tsv


    atlas-utils  level  -l g   feature-table.annotation.tsv  \
    >feature-table.genus.tsv


License: BY-NC-SA 4.0
    
