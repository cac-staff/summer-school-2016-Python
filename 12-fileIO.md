---
layout: page
title: Programming with Python
subtitle: Reading and writing files
minutes: 20
---
> ## Learning Objectives {.objectives}
>
> *   Understand the difference between package-specific file I/O functions and Python's file I/O system.
> *   Learn to write to a file.
> *   Understand the meaning/use of special characters `\t` and `\n`.
> *   Learn the basics of parsing a file.

There are a number of ways to read and write files in Python. In fact, we've already used one of these: `numpy.loadtxt()`. As we've seen, Numpy's `loadtxt()` function is very useful for reading a tab- or comma-delimited file and storing the results as a Numpy array. That said, `numpy.loadtxt()` only covers a small number of use cases. `numpy.loadtxt()` won't help us if we're not dealing with numbers, for instance. For cases like these, we will learn make use of Python's basic file I/O abilities. 

## Reading files

At it's simplest, we can open a file with the `open()` function. There is an optional argument for what file opening mode we wish to use- the default is 'r' for reading. Let's open up an sample FASTA file (`data/ioExample.fa`) for this exercise. 

Although FASTA files are specific to genomics, they are a good example of a raw text format that can't be handled by the likes of `numpy.loadtxt()` (there is a SeqIO module from BioPython that handles this easily, but we're going to pretend that doesn't exist for the purpose of learning). FASTA files are quite simple in structure- a `>` provides a label and other information for a nucleotide sequence (these are the letters of the DNA alphabet), then the actual data follows. You can have several sequences like these in a file.

```{.python}
file = open('data/ioExample.fa', 'r')
```

Iterating through lines of the file is quite painless... by default, a for-loop will iterate through all the lines of the file.

```{.python}
for line in file:
    print(line)
```
```{.output}
>FBtr0070823 type=mRNA; loc=X:join(5901982..5902096,5902690..5905227); ID=FBtr0070823; name=Act5C-RB; dbxref=FlyBase_Annotation_IDs:CG4027-RB,FlyBase:FBtr0070823,REFSEQ:NM_078497,REFSEQ:NM_078497,REFSEQ:NM_078497; score=15; score_text=Strongly Supported; MD5=50caec406caeac9bd69f481d94bb76b9; length=2653; parent=FBgn0000042; release=r6.11; species=Dmel; 

TTTCAGTCGGTTTATTCCAGTCATTCCTTTCAAACCGTGCGGTCGCTTAGCTCAGCCTCGCCACTTGCGTTTACAGTAGT

TTTCACGCCTTGAATTTGTTAAATCGAACAAAAAGCTTACAAAATGTGTGACGAAGAAGTTGCTGCTCTGGTTGTCGACA

[cut rest of file output]
```

