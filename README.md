#UNIX Assignment Submission- Dua Vang 02/17/21

##Data Inspection

###Attributes of `fang_et_al_genotypes`

```
here is my snippet of code used for data inspection:

$ file fang_et_al_genotypes.txt
$ ls -lh fang_et_al_genotypes.txt
$ wc fang_et_al_genotypes.txt 
$ cat fang_et_al_genotypes.txt
$ head -n 1 fang_et_al_genotypes.txt
$ awk -F "\t" '{print NF; exit}' fang_et_al_genotypes.txt
$ cut -f3 fang_et_al_genotypes.txt
$ cut -f 3 fang_et_al_genotypes.txt |sort| uniq -c

```

By inspecting this file I learned that:

1. This file is an ASCII text, with very long lines; meaning it does **not** include any non-ASCII characters that will interfere with future analyses of the file. 
2. This file has a size of 11M, which is extremely big. 
3. This file also has 2783 lines, 2744038 words, and 11051939 characters. 
3. This file has 986 columns. 
	* 1st three columns:
		* Sample_ID
		* JG_OTU
		* Group
4. These are the counts of the unique words found in column 3/Group (which would be super helpful for data processing): 
	* 1 Group
	* 22 TRIPS
	* 15 ZDIPL
	* 17 ZLUXR
	* 10 ZMHUE
	* 290 ZMMIL
	* 1256 ZMMLR
	* 27 ZMMMR
	* 900 ZMPBA
	* 41 ZMPIL
	* 34 ZMPJA
	* 75 ZMXCH
	* 69 ZMXCP
	* 6 ZMXIL
	* 7 ZMXNO
	* 4 ZMXNT
	* 9 ZPERR


###Attributes of `snp_position.txt`

```
here is my snippet of code used for data inspection:

$ file snp_position.txt
$ ls -lh snp_position.txt
$ cat snp_position.txt
$ head snp_position.txt
$ awk -F "\t" '{print NF; exit}' snp_position.txt


```

By inspecting this file I learned that:

1. This file is also an ASCII file. 
2. It has a file size of 81K, which is not as nearly as big as the fang_ et_al file.
3. The code I used also revealed that it has 15 columns: 
	* SNP_ID	
	* cdv_ marker_id	
	* Chromosome	
	* Position	
	* alt_pos	
	* mult_positions	
	* amplicon
	* cdv_ map_feature.name	
	* gene	
	* candidate/random	
	* Genaissance_ daa_id	
	* Sequenom_ daa_id	
	* count_amplicons	
	* count_cmf	
	* count_gene

##Data Processing: file preparations

**1) Preparing an alternative SNP file that will only have columns SNP_ID, Position, and Chromosome:**

* a. Cut out the columns SNP_ID (1) , Position (3), and Chromosome (4) and double checking using ```head -n5```:

	```
	$ cut -f -1,3,4 snp_position.txt > snp_pos_chrom.txt
	$ head -n5 snp_pos_chrom.txt
	```
* b. Sort snp_position.txt alpha-numerically in column 1: 
	
	```
	$ (head -n1 snp_pos_chrom.txt && tail -n +2 snp_pos_chrom.txt | sort -f -k1,1) > snp_pos_chrom_sorted.txt
	```
	
* c. Check to make sure file is sorted and that the header is still there: 

	```
	$ head -n5 snp_pos_chrom_sorted.txt
	```
		

**2) Preparing files that contain 3 Maize groups = ZMMIL, ZMMLR, and ZMMMR :**



* a. Use ```awk``` to match for ZMMIL, ZMMLR, and ZMMMR and output into file called maize_genotypes.txt:

	```
	$ awk 'NR==1; $3 ~ /ZMMIL|ZMMLR|ZMMMR/' fang_et_al_genotypes.txt > maize_genotypes.txt
	```
	
	*```NR==1``` is used to ensure that the header is retained in the output file. 

* b. Use ```cut```, ```head```, and ```awk``` to double check that maize_genotypes.txt only has the 3 maize groups I want, along with all of the other data associated with each of them. 

	```
	$ cut -f3 maize_genotypes.txt |sort| uniq -c 
	$ head -n5 maize_genotypes.txt
	$ awk -F "\t" '{print NF; exit}' maize_genotypes.txt
	```
	```
	output: 	     
	290 ZMMIL   
	1256 ZMMLR  
	27 ZMMMR
	
	986 columns	  
	```			  

* c. Transpose maize_genotypes.txt:

	
	```
	$ awk -f transpose.awk maize_genotypes.txt > transposed_maize_genotypes.txt
	
	```
	
* d. Sort transposed_ maize_genotypes.txt alpha-numerically in column 1 while excluding the header using ```(head -n1```: 

	```
	$ (head -n1 transposed_maize_genotypes.txt && tail -n +2 transposed_maize_genotypes.txt | sort -f -k1,1) > transposed_maize_genotypes_sorted.txt
	
	```	
	
