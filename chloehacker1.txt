## Assignment-1
Chloe Hacker
Assignment 1 due Monday January 29, 2018

### 1. Get the data files
Download the following data files from the internet using the ```curl``` command: ```http://eaton-lab.org/pdsb/test.fastq.gz``` and ```http://eaton-lab.org/pdsb/iris-data-dirty.csv```. Use the ```less``` or ```zless``` commands to look at the files. Then use the ```head``` command to print the first 5 lines from each file as output.

Here is the code I used for problem 1.

```
> curl http://eaton-lab.org/pdsb/test.fastq.gz
> zless test.fastq.gz
> head test.fastq.gz
> curl http://eaton-lab.org/pdsb/iris-data-dirty.csv | head
```
### II. Clean the data
Use ```grep```, ```uniq```, ```sed```. Check that all of the species names are spelled correctly in the file ```iris-data-dirty.csv```. Also check for missing values stored as NA. Create a new file where mispelled names are replaced with the correct values, and lines with NA are excluded, and save it as ```iris-data-clean.csv```. Use ```cut```, ```sort``` and ```uniq``` to list the number of data values there are for each species in the new cleaned data file.

Here is the code I used to create the ```iris-data-clean.csv```. 

```
> sed 's/Iris-setsa/Iris-setosa/g' 1assignment.txt > iris-data-clean.csv
> sed 's/Iris-versicolour/Iris-versicolor/g' 1assignment.txt > iris-data-clean.csv
> sed '/NA/d' 1assignment.txt > iris-data-clean.csv
```
This [link](https://askubuntu.com/questions/20414/find-and-replace-text-within-a-file-using-commands) helped with the replacement of misspelled words.
This is the code I used to list the number of data values for each species in the cleaned data file.
```
> grep Iris-setosa iris-data-clean.csv | sort| wc -l
> grep Iris-versicolor iris-data-clean.csv | sort| wc -l
> grep Iris-virginica iris-data-clean.csv | sort| wc -l
```
And got the counts as ```Iris-setosa``` = ```49```, ```Iris-versicolor``` = ```47```, and ```Iris-virginica``` = ```50```.

### III. Summarize sequence data file
Find how many lines in the data file ```test.fastq.gz``` start with "TGCAG" and end with "GAG"

The code I used for this question is as follows.

```
> gunzip test.fastq.gz
> grep -c '^TGCAG.*GAG$' test.fastq
```
And this code output ```44```.

This [link](https://stackoverflow.com/questions/3137094/how-to-count-lines-in-a-document) helped with counting lines in a data file. 

### IV. Summarize sequence data file
Write a ```for-loop``` to separate the reads from the file ```test.fastq.gz``` based on the taxon name in the label, and write the reads to separately named files in the new directory called ```sorted_reads/```. The answer to this question will require more than a single line. See the lecture materials about using variables in for-loops. This will also be tricky because each read in the data file spans four lines (this is a standard genetic sequence file format), so for each read that you correctly identify you must grab the line with the sequence data below it, as well as the repeat label after that, and the quality information line after that. For a hint, see additional options for the ```grep``` command that can be used to select multiple lines.

I made a directory first, then created a variable that outputs the names of the taxon. Then I used the for loop to loop through the equation and make a new file for each taxa and accompanying data lines and put it into the ```sorted_reads``` directory.

```
zless test.fastq.gz | sort | grep '^@[0-9]' | cut -d '.' -f 1 | uniq |cut -d '_' -f 2 | uniq
names= $(zless test.fastq.gz |sort | grep '^@[0-9]' | cut -d '.' -f 1 | uniq |cut -d '_' -f 2 | uniq)

for i in $names
> do
> echo $i
> grep -A3 $i test.fastq.gz > sorted_reads
> done
```

This [link](https://www.cyberciti.biz/faq/bash-for-loop/) helped with the for loop.
