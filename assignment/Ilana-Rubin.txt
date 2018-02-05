# Assignment 1

## I. Get the data files
Download the following data files from the internet using the curl command: http://eaton-lab.org/pdsb/test.fastq.gz and http://eaton-lab.org/pdsb/iris-data-dirty.csv. Use the less or zless commands to look at the files. Then use the head command to print the first 5 lines from each file as output.

I downloaded test.fastq.gz using the `curl` command with the `-o` option to save it to my directory with the same file name,  confirmed that it downloaded using `ls -lt`, viewed it using `zless`, and then referenced this [stackoverflow thread](https://stackoverflow.com/questions/15747912/how-do-i-use-head-and-tail-to-print-specific-lines-of-a-file) for how to use the `head` command with `-5` to indicate that the output would show the first 5 lines of the file. The output was illegible, so I unzipped the file with `gunzip`, and then tried again successfully.
```
>curl http://eaton-lab.org/pdsb/test.fastq.gz -o test.fastq.gz
>ls -lt
>zless test.fastq.gz
>head -5 test.fastq.gz
>gunzip test.fastq.gz
>head -5 test.fastq
```
Output:
```
@29154_superba.1 GRC13_0027_FC:4:1:12560:1179 length=74
TGCAGGAAGGAGATTTTCGNACGTAGTGNNNNNNNNNNNNNNGCCNTGGATNNANNNGTGTGCGTGAAGAANAN
+29154_superba.1 GRC13_0027_FC:4:1:12560:1179 length=74
IIIIIIIGIIIIIIFFFFF#EEFE<?################################################
@29154_superba.2 GRC13_0027_FC:4:1:15976:1183 length=74

```

For the second file, iris-data-dirty.csv, I followed the same steps, except that it was not necessary to unzip the file:
```
>curl http://eaton-lab.org/pdsb/iris-data-dirty.csv -o iris-data-dirty.csv
>ls -lt
>less iris-data-dirty.csv
>head -n 5 iris-data-dirty.csv
```
Output:
```
5.1,3.5,1.4,0.2,Iris-setosa
4.9,3.0,1.4,0.2,Iris-setosa
4.7,3.2,1.3,0.2,Iris-setosa
4.6,3.1,1.5,0.2,Iris-setosa
5.0,3.6,1.4,0.2,Iris-setosa
```

## II. Clean the data
Use grep, uniq, sed. Check that all of the species names are spelled correctly in the file iris-data-dirty.csv. Also check for missing values stored as NA. Create a new file where mispelled names are replaced with the correct values, and lines with NA are excluded, and save it as iris-data-clean.csv. Use cut, sort and uniq to list the number of data values there are for each species in the new cleaned data file.

I used cut with `-d "," -f 5` to show the fifth column with `sort` to sort species names alphabetically. I added `uniq -c | sort` to list the number of instances for each unique species name and list them by frequency. Seeing that that produced one blank line along with the species names, I added `-s` option to `cut` to exclude it from the output
```
>cat iris-data-dirty.csv | cut -sd "," -f 5 | sort | uniq -c | sort

```
Output:
```
      1 Iris-setsa
      1 Iris-versicolour
     49 Iris-setosa
     49 Iris-versicolor
     50 Iris-virginica
```
Seeing that there are only one instance each of the "setsa" and "versicolour" spellings, I corrected them to "setosa" and "versicolor," respectively using `sed`, deleted lines with "NA" using `sed '/NA/d'` and sent the output to a new file.
I referenced [The Linux Juggernaut](https://www.linuxnix.com/sed-delete-a-matched-line-from-a-file/) for how to use `sed` to delete lines containing NA. I checked that the new file was in my directory and viewed it to see the changes.
```
>cat iris-data-dirty.csv | \ 
>sed 's/setsa/setosa/g' | \ 
>sed 's/versicolour/versicolor/g' | \ 
>sed '/NA/d' > iris-data-clean.csv
>ls -lt
>less iris-data-clean.csv
```
I used my first command line again with the clean file to list the species by the number of data values for each.
```
>cat iris-data-clean.csv | cut -sd "," -f 5 | sort | uniq -c | sort
```
Output:
```
     48 Iris-versicolor
     50 Iris-setosa
     50 Iris-virginica
```
## III. Summarize sequence data file
Find how many lines in the data file test.fastq.gz start with "TGCAG" and end with "GAG"

I already unzipped the file test.fastq.gz, so here I am using the unzipped file test.fastq. I learned from Vijay Ramesh's message in the [gitter chatroom](https://gitter.im/programming-for-bio/Lobby) that "a . signifies the end of one set of characters and a * signifies the start of another and you need a $ after to show that it is ending with those." 
```
>cat test.fastq | grep -c '^TGCAG.*GAG$'
```
The result is that there are 44 lines in the file that start with "TGCAG" and end with "GAG":
```
44
```

## IV. Summarize sequence data file
Write a for-loop to separate the reads from the file test.fastq.gz based on the taxon name in the label, and write the reads to separately named files in the new directory called sorted_reads/. The answer to this question will require more than a single line. See the lecture materials about using variables in for-loops. This will also be tricky because each read in the data file spans four lines (this is a standard genetic sequence file format), so for each read that you correctly identify you must grab the line with the sequence data below it, as well as the repeat label after that, and the quality information line after that. For a hint, see additional options for the grep command that can be used to select multiple lines.

I referred to `grep --help` and my notes from the second class lecture to revise my answer to this question. I began by making a sorted_reads directory. I used `grep` to select lines beginning with @ followed by a number, and used `cut` to select the first column separated by a ".", sorted the output of unique values alphabetically, and removed the @ from each line by cutting out the second byte in the output lines.
```
>mkdir sorted_reads
>cat test.fastq | grep "^@[0-9]" | cut -d '.' -f 1 | sort | uniq | cut -b 2-
 ```
I created a variable for taxon, using the previous code, and checked the result using `echo`:
```
>taxon=$(cat test.fastq | grep "^@[0-9]" | cut -d '.' -f 1 | sort | uniq | cut -b 2-)
>echo $taxon | sort
```
I created a for-loop to create a new file in the sorted_reads/ directory for each unique taxon name, using the `-A` option listed in `grep --help` to grab 4 lines at once (the line matching the $taxon conditions as well as the next 3 lines). Finally, I looked in the sorted_reads/ directory to see a list of the files created.
```
>for taxon in $taxon
>do
>cat test.fastq | grep @$taxon -A 4 > sorted_reads/$taxon.fastq
>done
>cd sorted_reads/
>ls -lt sorted_reads/
```
Output:
```
-rw-r--r-- 1 ILANA Z 197611  25009 Feb  3 14:15 29154_superba.fastq
-rw-r--r-- 1 ILANA Z 197611  86624 Feb  3 14:15 30556_thamno.fastq
-rw-r--r-- 1 ILANA Z 197611  29275 Feb  3 14:15 30686_cyathophylla.fastq
-rw-r--r-- 1 ILANA Z 197611 107684 Feb  3 14:15 32082_przewalskii.fastq
-rw-r--r-- 1 ILANA Z 197611  21434 Feb  3 14:15 33413_thamno.fastq
-rw-r--r-- 1 ILANA Z 197611 107422 Feb  3 14:15 33588_przewalskii.fastq
-rw-r--r-- 1 ILANA Z 197611  85162 Feb  3 14:15 35236_rex.fastq
-rw-r--r-- 1 ILANA Z 197611 103379 Feb  3 14:15 35855_rex.fastq
-rw-r--r-- 1 ILANA Z 197611  48141 Feb  3 14:15 38362_rex.fastq
-rw-r--r-- 1 ILANA Z 197611  63052 Feb  3 14:15 39618_rex.fastq
-rw-r--r-- 1 ILANA Z 197611  53582 Feb  3 14:15 40578_rex.fastq
-rw-r--r-- 1 ILANA Z 197611  79376 Feb  3 14:15 41478_cyathophylloides.fastq
-rw-r--r-- 1 ILANA Z 197611  93523 Feb  3 14:15 41954_cyathophylloides.fastq
```

>Written with [StackEdit](https://stackedit.io/).
