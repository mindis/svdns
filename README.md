# Spectral Word Embedding with Negative Sampling

This software learns a word embedding from the input co-occurrence matrix (preferably extracted from a large corpus such as Wikipedia). This work is submitted to NIPS 2017 and is under review.

The following instructions will get you a copy of the project up and running on your local machine for development and testing purposes.

## Prerequisites for input preparation

The input to this algorithm is a word-word co-occurrence matrix. For calculating this co-occurrence matrix, we use existing software from GloVe which can be downloaded at:

* [vocab_count](https://github.com/stanfordnlp/GloVe/blob/master/src/vocab_count.c) - This file is used to scan the corpus and build a vocabulary.
* [cooccur](https://github.com/stanfordnlp/GloVe/blob/master/src/cooccur.c) - This file is used, given a vocabulary, to calculate the word-word co-occurrence matrix.

## Compiling the source code

The source code of the software is written in C and can be compiled using standard C compilers in any operating system (Linux, Windows, and macOS). To compile the prerequisites use:

```
$ gcc -Wall -m64 -O3 vocab_count.c -o vocab_count -lm -lpthread
$ gcc -Wall -m64 -O3 cooccur.c -o cooccur -lm -lpthread
```

You can ignore `-Wall` (show all warnings), `-m64` (compile for 64-bit system), `-O3` (optimization level 3). However, `-lm` (link math library) and `-lpthread` (multi-threading library) are required to compile and run the program.

To compile our program run:

```
$ gcc -Wall -fopenmp -m64 -O3 svdns.c -o svdns -lm
```

Our program uses OpenMP shared memory multi-threading library which is standard and is implemented in almost every C compiler. If you ignore `-fopenmp` switch, it will run on a single thread, however, for better performance use this option.

## Running the software to train a word embedding

For this purpose, you need to have a large text corpus (e.g Wikipedia) in a single text file. For instance, dump of June 1st, 2017 of Wikipedia (articles in XML format) can be downloaded at: `https://dumps.wikimedia.org/enwiki/latest/enwiki-latest-pages-articles.xml.bz2`. For the latest dump, you can always refer to `https://dumps.wikimedia.org/enwiki/latest/`.

These types of corpuses require a lot of preprocessing such as removing HTML tags and structure to get clean text from it, handling or removing special characters, etc. We will not go through the details of preprocessing but it is a neccessary step in order to get a high quality embedding with meaningful and manageable sized vocabulary.

After downloading and extracting the zip file, and also preprocessing steps you will get the clean text file. Let's call the clean text file `corpus_clean.txt`. In order to train and obtain a word embedding, run the following 3 commands one after another:

```
$ ./vocab_count -min-count 5 < ./corpus_clean.txt > ./vocab.txt
$ ./cooccur -window-size 10 -vocab-file ./vocab.txt < ./corpus_clean.txt > ./cooccurrence_matrix.bin
$ ./svdns -pmi2 -pmicutoff -2.5 -shift 2.5 -dimension 100 -thread 8 -vocab ./vocab.txt -input ./cooccurrence_matrix.bin -output ./svdns_embedding_d100.txt
```

After running the above commands, `svdns_embedding_d100.txt` will be generated which contains the word embeddings. Each row will contain a word and its corresponding vector representation.

## Options and switches for executing the code

For `vocab_count` it is good to limit the vocabulary to the words occuring at least `-min-count` times. This option will remove extremely rare words from the vocabulary.

For `cooccur` you need to use a proper `window-size`. Reasonable range for `window-size` is between 5 and 15.

For our algorithm `svdns` there are several switches that can be used:

* -pmi2, pmi10: base 2 and base 10 Pointwise Mutual Information (PMI) calculation

## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details

## Acknowledgments

* Hat tip to anyone who's code was used
* Inspiration
* etc
