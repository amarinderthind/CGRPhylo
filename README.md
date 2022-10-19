# CGRPhylo Pipeline: Chaos Game Representation for phylogeny

A CGRPhylo pipeline combines the R core module with various packages to compare multiple whole genome sequences using Chaos Game Representation (CGR). CRG core function creates the frequencies object for each sequence which can be used to calculate distances among sequences. Later, CGR-based distance matrices can be converted to a phylogeny tree using neighbour-joining (NJ) or other methods. A major advantage of the CGRphylo R pipeline is its ability to handle large DNA sequences (per a user machine) and its effectiveness at classifying very similar sequences.

### How to start
Line-to-line Rscript is available in cgrPhlyo.r (and cgrPhylo.rmd) script. You can find out what to aspect in the o/p by following cgrPhlyo.pdf (or cgrPhylo.html).

### Background
Chaos Game Representation (CGR) is an iterative mapping technique to construct a two dimensional representation of genomic sequences (Jeffrey, 1990). CGRs have been conventionally used to visualize the large nucleotide sequences. However, apart from visualization, CGRs can be used to compare DNA sequences, construct cladograms and address various biological problems. It effectively classify very similar sequences (e.g., sub-subtypes of HIV-1 sequences) and deliver better resolved classification trees when compared to standard Maximum Likelihood methods. 

It efficiently classifies sequences based on both inter-species and intra-species variation in a computationally less intense manner. It analyses whole genome variations using an alignment free and scale invariant method resulting in trees that can be used to interpret similarity between multiple whole genome sequences, even when they are closely related.

###
![figure1](https://user-images.githubusercontent.com/45668229/195962013-fef235d1-6987-4b98-bab9-7d6083f01e5e.png)

 Word Frequency is a frequencies of all different k-letter words corresponding to CGR map. Following figure shows various K-letter words (above) and their calculated frequencies (below) at k=3 

![image](https://user-images.githubusercontent.com/45668229/186616875-97dcc3aa-0d9d-4f1b-a0f9-4db0f96f8390.png)

![image](https://user-images.githubusercontent.com/45668229/186616980-55dcef85-6164-496f-86c1-add97badadcf.png)

#### Steps of the pipeline

Input required is a set of two or more genome sequences in FASTA format. The other input required by user is “word length (K value)” for which the frequencies of all the words in the sequences are calculated. User can also specify the Out-group for construction of Neighbor Joining Tree. 

These Sequences are then used to calculate frequencies of words at user specified word length. , each sequence gets it's own frequency matrices. There is also an option to visualize the CGR plots. Based on these word frequencies the pair-wise distance (Euclidean-default,or  Square euclidean or Manhattan) is calculated between all pairs of sequences in input data. Package integrate other packages where distance matrices can be converted to NJ tree or other and visualized. We provide the option to convert distance matrix into MEGA and phylip distance formats, which is widely used program for visualization. 

##### Input file requirements

The DNA sequences to be analyzed can be uploaded in the form fasta file. All the sequences must be in FASTA format. Check the example file for details.  

```
setwd(".")
source('cgat_function.r')
source('distances_n_other.r')

file <- "recom_3.fasta"

library("seqinr")
fastafile <- seqinr::read.fasta(file = file, seqtype = "DNA", as.string = TRUE, set.attributes = FALSE)
```

##### Filtering and trimming, if required 
```
library(stringr)

N_filter <- 50  ## filter sequence with n bases > this value
fasta_filtered <- fastafile_new(fastafile, N_filter) ## create filtered sequence file

#write fasta file from filtered sequences
seqinr::write.fasta(sequences=fasta_filtered,names =names(fasta_filtered),file.out=paste("recombinant_XBB.1_Filter",N_filter,".fasta",sep = ''))
```

##### Sequence length and GC content /Meta info
```
meta <- create_meta(fastafile, N_filter) ## create seq features information
print(paste("std dev for seq length is",sd(meta$length),sep=" "))
print(paste("Median of the seq length is",median(meta$length),sep=" "))
print("Range of the seq length")
range(meta$length)

#boxplot(meta$length, ylab="Sequence length") ## overall

dotchart(meta$length, labels = meta$name, xlab = "Sequence length", pch = 21, bg = "green", pt.cex = 1, cex = 0.7)

## box plot for each strains (In this example first part of the name is strain name)

meta$strains <- as.character(lapply(meta$name, function(x) strsplit(x, '_')[[1]][1])) ## split strains names

library(ggplot2)
ggplot(meta, aes(x = strains, y =length, color = strains )) + geom_boxplot()+ ylab("Sequence length (group level)")+coord_flip() 

#boxplot(log2(meta$length+1))
len_trim <- min(meta$length)

## Save meta
#write.csv(meta,paste("length_and_names",N_filter,".csv"))
```
![image](https://user-images.githubusercontent.com/45668229/196326195-f5a3172d-78f1-4bc8-a225-51d80a993834.png)


##### Selection of "Word Length"
The clustering of the sequences is based on the distances calculated from the frequencies of DNA words. The word length to be used for the calculation can be specified. This default word length used is 6.  

##### Visualization of CGR plot
CGRs for each sequence can be visualized by selecting the sequence.

![CGR_2plots](https://user-images.githubusercontent.com/45668229/196325788-e054df7d-2689-4e77-89c7-53c9f6797a6c.png)

###  Saving Results in different output formats

The results can be saved into mega or phylip format for tree visulization with third party tools.

Distance Matrix (as shown below) contains pairwise distance matrix generated using input sequences. By default CGRPhylo use Euclidean distance method to calculate pairwise distances between multiple whole genome sequences.


![tt](https://user-images.githubusercontent.com/45668229/195969269-dd01ab1c-d94b-4e52-9abc-bb8c14474524.png)

Outtree,  users can save outtree files created by NJ method of Bioconductor package 'ape'. These files contains information about trees generated in standard NEWICK format or other. These files are compatible with standard bioinformatics tools like TreeView , MEGA etc. to create, view , edit and customize trees. 

 ![figure5 copy](https://user-images.githubusercontent.com/45668229/195969475-b84d614e-e43d-4e67-ba25-416a5a102f44.png)

