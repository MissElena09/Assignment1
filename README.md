# UNIX Assignment

## Data Inspection

### Attributes of fang_et_al_genotypes.txt

```
here is my snippet of code used for data inspection:
```
```
$head -n 1 fang_et_al_genotypes.txt
```
```
$tail -n 1 fang_et_al_genotypes.txt
```
```
$wc -l fang_et_al_genotypes.txt (lines)
```

```
$du -h fang_et_al_genotypes.txt (file size)
```

```
$awk -F "\t" '{print NF; exit}'fang_et_al_genotypes.txt
```
By inspecting this file I learned that:

1. This file has 2783 lines
2. This file has 986 columns
3. File size = 6.1M

### Attributes of snp_position.txt

```
here is my snippet of code used for data inspection:
```
```
$head -n 1 snp_position.txt
```
```
$tail -n 1 snp_position.txt
```
```
$wc -l snp_position.txt (lines)
```

```
$du -h snp_position.txt (file size)
```

```
$awk -F "\t" '{print NF; exit}'snp_position.txt (columns)
```
By inspecting this file I learned that:

1. This file has 984 lines
2. This file has 15 columns
3. File size = 38K

## DATA PROCESSING
### Inspecting fang_et_al_genotypes.txt

```
$cut -f3 fang_et_al_genotypes.txt | sort| uniq
```
This command is used to investigate how many different groups there are.

### Data extraction
```
$grep -E "(ZMMIL|ZMMLR|ZMMMR)" fang_et_al_genotypes.txt > maize_genotypes.txt
```
This command extracts the maize genotype data from the fang_et_al_genotypes.txt(without header info)

```
$grep -E "(ZMPBA|ZMPIL|ZMPJA)" fang_et_al_genotypes.txt > teosinte_genotypes.txt
```
This command extracted the teosinte genotype data from the fang_et_al_genotypes.txt(without header info)

```
$head -n 1 fang_et_al_genotypes.txt > header.txt
```
This command extracts the header information from the fang_et_al_genotypes.txt file

```
$cat maize_genotypes.txt >> header.txt
```
combine the header information with the maize_genotype information

```
$mv header.txt maize_genotypes.txt
```
this renames the header.txt to mainze_genotypes.txt

the same was repeated for the teosinte_genotypes.txt
### Checkpoint
```
$awk -F "\t" '{print NF; exit}' maize_genotypes.txt
$awk -F "\t" '{print NF; exit}' teosinte_genotypes.txt
```
checking the column number for maize_genotypes and teosinte_genotypes data

### Extract columns
```
$cut -f 3-986 maize_genotypes.txt > maize_genotypes_groupfirst.txt
```
use cut to extract column 3-986 in maize_genotypes.txt and send the standard out to maize_genotypes_groupfirst.txt

```
$cut -f 3-986 teosinte_genotypes.txt > teosinte_genotypes_groupfirst.txt
```
repeat for teosite_genotypes.txt

```
$cut -f 1,3,4 snp_position.txt > snp_position_beforeJoin.txt
```
Extract the snp_id, chromosome, nucleotide location column from the snp_position.txt

### Transpose
```
$awk -f transpose.awk maize_genotypes_groupfirst.txt>transposed_maize_genotypes.txt
```
transpose maize_genotypes_groupfirst.txt file

```
$awk -f transpose.awk teosinte_genotypes_groupfirst.txt > transposed_teosinte_genotypes.txt
```
transpose teosinte_genotypes_groupfirst.txt file

### Sort files

```
$sort -k1,1 snp_position_beforeJoin.txt >sorted_snp_position.txt
```

sort the snp_position_beforeJoin.txt and send it to sorted_snp_position.txt by first column


```
$sort -k1,1 transposed_maize_genotypes.txt>sorted_maize_genotypes.txt
$sort -k1,1 transposed_teosinte_genotypes.txt>sorted_teosinte_genotypes.txt
```

