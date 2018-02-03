# Assignment - 1

Shell Basics Assignment  
  
Based on instructions as per the first assignment, I have created a new branch and shall pull these edits in with the master branch. 

#### Question 1 : Download data and visualize it

```
# curl did not work for me and I used wget instead

wget http://eaton-lab.org/pdsb/test.fastq.gz

--2018-01-28 14:06:37--  http://eaton-lab.org/pdsb/test.fastq.gz
Resolving eaton-lab.org (eaton-lab.org)... 192.30.252.154, 192.30.252.153
Connecting to eaton-lab.org (eaton-lab.org)|192.30.252.154|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 225374 (220K) [application/gzip]
Saving to: ‘test.fastq.gz’

test.fastq.gz           100%[=====================================>] 220.09K  --.-KB/s    in 0.1s

2018-01-28 14:06:38 (1.58 MB/s) - ‘test.fastq.gz’ saved [225374/225374]
```
```
wget http://eaton-lab.org/pdsb/iris-data-dirty.csv

--2018-01-28 14:08:30--  http://eaton-lab.org/pdsb/iris-data-dirty.csv
Resolving eaton-lab.org (eaton-lab.org)... 192.30.252.153, 192.30.252.154
Connecting to eaton-lab.org (eaton-lab.org)|192.30.252.153|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 4549 (4.4K) [text/csv]
Saving to: ‘iris-data-dirty.csv’

iris-data-dirty.csv     100%[=====================================>]   4.44K  --.-KB/s    in 0s

2018-01-28 14:08:30 (150 MB/s) - ‘iris-data-dirty.csv’ saved [4549/4549]
```
```
head iris-data-dirty.csv

5.1,3.5,1.4,0.2,Iris-setosa
4.9,3.0,1.4,0.2,Iris-setosa
4.7,3.2,1.3,0.2,Iris-setosa
4.6,3.1,1.5,0.2,Iris-setosa
5.0,3.6,1.4,0.2,Iris-setosa
5.4,3.9,1.7,0.4,Iris-setosa
4.6,3.4,1.4,0.3,Iris-setosa
5.0,3.4,1.5,0.2,Iris-setosa
4.4,2.9,1.4,0.2,Iris-setosa
4.9,3.1,1.5,0.1,Iris-setosa
```
```
zless test.fastq.gz # Printing few outputs 

@29154_superba.6 GRC13_0027_FC:4:1:12660:1232 length=74
TGCAGGCCCAAAATCAACAATTATGCATAATACAACAAAGTTAATTAATTAATTATATTAAAAAAAGAAAAAGA
+29154_superba.6 GRC13_0027_FC:4:1:12660:1232 length=74
HHHHHHHHHHHHHHHHHHHHHHHHHHHHGGHHHHFHHHHHHHHHHHHHHHHHHHHHHHHHHHHHHHHHHHHHHH
@29154_superba.7 GRC13_0027_FC:4:1:13459:1226 length=74
TGCAGTCAATCACATTACTCTATACTCTTCTGCGTCAGGGATTTGATGGGAAACCAATGGTTAAAAAAGCGATC
+29154_superba.7 GRC13_0027_FC:4:1:13459:1226 length=74
HHHGGGEGGDGGG@HHHHHHEHHHGHHHG>DDGGEGGGGGBBGG>8CE>FEDGEDBFDEFGHHHHFHH<EBFEC
@29154_superba.8 GRC13_0027_FC:4:1:14162:1226 length=74
TGCAGTAACGTGAATTGCACTACGAAAACCTTCCAAACATAGGGCTATCACAACTTCATCATCACTTTGGTCAA
+29154_superba.8 GRC13_0027_FC:4:1:14162:1226 length=74
IIIIIIIIIIIIIIIIIIIIIDIIIIIIHIHIIIIIIDIIIIIIIIIIIIIIHHFIIFIIGIIIEHIHHHEIIG
@29154_superba.9 GRC13_0027_FC:4:1:17619:1233 length=74
```
#### Question - 2 : Clean the Iris .csv file (Correct misspelled species names and remove NA values)

``` bash
> cat iris-data-dirty.csv | cut -d ',' -f 5- | uniq -c  # Viewing the current .csv file for unique species names
```
```
# Below one can see errors in spelling as well as an observation with an empty space 
     11 Iris-setosa
      1 Iris-setsa
     38 Iris-setosa
      1 Iris-versicolour
     49 Iris-versicolor
     50 Iris-virginica
      1
```
```
# Removing the NAs and empty spaces and correcting the spelling names and saving it to a new .csv file
> cat iris-data-dirty.csv | grep -v 'NA' | grep -v '^$' | sed 's/setsa/setosa/g' | sed 's/versicolour/versicolor/g' > iris-data-clean.csv
```
```
# Viewing the number of unique species names in the new .csv file
> cat iris-data-clean.csv | cut -d ',' -f 5- | uniq -c  
```
```
     50 Iris-setosa
     48 Iris-versicolor
     50 Iris-virginica
```

#### Question 3 - Summarize the sequence datafile  

How many sequences start with "TGCAG" and end with "GAG" ?  

```
zcat test.fastq.gz | grep -c '^TGCAG.*GAG$'
```
```
  44
```
#### Question 4 - Summarize the sequence datafile  

```
# a) A list of unique species names was first retrieved (and stored in a text file called names)

zcat test.fastq.gz | grep '^@[0-9]' | cut -d '.' -f 1 |cut -d '_' -f 2 | sort -nr | uniq > names.txt

```
```
# Below is a list of variables (before transferred to the names variable)

thamno
superba
rex
przewalskii
cyathophylloides
cyathophylla

```
```
# b) The species names were iterated through the bigger file - test.fastq.gz to sort reads and save individual files

list=`cat names.txt` # Reference - https://stackoverflow.com/questions/7427262/how-to-read-a-file-into-a-variable-in-shell
mkdir sorted_reads

for i in $list; do zcat test.fastq.gz | grep -A4 "^@[0-9][0-9][0-9][0-9][0-9]_$i" > $i.txt ; mv $i.txt /home/vr2352/PDSB/Week-1/sorted_reads; done

```
``` 
# One can get into the folder with the sorted reads and view the list of files

cd sorted_reads
ls

cyathophylla.txt  cyathophylloides.txt  przewalskii.txt  rex.txt  superba.txt  thamno.txt

```


