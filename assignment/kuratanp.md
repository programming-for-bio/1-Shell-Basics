# assignment-1
## I. Get the data files
Download the following data files from the internet using the curl command: `http://eaton-lab.org/pdsb/test.fastq.gz` and `http://eaton-lab.org/pdsb/iris-data-dirty.csv`. Use the less or zless commands to look at the files. Then use the head command to print the first 5 lines from each file as output. 

I downloaded `http://eaton-lab.org/pdsb/test.fastq.gz` 
and  `http://eaton-lab.org/pdsb/iris-data-dirty.csv` using the curl command.  I wanted to see the fastq file without uncompressing it. So, I found the command `zcat < test.fastq.gz` at this [stack overflow website](https://stackoverflow.com/questions/8151380/how-to-get-few-lines-from-a-gz-compressed-file-without-uncompressing). I also used the `-n` option followed by an integer to indicate the number of lines to return.
```
> curl http://eaton-lab.org/pdsb/test.fastq.gz > test.fastq.gz 
> curl http://eaton-lab.org/pdsb/iris-data-dirty.csv > dirty.csv

> head -n 5 dirty.csv 
> zcat < test.fastq.gz | head -n 5 
```
```
> head -n 5 dirty.csv 
5.1,3.5,1.4,0.2,Iris-setosa
4.9,3.0,1.4,0.2,Iris-setosa
4.7,3.2,1.3,0.2,Iris-setosa
4.6,3.1,1.5,0.2,Iris-setosa
5.0,3.6,1.4,0.2,Iris-setosa
```
```
> zcat < test.fastq.gz | head -n 5 
@29154_superba.1 GRC13_0027_FC:4:1:12560:1179 length=74
TGCAGGAAGGAGATTTTCGNACGTAGTGNNNNNNNNNNNNNNGCCNTGGATNNANNNGTGTGCGTGAAGAANAN
+29154_superba.1 GRC13_0027_FC:4:1:12560:1179 length=74
IIIIIIIGIIIIIIFFFFF#EEFE<?################################################
@29154_superba.2 GRC13_0027_FC:4:1:15976:1183 length=74
TGCAGTTGTAAATACAAATATCCCAAAANNNNGNNNNNNNTNTAATATTTTGNAANNTTGAGGGGTGTGATNTN
+29154_superba.2 GRC13_0027_FC:4:1:15976:1183 length=74
GGGGHHHHHHHHHHHHHDHGHHHHCAAA##############################################
@29154_superba.3 GRC13_0027_FC:4:1:19092:1179 length=74
TGCAGGCTCTGACAAAGAANTCGACTGANNNNNNNNNNNNNNCACNGGTTCNNGNNNATGTCAATGTGGTANAN
Teachings-MacBook-Air:prog_homework teachinghouse$ zcat <test.fastq.gz | head -n 5
@29154_superba.1 GRC13_0027_FC:4:1:12560:1179 length=74
TGCAGGAAGGAGATTTTCGNACGTAGTGNNNNNNNNNNNNNNGCCNTGGATNNANNNGTGTGCGTGAAGAANAN
+29154_superba.1 GRC13_0027_FC:4:1:12560:1179 length=74
IIIIIIIGIIIIIIFFFFF#EEFE<?################################################
@29154_superba.2 GRC13_0027_FC:4:1:15976:1183 length=74
```

## II. Clean the data

Use `grep`, `uniq`, `sed`. Check that all of the species names are spelled correctly in the file `iris-data-dirty.csv`. Also check for missing values stored as `NA`. Create a new file where mispelled names are replaced with the correct values, and lines with `NA` are excluded, and save it as `iris-data-clean.csv`. Use `cut`, `sort` and `uniq` to list the number of data values there are for each species in the new cleaned data file.

First, I used `grep -n` to print all lines with _Iris-setosa_ including a misspelled one by searching for the pattern _sa_. Second, I used `grep -nv` to get all lines without much. This way, I can only see the ones that are misspelled and able to correct them. Third, using `sed`, I corrected the taxon name and saved it as a new csv file. 
I applied the same command lines for other taxa. To delete the lines containing the pattern 'NA', I used `sed '/NA/d'` (d for delete). I came up with this command line by reading this [website](http://www.theunixschool.com/2012/06/sed-25-examples-to-delete-line-or.html). Finally, I again used  `grep -n` to find the number of each taxa from my cleaned csv file.

```
> grep -n "..sa" dirty.csv
> grep -nv Iris-setosa dirty.csv 
> sed 's/setsa/setosa/g' dirty.csv > dirty1.csv 

> grep -n "..ver" dirty1.csv
> grep -nv Iris-versicolor dirty1.txt 
> sed 's/Iris-versicolour/Iris-versicolor/g' dirty1.csv > dirty2.csv

> grep -n "..versi" dirty2.txt 
> grep -nv virginica dirty2.txt 
  (all names were correct)

> sed '/NA/d' dirty2.csv > dirty3.csv
> less -s dirty3.txt 
> sed '/NA/d' dirty2.txt > iris-data-clean.csv

> grep -n "..se" iris-data-clean.csv -c
> grep -n "..virgini" iris-data-clean.csv -c
> grep -n "..ver" iris-data-clean.csv -c
```
```
Iris-setosa 50
Iris-virginica 50
Iris-versicolor 48
```

## III. Summarize sequence data file
Find how many lines in the data file `test.fastq.gz` start with "TGCAG" and end with "GAG"

I first decompressed `test.fastq.gz`. Then I used the `test.fastq`file to find sequences starting with "TGCAG" and saved them as a new fastq file. Next, I again used `grep` to select lines ending with "GAG" from the new fastq file I just created, along with the `-c`option, which would return the line number.  
```
> gunzip test.fastq.gz
> grep ^TGCAG test.fastq > test_s.fastq 
> grep GAG$ test_s.fastq -c
```
Answer: 
```
44
```
## IV. Summarize sequence data file
Write a `for-loop` to separate the reads from the file `test.fastq.gz` based on the taxon name in the label, and write the reads to separately named files in the new directory called `sorted_reads/`. The answer to this question will require more than a single line. See the lecture materials about using variables in for-loops. This will also be tricky because each read in the data file spans four lines (this is a standard genetic sequence file format), so for each read that you correctly identify you must grab the line with the sequence data below it, as well as the repeat label after that, and the quality information line after that. For a hint, see additional options for the `grep` command that can be used to select multiple lines.

I referenced the lecuture notes to come up with the following for loop commands. I first created a variable names `var` containing all of the plant names seperated by a space. In the `for loop`, each plant is investigated one after another. The `for` statement executes the commands indicated in a body.  I piped `echo` `grep` `mv` in order to outputs the string (plant name), to select 4 lines belong to each sequence belong to the plant name, and save as new fastq file label with its plant name, and the file is placed in  `sorted_reads/`.
```
> var=”superba thamno cyathophylla przewalskii rex cyathophylloides”

> for plant in  $var 
> do
>  echo $plant | grep $plant test.fastq -A 4 > $plant.fastq | mv $plant.fastq sorted_reads/
> done
```