sort the transposed_maize_genotypes.txt to sorted_maize_genotypes.txt, repeat for the transposed_teosinte_genotypes.txt by first column

### Join files
```
$join -1 1 -2 1 -t $'\t' sorted_snp_position.txt sorted_teosinte_genotypes.txt > sorted_joined_teosinte_genotypes.txt

$join -1 1 -2 1 -t $'\t' sorted_snp_position.txt sorted_maize_genotypes.txt > sorted_joined_maize_genotypes.txt

```

join the files together(no header info) by first column(common column): snp_id

### Sort files by increasing/decreasing position value
**Use inverse grep to get rid of the multiple/unknown position**

```
$ grep -v "multiple" sorted_joined_maize_genotypes.txt |grep -v "unknown"| sort -k3,3n > increasing_maize_genotypes.txt
```

sort it by increasing position

```
grep -v "multiple" sorted_joined_maize_genotypes.txt |grep -v "unknown"| sort -k3,3nr > decreasing_maize_genotypes.txt
```
sort it by decreasing position

```
cut -f 1-5 decreasing_maize_genotypes.txt | head
```
check the content

```
$grep -v "multiple" sorted_joined_teosinte_genotypes.txt |grep -v "unknown" | sort -k3,3n > increasing_teosinte_genotypes.txt

$grep -v "multiple" sorted_joined_teosinte_genotypes.txt |grep -v "unknown"| sort -k3,3r> decreasing_teosinte_genotypes.txt
```
repeat for teosinte
### Seperation of files
**chromosome number**
```
$for i in {1..10}; do awk '$2=='$i'' increasing_maize_genotypes.txt|sort -k3,3n > maize/maize_chr"$i"_increasing.txt; done
```
seperating the maize genotype files, increasing postion value, by chromosome number

```
$for i in {1..10}; do awk '$2=='$i'' increasing_teosinte_genotypes.txt|sort -k3,3n > teosinte/teosinte_chr"$i"_increasing.txt; done
```
seperating the teosinte genotype files, increasing position value, by chromosome number

**multiple/unknown position**
```
$grep "multiple" sorted_joined_maize_genotypes.txt>maize/maize_multiple_position.txt
```
maize genotype file with all SNPs with multiple positions in the genome

```
$grep "unknown" sorted_joined_maize_genotypes.txt>maize/maize_unknown_position.txt
```
maize genotype file with all SNPs with unknown positions in the genome

```
$grep "multiple" sorted_joined_teosinte_genotypes.txt>teosinte/teosinte_multiple_position.txt
```
teosinte genotype file with all SNPs with multiple positions in the genome

```
$grep "unknown" sorted_joined_teosinte_genotypes.txt>teosinte/teosinte_unknown_position.txt
```
teosinte genotype file with all SNPs with unknown positions in the genome

### Replace "?" by "-", Seperation of files 
```
$sed 's/?/-/g' decreasing_maize_genotypes.txt> decreasing_maize_genotypes_replacedmissingvalue.txt
```
replace all the missing value encoded by ? with - for maize

```
$sed 's/?/-/g' decreasing_teosinte_genotypes.txt> decreasing_teosinte_genotypes_replacedmissingvalue.txt
```
replace all the missing value encoded by ? with - for teosinte


```
$for i in {1..10}; do awk '$2=='$i'' decreasing_maize_genotypes_replacedmissingvalue.txt > maize/maize_chr"$i"_decreasing.txt; done
```
10 files (1 for each chromosome) with SNPs ordered based on decreasing position values and with missing data encoded by this symbol: - (maize)


```
$for i in {1..10}; do awk '$2=='$i'' decreasing_teosinte_genotypes_replacedmissingvalue.txt > teosinte/teosinte_chr"$i"_decreasing.txt; done
```
10 files(1 for each chromosome with SNPs ordered based on decreasing position values and with missing data encoded by this symbol: - (teosinte)