* e. To ensure that we still have the header along with the rest of the data, run ```head -n5```: 

	```
	$ head -n5 transposed_maize_genotypes_sorted.txt
	```


* f. Join transposed and sorted maize genotype with cut_ snp_sorted.txt at columns 1: 	

	* ```-t $'\t' ``` is inserted to set the delimiter as tabs and not space (which is the default delimiter for ```join```).
	*  ```-a1``` is to include lines that are unpairable in our cut snp file - meaning our header will also be included in our output.

	```
	$ join -t $'\t' -a1 -1 1 -2 1 snp_pos_chrom_sorted.txt transposed_maize_genotypes_sorted.txt > maizejoined.txt
	```
* g. Check to make sure our join was successful using ```wc -1```, ```head -n5``` and ```awk```:
	
	```
	$ wc -l  snp_pos_chrom_sorted.txt transposed_maize_genotypes_sorted.txt maizejoined.txt
	$ head -n 5 maizejoined.txt
	$ awk -F "\t" '{print NF; exit}' maizejoined.txt
	```	
	
	```
	output:  
		
	     
	984  cut_snp_position_sorted.txt
    986  transposed_maizegeno_header_sorted.txt
    984  maizejoined.txt  
    2954  total
    
    3 columns
	```
    
**2) Preparing files that contain 3 teosinte groups = ZMPBA, ZMPIL, and ZMPJA (Using the same commands above):**


* a. Use ```awk``` to match for ZMPBA, ZMPIL, and ZMPJA and output into file called teosinte_genotypes.txt:

	```
	$ awk 'NR==1; $3 ~ /ZMPBA|ZMPIL|ZMPJA/' fang_et_al_genotypes.txt > teosinte_genotypes.txt
	```
	
	* ```NR==1``` is used to ensure that the header is retained in the output file. 

* b. Use ```cut```, ```head```, and ```awk``` to double check that teosinte_genotypes.txt only has the 3 teosinte groups I want, along with all of the other data associated with each of them. 

	``` 
	$cut -f3 teosinte_genotypes.txt |sort| uniq -c 
	$ head -n5 teosinte_genotypes.txt
	$ awk -F "\t" '{print NF; exit}' teosinte_genotypes.txt
	
	```
	```
	output: 	
	900 ZMPBA     
	41 ZMPIL     
	34 ZMPJA 
	
	986 columns         
	```	

* c. Transpose teosinte_genotypes.txt:

	```
	  $ awk -f transpose.awk teosinte_genotypes.txt > transposed_teosinte_genotypes.txt
	
	```
	
* d. Sorting transposed_ teosinte_ genotypes.txt alpha-numerically in column 1 while excluding the header using ```(head -n1```: 

	```
	$ (head -n1 transposed_teosinte_genotypes.txt && tail -n +2 transposed_teosinte_genotypes.txt | sort -f -k1,1) > transposed_teosinte_genotypes_sorted.txt
	```	
	
* e. To ensure that we still have the header along with the rest of the data, run ```head -n5```: 

	```
	$ head -n5 transposed_teosinte_genotypes_sorted.txt
	```

* f. Joining transposed and sorted teosinte genotype with cut_ snp_sorted.txt at columns 1: 	

	* ```-t $'\t' ``` is inserted to set the delimiter as tabs and not space (which is the default delimiter for ```join```).
	*  ```-a1``` is to include lines that are unpairable in our cut snp file - meaning our header will also be included in our output.

	```
	$ join -t $'\t' -a1 -1 1 -2 1 snp_pos_chrom_sorted.txt transposed_teosinte_genotypes_sorted.txt > teosintejoined.txt
	```
	
* g. Checking to make sure our join was successful using ```wc -1```, ```head -n5``` and ```awk```:
	
	```
	$ wc -l  snp_pos_chrom_sorted.txt transposed_teosinte_genotypes_sorted.txt teosintejoined.txt
	$ head -n 5 teosintejoined.txt
	$ awk -F "\t" '{print NF; exit}' teosintejoined.txt
	```	
	
	```
	output:  

	984 snp_ pos_ chrom_ sorted.txt_      
    986  transposed_ teosinte_ genotypes_sorted.txt     
    984  teosintejoined.txt
    2954 total
    
    3 columns
	```


##Data Processing: Maize Data Codes and Analysis for the following tasks
For maize (Group = ZMMIL, ZMMLR, and ZMMMR in the third column of the fang_et _al _genotypes.txt file) we want 20 files in total:

**1) 10 files (1 for each chromosome) with SNPs ordered based on increasing position values and with missing data encoded by this symbol: ?**

* a. First, we need to determine how many groups are in each chromosome and how many snps we have that are unknown and multiple positions of the genome: 

	```
	$ cut -f2 maizejoined.txt |sort| uniq -c 
	$ cut -f3 maizejoined.txt |sort| uniq -c 
	```
	
