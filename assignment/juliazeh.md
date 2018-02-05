# assignment-1
# I. Get the data files
Download the following data files from the internet using the `curl` command: http://eaton-lab.org/pdsb/test.fastq.gz and http://eaton-lab.org/pdsb/iris-data-dirty.csv. Use the `less` or `zless` commands to look at the files. Then use the `head` command to print the first 5 lines from each file as output.
```
curl http://eaton-lab.org/pdsb/test.fastq.gz > test.fastq.gz
curl http://eaton-lab.org/pdsb/iris-data-dirty.csv > iris-data-dirty.csv
less iris-data-dirty.csv
zless test.fastq.gz
```
```
head -n 5 iris-data-dirty.csv
5.1,3.5,1.4,0.2,Iris-setosa
4.9,3.0,1.4,0.2,Iris-setosa
4.7,3.2,1.3,0.2,Iris-setosa
4.6,3.1,1.5,0.2,Iris-setosa
5.0,3.6,1.4,0.2,Iris-setosa
```
`Head` command didn't work on `test.fastq.gz` so I used a command to unzip the file and save a new file `test.fastq` which I could use the `head` command on. Source: https://unix.stackexchange.com/questions/156261/unzipping-a-gz-file-without-removing-the-gzipped-file

```
gunzip test.fastq.gz
head -n 5 test.fastq
@29154_superba.1 GRC13_0027_FC:4:1:12560:1179 length=74
TGCAGGAAGGAGATTTTCGNACGTAGTGNNNNNNNNNNNNNNGCCNTGGATNNANNNGTGTGCGTGAAGAANAN
+29154_superba.1 GRC13_0027_FC:4:1:12560:1179 length=74
IIIIIIIGIIIIIIFFFFF#EEFE<?################################################
@29154_superba.2 GRC13_0027_FC:4:1:15976:1183 length=74
```
# II. Clean the data
Use `grep`, `uniq`, `sed`. Check that all of the species names are spelled correctly in the file `iris-data-dirty.csv`. Also check for missing values stored as `NA`. Create a new file where mispelled names are replaced with the correct values, and lines with `NA` are excluded, and save it as `iris-data-clean.csv`. Use `cut`, `sort` and `uniq` to list the number of data values there are for each species in the new cleaned data file.

First I used `grep` to isolate any lines that did not have one of the three correctly spelled species names, which left me with two files, one which had "Iris-setsa" and one which had "Iris-versicolour." I then replaced these with the correct spellings using `sed` and saved this to  `iris-data-clean.csv`.
```
grep -v Iris-setosa iris-data-dirty.csv | grep -v Iris-virginica | grep -v Iris-versicolor
sed 's/Iris-setsa/Iris-setosa/g' iris-data-dirty.csv | sed 's/Iris-versicolour/Iris-versicolor/g' | grep -v NA > iris-data-clean.csv 
```
I then cut the lines to include just the species name and used `uniq -c` to give me the number of instances of each species.
Source for help with the commands `cut` and `uniq`: https://www.computerhope.com/unix/uuniq.htm
 ```
 cut -d, -f 5 iris-data-clean.csv | uniq -c
  50 Iris-setosa
  48 Iris-versicolor
  50 Iris-virginica
```
# III. Summarize sequence data file
Find how many lines in the data file `test.fastq.gz` start with "TGCAG" and end with "GAG"

I knew I needed to use the `grep` command for this, but was unclear on how the syntax should look. Source for help with syntax using the `grep -c` command: https://stackoverflow.com/questions/28899349/find-lines-starting-with-one-specific-character-and-ending-with-another-one
```
grep -c '^TGCAG.*GAG$' test.fastq
44
```

# IV. Summarize sequence data file
Write a `for-loop` to separate the reads from the file `test.fastq.gz` based on the taxon name in the label, and write the reads to separately named files in the new directory called `sorted_reads/`. The answer to this question will require more than a single line. See the lecture materials about using variables in for-loops. This will also be tricky because each read in the data file spans four lines (this is a standard genetic sequence file format), so for each read that you correctly identify you must grab the line with the sequence data below it, as well as the repeat label after that, and the quality information line after that. For a hint, see additional options for the `grep` command that can be used to select multiple lines.

To begin, I created a new directory called `sorted_reads/`. I then needed to isolate the unique taxon names, which I knew I could do with `grep`, `cut`, and `uniq`. For help with the syntax for this line of code, I looked to the gitter class chat. Also using what I learned in the class chat, I assigned all of these unique taxon names to a variable. After switching to the new directory, I used a `for-loop` with the variable `taxon` to create files with each of the taxon names. I then used the `grep` command and found a source online which told me that I could grab the 3 lines below the line containing the taxon name I wanted using `grep -A3`. I did this for each of the different taxon and saved them to their respective files in `sorted_reads/`. 

Sources: gitter class chat, https://askubuntu.com/questions/27838/how-to-grep-2-or-3-lines-one-containing-the-text-i-want-and-the-others-just-be
```
mkdir sorted_reads/
grep '^@[0-9]' test.fastq | cut -d '.' -f 1 | uniq |cut -d '_' -f 2 | uniq
taxon=$(grep '^@[0-9]' test.fastq | cut -d '.' -f 1 | uniq |cut -d '_' -f 2 | uniq)
cd sorted_reads/
for i in $taxon; do touch $i.txt; done
grep -A3 superba test.fastq >sorted_reads/superba.txt
grep -A3 cyanthophylla test.fastq >sorted_reads/cyathophylla.txt
grep -A3 cyanthophylloides test.fastq >sorted_reads/cyathophylloides.txt
grep -A3 thamno test.fastq >sorted_reads/thamno.txt 
grep -A3 przewalskii test.fastq >sorted_reads/przewalskii.txt
grep -A3 rex test.fastq >sorted_reads/rex.txt
```
