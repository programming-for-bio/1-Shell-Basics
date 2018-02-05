
Meredith VanAcker


----------


### I. Get the data files 


Download the following data files from the internet using the `curl` command: http://eaton-lab.org/pdsb/test.fastq.gz and http://eaton-lab.org/pdsb/iris-data-dirty.csv. Use the `less` or `zless` commands to look at the files. Then use the `head` command to print the first 5 lines from each file as output.

I referenced [this](https://unix.stackexchange.com/questions/) website to get information on how to write a curl download into a file because I was unable to download the file using curl.  However once I added in -0 and assigned the output to a file the curl command successfully pulled both the files from the lab website.   

```python
> curl -o test.fastq.gz "http://eaton-lab.org/pdsb/test.fastq.gz"
> gunzip -d test.fast.gz
> less test.fast
> head test.fast

> curl -0 http://eaton-lab.org/pdsb/iris-data-dirty.csv >>iris-data-dirty.csv
> less iris-data-dirty.csv
> head iris-data-dirty.csv
```

### II. Clean the data  

Use `grep`, `uniq`, `sed`. Check that all of the species names are spelled correctly in the file `iris-data-dirty.csv`. Also check for missing values stored as `NA`. Create a new file where mispelled names are replaced with the correct values, and lines with` NA` are excluded, and save it as `iris-data-clean.csv`. Use `cut`, `sort` and `uniq` to list the number of data values there are for each species in the new cleaned data file.


I used this [website](https://stackoverflow.com/questions/1915636/is-there-a-way-to-uniq-by-column) to first identify the unique spellings of flower species in the fifth column of the file.

``` python
 sort -u -t, -k5,5 iris-data-dirty.csv 
 ```
There are two species misspellings between Iris-setosa and Iris-setsa and Iris-versicolor and Iris-versicolour. 

```python
grep "NA" iris-data-dirty.csv
touch iris-data-clean.csv
cp iris-data-dirty.csv iris-data-clean.csv
cat iris-data-clean.csv 
sed 's/Iris-versicolour/Iris-versicolor/g;s/Iris-setsa/Iris-setosa/g' iris-data-dirty.csv > iris-data-clean.csv
egrep -v "NA" iris-data-clean.csv > iris-data-clean2.csv

cut -f 5 -d ',' iris-data-clean2.csv | sort | uniq -c
```

I used [this](https://www.linuxnix.com/sed-find-and-replace-multiple-search-patterns/) site to find how to combine multiple replacements in one sed command. I used [this](https://superuser.com/questions/351167/grep-command-to-remove-lines-containing-a-specified-word) site to remove rows with NA and copy the data to a new file. 

 
### III. Summarize Sequence Data Files 
Find how many lines in the data file test.fastq.gz start with "TGCAG" and end with "GAG"


``` python
grep -c '^TGCAG.*GAG$' test.fast
```
There are 44 lines that start with TGCAG and end in GAG

### IV. Summarize sequence data file

Write a for-loop to separate the reads from the file test.fastq.gz based on the taxon name in the label, and write the reads to separately named files in the new directory called sorted_reads/. 


```python

variable=$(grep '^@[0-9]' test.fast | cut -d '.' -f 1 | sort | uniq | sort | cut -b 2-)
echo $variable
mkdir sorted_reads/

for i in $variable
do
zcat test.fast.gz | grep @$variable -A 4 > sorted_reads/$variable.csv
done

```

I used this [site](https://askubuntu.com/questions/27838/how-to-grep-2-or-3-lines-one-containing-the-text-i-want-and-the-others-just-be) for understanding how to grab the lines under the targeted line using grep.