# assignment-1
This is assignment 1 for programming for biology

## I. Get the data files

Query the following data files from the internet using the `curl` command: `http://eaton-lab.org/pdsb/test.fastq.gz` and `http://eaton-lab.org/pdsb/iris-data-dirty.csv`. Use the `less` or `zless` commands to look at the files. Then use the `head` command to print the first 5 lines from each file as output.

Knowing from lecture that the `curl` command downloads files from URLs, and that the `-O` option saves the file in the active directory, I downloaded the `test.fastq.gz` and the `iris-data-dirty.csv` files from the provided URLs. Looking ahead at the next questions, I noticed that the `head` function would have difficulty giving a readable output from the `test.fastq.gz` file because it was compressed. Thus, before running the `zless` and `head` commands, I ran the `gunzip` function on the `test.fastq.gz` file to extract the `test.fastq` file.

```
> curl -O http://eaton-lab.org/pdsb/test.fastq.gz -O  http://eaton-lab.org/pdsb/iris-data-dirty.csv
> gunzip test.fastq.gz
> zless test.fastq
> zless iris-data-dirty.csv
```
```
> head test.fastq
```
```
@29154_superba.1 GRC13_0027_FC:4:1:12560:1179 length=74
TGCAGGAAGGAGATTTTCGNACGTAGTGNNNNNNNNNNNNNNGCCNTGGATNNANNNGTGTGCGTGAAGAANAN
+29154_superba.1 GRC13_0027_FC:4:1:12560:1179 length=74
IIIIIIIGIIIIIIFFFFF#EEFE<?################################################
@29154_superba.2 GRC13_0027_FC:4:1:15976:1183 length=74
TGCAGTTGTAAATACAAATATCCCAAAANNNNGNNNNNNNTNTAATATTTTGNAANNTTGAGGGGTGTGATNTN
+29154_superba.2 GRC13_0027_FC:4:1:15976:1183 length=74
GGGGHHHHHHHHHHHHHDHGHHHHCAAA##############################################
@29154_superba.3 GRC13_0027_FC:4:1:19092:1179 length=74
TGCAGGCTCTGACAAAGAANTCGACTGANNNNNNNNNNNNNNCACNGGTTCNNGNNNATGTCAATGTGGTANAN
```
```
> head iris-data-dirty.csv
```
```
5.1,3.5,1.4,0.2,Iris-setosa
4.9,3.0,1.4,0.2,Iris-setosa
4.7,3.2,1.3,0.2,Iris-setosa
4.6,3.1,1.5,0.2,Iris-setosa
5.0,3.6,1.4,0.2,Iris-setosa
5.4,3.9,1.7,0.4,Iris-setosa
4.6,3.4,1.4,0.3,Iris-setosa
5.0,3.4,1.5,0.2,Iris-setosa
4.4,2.9,1.4,0.2,Iris-setosa
4.9,3.1,1.5,0.1,Iris-setosa
```

## II. Clean the data

Use `grep`, `uniq`, `sed`. Check that all of the species names are spelled correctly in the file `iris-data-dirty.csv`. Also check for missing values stored as `NA`. Create a new file where mispelled names are replaced with the correct values, and lines with `NA` are excluded, and save it as `iris-data-clean.csv`. Use `cut`, `sort` and `uniq` to list the number of data values there are for each species in the new cleaned data file.

To begin, the first thing I did was to search for all occurrences where species names were spelled incorrectly. To do so, I used the `grep` command and specified for the command to look for occurrences where the species names (setosa, versicolor, and virginica) were NOT spelled correctly (using the `-nv` option). After locating the misspellings of the species names, I replaced the misspellings by using the `sed` command and deleted lines containing `,NA,` using the `grep` command and the `-v` option. To count the number of lines of data exist for each species, the command `zless` was combined with the commands `cut` and `uniq` to view the data with characters after the 21st place on the line (`cut -c 22-`) and then (`-c`)ounting the (`uniq`)ue occurrences of the remaining text in each line (which in this case was the species names).


```
> grep -nv virginica iris-data-dirty.csv | grep -nv versicolor | grep -nv setosa
> sed 's/setsa/setosa/g' iris-data-dirty.csv | sed 's/versicolour/versicolor/g' | grep -v ",NA," > iris-data-clean.csv'
> zless iris-data-clean.csv | cut -c 22- | uniq -c
```

Alternatively, following cleaning, the `cut` command could be used with the modifier `-d` to cut the data into columns at `','` and then refer to the 5th column using `-f 5`. Data could then be sorted using `sort` and then unique names could be counted using `uniq` and the `-c` modifier. 

```
> cut -d ',' -f 5 iris-data-dirty.csv | sort | uniq -c
```
```
     50 setosa
     48 versicolor
     50 virginica
```

## III. Summarize sequence data file

Find how many lines in the data file `test.fastq.gz` start with "TGCAG" and end with "GAG"

I used `grep` to search for both `"^TGCAG"` and `"GAG$"` and counted the occurrences using `cat` with the option `-n`.

```
> zless test.fastq | grep "^TGCAG" | grep "GAG$" | cat -n
```
Terminal shows 44 lines that met the criteria specified above.

## IV. Summarize sequence data file

Write a `for-loop` to separate the reads from the file `test.fastq.gz` based on the taxon name in the label, and write the reads to separately named files in the new directory called `sorted_reads/`. The answer to this question will require more than a single line. See the lecture materials about using variables in for-loops. This will also be tricky because each read in the data file spans four lines (this is a standard genetic sequence file format), so for each read that you correctly identify you must grab the line with the sequence data below it, as well as the repeat label after that, and the quality information line after that. For a hint, see additional options for the `grep` command that can be used to select multiple lines.

To begin, I used the `zless` command to view the names of the species within `test.fastq` such that I could reference the species names in my for loop. After this, I created a new directory within `$HOME` named `sorted_reads` and then changed directory to the newly created directory. In the for loop I created a list of items which consisted of the species names `superba thamno cyathophylla przewalskii rex cyathophylloides`. Using `grep`, I searched for the line that included the search term (`$item`) as well as 1 line after (`-A 1`) within the `test.fastq` file in the parent directory and wrote the files to independent files defined by `$item.fastq`. Because each sequence read is 4 lines, I initially used the option `-A 3` but this was erroneous as the species names are mentioned once on line 1 of the 4 lines and once on line 3 of the 4 lines, thus the `grep` was producing an overlap between search terms.

```
> zless test.fastq 
> mkdir sorted_reads
> cd $HOME/sorted_reads
> for item in superba thamno cyathophylla przewalskii rex cyathophylloides
> do
> grep -A 1 $item ../test.fastq > $item.fastq
> done
```
