#Unix Assignment
This is the Unix assignment of BCB546X 2017 spring semester.
Requiements:
* Document the work in a version-controlled repository using `git`.
* The repository should include a `README.md` file in Markdown format.
* The README.md file describes the workflow for the data inspection and data processing and all the files created.
* When the assignment is finished, send a url linking to the GitHub repository via a Slack direct message to all three instructors.

My assignment consist of two components:
## _1. Data Inspection_

### Describe the structure of `fang_et_al_genotypes.txt` and `snp_position.txt`
* File size
```
$ du -lh fang_et_al_genotypes.txt snp_position.txt
11M     fang_et_al_genotypes.txt
84K     snp_position.txt
```
* Number of lines, words and bytes
```
$ wc  fang_et_al_genotypes.txt snp_position.txt
2782  2744038 11051938 fang_et_al_genotypes.txt
984    13198    82763 snp_position.txt
3766  2757236 11134701 total
``` 

* Number of columns
```
$ awk '{print NF; exit}' fang_et_al_genotypes.txt
986
```
```
$ awk '{print NF; exit}' snp_position.txt
15
```
* The first four columns of `snp_position.txt`
```
$ head -n 1 snp_position.txt | awk '{print $1,$2,$3,$4}'
SNP_ID cdv_marker_id Chromosome Position
```
* The first four columns of `fang_et_al_genotype.txt`
```
$ head -n 1 fang_et_al_genotypes.txt | awk '{print $1,$2,$3,$4}'
Sample_ID JG_OTU Group abph1.20
```

##_2. Data Processing_

Required file format

|SNP_ID| Chromosome | Position | genotype |
|------|:-----------|----------|---------:|

### 2.1 Extract the genotype file 
####2.1.1 group ZMMIL,AMMLR and ZMMMR
keep the head

```
$ head -n 1 fang_et_al_genotypes.txt > zmlrr.txt

```
append the ZMMIL, ZMMLR, and ZMMMR geneotype data to zmlrr.txt
```
$ awk '$3 ~ /ZMMIL|ZMMLR|ZMMMR/ {print $0}' fang_et_al_genotypes.txt >> zmlrr.txt
```
delete the 2nd and 3rd columns and save the other columns to the newfile zmlrr_genotype.txt
```
$ cut -f 1,4-986 zmlrr.txt > zmlrr_genotype.txt
```
check the columns number of the zmlrr_genotype.txt
```
$ awk '{print NF; exit}' zmlrr_genotype.txt
984
```
check the first four columns and the first four rows fo the zmlrr_genotype.txt
```
$ head -n 3 zmlrr_genotype.txt | awk '{print $1,$2,$3,$4}'
Sample_ID abph1.20 abph1.22 ae1.3
ZDP_0752a C/G A/A T/T
ZDP_0793a C/G A/A T/T
```
transpose the zmlrr_genotype.txt file to the zmlrr_trans_genotype.txt
```
$ awk -f transpose.awk zmlrr_genotype.txt > zmlrr_trans_genotype.txt
```
check the first ten rows and the first three columns of the zmlrr_trans_genotype.txt
```
$ head zmlrr_trans_genotype.txt | awk '{print $1,$2,$3}'
Sample_ID ZDP_0752a ZDP_0793a
abph1.20 C/G C/G
abph1.22 A/A A/A
ae1.3 T/T T/T
ae1.4 G/G G/G
ae1.5 C/C C/T
an1.4 C/G C/G
ba1.6 G/G A/G
ba1.9 G/G G/T
bt2.5 C/C C/C
```

sort the zmlrr_trans_genotype.txt to zmlrr_trans_genotype_sorted.txt
```
$ tail -n +2 zmlrr_trans_genotype.txt | sort -k1,1 > zmlrr_trans_genotype_sorted.txt
```

check the first ten rows and the first three columns of zmlrr_trans_genotype_sorted.txt
```
$ head zmlrr_trans_genotype_sorted.txt | awk '{print $1,$2,$3}'
abph1.20 C/G C/G
abph1.22 A/A A/A
ae1.3 T/T T/T
ae1.4 G/G G/G
ae1.5 C/C C/T
an1.4 C/G C/G
ba1.6 G/G A/G
ba1.9 G/G G/T
bt2.5 C/C C/C
bt2.7 ?/? ?/?
```



