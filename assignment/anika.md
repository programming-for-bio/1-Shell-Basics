# assignment-1
PDSB

## Get the files
Q1. Download the following data files from the internet using the curl command: http://eaton-lab.org/pdsb/test.fastq.gz and http://eaton-lab.org/pdsb/iris-data-dirty.csv. Use the less or zless commands to look at the files. Then use the head command to print the first 5 lines from each file as output.

A1. Get the file from the internet using curl (with -O to suppress the output from the screen). Then use zless to look at the files. Before printing lines the file needs to ge unzipped if zipped (*.gz). 

Sources:
- for curl syntax: https://www.thegeekstuff.com/2012/07/wget-curl 

Code block:
```bash
> curl -O http://eaton-lab.org/pdsb/test.fastq.gz
> zless test.fastq.gz
> gunzip test.fastq.gz
> head -5 test.fastq
```
Output:
```bash
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  220k  100  220k    0     0  1572k      0 --:--:-- --:--:-- --:--:-- 1760k

@29154_superba.1 GRC13_0027_FC:4:1:12560:1179 length=74
TGCAGGAAGGAGATTTTCGNACGTAGTGNNNNNNNNNNNNNNGCCNTGGATNNANNNGTGTGCGTGAAGAANAN
+29154_superba.1 GRC13_0027_FC:4:1:12560:1179 length=74
IIIIIIIGIIIIIIFFFFF#EEFE<?################################################
@29154_superba.2 GRC13_0027_FC:4:1:15976:1183 length=74
```

Code block:
```bash
curl -O http://eaton-lab.org/pdsb/iris-data-dirty.csv
zless iris-data-dirty.csv
head -5 iris-data-dirty.csv
```
Output:
```bash
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  4549  100  4549    0     0   143k      0 --:--:-- --:--:-- --:--:--  296k

5.1,3.5,1.4,0.2,Iris-setosa
4.9,3.0,1.4,0.2,Iris-setosa
4.7,3.2,1.3,0.2,Iris-setosa
4.6,3.1,1.5,0.2,Iris-setosa
5.0,3.6,1.4,0.2,Iris-setosa
```

## Clean the data
Q2. Use grep, uniq, sed. Check that all of the species names are spelled correctly in the file iris-data-dirty.csv. Also check for missing values stored as NA. Create a new file where mispelled names are replaced with the correct values, and lines with NA are excluded, and save it as iris-data-clean.csv. Use cut, sort and uniq to list the number of data values there are for each species in the new cleaned data file.

To look for misspellings I sorted the file by unique using sort -u based on the 5th column. Misspellings: "versicolour" for "versicolor"; "setsa" for "setosa"

Sources: https://stackoverflow.com/questions/1915636/is-there-a-way-to-uniq-by-column (to find unique from a sort)

Code block:
Look for misspelling:
```bash
sort -u -t "," -k5,5 iris-data-dirty.csv
```
Code block - copied answer
```bash
5.1,3.5,1.4,0.2,Iris-setosa
4.8,3.4,1.6,0.2,Iris-setsa
6.4,3.2,4.5,1.5,Iris-versicolor
7.0,3.2,4.7,1.4,Iris-versicolour
6.3,3.3,6.0,2.5,Iris-virginica
```

To check for missing values stored as NA I used grep which searches for a string. I found two lines with NAs.
Code block:
```bash
grep "NA" iris-data-dirty.csv
```
Output:
```bash
6.3,NA,4.9,1.5,Iris-versicolor
5.6,NA,4.1,1.3,Iris-versicolor
```

To create a new file where misspelled names are replaced with the correct values, I used sed to replace. The > sign was used to store the output as a new file. NA values were removed by reverse selecting them with grep -v. To check that this worked we can repeat the commands from the first two parts. This shows that we have eliminated all NAs and all misspellings.

Sources: https://stackoverflow.com/questions/26568952/how-to-replace-multiple-patterns-at-once-with-sed was used to figure out how to replace multiple patterns in one go.

Code block:
```bash
grep -v "NA" iris-data-dirty.csv | sed -e 's/versicolour/versicolor/g;s/setsa/setosa/g' > iris-data-clean.csv
```
When you look for unique Iris+wildcard it results in
```bash
5.1,3.5,1.4,0.2,Iris-setosa
7.0,3.2,4.7,1.4,Iris-versicolor
6.3,3.3,6.0,2.5,Iris-virginica
```

To list the number of data values there are in the cleaned file use cut, then sort, than uniq -c to count the instances of each unique. cut -f5 gets us only the 5th column (species name) and -d "," tells unix that the file is comma delimited. 
Code block:
```bash
cut -f5 -d "," iris-data-clean.csv | sort | uniq -c
```
Which gives the output:
```bash
     50 Iris-setosa
     48 Iris-versicolor
     50 Iris-virginica
```

## Summarize sequence data file
Q3. Find how many lines in the data file test.fastq.gz start with "TGCAG" and end with "GAG"

Since the file is zipped we start with zcat and then use grep twice for the two different filtering criteria. Starting grep with ^ indicates the string should start with that pattern and ending it with $ indicates the string should end with that. Wc -l is used to count the number of lines. In total there are 44 lines that match the patterns. 

Sources: https://www.tecmint.com/wc-command-examples/ for the line count command; http://www.robelle.com/smugbook/regexpr.html for how to indicate the line should start or end with that pattern.

Code block:
```bash
zcat test.fastq.gz | grep "^TGCAG" | grep "GAG$" | wc -l
```
Output:
```
44
```

## Summarize sequence data file
Q4. Write a for-loop to separate the reads from the file test.fastq.gz based on the taxon name in the label, and write the reads to separately named files in the new directory called sorted_reads/. The answer to this question will require more than a single line. See the lecture materials about using variables in for-loops. This will also be tricky because each read in the data file spans four lines (this is a standard genetic sequence file format), so for each read that you correctly identify you must grab the line with the sequence data below it, as well as the repeat label after that, and the quality information line after that. For a hint, see additional options for the grep command that can be used to select multiple lines.

The strategy was to get the unique taxa names using grep, cut, uniq, and sort (there is probably a more efficient way to do this but I did get the unique list). Then use a for loop to iterate through the reads and identify the taxon and send matching reads to output file. Grep -A4 selected the four lines after the pattern match. I first made the output file and then moved it to the sorted reads folder.

Sources: http://www.compciv.org/bash-guide/ for how to do for loops in bash; https://stackoverflow.com/questions/7427262/how-to-read-a-file-into-a-variable-in-shell for how to get a text file stored back into a variable. 

Code block:
```bash
#Get species names:
zcat test.fastq.gz | grep '^@[0-9]' | cut -d "." -f 1 | uniq -c | sort -nr > test.txt
cat test.txt | grep '^_*' | cut -d "_" -f 2 | sort -nr | uniq > list.txt
```
Output:
```bash
thamno
superba
rex
przewalskii
cyathophylloides
cyathophylla
```
Code block:
```bash
#make directory
mkdir sorted_reads
```
```bash
#convert list of taxa back to a variable (from stored file)
value=`cat list.txt`
#for loop iterating across taxa (in $value)
for var1 in $value; do zcat test.fastq.gz | grep -A4 "^@[0-9][0-9][0-9][0-9][0-9]_$var1" > $var1.txt; mv $var1.txt ~/sorted_reads; done
```
Nothing to show for the output since it wrote the information to files and then moved those files to the new folder.
