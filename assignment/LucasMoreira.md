# Assignment 1

## I. Get the data files

Download the following data files from the internet using the curl command: http://eaton-lab.org/pdsb/test.fastq.gz and http://eaton-lab.org/pdsb/iris-data-dirty.csv. Use the less or zless commands to look at the files. Then use the head command to print the first 5 lines from each file as output.

```shell
curl http://eaton-lab.org/pdsb/test.fastq.gz > test.fastq.gz

curl http://eaton-lab.org/pdsb/iris-data-dirty.csv > iris-data-dirty.csv

zless test.fastq.gz

less iris-data-dirty.csv
```
## II. Clean the data
Use grep, uniq, sed. Check that all of the species names are spelled correctly in the file iris-data-dirty.csv. Also check for missing values stored as NA. Create a new file where mispelled names are replaced with the correct values, and lines with NA are excluded, and save it as iris-data-clean.csv. Use cut, sort and uniq to list the number of data values there are for each species in the new cleaned data file.

`iris-data-dirty.csv` has 5 columns. Species names are in the fifth column. Let's check how many mispelled names there are:

```shell
cut -f 5 -d ',' iris-data-dirty.csv | sort | uniq 

Iris-setosa
Iris-setsa
Iris-versicolour
Iris-versicolor
Iris-virginica
```

There are two misspellings: Iris-setsa and Iris-versicolour. Let's replace them by correct names and output to a different file:

```shell
sed 's/Iris-setsa/Iris-setosa/g' iris-data-dirty.csv | sed 's/Iris-versicolour/Iris-versicolor/g' > iris-data-namereplaced.csv
```

Now let's exclude lines with NA (using [reverse `grep`](https://unix.stackexchange.com/questions/203830/what-is-the-opposite-of-grep)): 
```shell
grep -v 'NA' iris-data-namereplaced.csv > iris-data-clean.csv
```
Now let's list the number of data values there are for each species in the new cleaned data file:

```bash
cut -f 5 -d ',' iris-data-clean.csv | uniq -c | sort
     48 Iris-versicolor
     50 Iris-setosa
     50 Iris-virginica
 ```
## III. Summarize sequence data file

Find how many lines in the data file test.fastq.gz start with "TGCAG" and end with "GAG"

First, we need to uncompress the file:
```shell
gunzip test.fastq.gz
```

Then, we are using [*anchoring*](https://linux.die.net/man/1/grep) in ``grep`` to find how many sequences start with a given pattern:
```shell
grep '^TGCAG' test.fastq | grep -c 'GAG$'
44
```
## IV. Summarize sequence data file
Write a for-loop to separate the reads from the file test.fastq.gz based on the taxon name in the label, and write the reads to separately named files in the new directory called sorted_reads/. The answer to this question will require more than a single line. See the lecture materials about using variables in for-loops. This will also be tricky because each read in the data file spans four lines (this is a standard genetic sequence file format), so for each read that you correctly identify you must grab the line with the sequence data below it, as well as the repeat label after that, and the quality information line after that. For a hint, see additional options for the grep command that can be used to select multiple lines.

Using grep, we can get only the lines starting with @ and followed by a number from 0 to 9 (`grep '^@[0-9]'`) and print what's uniq:
```shell
cut -d '_' -f 1 test.fastq | grep '^@[0-9]' | uniq | sed 's/@//g' > samples.txt

cat samples.txt
29154
30556
30686
32082
33413
33588
35236
35855
38362
39618
40578
41478
41954
```

For each uniq name, we extract the matching lines and three lines below them. We then export them to a new file:
```shell
mkdir sorted_reads
cd sorted_reads

for i in `cat ../samples.txt`; grep -B 3 "$i" > $i.fastq; done
```