####2.1.2 ZMMIL,AMMLR and ZMMMR
keep the head
```
$ head -n 1 fang_et_al_genotypes.txt > zmala.txt

```
append the ZMMIL, ZMMLR, and ZMMMR data to zmala.txt
```
$ awk '$3 ~ /ZMPBA|ZMPIL|ZMPJA/ {print $0}' fang_et_al_genotypes.txt >> zmala.txt
```
delete the 2nd and 3rd columns and save the other columns to the new file zmala_genotype.txt
```
$ cut -f 1,4-986 zmala.txt > zmala_genotype.txt 
```
check the first four columns and the first rows of the zmala_genotype.txt
```
$ awk '{print $1,$2,$3,$4}' zmala_genotype.txt | head -n 4
Sample_ID abph1.20 abph1.22 ae1.3
S0881 C/G A/A T/T
S1057 C/G A/A G/T
S1087 G/G A/A T/T
```

transpose the zmala_genotype.txt file to the zmala_trans_genotype.txt
```
$ awk -f transpose.awk zmala_genotype.txt > zmala_trans_genotype.txt
```

check the first columns and first rows of the zmala_trans_geneotype.txt
```
$ head -n 4 zmala_trans_genotype.txt | awk '{print $1,$2,$3,$4}'
Sample_ID S0881 S1057 S1087
abph1.20 C/G C/G G/G
abph1.22 A/A A/A A/A
ae1.3 T/T G/T T/T
```


sort the zmala_trans_genotype.txt to the file zmala_trans_genotype_sorted.txt
```
$ tail -n +2 zmala_trans_genotype.txt | sort -k1,1 > zmala_trans_genotype_sorted.txt
```
check the first ten rows and the first three columns of zmala_trans_genotype_sorted.txt
```
$ head zmala_trans_genotype_sorted.txt | awk '{print $1,$2,$3}'
abph1.20 C/G C/G
abph1.22 A/A A/A
ae1.3 T/T G/T
ae1.4 G/G A/G
ae1.5 T/T T/T
an1.4 C/C C/C
ba1.6 A/G A/A
ba1.9 G/G G/G
bt2.5 T/T C/T
bt2.7 A/A A/A
```

###2.2 Join the two files
####2.2.1 Extract the first , the third and the fourth columns of the snp_position.txt and save to the snp_positon_134.txt
```
$ cut -f 1,3,4 snp_position.txt > snp_position_134.txt
```
check the ten rows of the snp_position_134.txt
```
$ head snp_position_134.txt
SNP_ID  Chromosome      Position
abph1.20        2       27403404
abph1.22        2       27403892
ae1.3   5       167889790
ae1.4   5       167889682
ae1.5   5       167889821
an1.4   1       240498509
ba1.6   3       181362952
ba1.9   3       181362666
bt2.5   4       66290049
```

check the sort status of the snp_position_134.txt

```
$ sort -k1,1 -c snp_position_134.txt
sort: snp_position_134.txt:2: disorder: abph1.20        2       27403404
```

