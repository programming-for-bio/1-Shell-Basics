# assignment-1

# 1) Get the data files 

Download the following data files from the internet using the curl command: http://eaton-lab.org/pdsb/test.fastq.gz and http://eaton-lab.org/pdsb/iris-data-dirty.csv. Use the less or zless commands to look at the files. Then use the head command to print the first 5 lines from each file as output.

To download the two files, I used the `curl` command, as the instructions specified, followed by `-O` to direct the files to a given location (my working directory). The file named "test.fastq.gz" needed to be unzipped with `gunzip` in order to be read using the `head` tool. I specified the number of lines to be printed using `head -n 5` which allowed me to print the first 5 lines from each file. The linked resources were used to help with [curl](https://curl.haxx.se/docs/manpage.html) and [head](https://stackoverflow.com/questions/20275072/read-first-x-lines-of-csv-into-new-outfile). 

```
@29154_superba.1 GRC13_0027_FC:4:1:12560:1179 length=74
TGCAGGAAGGAGATTTTCGNACGTAGTGNNNNNNNNNNNNNNGCCNTGGATNNANNNGTGTGCGTGAAGAANAN
+29154_superba.1 GRC13_0027_FC:4:1:12560:1179 length=74
IIIIIIIGIIIIIIFFFFF#EEFE<?################################################
@29154_superba.2 GRC13_0027_FC:4:1:15976:1183 length=74

5.1,3.5,1.4,0.2,Iris-setosa
4.9,3,1.4,0.2,Iris-setosa
4.7,3.2,1.3,0.2,Iris-setosa
4.6,3.1,1.5,0.2,Iris-setosa
5,3.6,1.4,0.2,Iris-setosa

```

```
Jessicas-Air:~ jessicahoch$ cd PDSB
Jessicas-Air:PDSB jessicahoch$ pwd
/Users/jessicahoch/PDSB
Jessicas-Air:PDSB jessicahoch$ curl -O http://eaton-lab.org/pdsb/iris-data-dirty.csv
Jessicas-Air:PDSB jessicahoch$ curl -O  http://eaton-lab.org/pdsb/test.fastq.gz
Jessicas-Air:PDSB jessicahoch$ zless test.fastq.gz
Jessicas-Air:PDSB jessicahoch$ zless iris-data-dirty.csv
Jessicas-Air:PDSB jessicahoch$ head -n 5 iris-data-dirty.csv
Jessicas-Air:PDSB jessicahoch$ gunzip test.fastq.gz
Jessicas-Air:PDSB jessicahoch$ head -n 5 test.fastq
```

# 2) Clean the data 

Use grep, uniq, sed. Check that all of the species names are spelled correctly in the file iris-data-dirty.csv. Also check for missing values stored as NA. Create a new file where mispelled names are replaced with the correct values, and lines with NA are excluded, and save it as iris-data-clean.csv. Use cut, sort and uniq to list the number of data values there are for each species in the new cleaned data file.

To check that all the species names were spelled correctly, I sorted iris-data-dirty.csv with `sort` and looked at the unique values with `uniq`. From here, after seeing the incorrect spellings, I used `sed` to replace the misspelled species with the correct ones and delete lines with "NA" fields. To count the number of values, I used `cut` and removed all of the data before character 16 in each line. 

```
dyn-160-39-253-132:PDSB jessicahoch$ sort -t, -k5 iris-data-dirty.csv > sorted.csv
dyn-160-39-253-132:PDSB jessicahoch$ sort -u -t, -k5 sorted.csv > uniq.csv
dyn-160-39-253-132:PDSB jessicahoch$ cat uniq.csv
dyn-160-39-253-132:PDSB jessicahoch$ sed 's/Iris-setsa/Iris-setosa/g' iris-data-dirty.csv > iris-data-1.csv
dyn-160-39-253-132:PDSB jessicahoch$ sed 's/Iris-versicolour/Iris-versicolor/g' iris-data-1.csv > iris-data-2.csv 
dyn-160-39-253-132:PDSB jessicahoch$ cat iris-data-2.csv 
dyn-160-39-253-132:PDSB jessicahoch$ sed '/NA/d' iris-data-2.csv > iris-data-clean.csv
dyn-160-39-253-132:PDSB jessicahoch$ cat iris-data-clean.csv | cut -b 17- | uniq -c
  50 Iris-setosa
  48 Iris-versicolor
  50 Iris-virginica
```

# 3) Summarize sequence data file 

Find how many lines in the data file test.fastq.gz start with "TGCAG" and end with "GAG" 

To find how many lines of the data file test.fastq start with "TCGAG," I used `grep` and "^" to indicate the beginning of the line, followed by the characters. To find how many lines end with "GAG," I used `grep` and "$" after the end of the line characters. I returned the output to a new file (summ.txt) and used `cat` followed by `-n` to find the number of rows. The linekd resources were used to figure out how to use `grep` with the [beginning](https://askubuntu.com/questions/639157/grep-beginning-of-line) and [end](http://uniforumchicago.org/slides/regexp/tsld016.htm) of a line. 

```
     1	TGCAGCAAGTAAAGGGCCGAGGGACCGGCCCGAGAGACGACGTCCTGGCGGAGTGCTGTTTGGTGATGGAGGAG
     2	TGCAGCAAGATAAAACACGTTGTGAAGAGAGCAGAAACACACACACAGACCAGCAACGCAAGAGAGTTCGGGAG
     3	TGCAGGTCTAAATAAAGGTTTAGCAAAACTTGATGAAATCCTAGCTCAAAGCTTTCCTGACTCAACCAAGAGAG
     4	TGCAGGGCGCAGAGGGAGAGGCAGCAGGAAGCTCAGGGGCATAGGAGGGGTGGGCTGTCGGGAAAGGGCCTGAG
     5	TGCAGTTCGATGCGCCGATCAAGACGCAGGCACTGGCCCAGATGATGGGCAATACGACCGGCTACCGGATCGAG
     6	TGCAGATGAGATAATCGACAATGTCGATATACCCGTTCGCTGAAGCCATGTGTAATGCTGGAAAATTACATGAG
     7	TGCAGCGCCGTGTCCTCCGCGTTGTTCTTGGCCACGCACCAGAGCTGGTTCCCTTGGGCGGAGACGAGAGAGAG
     8	TGCAGACTCCGCACTCGAAGGCGCGTCCTCCGGCGGTCATGGAGGCGGCGGCCCCGCCCCCCCGAGACGGGGAG
     9	TGCAGGCCGACTTTAGGGAAGTGTCTTTGTTTGAACTGCCCAGCAAACCGGACTGGATCGGCGGAGCAGACGAG
    10	TGCAGACTCCTAATTGAATCTAACAAAAAGAAGCGAGAAAATTGTGCTTACCAACGACGGGTGCCGAACAAGAG
    11	TGCAGGAAATCCTCGTTGCGTCGATTGCGTGGAACAAAAACCACGACATCACCGGTGGACTGGTCTGCGTGGAG
    12	TGCAGAGGCGGCATACCATCAGCGCTTTGGTTTCGAGAGGCGGGAGGGGGGTGGCCAAACCGCTACAAAGGGAG
    13	TGCAGCTGGAGCTTCGGGCCGCAATGGTTAATACGTGGAGGAACGGGACGTAGGGGATGTCTTTCGAGGATGAG
    14	TGCAGCTAGCCAAAGAAATTAATAAAGCCTTGCGCGTACCGCACGATGTGAAAATGCAGCTGGGGCTGCTGGAG
    15	TGCAGTTGTTGAAGTTATGGTTGGAAATTCGAGTTCCAGTAATTGTTCTTCAGGAAAACTTTTCACATGGGGAG
    16	TGCAGCGTATCCCGACACGACGGCGGGAGCGGCTAAGATGACTGATATGGGGGGAGGGGCAGAAGCTGTGTGAG
    17	TGCAGTTATCAGATGAACGCGTTCGCAATGTTCAACTCTAACAGCCTGTTGAGACAATTATTTAGGCATATGAG
    18	TGCAGGGGTCTTCTTTCCGACACACGTTTGAAGCAGCAAGAAATGAATAATAATGATGATATTGAAGCAATGAG
    19	TGCAGCTTGCCGGCAAGCAGGAGCGCGAGAACCGCATCGACGAGATCAAGGCTTCGGTCAAGGCCGACCTCGAG
    20	TGCAGCTTGGAACTATTGTTGTTGTTGTTGAACAGAGACGGTGGGATGTTGCCCGAGAAGGTGTTGTTTCTGAG
    21	TGCAGATCGCCCCAACACACCATTTCTTCCCAACAGCTTCACCCCGGGATTGTCGATAAGGTATCTCACCTGAG
    22	TGCAGACCGATAAGATCTGGACCGGCCGGCTCCGCGTCGTCTCTTGCGCTTCCCGGTGCGAGATCCGGCTCGAG
    23	TGCAGAAAACACATTTTTGTCAAACGTTGGCTGAAAAACATGGACTTACTTGTTAATGCTTAGTAGGACAAGAG
    24	TGCAGAGACTAAAGATCATCGAGCAGGAGATCAGAGACGTGAAGCAGGAGATTCAAGGTCTGAGATACTACGAG
    25	TGCAGAAACCAAGGGGACAAAAAATTGCTGCGGTGAGGTGTACTGTTACTGAGTGCACTTTGAGCCCTTGTGAG
    26	TGCAGCTGATACTCAAGTTGATTTGTCATGGCAGATGACTCTTCAGAAATTATGGGAAAGCCTCAGTCCTGGAG
    27	TGCAGCTCACCAGGTGGCTCTAGGTTCTCTAAAACTGTCTCCAGGGGCTTTGACAGTGTCCAGTGGGCTCTGAG
    28	TGCAGATTTTTTTTGCTGAAGAAGTGCTTTGAAGTTTAGATAACAACCAACTTCTAAACTAAATACAAAAGGAG
    29	TGCAGGCCGCTGAGTTTCGCGCCCTCGTCGAGGCCGATGAGCGGGACGCCCTGGGTGGCCCGGCCGAGTTCGAG
    30	TGCAGAGGGATGACACTTGAAGGGGGAAGAAGGATGGTCTTTGAAAGGGGATATCTTGAAGGGTCTTCAAAGAG
    31	TGCAGATGATCGCCACGGGCGAGGAGGTGAACCGCGTTCCCGACATGCTGATGCGCGCGGCCGAGCTGCACGAG
    32	TGCAGATGACCTGGATCTTCCTGAACCAACTCANNAAATTTGAGAAAACTATTGGATTATAATTCTTACTGGAG
    33	TGCAGACAACGATAATTCATTTGAAAAAAGAACAAAAAAAAAATCGATCAACCACATTTTTATAGGAATAGGAG
    34	TGCAGCTAAGAAACCTCCTGAGTTTGAAGAATTTGGAAAAGATGAAAAAGTTGAGGAACTTGGAGAACCTGGAG
    35	TGCAGCATGCTACGTATTGTCATCTCATGGTTGCAAGGCTTTACAAAACTGGCTTCCACCTACTCCTGTCAGAG
    36	TGCAGAGAAAATGCTACAAATAATGAAGAAACTTTACTGGAGCAGGAGGCATTGGAGGTGAATGGAAGAGAGAG
    37	TGCAGCCCTCGATTATATAATCTCTATAGAAAGTGACATTTTCGTTCCCTCCCATTCTGGTAACATGGCAAGAG
    38	TGCAGTACAAAAAGACCGGTGTGAAAGATGCAGGGATTGATAAAGGCAGCACAAAACTGGCCACATTGAAAGAG
    39	TGCAGAGGGCGCATCAGAAAGGGGTTAGTTTTTCTTTTTTCTTTAGTCCGTTTTGGGTTTTTAAGATTATGGAG
    40	TGCAGGGTTATCTAGAAGTACTTTGATAGGGAAATTGCCCGCCTTAGTTATGGCTGCTTCCAGGGCTTTGAGAG
    41	TGCAGGATCAAGCCATTCAACAGCAGGATACAAACATTGTGAGGCTTTATGCAAAATCTGCCGCATACCTTGAG
    42	TGCAGACTGTCGAGGGCAACGCTAATGTCCGAGTAGACCTCGTCGATAGACTTGCTCGAGTTGATCTGTTGGAG
    43	TGCAGCAGACCGATTTCCTCCTTTACTCTCCTCAAGTGCCTTTTGTCTGTGTCTTTGATAGCTTTCGCACTGAG
    44	TGCAGCGGCTGGTCTGGAACGGAGCCGTACCGTTTGAAATTCGGACGGGGAAAGCCATGCCCGACGGGTTCGAG
```


```
   Jessicas-Air:PDSB jessicahoch$ touch summ.txt
   Jessicas-Air:PDSB jessicahoch$ grep ^TGCAG test.fastq | grep 'GAG$' > summ.txt
   Jessicas-Air:PDSB jessicahoch$ cat -n summ.txt 
   ```
   
   # 4) Summarize sequence data file
   
Write a for-loop to separate the reads from the file test.fastq.gz based on the taxon name in the label, and write the reads to separately named files in the new directory called sorted_reads/. The answer to this question will require more than a single line. See the lecture materials about using variables in for-loops. This will also be tricky because each read in the data file spans four lines (this is a standard genetic sequence file format), so for each read that you correctly identify you must grab the line with the sequence data below it, as well as the repeat label after that, and the quality information line after that. For a hint, see additional options for the grep command that can be used to select multiple lines.

To get a list of taxa included, I used `grep` and `cut` to find the taxa names (selecting out all the other information). I used `sort` and `uniq` to generate the list of taxa. Then, I wrote a `for-loop` essentially saying that for each taxon, select the line with the name of each taxon plus one after it and write it to a new file for each taxon. 


```
dyn-160-39-253-132:~ jessicahoch$ cat test.fastq | grep '^@[0-9]' | cut -d '.' -f 1 |cut -d '_' -f 2 | sort| uniq
cyathophylla
cyathophylloides
przewalskii
rex
superba
thamno
dyn-160-39-253-132:~ jessicahoch$ for item in superba thamno cyathophylla przewalskii rex cyathophylloides; do grep -A 1 $item test.fastq > $item.txt; done
```


   
