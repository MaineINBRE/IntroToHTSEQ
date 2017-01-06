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
 Image from <a href="http://www-huber.embl.de/users/anders/HTSeq/doc/overview.html">here</a>

***

## Script

The generic script that we utilize follows this format:

```
samtools view -h -o [INDEXED SAM FILE] [INDEXED BAM FILE]

htseq-count -m union -f sam -r name -t exon -i gene_id -s no [INDEXED SAMLE FILE] [GTF FILE] 
```

Let's explain what each step does. 

### `samtools`

Though not explicitly a part of the `HTSeq` universe, [Samtools](http://samtools.sourceforge.net) is a ubiquitous utility used in sequence analysis pipelines. The `view` function converts BAM files to SAM files (which are human readable). 

  * `-h` adds a head to the SAM file
  * `-o` sets the output file name

### `htseq-count`

This is the function that performs the counts in features. 

  * `-m union`: use the `union` mode, as explained above
  * `-f sam`: format of the input data. Here, a SAM file. 
  * `-r name`: for paired-end data, the alignemtn have to be sorted by either read name or alignment position. Here, we're letting `htseq-count` know that it's been sorted by name.
  * `-t exon`: feature type from the GTF file to be used. 
  * `-i gene_id`:  attribute from the GTF file to be used as the feature ID.
  * `-s no`:  lettting `htseq-count` know that the data is *not* from a strand-specific assay. 

***


