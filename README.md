# The 4-gram Study

**NOTE:** This document is a work-in-progress as of 2019-06-21.

This repository documents the 4-gram study conducted in 2013 by the Wordtree Foundation, specifically Duane & Chris Johnson. The study connected the Book of Mormon (1830) and The Late War Between the United States and Great Britain, by Gilbert Hunt (1812). Other books of interest included The First Book of Napoleon and the Apocrypha.

The intent of this document is to make it easy to reproduce and/or extend the results of the study.


## Methodology

The 4-gram study was, at its core, a search algorithm. Using a computer algorithm, the study compared approximately 100,000 books published prior to 1830 with the Book of Mormon (1830 edition).

The authors sought to find a particular kind of similarity between the Book of Mormon & other books. The "similarity function" was the use of contextually rare 4-grams (i.e. "any four words in sequence") to discover possible influence. The central idea was that if there were a book (or books) that showed an overwhelming number of unusual similarities, then that book may have been an influence on the author of the Book of Mormon (i.e. probably Joseph Smith).


## Data

The study used data published by [archive.org](https://archive.org/details/texts)--English books scanned and [OCRed](https://en.wikipedia.org/wiki/Optical_character_recognition) to produce text files suitable to language analytics software.


## Replication

The following directions should replicate the results of the study, with as much fidelity as possible to the original study. However, given that some data may have changed in the meantime (e.g. archive.org updates its library with new books, and may improve quality of OCR over time), it's possible to get slightly divergent results.


### 1. Get English-language Books as Text Files, 1650 to 1830.

#### (Option 1 - Recommended) Download our prepared data directly.

**TODO:** link to prepared data


#### (Option 2) Download English-language books from archive.org

The [archdown](https://github.com/wordtreefoundation/archdown) command-line tool developed by the Wordtree Foundation is a helpful tool for this purpose.

Prerequisites: Ruby 2.x

Download and unzip https://github.com/wordtreefoundation/archdown/archive/master.zip (or use `git clone git@github.com:wordtreefoundation/archdown.git` if you're a developer). Then install & run `archdown`:

```
$ bundle install
$ mkdir library
$ bundle exec bin/archdown -l ./library -y 1650-1830
```

Note that archive.org has since changed its policies to limit the tool to downloading 10,000 files at a time, so it may require some manual restarting, updating the start year each time to avoid redundant downloading.


### 2. Get the Book of Mormon, original 1830 edition.

#### (Option 1 - Recommended) Download our Book of Mormon Database Tool.

The [bomdb](https://github.com/wordtreefoundation/bomdb) command-line tool developed by the Wordtree Foundation has the 1830 original edition.

#### (Option 2) Get the Book of Mormon from another source.

- Download the [Book of Mormon - unknown edition](http://www.gutenberg.org/ebooks/17) from Project Gutenberg
- Download the [Book of Mormon - 1830 edition](https://www.loc.gov/rr/rarebook/digitalcoll/digitalcoll-mormon.html) (PDF) from Library of Congress
- Download the [Book of Mormon - 1830 edition](http://www.xristian.org/ft/mormbomtext.pdf) (PDF) from Xristian.org

The PDF versions will need some work by you before they can be used as text in the next step.

### 3. Process pre-1830s books to calculate rough Language Model

TODO: steps to reproduce "rough language model"

### 4. Create a score for each book

TODO: steps to reproduce scoring

### 5. Rank all books according to score

TODO: steps to reproduce ranking

