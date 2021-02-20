#UNIX Assignment Submission- Dua Vang 02/17/21

##Data Inspection

###Attributes of `fang_et_al_genotypes`

```
here is my snippet of code used for data inspection:

$ file fang_et_al 
$ ls -lh fang_et_al 
$ wc fang_et_al 
$ cat fang_et_al
$ head -n 1 fang_et_al
$ awk -F "\t" '{print NF; exit}' fang_et_al_genotypes.txt
$ cut -f3 fang_et_al_genotypes.txt

```

By inspecting this file I learned that:

1. This file is an ASCII text, with very long lines; meaning it does **not** include any non-ASCII characters that will interfere with future analyses of the file. 
2. This file has a size of 11M, which is extremely big. 
3. This file also has 2783 lines, 2744038 words, and 11051939 characters. 
3. This file has 986 columns. Column 3 has the group IDs that we'll need to use for data processing. 



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

##Data Processing

###Maize Data

```
here is my snippet of code used for data processing
```

Here is my brief description of what this code does


###Teosinte Data

```
here is my snippet of code used for data processing
```

Here is my brief description of what this code does