Notice that there is an extra space between every line in this case. This is caused by two things: each line in the file ends with the `\n` character (which triggers a new line), and the `print()` function ends every line with a `\n` by default (giving us 2 `\n`s, or 2 line breaks between each line. Let's fix our program to not add an extra `\n` when printing things back. We will also close the file at the end of our program with `file.close()`.

Our program should now look something like this:

```{.python}
file = open('data/ioExample.fa')

for line in file:
    print(line, end='')

file.close()
```
```{.output}
>FBtr0070823 type=mRNA; loc=X:join(5901982..5902096,5902690..5905227); ID=FBtr0070823; name=Act5C-RB; dbxref=FlyBase_Annotation_IDs:CG4027-RB,FlyBase:FBtr0070823,REFSEQ:NM_078497,REFSEQ:NM_078497,REFSEQ:NM_078497; score=15; score_text=Strongly Supported; MD5=50caec406caeac9bd69f481d94bb76b9; length=2653; parent=FBgn0000042; release=r6.11; species=Dmel; 
TTTCAGTCGGTTTATTCCAGTCATTCCTTTCAAACCGTGCGGTCGCTTAGCTCAGCCTCGCCACTTGCGTTTACAGTAGT
TTTCACGCCTTGAATTTGTTAAATCGAACAAAAAGCTTACAAAATGTGTGACGAAGAAGTTGCTGCTCTGGTTGTCGACA
ACGGCTCTGGCATGTGCAAGGCCGGATTTGCCGGAGACGATGCTCCCCGCGCCGTCTTCCCATCGATTGTGGGACGTCCC

[cut rest of file output]
```

## Using `with`

Remembering to close our files every time we read from them can be a pain. Fortunately, Python has a solution for us: the `with` keyword. `with` starts an indented block of text, and once our program leaves this block, Python will automatically close the file. We save the variable name (in this case the filename) using the word `as`. This is best demonstrated after trying things out ourselves:
```{.python}
with open('data/ioExample.fa') as file:
    for line in file:
        print(line, end='')
```
```{.output}
>FBtr0070823 type=mRNA; loc=X:join(5901982..5902096,5902690..5905227); ID=FBtr0070823; name=Act5C-RB; dbxref=FlyBase_Annotation_IDs:CG4027-RB,FlyBase:FBtr0070823,REFSEQ:NM_078497,REFSEQ:NM_078497,REFSEQ:NM_078497; score=15; score_text=Strongly Supported; MD5=50caec406caeac9bd69f481d94bb76b9; length=2653; parent=FBgn0000042; release=r6.11; species=Dmel;
TTTCAGTCGGTTTATTCCAGTCATTCCTTTCAAACCGTGCGGTCGCTTAGCTCAGCCTCGCCACTTGCGTTTACAGTAGT
TTTCACGCCTTGAATTTGTTAAATCGAACAAAAAGCTTACAAAATGTGTGACGAAGAAGTTGCTGCTCTGGTTGTCGACA
ACGGCTCTGGCATGTGCAAGGCCGGATTTGCCGGAGACGATGCTCCCCGCGCCGTCTTCCCATCGATTGTGGGACGTCCC

[cut rest of file output]
``` 

By now, many of you will have noticed that FASTA files include a header line (that starts with `>`). Let's change our program to only print these header lines.

```{.python}
with open('data/ioExample.fa') as file:
    for line in file:
        if line[0] == '>':
            print(line, end='')
```
```{.output}
>FBtr0070823 type=mRNA; loc=X:join(5901982..5902096,5902690..5905227); ID=FBtr0070823; name=Act5C-RB; dbxref=FlyBase_Annotation_IDs:CG4027-RB,FlyBase:FBtr0070823,REFSEQ:NM_078497,REFSEQ:NM_078497,REFSEQ:NM_078497; score=15; score_text=Strongly Supported; MD5=50caec406caeac9bd69f481d94bb76b9; length=2653; parent=FBgn0000042; release=r6.11; species=Dmel; 
>FBtr0070822 type=mRNA; loc=X:join(5900861..5901013,5902690..5904173); ID=FBtr0070822; name=Act5C-RA; dbxref=FlyBase_Annotation_IDs:CG4027-RA,FlyBase:FBtr0070822,REFSEQ:NM_167053,REFSEQ:NM_167053; score=15; score_text=Strongly Supported; MD5=c727ce58ff19fd7683f6319cb502963e; length=1637; parent=FBgn0000042; release=r6.11; species=Dmel; 
>FBtr0100662 type=mRNA; loc=X:join(5900861..5901013,5902690..5904788); ID=FBtr0100662; name=Act5C-RC; dbxref=FlyBase_Annotation_IDs:CG4027-RC,FlyBase:FBtr0100662,REFSEQ:NM_001014726,REFSEQ:NM_001014726; score=15; score_text=Strongly Supported; MD5=ee68d31c61dc8c920876794d471f9ad6; length=2252; parent=FBgn0000042; release=r6.11; species=Dmel; 
>FBtr0100663 type=mRNA; loc=X:join(5900861..5901013,5902690..5904464); ID=FBtr0100663; name=Act5C-RD; dbxref=FlyBase_Annotation_IDs:CG4027-RD,FlyBase:FBtr0100663,REFSEQ:NM_001014725,REFSEQ:NM_001014725; score=15; score_text=Strongly Supported; MD5=3006846211d7279213e967e20265cb0b; length=1928; parent=FBgn0000042; release=r6.11; species=Dmel; 
>FBtr0345894 type=mRNA; loc=X:join(5900861..5901013,5902690..5905399); ID=FBtr0345894; name=Act5C-RE; dbxref=FlyBase:FBtr0345894,FlyBase_Annotation_IDs:CG4027-RE,REFSEQ:NM_001297986; score=15; score_text=Strongly Supported; MD5=538411fdf0fb678c9c68f422d83255db; length=2863; parent=FBgn0000042; release=r6.11; species=Dmel; 
```

## Cleaning up input with `split()` and `rstrip()`

At this point, our simple program prints out the headers for the different DNA sequences, but we don't yet have a way of parsing this information to make it useable (each header is just one gigantic string). To break this data up into more useable chunks, we will use the split() function. `split()` breaks up a string into a list using a delimiter character we specify.

Note that we will change the print command to use line breaks again (by removing the `end` argument). We will break up each line using semicolons as a delimiter.
```{.python}
with open('data/ioExample.fa') as file:
    for line in file:
        if line[0] == '>':
            line = line.split(';')
            print(line)
```
```{.output}
['>FBtr0070823 type=mRNA', ' loc=X:join(5901982..5902096,5902690..5905227)', ' ID=FBtr0070823', ' name=Act5C-RB', ' dbxref=FlyBase_Annotation_IDs:CG4027-RB,FlyBase:FBtr0070823,REFSEQ:NM_078497,REFSEQ:NM_078497,REFSEQ:NM_078497', ' score=15', ' score_text=Strongly Supported', ' MD5=50caec406caeac9bd69f481d94bb76b9', ' length=2653', ' parent=FBgn0000042', ' release=r6.11', ' species=Dmel', ' \n']
['>FBtr0070822 type=mRNA', ' loc=X:join(5900861..5901013,5902690..5904173)', ' ID=FBtr0070822', ' name=Act5C-RA', ' dbxref=FlyBase_Annotation_IDs:CG4027-RA,FlyBase:FBtr0070822,REFSEQ:NM_167053,REFSEQ:NM_167053', ' score=15', ' score_text=Strongly Supported', ' MD5=c727ce58ff19fd7683f6319cb502963e', ' length=1637', ' parent=FBgn0000042', ' release=r6.11', ' species=Dmel', ' \n']
['>FBtr0100662 type=mRNA', ' loc=X:join(5900861..5901013,5902690..5904788)', ' ID=FBtr0100662', ' name=Act5C-RC', ' dbxref=FlyBase_Annotation_IDs:CG4027-RC,FlyBase:FBtr0100662,REFSEQ:NM_001014726,REFSEQ:NM_001014726', ' score=15', ' score_text=Strongly Supported', ' MD5=ee68d31c61dc8c920876794d471f9ad6', ' length=2252', ' parent=FBgn0000042', ' release=r6.11', ' species=Dmel', ' \n']
['>FBtr0100663 type=mRNA', ' loc=X:join(5900861..5901013,5902690..5904464)', ' ID=FBtr0100663', ' name=Act5C-RD', ' dbxref=FlyBase_Annotation_IDs:CG4027-RD,FlyBase:FBtr0100663,REFSEQ:NM_001014725,REFSEQ:NM_001014725', ' score=15', ' score_text=Strongly Supported', ' MD5=3006846211d7279213e967e20265cb0b', ' length=1928', ' parent=FBgn0000042', ' release=r6.11', ' species=Dmel', ' \n']
['>FBtr0345894 type=mRNA', ' loc=X:join(5900861..5901013,5902690..5905399)', ' ID=FBtr0345894', ' name=Act5C-RE', ' dbxref=FlyBase:FBtr0345894,FlyBase_Annotation_IDs:CG4027-RE,REFSEQ:NM_001297986', ' score=15', ' score_text=Strongly Supported', ' MD5=538411fdf0fb678c9c68f422d83255db', ' length=2863', ' parent=FBgn0000042', ' release=r6.11', ' species=Dmel', ' \n']
```

One thing that to notice is that the end of our lines still have the `\n` character, and the line still begins with `>` (we don't want that part). We can get rid of these with some clever indexing before we use `split()`.

```{.python}
with open('data/ioExample.fa') as file:
    for line in file:
        if line[0] == '>':
            line = line[1:-1]
            line = line.split(';')
            print(line)
```
```{.output}
['FBtr0070823 type=mRNA', ' loc=X:join(5901982..5902096,5902690..5905227)', ' ID=FBtr0070823', ' name=Act5C-RB', ' dbxref=FlyBase_Annotation_IDs:CG4027-RB,FlyBase:FBtr0070823,REFSEQ:NM_078497,REFSEQ:NM_078497,REFSEQ:NM_078497', ' score=15', ' score_text=Strongly Supported', ' MD5=50caec406caeac9bd69f481d94bb76b9', ' length=2653', ' parent=FBgn0000042', ' release=r6.11', ' species=Dmel', ' ']
['FBtr0070822 type=mRNA', ' loc=X:join(5900861..5901013,5902690..5904173)', ' ID=FBtr0070822', ' name=Act5C-RA', ' dbxref=FlyBase_Annotation_IDs:CG4027-RA,FlyBase:FBtr0070822,REFSEQ:NM_167053,REFSEQ:NM_167053', ' score=15', ' score_text=Strongly Supported', ' MD5=c727ce58ff19fd7683f6319cb502963e', ' length=1637', ' parent=FBgn0000042', ' release=r6.11', ' species=Dmel', ' ']
['FBtr0100662 type=mRNA', ' loc=X:join(5900861..5901013,5902690..5904788)', ' ID=FBtr0100662', ' name=Act5C-RC', ' dbxref=FlyBase_Annotation_IDs:CG4027-RC,FlyBase:FBtr0100662,REFSEQ:NM_001014726,REFSEQ:NM_001014726', ' score=15', ' score_text=Strongly Supported', ' MD5=ee68d31c61dc8c920876794d471f9ad6', ' length=2252', ' parent=FBgn0000042', ' release=r6.11', ' species=Dmel', ' ']
['FBtr0100663 type=mRNA', ' loc=X:join(5900861..5901013,5902690..5904464)', ' ID=FBtr0100663', ' name=Act5C-RD', ' dbxref=FlyBase_Annotation_IDs:CG4027-RD,FlyBase:FBtr0100663,REFSEQ:NM_001014725,REFSEQ:NM_001014725', ' score=15', ' score_text=Strongly Supported', ' MD5=3006846211d7279213e967e20265cb0b', ' length=1928', ' parent=FBgn0000042', ' release=r6.11', ' species=Dmel', ' ']
['FBtr0345894 type=mRNA', ' loc=X:join(5900861..5901013,5902690..5905399)', ' ID=FBtr0345894', ' name=Act5C-RE', ' dbxref=FlyBase:FBtr0345894,FlyBase_Annotation_IDs:CG4027-RE,REFSEQ:NM_001297986', ' score=15', ' score_text=Strongly Supported', ' MD5=538411fdf0fb678c9c68f422d83255db', ' length=2863', ' parent=FBgn0000042', ' release=r6.11', ' species=Dmel', ' ']
```

Excellent, why don't we try saving each line as an element of a list? The output of this code should be identical to that from before. I've added some comments here and there for readability.

```{.python}
# initialize our list
data = []

# open and parse the file
with open('data/ioExample.fa') as file:
    for line in file:
        if line[0] == '>':
            line = line[1:-1]
            line = line.split(';')
            data.append(line)

# print back the data
for header in data:
    print(header)
```

## Writing files

Writing files is done in an almost identical manner to reading them. To demonstrate this, we will write our data to a file, and separate the data elements with tabs (`\t`). Note that we absolutely need to manually add in a `\n` at the end of each line, or all of the data will be written to one line.

```{.python}
# initialize our list
data = []

# open and parse the file
with open('data/ioExample.fa') as file:
    for line in file:
        if line[0] == '>':
            line = line[1:-1]
            line = line.split(';')
            data.append(line)

# print back the data
with open('data/ioOutput.tsv') as output:
    for header in data:
        for element in header:
            output.write(element + '\t')
        output.write('\n')
```
```{.output}
Traceback (most recent call last):
  File "/home/jeff/Documents/PycharmProjects/swc-sandbox/fastaIO.py", line 16, in <module>
    output.write(element + '\t')
io.UnsupportedOperation: not writable
```

Oh no, it looks like Python isn't letting us write to the file! The reason for this is that we need to call `open()` with the optional `mode` argument. Specifically, we must use `mode='w'` to indicate we wish to write to the file (the default mode is `'r'`) for reading.

```{.python}
# initialize our list
data = []

# open and parse the file
with open('data/ioExample.fa', mode='r') as file:
    for line in file:
        if line[0] == '>':
            line = line[1:-1]
            line = line.split(';')
            data.append(line)

# print back the data
with open('data/ioOutput.tsv', mode='w') as output:
    for header in data:
        for element in header:
            output.write(element + '\t')
        output.write('\n')
```

The file produced should look like this:
```{.output}
FBtr0070823 type=mRNA	 loc=X:join(5901982..5902096,5902690..5905227)	 ID=FBtr0070823	 name=Act5C-RB	 dbxref=FlyBase_Annotation_IDs:CG4027-RB,FlyBase:FBtr0070823,REFSEQ:NM_078497,REFSEQ:NM_078497,REFSEQ:NM_078497	 score=15	 score_text=Strongly Supported	 MD5=50caec406caeac9bd69f481d94bb76b9	 length=2653	 parent=FBgn0000042	 release=r6.11	 species=Dmel	 	
FBtr0070822 type=mRNA	 loc=X:join(5900861..5901013,5902690..5904173)	 ID=FBtr0070822	 name=Act5C-RA	 dbxref=FlyBase_Annotation_IDs:CG4027-RA,FlyBase:FBtr0070822,REFSEQ:NM_167053,REFSEQ:NM_167053	 score=15	 score_text=Strongly Supported	 MD5=c727ce58ff19fd7683f6319cb502963e	 length=1637	 parent=FBgn0000042	 release=r6.11	 species=Dmel	 	
FBtr0100662 type=mRNA	 loc=X:join(5900861..5901013,5902690..5904788)	 ID=FBtr0100662	 name=Act5C-RC	 dbxref=FlyBase_Annotation_IDs:CG4027-RC,FlyBase:FBtr0100662,REFSEQ:NM_001014726,REFSEQ:NM_001014726	 score=15	 score_text=Strongly Supported	 MD5=ee68d31c61dc8c920876794d471f9ad6	 length=2252	 parent=FBgn0000042	 release=r6.11	 species=Dmel	 	
FBtr0100663 type=mRNA	 loc=X:join(5900861..5901013,5902690..5904464)	 ID=FBtr0100663	 name=Act5C-RD	 dbxref=FlyBase_Annotation_IDs:CG4027-RD,FlyBase:FBtr0100663,REFSEQ:NM_001014725,REFSEQ:NM_001014725	 score=15	 score_text=Strongly Supported	 MD5=3006846211d7279213e967e20265cb0b	 length=1928	 parent=FBgn0000042	 release=r6.11	 species=Dmel	 	
FBtr0345894 type=mRNA	 loc=X:join(5900861..5901013,5902690..5905399)	 ID=FBtr0345894	 name=Act5C-RE	 dbxref=FlyBase:FBtr0345894,FlyBase_Annotation_IDs:CG4027-RE,REFSEQ:NM_001297986	 score=15	 score_text=Strongly Supported	 MD5=538411fdf0fb678c9c68f422d83255db	 length=2863	 parent=FBgn0000042	 release=r6.11	 species=Dmel	 	

```

> ## Applying the concepts {.challenge}
> It looks like there is a lot of redundant information in our file. Everything to the right of the `=` signs is useful, the stuff to the left of the `=` is just a label. Find a way to write only the stuff to the right of the equal signs.

> ## Appending to a file {.challenge}
> Notice how our file we just wrote completely overwrote the previous contents of the file. Can you find a way to append to the end of a file instead? 
> 
> Hint: check the `mode` argument of the `open()` documentation.

