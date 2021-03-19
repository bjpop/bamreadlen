# Overview 

This program computes the sequencing read length of an input BAM file.

The input is a single BAM file and the output is a single integer that indicates the read length.

It does this by collecting the first `max_reads` (default = 10,000) reads from the start of the BAM file and computing the median read length. This algorithm only makes sense in the contect of sequencing technologies where there is a common expected length for (most of) the reads in the file, such as is typically found in the outputs of short read high throughput DNA sequencing machines.

In this instance the read length is taken to be the number of bases reported in the sequence string in a BAM file record.  

`bamreadlen` uses this algorithm to avoid problems when not all reads in the BAM file are exactly the same length. For example a small number of the first reads in the file might be shorter than the dominant read length. `bamreadlen` tries to calculate a value that is likely to be equal to the dominant read length by sampling a large number of reads from the start of the file. This assumption could break down in unusual circumstances; for example if the reads were sorted by read length. However, this scenario is unlikely to happen in practice.

In the examples below, `$` indicates the command line prompt.

# Licence

This program is released as open source software under the terms of [MIT License](https://raw.githubusercontent.com/bjpop/bamreadlen/main/LICENSE).

# Installing

This is a BASH shell script. Copy it to your local computer to install. It can be run from any directory.

## Dependencies

This program depends on:
 * samtools
 * awk
 * BASH

It will fail with an error message if these dependenies are not available in the PATH on your computer.

You will need to install these dependencies yourself if they aren't already available on your local system.

# Usage 

## Help message

`bamreadlen` can display usage information on the command line via the `-h`:

```
$ ./bamreadlen -h 
bamreadlen: calculate the sequencing read length from a BAM file 
Usage:
    bamreadlen [-h] [-n max_reads] -i input_bam_file_name 

   -i <input_bam_file_name> is required, this specifies the input BAM file name

   -n <max_reads>           is optional, if specified this is the maximum
                            number of reads to consider from the file

   -h                       displays an help message
                     
Example:
    bamreadlen -i example.bam 
Dependencies:
   The following tools must be installed on your computer to use this script,
   and be accessible via the PATH environment variable:
   - bash 
   - awk 
   - samtools 
```

## General usage 

The simplest use case is to specify a BAM file on the command line with the `-i` parameter:
```
./bamreadlen -i /path/to/bam/file
```
where `/path/to/bam/file` is the filepath to your input BAM file.

You can optionally change the number of reads that `bamreadlen` will use to calculate the result with the `-n` parameter (the default is 10,000):

```
./bamreadlen -n 10 -i /path/to/bam/file 
```

The larger the value of `-n` the more likely `bamreadlen` will compute the correct result. The tradeoff is that larger values of `-n` also increase the execution time and memory needed (although `bamreadlen` should be quite fast even for millions of reads considered).

If you have many samples to process then the following BASH loop might be useful, assuming ``bamreadlen`` is in your PATH (otherwise specify the path to the script):

```
for file in *.bam; do
    sample=$(basename $file .bam)
    readlen=$(bamreadlen -i $file)
    echo "$sample,$readlen"
done
```

## Exit status values

`bamreadlen` returns the following exit status values:

* 0: The program completed successfully.
* 1: File I/O error. This can occur if the input BAM file cannot be opened for reading. This can occur because the file does not exist at the specified path, or `bamreadlen` does not have permission to read from the file. 
* 2: A command line error occurred. This can happen if the user specifies an incorrect command line argument. In this circumstance bamreadlen will also print a usage message to the standard error device (stderr).

# Bug reporting and feature requests

Please submit bug reports and feature requests to the issue tracker on GitHub:

[bamreadlen issue tracker](https://github.com/bjpop/bamreadlen/issues)
