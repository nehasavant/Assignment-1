## Assignment-1  
Neha Savant  
29 Jan 2018
>Your assignment is to create a markdown document called README.md, or assignment.md, or whatever you wish to call it, and to save it in a repository in your github account. In this file you should document your answers to all of the problems below in the following format using markdown. Use a header for each question, then use **plain text** to copy the question, then use **plain text** to write your answer and link to any online resources that helped you, and finally include a **code-block** for your code and a **code-block** with your copied answer. Use an online markdown live editor to test your code, or the "preview" button on github to make sure that it is formatted to look clean and easily readable.  


### 1. Get the data files.
Download the following data files from the internet using the curl command: http://eaton-lab.org/pdsb/test.fastq.gz and http://eaton-lab.org/pdsb/iris-data-dirty.csv. Use the less or zless commands to look at the files. Then use the head command to print the first 5 lines from each file as output.

```
curl http://eaton-lab.org/pdsb/test.fastq.gz > test.fastq.gz
curl http://eaton-lab.org/pdsb/iris-data-dirty.csv > iris-data-dirty.csv
zless test.fastq.gz
zless iris
```

### 2. Clean the data.
Use grep, uniq, sed. Check that all of the species names are spelled correctly in the file iris-data-dirty.csv. Also check for missing values stored as NA. Create a new file where misspelled names are replaced with the correct values, and lines with NA are excluded, and save it as iris-data-clean.csv. Use cut, sort and uniq to list the number of data values there are for each species in the new cleaned data file.

```
#Finds which lines were misspelled and how they were misspelled.  
egrep -v -n "$Iris-versicolor|$Iris-setosa|$Iris-virginica" iris-data-dirty.csv

#Replaces setsa with setosa,  replaces versicolour with versicolor and returns all lines that don't have NAs.
sed -e 's/setsa/setosa/g' -e 's/versicolour/versicolor/g' iris-data-dirty.csv | grep -v 'NA' > iris-data-clean.csv

#Outputs three values with the # of lines of each species.
cut -s -d "," -f 5 iris-data-clean.csv | uniq -c
```

This link helped me find egrep so I can grep for multiple patterns: https://unix.stackexchange.com/questions/37313/how-do-i-grep-for-multiple-patterns  
This link told me I could use -e multiple times with the send command:  https://unix.stackexchange.com/questions/97428/sed-how-to-do-several-consecutive-substitutions-but-process-file-only-once

### 3. Summarize sequence data file.
Find how many lines in the data file test.fastq.gz start with "TGCAG" and end with "GAG"

```
#zcat is used to decompress files.  
zcat < test.fastq.gz | grep -e "^TGCAG.*GAG$" | wc -l
```
This link helped me discover how to count lines in a terminal output: https://stackoverflow.com/questions/12457457/count-number-of-lines-in-terminal-output
This link helped me figure out a way to use zcat work on a mac: https://serverfault.com/questions/570024/zcat-gzcat-works-in-linux-not-on-osx-general-linux-osx-compatibility


### 4. Summarize sequence data file.
Write a for-loop to separate the reads from the file test.fastq.gz based on the taxon name in the label, and write the reads to separately named files in the new directory called sorted_reads/. The answer to this question will require more than a single line. See the lecture materials about using variables in for-loops. This will also be tricky because each read in the data file spans four lines (this is a standard genetic sequence file format), so for each read that you correctly identify you must grab the line with the sequence data below it, as well as the repeat label after that, and the quality information line after that. For a hint, see additional options for the grep command that can be used to select multiple lines.

```
curl http://eaton-lab.org/pdsb/test.fastq.gz | zcat > seqdata

SPECIES=$(grep '^@[0-9]' seqdata| cut -d "." -f 1 | cut -d "_" -f 2 | sort | uniq)

for item in $SPECIES
do
  mkdir sorted_reads
  touch $item
  grep -A3 $item seqdata | cat >> $item
  mv $item sorted_reads
done
 ```
