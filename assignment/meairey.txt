# Assignment-01
### Question 1: Get the data files:

I used the `curl` function to pull down the files from the web. I then used the `zless` command to get a preview of the file. Then the `head` command to give me the first five lines of each of the files. 
```
curl-O http://eaton-lab.org/pdsb/test.fastq.gz
curl-O http://eaton-lab/pdsb/iris-data-dirty.csv
zless test.fastq.gz
zless iris-data-dirty.csv
head test.fastq.gz
head iris-data-dirty.csv
```
### Question 2: Clean up the data

In this part, I `cut` the data after the comma and `uniq` to only show unique lines. This allowed me to see where the typos and errors were in the data set. I used `gunzip` to extract the files and then used `sed` to replace the typos and errors with the correct spellings. I used the `command > iris-data-clean.csv` to create the new file for the cleaned data. The `grep -v NA` allowed me to remove lines with NAs in them. 
```
zcat iris-data-dirty.csv | cut -s -d "," -f 5 | uniq -c
gunzip iris-data-dirty.csv
sed 's/setsa/setosa/g' iris-data-dirty.csv > iris-data-clean.csv
sed -i 's/versicolour/versicolor/g' iris-data-clean.csv
cat iris-data-clean.csv | grep -v "NA" > iris-data-clean.csv
```
* _Iris setosa_ 50 Individuals
* _Iris versicolor_ 48 Individuals
* _Iris virginica_ 50 Individuals

### Question 3: Summarize sequence data

I used the `gunzip` command to unzip the file. I then used the '^' syntax to `grep` for any lines that started with that certain sequence. The '$' syntax was used to `grep` for lines that ended with that paricular sequence. The `grep -c ^` allowed me to count the number of lines of code ended with that sequence. 
```
gunzip test.fastq.gz
grep '^TGCAG' test.fastq | grep 'GAG$' | grep -c ^
```
44 sequences match the criteria 

### Question 4: Summarize sequence data 

I used the `mkdir` command to create the new directory sorted_reads. I then moved the test.fastq file to this new directory using the `mv` command. I then built a for loop that would select for one of the names of the taxons. For each one of the names of the taxons I asked the loop to select lines with the taxon name and the line after that using the `grep -A1 $item` command. I then asked it to copy each one of those selected lines to a new file that has the name of the taxon in the file name.  

```
mkdir sorted_reads
mv test.fastq sorted_reads/
for item in cyathophylloides superba thamno cyathophylla przewalskii rex
do
grep -A1 $item test.fastq >> $item.fastq
done
```
