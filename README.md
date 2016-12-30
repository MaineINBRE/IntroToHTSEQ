# Introduction to HTSeq

[HTSeq](http://www-huber.embl.de/users/anders/HTSeq/doc/overview.html) is a Python package that provides infrastructure to process data from high-throughput sequencing assays. It does many, many things - but one of the most useful functions it serves for our purposes is to produce a count of reads per gene from RNA-seq data. 


## Counting

Given a file with aligned sequencing reads and a list of genomic features, a common task is to count how many reads map to each feature. n the case of RNA-Seq, the features are typically genes, where each gene is considered here as the union of all its exons. 

Special care must be taken to decide how to deal with reads that overlap more than one feature. The htseq-count script allows to choose between three modes. 

The three overlap resolution modes of htseq-count work as follows: For each position *i* in the read, a set *S*(*i*) is defined as the set of all features overlapping position *i*. Then, consider the set *S*, which is (with *i* running through all position within the read or a read pair):

  * The union of all the sets *S*(*i*). This is the `union` mode.
  * The intersection of all the sets *S*(*i*). This is the `intersection-strict` mode. 
  * The intersection of all non-empty sets *S*(*i*). This is the `intersection-nonempty` mode. 

If *S* contains precisely one feature, the read (or read pair) is counted for this feature. If it contains more than one feature, the read (or read pair) is counted as `ambiguous` (and not counted for any features), and if *S* is empty, the read (or read pair) is counted as `no_feature`.

The following figure explains each mode:

<p align="center">
<kbd>
  <img src="Images/counts.png"/>
 </kbd>
 </p>

***