sort the snp_position.txt according to the SNP_ID and save to the snp_position_134_sorted.txt file
```
$ tail -n +2 snp_position_134.txt | sort -k1,1 > snp_position_134_sorted.txt
```
check the first ten rows of the snp_position_134_sorted.txt
```
$ head snp_position_134_sorted.txt
abph1.20        2       27403404
abph1.22        2       27403892
ae1.3   5       167889790
ae1.4   5       167889682
ae1.5   5       167889821
an1.4   1       240498509
ba1.6   3       181362952
ba1.9   3       181362666
bt2.5   4       66290049
bt2.7   4       66290994
```
#### 2.2.2 join the `snp_position_134_sorted.txt` and the `zmlrr_trans_genotype_sorted.txt`
```
$ join -t $'\t' -1 1 -2 1 snp_position_134_sorted.txt zmlrr_trans_genotype_sorted.txt > zmlrr_with_position.txt
```
check the first ten rows and first five columns of the zmlrr_with_position.txt
```
$ head zmlrr_with_position.txt | awk '{print $1,$2,$3,$4,$5}'
abph1.20 2 27403404 C/G C/G
abph1.22 2 27403892 A/A A/A
ae1.3 5 167889790 T/T T/T
ae1.4 5 167889682 G/G G/G
ae1.5 5 167889821 C/C C/T
an1.4 1 240498509 C/G C/G
ba1.6 3 181362952 G/G A/G
ba1.9 3 181362666 G/G G/T
bt2.5 4 66290049 C/C C/C
bt2.7 4 66290994 ?/? ?/?
```
check the last ten rows and first five columns of the zmlrr_with_position.txt
```
$ tail -n 10 zmlrr_with_position.txt | awk '{print $1,$2,$3,$4,$5}'
te1.3 3 163617892 A/G G/G
te1.4 3 163617645 G/G T/T
zagl1.1 1 4912526 A/A A/A
zagl1.6 1 4835658 T/T T/T
zap1.2 2 233128584 A/G A/A
zen1.1 unknown unknown G/G G/G
zen1.2 unknown unknown A/A A/A
zen1.4 unknown unknown ?/? T/T
zfl2.6 2 12543294 G/G C/C
zmm3.4 9 16966348 C/C C/C
```


####2.2.3 join the snp_position_134_sorted.txt and zmala_trans_genotype_sorted.txt
```
$ join -t $'\t' -1 1 -2 1 snp_position_134_sorted.txt zmala_trans_genotype_sorted.txt > zmala_with_position.txt
```

check the first ten rows and first five columns of the zmala_with_position.txt
```
$ head zmala_with_position.txt | awk '{print $1,$2,$3,$4,$5}'
abph1.20 2 27403404 C/G C/G
abph1.22 2 27403892 A/A A/A
ae1.3 5 167889790 T/T G/T
ae1.4 5 167889682 G/G A/G
ae1.5 5 167889821 T/T T/T
an1.4 1 240498509 C/C C/C
ba1.6 3 181362952 A/G A/A
ba1.9 3 181362666 G/G G/G
bt2.5 4 66290049 T/T C/T
bt2.7 4 66290994 A/A A/A
```
check the last ten rows and first five columns of the zmala_with_position.txt
```
$ tail zmala_with_position.txt | awk '{print $1,$2,$3,$4,$5}'
te1.3 3 163617892 G/G G/G
te1.4 3 163617645 G/G G/T
zagl1.1 1 4912526 A/C A/C
zagl1.6 1 4835658 C/T C/T
zap1.2 2 233128584 A/A A/G
zen1.1 unknown unknown G/G G/G
zen1.2 unknown unknown G/G A/G
zen1.4 unknown unknown T/T C/C
zfl2.6 2 12543294 G/G ?/?
zmm3.4 9 16966348 C/C ?/?
```


###2.3 For maize (Group = ZMMIL, ZMMLR, and ZMMR in the third column of the `fang_et_al_genotypes.txt` file) need to seperate into 20 files
* 10 files (1 for each chromosome) with SNPs ordered based on increasing position values and with missing data encoded by ?
```
$  more zmlrr_with_position.txt | sed 's/?.?/?/g' | sort -k2,2n -k3,3n | awk -F '\t' '{print >"zmlrr_with_incre_pos_chr"$2".txt"}'
```
check the files
```
$ wc *lrr*incre*
     53   83528  326406 zmlrr_with_incre_pos_chr10.txt
    155  244280  952543 zmlrr_with_incre_pos_chr1.txt
    127  200152  780527 zmlrr_with_incre_pos_chr2.txt
    107  168632  659471 zmlrr_with_incre_pos_chr3.txt
     91  143416  559941 zmlrr_with_incre_pos_chr4.txt
    122  192272  755862 zmlrr_with_incre_pos_chr5.txt
     76  119776  469119 zmlrr_with_incre_pos_chr6.txt
     97  152872  597395 zmlrr_with_incre_pos_chr7.txt
     62   97712  382559 zmlrr_with_incre_pos_chr8.txt
     60   94560  370064 zmlrr_with_incre_pos_chr9.txt
      6    9450   37068 zmlrr_with_incre_pos_chrmultiple.txt
     27   42552  166473 zmlrr_with_incre_pos_chrunknown.txt
    983 1549202 6057428 total
```