* b. Next, we have to use ```awk``` and ```sort```  to cut and sort each chromosome by increasing position values and with missing data encoded by this symbol: ? using ```sed```: 

	```
	$ for i in {1..10}; do awk 'NR==1; $2=='$i'' maizejoined.txt | (head -n1 && tail -n +2 | sort -k3,3n) | sed 's/-/?/g' > chr"$i"_maize_genotypes.txt; done
	$ head -n2 chr1__maize_genotypes.txt
	$ tail -n2 chr1__maize_genotypes.txt	
	```
	
*i* is our variable for column 2. For every i (1-10), it will print our header, sort numerically in column 3 (except for the header), and then match any missing data with "?" 

**2) 10 files (1 for each chromosome) with SNPs ordered based on decreasing position values and with missing data encoded by this symbol: -**

```
$ for i in {1..10}; do awk 'NR==1; $2=='$i'' maizejoined.txt | (head -n1 && tail -n +2 | sort -k3,3nr) | sed 's/?/-/g' > chr"$i"_maize_genotypes_reverse.txt; done
$ head -n2 chr1__maize_genotypes_reverse.txt
$ tail -n2 chr1__maize_genotypes_reverse.txt
```

**3) 1 file with all SNPs with unknown positions in the genome (these need not be ordered in any particular way)**

```
$ awk 'NR==1; $3 ~ /unknown/' maizejoined.txt > maize_unknown_genotypes.txt
	
$ cut -f3 maize_unknown_genotypes.txt |sort| uniq -c
```

**4) 1 file with all SNPs with multiple positions in the genome (these need not be ordered in any particular way)**


```
$ awk 'NR==1; $3 ~ /multiple/' maizejoined.txt > maize_multiple_genotypes.txt
$ cut -f3 maize_multiple_genotypes.txt |sort| uniq -c
```



##Data Processing: Teosinte Data and Analysis for the following tasks
For teosinte (Group = ZMPBA, ZMPIL, and ZMPJA in the third column of the fang_et_al_genotypes.txt file) we want 20 files in total

**1) 10 files (1 for each chromosome) with SNPs ordered based on increasing position values and with missing data encoded by this symbol: ?**

* a. First, we need to determine how many groups are in each chromosome and how many snps we have that are unknown and multiple positions of the genome: 


	```
	$ cut -f2 teosintejoined.txt |sort| uniq -c 
	$ cut -f3 teosintejoined.txt |sort| uniq -c 
	```

* b. Next, we have to use ```awk``` and ```sort```  to cut and sort each chromosome by increasing position values and with missing data encoded by this symbol: ? using ```sed```: 

	```
	$ for i in {1..10}; do awk 'NR==1; $2=='$i'' teosintejoined.txt | (head -n1 && tail -n +2 | sort -k3,3n) | sed 's/-/?/g' > chr"$i"_teosinte_genotypes.txt; done
	
	$ head -n2 chr1_teosinte_genotypes.txt
	$ tail -n2 chr1_teosinte_genotypes.txt
	
	```
	
*i* is our variable for column 2. For every i (1-10), it will print our header, sort numerically in column 3 (except for the header), and then match any missing data with "?" 

**2) 10 files (1 for each chromosome) with SNPs ordered based on decreasing position values and with missing data encoded by this symbol: -**

```
$ for i in {1..10}; awk 'NR==1; $2=='$i'' teosintejoined.txt | (head -n1 && tail -n +2 | sort -k3,3nr) | sed 's/?/-/g' > chr"$i"_teosinte_genotypes_reverse.txt 
$ head -n2 chr1_teosinte_genotypes_reverse.txt
$ tail -n2 chr1_teosinte_genotypes_reverse.txt
```

**3) 1 file with all SNPs with unknown positions in the genome (these need not be ordered in any particular way)**

```
$ awk 'NR==1; $3 ~ /unknown/' teosintejoined.txt  > teosinte_unknown_genotypes.txt	
$ cut -f3 teosinte_unknown_genotypes.txt |sort| uniq -c
$ head -n2 teosinte_unknown_genotypes.txt
```

**4) 1 file with all SNPs with multiple positions in the genome (these need not be ordered in any particular way)**

```
$ awk 'NR==1; $3 ~ /multiple/' teosintejoined.txt  > teosinte_mulitple_genotypes.txt
$ cut -f3 teosinte_mulitple_genotypes.txt|sort| uniq -c
$ head -n2 teosinte_mulitple_genotypes.txt
```


**5) Clean filed and organize into their respective folders:**


```
$ mkdir all_maize_genotypes
$ mkdir all_teosinte_genotypes 
$ mkdir intermed_files
```

**6) Final step - save, commit, and push files to Git repository:** 

```
$ git add . 
$ git commit -m “update"
$ git push 
```
