# assignment-1
#### Alexander Ferrena
#### apf2139


## Section 1: (w)get data.

I had a problem with the Git Bash program (no permissions?) so I used MobaXTerm.
I could neither use nor install curl, so I just used wget:

```
wget http://eaton-lab.org/pdsb/test.fastq.gz
wget http://eaton-lab.org/pdsb/iris-data-dirty.csv
```

I used zless and less on each file respectively. The test.fastq.gz contains sequencing reads with four lines of information per read, including metadata, the actual sequence bases, and read quality for each base. The iris-data-dirty file appears to be a comma-separated data matrix, containing numbers in the 4 columns and the species name in the 5th; there are several NAs in the number columns and several misspellings in the species column.

## Section 2: clean data.

I scanned the list by eye and noticed two spelling errors, the first mispelled "setosa" to "setsa" and the second using the British spelling of "colour" rather than keeping with the matrix' pattern of spelling "versicolor". I used the following lines of code to determine the line of each:

```
grep -n setsa iris-data-dirty.csv
grep -n colour iris-data-dirty.csv
```

I determined there were spelling errors in lines 12 and 51. I corrected the mispellings in a backup file:

```
sed 's/setsa/setosa/g' 1iris-data-dirty.csv > 1iris-data-dirty.csv
sed -i 's/versicolour/versicolor/g' 1iris-data-dirty.csv
```

Then, I used the following in order to remove the NAs and save the output as "clean" data.

```
sed '/NA/d' 1iris-data-dirty.csv > iris-data-clean.csv
```

Finally, I used the following to count the number of rows associated with each species. The output is also shown.
```
cut -b 22-26 iris-data-clean.csv | uniq -c
     50 setos
     48 versi
     50 virgi
```

The Iris versicolor data originally contained two rows with NAs which were removed, leaving just 48 rows. The other two species, Iris setosa and Iris virginica, both had 50 rows of data.

## section 3: count TGCAG~GAG

to do this, I used the following code, the output is also included
```
cut -b 1-5,72-74 test.fastq | grep TGCAGGAG | uniq -c
     44 TGCAGGAG
```

## section 4: summarize and sort.

first, I tried creating an index variable in a similar way to the one used in the lecture slides:
```
ind=$(cat test.fastq | grep '^@[1-9]' | cut -d '.' -f 1 | uniq)
echo $ind
@29154_superba @30556_thamno @30686_cyathophylla @32082_przewalskii @33413_thamno @33588_przewalskii @35236_rex @35855_rex @38362_rex @39618_rex @40578_rex @41478_cyathophylloides @41954_cyathophylloides
```

I used this variable in a for loop as follows:
```
for i in $ind; do grep -A3 $i test.fastq > sorted_reads/$i.txt; done
```
this probably did not sufficiently answer the question. rather than taking creating a file for each taxon, it created on for each sample type. for example, there appear to be 4 different samples associated with "rex".


In order to sort by taxon, I saved an index variable containing each taxon name:
```
index=$(cat test.fastq | grep '^@[1-9]' | cut -d '.' -f 1 | cut -d '_' -f 2 | uniq)
echo $index
superba thamno cyathophylla przewalskii thamno przewalskii rex cyathophylloides
```

I then ran the following for loop, changing the number of lines grabbed by grep from -A3 to -A1 to reflect the fact that the taxon name is printed twice in each read.
```
for i in $index; do grep -A1 $i test.fastq > sorted_reads/$i.txt; done
```
This created separate files for each taxon.

However, I noticed that there appeared to be errors in the FASTQ file in the form of missing lines. For example, sometimes the "read quality" line appeared to be missing. Here is an example from the newly created rex.txt file:
```
tail sorted_reads/rex.txt
+40578_rex.207 GRC13_0027_FC:4:1:2322:1705 length=74
HHHHHHHHHHHHHHHGEDBHHHGHH@HHFHHHBHHHHHHGHHHHHHHHHHHHHAGGGGGHHHHHHBHHH>GDBD
@40578_rex.208 GRC13_0027_FC:4:1:2446:1715 length=74
TGCAGATTTCACCCTATCTACACCCGGAGCCACTCCCCTAGCGGTCCTGCCACCACGCCGCAGAACGGGAGCTC
+40578_rex.208 GRC13_0027_FC:4:1:2446:1715 length=74
IIIIIIIIIIIIIIIIIIIIHBIIGGEGDGGGGEIIIIAIHIHHBIHDHIIGHHHHDHHDFEAEE@?E>@@=6@
@40578_rex.209 GRC13_0027_FC:4:1:4524:1712 length=74
TGCAGCAGCCTTGCTCGTACAGCTCAGATGCATTTCCACGTTACTCTTACGATTCGACTTACTCTGGAAATGTT
+40578_rex.209 GRC13_0027_FC:4:1:4524:1712 length=74
@41478_cyathophylloides.1 GRC13_0027_FC:4:1:3031:1187 length=74
```
As we can see, read rex.209 appears to be missing its "read quality" line, so by using grep -A1 to grab the next line simply takes the first line from the first read for the taxon cyathophylloides. I am not sure how to fix this problem.