* 10 files (1 for each chromosome) with SNPs ordered based on decreasing position values and with missing data encoded by -
```
$ more zmlrr_with_position.txt | sed 's/?.?/-/g' | sort -k2,2n -k3,3nr | awk -F '\t' '{print >"zmlrr_with_decre_pos_chr"$2".txt"}'
```

check the files
```
$ wc *lrr*de*
     53   83528  326406 zmlrr_with_decre_pos_chr10.txt
    155  244280  952543 zmlrr_with_decre_pos_chr1.txt
    127  200152  780527 zmlrr_with_decre_pos_chr2.txt
    107  168632  659471 zmlrr_with_decre_pos_chr3.txt
     91  143416  559941 zmlrr_with_decre_pos_chr4.txt
    122  192272  755862 zmlrr_with_decre_pos_chr5.txt
     76  119776  469119 zmlrr_with_decre_pos_chr6.txt
     97  152872  597395 zmlrr_with_decre_pos_chr7.txt
     62   97712  382559 zmlrr_with_decre_pos_chr8.txt
     60   94560  370064 zmlrr_with_decre_pos_chr9.txt
      6    9450   37068 zmlrr_with_decre_pos_chrmultiple.txt
     27   42552  166473 zmlrr_with_decre_pos_chrunknown.txt
    983 1549202 6057428 total
```




###2.4 For teosinte (Group = ZMPBA, ZMPIL, and ZMPJA in the third column of the `fang_et_al_genotypes.txt` fiel) need to seperate into 20 files:

* 10 files (1 for each chromosome) with SNPs ordered based on increasing positions values and with missing data encoded by ?
```
$ more zmala_with_position.txt | sed 's/?.?/?/g' | sort -k2,2n -k3,3n | awk -F '\t' '{print >"zmala_with_incre_pos_chr"$2".txt"}'
```
check the files
```
wc *incre*
     53   51834  203652 zmala_with_incre_pos_chr10.txt
    155  151590  593685 zmala_with_incre_pos_chr1.txt
    127  124206  486803 zmala_with_incre_pos_chr2.txt
    107  104646  410133 zmala_with_incre_pos_chr3.txt
     91   88998  347659 zmala_with_incre_pos_chr4.txt
    122  119316  469278 zmala_with_incre_pos_chr5.txt
     76   74328  291107 zmala_with_incre_pos_chr6.txt
     97   94866  373563 zmala_with_incre_pos_chr7.txt
     62   60636  238051 zmala_with_incre_pos_chr8.txt
     60   58680  230668 zmala_with_incre_pos_chr9.txt
      6    5862   23214 zmala_with_incre_pos_chrmultiple.txt
     27   26406  103275 zmala_with_incre_pos_chrunknown.txt
    983  961368 3771088 total
```

* 10 files (1 for each chromosome) with SNPs ordered based on decreasing position values and with missing data encoded by - 
```
$ $  more zmala_with_position.txt | sed 's/?.?/-/g' | sort -k2,2n -k3,3nr | awk -F '\t' '{print >"zmala_with_decre_pos_chr"$2".txt"}'
```
check the files 
```
$ wc *ala*de*
     53   51834  203652 zmala_with_decre_pos_chr10.txt
    155  151590  593685 zmala_with_decre_pos_chr1.txt
    127  124206  486803 zmala_with_decre_pos_chr2.txt
    107  104646  410133 zmala_with_decre_pos_chr3.txt
     91   88998  347659 zmala_with_decre_pos_chr4.txt
    122  119316  469278 zmala_with_decre_pos_chr5.txt
     76   74328  291107 zmala_with_decre_pos_chr6.txt
     97   94866  373563 zmala_with_decre_pos_chr7.txt
     62   60636  238051 zmala_with_decre_pos_chr8.txt
     60   58680  230668 zmala_with_decre_pos_chr9.txt
      6    5862   23214 zmala_with_decre_pos_chrmultiple.txt
     27   26406  103275 zmala_with_decre_pos_chrunknown.txt
    983  961368 3771088 total
```
Total of 40 files were produced.




