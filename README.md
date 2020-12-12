# bio.sh
Basic intro to Linux and shell script for Bioinformatics


### Outline

1. Unix and the command line
2. Connect and sharing
3. Basic commands
4. Users and groups
5. Get into your machine and system
6. Examples for Bioinformatics

---
### 1. Unix and the command line
**Unix history**

The Unix operating system derive from the original AT&T UNIX operating system, developed in the mid 1960s at the Bell Labs research center by Ken Thompson, Dennis Ritchie, among others.
![Unix history](https://upload.wikimedia.org/wikipedia/commons/7/77/Unix_history-simple.svg)

**Linux history**
* A UNIX based operating system
* Began in 1991 by Linus Torvalds

![Linux history](https://external-preview.redd.it/_h7fSIEBHmsQGQX4Xg95bjJHYYK88YyWxAJmfSJtqaw.png?auto=webp&s=ba3038caf7708af7aa66ba82d00f578f4854a5ef)

**Ubuntu**
* Ubuntu is one (out of many) open source operating system based on Linux.
https://ubuntu.com/download/desktop
![ubuntu](https://assets.ubuntu.com/v1/8dd99b80-ubuntu-logo14.png)

**Linux file system**
* The Filesystem Hierarchy Standard (FHS) defines the main directories and their contents in Linux operating systems. 
![Linux file system](https://i.stack.imgur.com/hN4lt.jpg)

**Linux shell**
* **Shell:** the shell is a program that takes your commands from the keyboard and gives them to the operating system to perform
* **Terminal:** a program that run a shell
* **Directory:** folder, or location of a file

---
### 2. Connect and transfer
**What I need to connect to a remote Linux server?**
* **Windows:** Putty (https://www.putty.org/), Ubuntu based bash shell
* **Linux/Mac:** ssh (built-in on terminal)

**SSH syntax**
* `ssh urer@hostname`
* Example 1: `ssh joel@darwin.dei.uc.pt`
* Example 2: `ssh maria@193.137.200.184`

**SCP syntax**
SCP is used to copy file to and from the server
* scp file.txt urer@hostname:/some/remote/directory &leftarrow; copy local file to remote server
* scp urer@hostname:file.txt /some/local/directory &leftarrow; copy remote file to my computer

* Example 1: scp P00750.fasta joel@darwin.dei.uc.pt:/
* Example 2: scp maria@193.137.200.184:P00750.fasta ./

**Wget syntax**
Wget is used to get public files from a server.
* wget example-url
* Example 1: wget https://www.uniprot.org/uniprot/P00750.fasta

---
### 3. Basic commands
`ls` &leftarrow; list files from current directory
`ls /home/reports` &leftarrow; list files from a specific directory
`ls -lhS` (optional arguments: http://manpages.ubuntu.com/manpages/trusty/man1/ls.1.html)
`head file.txt -n3` &leftarrow; print the first 3 lines
`cp file.txt file1.txt` &leftarrow; make a copy of file.txt
`rm file.txt` &leftarrow; delete file.txt
`mv file1.txt file.txt` &leftarrow; rename (change location and name) file1.txt to file.txt

* mkdir some_folder &leftarrow; create a folder
* mkdir some_folder_{0..9} &leftarrow; create multiple folders

* cd &leftarrow; go to home directory
* cd some_folder &leftarrow; change folder
* cd some_folder/reports &leftarrow; change folder
* cd \~/some_folder/reports &leftarrow; relative path from home directory
* cd ./some_folder/reports &leftarrow; relative path from current directory
* cd ../some_folder/reports &leftarrow; relative path from parent directory

---
### 4. On-the-go Python example
* Create/open file with the nano text editor. 

`nano ./code/example.py`
```python
import sys
if len(sys.argv)>1:
	for i in range(int(sys.argv[1])):
		print(i)
```
`python3 example.py 10`

`python3 example.py 10 >out.txt`
`python3 example.py 10 >>out.txt`

* python3 example.py 10 | sort -r | head -n3

---
### 5. Foreground and background
* Terminate a process: CTRL-C
* Suspending a process: CTRL-Z
* fg &leftarrow; bring it back:
* sleep 20 & &leftarrow; & means that the command will run on background

---
### 6. Usefull -generic- commands
* whoami &leftarrow; Who am I?
* lsb_release -a &leftarrow; linux distribution and version
* ifconfig &leftarrow; get my IP address
* df -h &leftarrow; disk space available
* du -sbh &leftarrow; size of the directory
* w &leftarrow; who is logged to this computer
* top &leftarrow; what processes are running

---
### 7. Advanced commands
* grep: search for a pattern
$grep "ATG" file

* wc -l: count line, word and byte
$wc -l file
$grep "ATG" file | wc -l

* sort: sort text file
$sort -r file &leftarrow; sort file by reverse order

* tr: translate, squeeze, and/ delete chars
$ cat file | tr -d '>|' &leftarrow; delete chars from a text file

* uniq: filter adjacent matching lines
$uniq -c file

* cut: print selected parts of lines
$cut -d '|' -f3 file &leftarrow; split line by and get collum number 3

* sed: stream editor for filtering and processing text
* awk: pattern scanning and processing language

### Bash vs Python

* Using the best of both worlds [(more)](https://opensource.com/article/19/4/bash-vs-python)

---
### 8. Examples for Bioinformatics:
1. Simple processing of a tab delimited file
..* a) Get a tab file from Uniprot
wget -O ./data/9606.uniprot.tab 'https://www.uniprot.org/uniprot/?query=*&format=tab&columns=id,entry%20name,reviewed,protein%20names,genes,organism,length&fil=organism:%22Homo%20sapiens%20(Human)%20[9606]%22'


..* b) Format and general stats
head -n10 ./data/9606.uniprot.tab
tail -n10 ./data/9606.uniprot.tab
wc -l ./data/9606.uniprot.tab

..* c) Search for a specific patern
grep "BRCA2" ./data/9606.uniprot.tab
grep "ubiquitin" ./data/9606.uniprot.tab

..* d) Search on a specific col
awk -F"\t" '$7>2000' ./data/9606.uniprot.tab
awk -F'\t' '$3 == "unreviewed"' ./data/9606.uniprot.tab
awk -F'\t' '$3 == "unreviewed"||$7<200' ./data/9606.uniprot.tab

..* e) Get all proteins for a specific search
awk -F"\t" '$7>2000' ./data/9606.uniprot.tab | cut -f1,7 |sort -k2n


2. Search over a FASTA file

..* a) Get human fasta file from Uniprot
wget -O ./data/9606.uniprot.fasta 'https://www.uniprot.org/uniprot/?query=*&format=fasta&fil=organism:%22Homo%20sapiens%20(Human)%20[9606]%22'

..* b) Convert fasta file to one sequence per line
awk '/^>/ {printf("%s%s|",(N>0?"\n":""),$0);N++;next;} {printf("%s",$0);} END {printf("\n");}' <./data/9606.uniprot.fasta >./tmp/9606.uniprot.line.fasta

..* c) Get the ids from a fasta file (or other field)
cat ./tmp/9606.uniprot.line.fasta | cut -d '|' -f2 |sort 
 
..* d) Intersect two lists

..* e) C2H2 zinc finger motif (assume zinc finger motif to be CXXXCXXXXXXXXXXHXXXH)
cat ./tmp/9606.uniprot.line.fasta | grep --color "C..C............H...H"

..* f) Any regular expression
cat ./tmp/9606.uniprot.line.fasta | grep --color "L[AST]Q"

3. Using VCF files
