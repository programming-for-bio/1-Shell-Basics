# Assignment 1
first assignment for pdsb

### I. Get the data files
Q. Download the following data files from the internet using `curl`...  
A. I used the `curl` command following by the link address and the `-o` option to download the files and save them as output to files with the same name.  

```bash
> curl http://eaton-lab.org/pdsb/test.fastq.gz -o test.fastq.gz  
> curl http://eaton-lab.org/pdsb/iris-data-dirty.csv -o iris-data-dirty.csv  
```

### II. Clean the data
Q. Use `grep`, `uniq`, `sed` to fix misspelled taxon names and remove lines with NA values. 

A. To get the taxon names I used `cut` and selected the last column with `-d "," -f 5`. I ingored the last empty line in the file with the `-s` option. Then I sorted the reads before calling `uniq` so they would be collapsed properly. I used the `-c` option with uniq to return the counts, and then called `sort` one last time to sort based on the counts. Two names appear infrequently and are most likely misspellings.

```bash
## look for which taxon names seem to be incorrect
> cut -sd "," -f 5 iris-data-dirty.csv | sort | uniq -c | sort
```

```
      1 Iris-setsa
      1 Iris-versicolour
     49 Iris-setosa
     49 Iris-versicolor
     50 Iris-virginica
```

```bash
## look for which lines have 'NA' in them
> grep NA iris-data-dirty.csv
```
```
6.3,NA,4.9,1.5,Iris-versicolor
5.6,NA,4.1,1.3,Iris-versicolor
```

```bash
## full script: ignore NA lines, sub corrected names, and count the names
> grep -v NA iris-data-dirty.csv | \
    sed 's/setsa/setosa/g' | \
    sed 's/versicolour/versicolor/g' > iris-data-clean.csv
  
## count names in clean data file
> cut -sd "," -f 5 iris-data-clean.csv | sort | uniq -c | sort
```
```
     48 Iris-versicolor
     50 Iris-setosa
     50 Iris-virginica
```

### III. Summarize sequence data file  
Q. Find how many lines in the data file `test.fastq.gz` start with "TGCAG" and end with "GAG". 
A. I used the `grep` tool to search for matching lines using regular expressions. To find lines that start with TGCAG I used the starting anchor tag "^" and to find matches at the end I used the end anchor tag "$". I combined two grep calls with a pipe, and streamed the data from the file with `zcat`. Finally, I passed the `-c` option to the last grep call to return the number of matches.

```bash
> zcat test.fastq.gz | grep '^TGCAG' | grep -c 'GAG$' 
```

```
44
```

### IV. Summarize sequence data file
Q. Write a for-loop to separate the reads from the file test.fastq.gz based on taxon names in the label. 
A. Here the trick was finding that the `grep` command has an option `-A` that allows you to print some number of lines following the line that has the match. Therefore I only needed to write a regular expression to match the first line of every read and then I could print out all 4 lines following it. So, I first got all unique taxon names and save it as a variable. Then I used a for-loop to test over each unique name and write the output to a new file. 

```bash
## make a directory for the output files
> mkdir sorted_reads/

## get all uniq taxon names as a list
> names=$(zcat test.fastq.gz | grep "^@[0-9]_*" | cut -d '.' -f 1 | sort | uniq | cut -b 2-)

## match lines that start with @ and then the name and use -A to select 4 lines
> for name in $names;
> do
>    zcat test.fastq.gz | grep @$name -A 4 > sorted_reads/$name.fastq.gz
> done
```

