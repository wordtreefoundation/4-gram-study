# The 4-gram Study

**NOTE:** This document is a work-in-progress as of 2019-06-26.

This repository documents the 4-gram study conducted in 2013 by the Wordtree Foundation, specifically Duane & Chris Johnson. The [study](http://wordtree.org/the-late-war/) connected the Book of Mormon (1830) and The Late War Between the United States and Great Britain, by Gilbert Hunt (1812). Other books of interest included The First Book of Napoleon and the Apocrypha.

The intent of this document is to make it easy to reproduce and/or extend the results of the study.


## Methodology

The 4-gram study was, at its core, a search algorithm. Using a computer algorithm, the study compared approximately 100,000 books published prior to 1830 with the Book of Mormon (1830 edition).

The authors sought to find a particular kind of similarity between the Book of Mormon & other books. The "similarity function" was the use of contextually rare 4-grams (i.e. "any four words in sequence") to discover possible influence. The central idea was that if there were a book (or books) that showed an overwhelming number of unusual similarities, then that book may have been an influence on the author of the Book of Mormon (i.e. probably Joseph Smith).


## Data

The study used data published by [archive.org](https://archive.org/details/texts)--English books scanned and [OCRed](https://en.wikipedia.org/wiki/Optical_character_recognition) to produce text files suitable to language analytics software.


## Replication

The following directions should replicate the results of the study, with as much fidelity as possible to the original study. However, given that some data may have changed in the meantime (e.g. archive.org updates its library with new books, and may improve quality of OCR over time), it's possible to get slightly divergent results.

### Prerequisites

We assume a Unix-type environment. All of the commands should work on Mac OS X. In Windows, you can install the Ubuntu subsystem and it will work as well.

You should have the following *compilers, tools, and utilities*:

- `gcc` (GNU C Compiler)
- `ruby` (Ruby interpreter)
- `git` (Distributed Version Control System)
- text tools like `awk`, `sed`, `sort`, `uniq`, `xargs`
- `parallel` (GNU Parallel)
- `gzip` and `gunzip` (compression commands)

*Hardware & Network*

This documentation assumes hardware specifications as follows:

- 3.5GHz 4-core CPU
- 64 GB RAM Memory
- 1 TB SSD Hard Drive

In addition, a broadband connection to the internet was used with 150 MBit download speed.


### Folder Structure

Throughout this guide, we will assume a folder structure as follows:

```
+- study
   +- library       <-- 150k books from archive.org
   +- bomdb         <-- various editions of the Book of Mormon
   +- ngram-tools   <-- fast C-based tools for processing text as ngrams
```

### 1. Get the Book of Mormon, original 1830 edition

To algorithmically compare the Book of Mormon & other books, we need a text-only version of the Book of Mormon (scanned images of pages, even in PDF format, won't do). It's surprisingly difficult to find this data on the larger web, so we've built some data repositories and tools to make it easier.

#### (Option 1 - Recommended) Download the Book of Mormon from our Books Repository
*(~2min on a 150Mbit network)*

We have a repository of [pseudo-biblical books](https://github.com/wordtreefoundation/books) (i.e. books that mimic the "Biblical Style" of the King James Version of the bible--if you're interested in this American phenomenon, look up Eran Shalev and [American Zion](https://www.amazon.com/American-Zion-Testament-Political-Revolution/dp/0300205902)). This repository includes the Book of Mormon.

Download the [Book of Mormon - 1830 edition](https://raw.githubusercontent.com/wordtreefoundation/books/master/pseudo_biblical/Book%20of%20Mormon%20-%20Joseph%20Smith%20-%201830.md) from our Books repository

#### (Option 2) Download our Book of Mormon Database Tool
*(~20min on a 150Mbit network)*

The [bomdb](https://github.com/wordtreefoundation/bomdb) command-line tool (developed by the Wordtree Foundation) has the 1830 original edition, among several other editions. It is very flexible, and allows you to do things like only compare ranges of chapters or verses, or choose which edition of the Book of Mormon you'd like to compare.

Download & install bomdb, then write the entire Book of Mormon, 1830 edition, without verse or chapter numbers to "bom.txt":

```
$ cd bomdb
$ bundle install
$ bundle exec bin/bomdb show --edition=1830 --no-color --no-verses >bom.txt
```

#### (Option 3) Get the Book of Mormon from Another Source
*(~5min on a 150Mbit network)*

- Download the [Book of Mormon - unknown edition](http://www.gutenberg.org/ebooks/17) (TXT) from Project Gutenberg
- Download the [Book of Mormon - 1830 edition](https://www.loc.gov/rr/rarebook/digitalcoll/digitalcoll-mormon.html) (PDF) from Library of Congress
- Download the [Book of Mormon - 1830 edition](http://www.xristian.org/ft/mormbomtext.pdf) (PDF) from Xristian.org

The PDF versions will need some work by you before they can be used as text in the next step.


### 2. Get English-language Books as Text Files, 1650 to 1830

#### (Option 1 - Recommended) Download our prepared data directly
*(~1hr on a 150Mbit network)*

We've prepared an archive of books from 1650 to 1829, which you can [download here](https://s3.amazonaws.com/data.wordtree.org/archive-org-english-books-1650-1829-asof-2019-06-24.tar.gz) (about 30GB). This archive was downloaded in June of 2019.

These books are text-only, with a small (human-readable) YAML header at the top to identify its title, year, and author(s). They've been stored in sub-folders and sub-sub-folders so that a normal filesystem will handle the large quantity of books.

#### (Option 2) Download English-language books from archive.org
*(~3 days on a 150Mbit network)*

The [archdown](https://github.com/wordtreefoundation/archdown) command-line tool (developed by the Wordtree Foundation) is a helpful tool for querying & downloading directly from archive.org. It also arranges downloads in a tree structure so the filesystem can hold lots of books.

*Prerequisites: Ruby 2.x*

Download and unzip https://github.com/wordtreefoundation/archdown/archive/master.zip (or use `git clone git@github.com:wordtreefoundation/archdown.git`). Then install & run `archdown`:

```
$ cd archdown
$ bundle install
$ mkdir ../library
$ bundle exec bin/archdown -l ../library -y 1650-1830
```

Note that archive.org has since changed its policies to limit the tool to downloading 10,000 files at a time, so it may require some manual restarting, updating the start year each time to avoid redundant downloading.


### 3. Count the number of 4-grams in each pre-1830 book, including the Book of Mormon
*(~6hrs on a 4-core 3.5Ghz machine with 1TB SSD & 64GB of RAM)*

Let's count the 4-grams in the Book of Mormon to get started, and then use that as a starting-off point to count 4-grams in other books.

Use [ngram-tools](https://github.com/wordtreefoundation/ngram-tools) to count each 4-gram in the Book of Mormon and store it in a `.4grams` file. Build instructions and usage examples are also contained in the [ngram-tools readme](https://github.com/wordtreefoundation/ngram-tools).

```
$ ./text-to-ngrams 3 ../bomdb/bom.txt | sort | uniq -c | sort -bgr >bom.4grams
Input File: ../bomdb/bom.txt
size (bytes): 1417363
  ngrams emitted: 253386
```

We can check that the `bom.4grams` file contains a list of ngrams, counted & sorted:

```
$ less bom.4grams

   1398 came to pass
   1396 it came to
   1333 to pass that
   1164 and it came
    481 of the lord
    444 the land of
...
```

**Next: Counting 4-grams in the entire library**

Now that we know how to count 4-grams in one book, let's apply this to counting the 4-grams in all of our downloaded archive.org books:

```
$ find ../library/ -name "*.md" | shuf >library.toc
$ cat library.toc \
  | xargs -n 1 -I {} \
    sh -c "./text-to-ngrams 4 {} | sort | uniq -c >{}.4grams.tallied"
```

Breaking it down:

- The `find` command recursively looks for files in a folder that matches a pattern. We need this because filesystems typically can't contain 100,000 files in a single directory so we've created little sub-folders that each contain just a subset of the files. We're looking for ".md" files because the `archdown` command stores the plain-text archive.org books with a yaml header file that is compatible with markdown file format.
- The `shuf` command randomly shuffles the order of the text files, and we store it in a "table of contents" file called `library.toc`.
- We use the `library.toc` file to pipe the filenames that need processing to `xargs`.
- The `xargs` command takes the list of files as input and sends each one as an argument to the next command (`text-to-ngrams`). We need `-n 1` because `text-to-ngrams` doesn't take multiple files as input (just one file). We also use `-I {}` because we need to be able to specify where to "insert" each book filename in the next command.
- The `sh` command creates a new shell. This is necessary because we need to pipe several commands together.
- Next, we emit each 4-gram in each file via `text-to-ngrams`, then sort and count (via `uniq -c`) the ngrams.
- Finally, we redirect the output of the whole pipe chain to the original file location (in its original `library` sub-folder) but with the `.4grams` suffix appended so that we don't clobber the original text ('.md') file.

For faster processing on a mult-core CPU, use [GNU Parallel](https://www.gnu.org/software/parallel/):

```
$ find ../library/ -name "*.md" | shuf >library.toc
$ parallel -j4 --progress \
    -a library.toc \
    ./text-to-ngrams 4 {} \
    '|' ./tally-lines -c \
    '>' {}.4grams.tallied
```

The `*.md.4grams.tallied` files will contain two columns, the tally column (numbers), followed by the ngrams column (words). The two columns will be separated by a tab character ('\t'):

```
        5       a added providing for
        1       a added providing that
        1       a added provisions relative
        6       a added regulating the
        1       a added regulating workmens
       18       a added relative to
        7       a added requiring the
        1       a added restricting the
        1       a added to prevent
        1       a added under heading
        1       a adjacent to erithmo
        2       a adopt for the
        ... etc ...
```

### 4. Process pre-1830s books to calculate rough Language Model

In order to properly weight the significance of matching ngrams, we need a rough "language model". What this means is that rather than using intuition to tell us whether a sequence of words is rare, we should use math. For instance, "it came to pass" might be in both the Book of Mormon and a sermon preached in 1820, but that [doesn't mean they are connected](https://books.google.com/ngrams/graph?content=it+came+to+pass&year_start=1800&year_end=2000&corpus=15&smoothing=3&share=&direct_url=t1%3B%2Cit%20came%20to%20pass%3B%2Cc0#t1%3B%2Cit%20came%20to%20pass%3B%2Cc0) because "it came to pass" is a very common 4-gram.

By "rough" language model, we mean that this isn't a sophisticated language model, it's just a tally of occurrences. In a future study, it would be much better to use a proper language model. But in 2013, a tally is what we used.

Using our [ngram-tools](https://github.com/wordtreefoundation/ngram-tools), you can fairly easily clean up & tally ngrams (specifically, 4-grams) from any book in the archive.org library that you downloaded.

We'll use a shell-script based "map-reduce" algorithm to vastly speed up calculating a baseline. First, let's get a randomly shuffled list of the entire library's books:

```
$ find ../library/ -name '*.md.4grams' | shuf >library.toc
```

Then, let's sum up the ngrams in sets of 64 books:

```
$ mkdir output
$ cat library.toc \
  | parallel -j4 -N64 --progress \
      --joblog jobs.log \
      --files --tmpdir output \
      gunzip -c {} '|' ./tally-lines '|' gzip -c
```

Note: if you run out of memory, you can reduce `-N64` to something smaller. This is the number of books held in memory per process.

**TODO: explain next reduction step**

Note that calculating the baseline may take a long time (estimate 5 seconds per book, 150,000 books, that could take 200 hours).


### 5. Create a score for each book

TODO: steps to reproduce scoring


### 6. Rank all books according to score

TODO: steps to reproduce ranking

