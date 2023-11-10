# CGRphylo Pipeline: Chaos Game Representation for Phylogeny

A CGRphylo pipeline combines the R core module with various packages to compare multiple whole genome sequences using Chaos Game Representation (CGR). CRG core function creates the frequencies object for each sequence which can be used to calculate distances among sequences. Later, CGR-based distance matrices can be converted to a phylogeny tree using neighbour-joining (NJ) or other methods. A major advantage of the CGRphylo R pipeline is its ability to handle large DNA sequences (per a user machine) and its effectiveness at classifying very similar sequences.

## Cite this pipeline as:
Thind Singh Amarinder and Sinha Somdatta*, Using Chaos-Game-Representation for Analysing the SARS-CoV-2 Lineages, Newly Emerging Strains and Recombinants, Current Genomics 2023; 24 (3) . https://dx.doi.org/10.2174/0113892029264990231013112156

## Acknowledgment 
We acknowledge the National Network for Mathematical and Computational Biology (NNMCB), DST, India for the internship programme at IISER Mohali for the initial part of the project.

### How to start
Line-to-line Rscript is available in cgrPhlyo.r (and CGRphylo.rmd) script. You can find out what to aspect in the o/p by following cgrPhlyo.pdf (or CGRphylo.html).

### Background
Chaos Game Representation (CGR) is an iterative mapping technique to construct a two-dimensional representation of genomic sequences (Jeffrey, 1990). CGRs have been conventionally used to visualize large nucleotide sequences. However, apart from visualization, CGRs can be used to compare DNA sequences, construct cladograms and address various biological problems. It effectively classifies very similar sequences (e.g., sub-subtypes of HIV-1 sequences) and delivers better-resolved classification trees when compared to standard Maximum Likelihood methods. 

It efficiently classifies sequences based on both inter-species and intra-species variation in a computationally less intense manner. It analyses whole genome variations using an alignment-free and scale-invariant method resulting in trees that can be used to interpret similarity between multiple whole genome sequences, even when they are closely related.

###
![figure1](https://user-images.githubusercontent.com/45668229/195962013-fef235d1-6987-4b98-bab9-7d6083f01e5e.png)

 Word Frequency is the frequency of all different k-letter words corresponding to the CGR map. The following figure shows various K-letter words (above) and their calculated frequencies (below) at k=3 
 
 <p align="center">
 <img src="https://user-images.githubusercontent.com/45668229/186616875-97dcc3aa-0d9d-4f1b-a0f9-4db0f96f8390.png"  width="45%" height="400">&nbsp; &nbsp; &nbsp; &nbsp;
<img src="https://user-images.githubusercontent.com/45668229/186616980-55dcef85-6164-496f-86c1-add97badadcf.png"  width="45%" height="400">
</p>

Another fascinating property of the CGR plot is its fractal nature. The iterative process of plotting points on the CGR plot creates intricate and self-similar patterns at different scales. This means that each square box in the plot contains a smaller version of the entire plot, exhibiting similarity to the overall pattern. This characteristic of self-replication is typical of fractals, complex geometric structures that reveal repeating patterns at various levels of magnification.

<p align="center">
 
<img src="https://github.com/amarinderthind/CGRphylo/assets/45668229/3de4ad69-6c9b-4958-aaa5-98d12d2dae5d.png"  width="60%" height="400">
</p>



##### Steps of the pipeline

Input required is a set of two or more genome sequences in FASTA format. The other input required by the user is “word length (K value)” for which the frequencies of all the words in the sequences are calculated. Users can also specify the Out-group for the construction of the Neighbor-Joining Tree. 

These Sequences are then used to calculate frequencies of words at user-specified word lengths. , each sequence gets its own frequency matrices. There is also an option to visualize the CGR plots. Based on these word frequencies the pair-wise distance (Euclidean-default, or  Square Euclidean, or Manhattan) is calculated between all pairs of sequences in input data. Package integrate other packages where distance matrices can be converted to NJ tree or other and visualized. We provide the option to convert distance matrix into MEGA and phylip distance formats, which is a widely used program for visualization. 

##### Input file requirements

The DNA sequences to be analyzed can be uploaded in the form fasta file. All the sequences must be in FASTA format. Check the example file (Input_recom_SARS_cov2.fasta) for details.  

```
setwd(".")
source('cgat_function.r')
source('distances_n_other.r')
source('cgrplot.r')

file <- "Input_recom_SARS_cov2.fasta"

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

`create_meta` function extracts various types of information from the sequences and stores them into data frame. 

```
meta <- create_meta(fastafile, N_filter) ## create seq features information
print(paste("std dev for seq length is",sd(meta$length),sep=" "))
print(paste("Median of the seq length is",median(meta$length),sep=" "))
print("Range of the seq length")
range(meta$length)

#boxplot(meta$length, ylab="Sequence length") ## overall

dotchart(meta$length, labels = meta$name, xlab = "Sequence length", pch = 21, bg = "green", pt.cex = 1, cex = 0.7)

dotchart(meta$GC_content, labels = meta$name, xlab = "GC content", pch = 21, bg = "green", pt.cex = 1, cex = 0.7)

```
 <p align="center">
<img src="https://user-images.githubusercontent.com/45668229/196873578-f08d9bd1-2778-44eb-be0a-f6b5c6bd2470.png" width=45% height="400">&nbsp; &nbsp; &nbsp; &nbsp;
<img src="https://user-images.githubusercontent.com/45668229/196873233-fc4838ac-3787-446f-9ed3-3a3ab40e756b.png" width=45% height="400">
 
</p>

## Box plot for each strain 
```
# In this example the first part of the sequence name {i.e. beforere_ } is the strain name.

meta$strains <- as.character(lapply(meta$name, function(x) strsplit(x, '_')[[1]][1])) ## split strains names

library(ggplot2)
ggplot(meta, aes(x = strains, y =length, color = strains )) + geom_boxplot()+ ylab("Sequence length (group level)")+coord_flip() 

#boxplot(log2(meta$length+1))
len_trim <- min(meta$length)

## Save meta
#write.csv(meta,paste("length_and_names",N_filter,".csv"))
```
 
<img src="https://github.com/amarinderthind/CGRphylo/assets/45668229/1cd61221-5475-4740-8296-b18b8246cd9e.png" width="1000" height="400">

##### Visualization of CGR plot
CGRs for each sequence can be visualized by selecting the sequence. `cgrplot` function creates the 'x' and 'y' coordinates for each base pair (to plot on CRG plot).

```
source('cgrplot.r') ## Load CGR plot function

cgr1 <- cgrplot(1) ## enter the number of sequence from  "fasta_filtered"


plot(cgr1[,1],cgr1[,2], main=paste("CGR plot of", names(fasta_filtered)[1],sep=''),
     xlab = "", ylab = "", cex=0.2, pch = 4, frame = TRUE) 

#compare 2 CGR plots
cgr2 <- cgrplot(4)

library('RColorBrewer')
pal <- brewer.pal(8,"Dark2")
par(mfrow=c(1,2))

plot(cgr1[,1],cgr1[,2], main=paste("CGR plot of ", names(fasta_filtered)[1],sep=''),
     xlab = "", ylab = "", cex.main=0.5, cex=0.4, pch = 4, frame = TRUE) 
plot(cgr2[,1],cgr2[,2], main=paste("CGR plot of ", names(fasta_filtered)[2],sep=''),
     xlab = "", ylab = "",cex.main=0.5,cex=0.4,pch = 4, frame = TRUE) 
```

![CGR_2plots](https://user-images.githubusercontent.com/45668229/196325788-e054df7d-2689-4e77-89c7-53c9f6797a6c.png)

##### Create frequency object for sequences for specific "Word Length"
The clustering of the sequences is based on the distances calculated from the frequencies of DNA words. The word length to be used for the calculation can be specified. This default word length used is 6.  `cgat` function does this job.

```
k_mer <- 6  ## define the value of K
Freq_mat_obj <- list()
sequence_new <- names(fasta_filtered) 

for(n in 1:length(fasta_filtered)) {   
  
  ##skip passing whole data to the function ##increase speed  ## "fasta_filtered" name is Locked.
  
  Freq_mat_obj[[n]] <- cgat(k_mer,n, len_trim) # executing one seq at a time    #k-mer,seq_length,trimmed_length
  print(paste("processing sequence : ",n , sequence_new[n], sep=" "))
  
}

names(Freq_mat_obj) <- sequence_new
```
## Calculate distances 
Users can use any of Euclidean (default), Square Euclidean, or Manhattan distance for the distance matrices. `matrixDistance` function takes inputs in the following way for distance calculations.

```
j <- length(sequence_new)

distance <- matrix(0,j,j)  ## defining the empty matrix from distance calculations
row.names(distance) <- sequence_new[1:j]
colnames(distance) <- sequence_new[1:j]

for(n in 1:j) { 
  for(cr in 1:j) {
distance[n,cr] <- matrixDistance(Freq_mat_obj[[n]],Freq_mat_obj[[cr]],distance_type ='Euclidean' )  ##'Euclidean','S_Euclidean' and Manhattan
}}

distance <- round(distance, 5)
distance[1:5,1:3]

#plot(hclust(as.dist(distance)))
```
###  Saving Results in different output formats (Mega, Phylip, Newick, Nexus)

For tree visualization with third-party tools, results can be saved into Mega or Phylip format using `saveMegaDistance` and `savePhylipDistance` respectively.

```
Mega_file_name <- paste('Recombi_N_filtering_',N_filter,'_mega_distance_file_for_k_',k_mer,'.meg')

### save 
saveMegaDistance(Mega_file_name, distance) ## inputs are file name (with path) and distance 

PhylipFile_name <- paste('Phylip_N_filtering_',N_filter,'distances_k_',k_mer,'.txt')

 ## inputs are file name (with path) and distance 
savePhylipDistance(PhylipFile_name, distance, mode= 'original')  ## relaxed or original/other

##typical phylip allows 10 charcter for texa name ##new relaxed formart allows 250
## http://www.phylo.org/index.php/help/relaxed_phylip
```

Distance Matrix (as shown below) contains a pairwise distance matrix generated using input sequences. By default, CGRphylo uses the Euclidean distance method to calculate pairwise distances between multiple whole genome sequences.


![tt](https://user-images.githubusercontent.com/45668229/195969269-dd01ab1c-d94b-4e52-9abc-bb8c14474524.png)

Creating and Saving tree into Newick, Nexus format (pipeline use `ape` and `treeio` Bioconductor package)

Outtree,  users can save outtree files created by the NJ method of Bioconductor package 'ape'. These files contain information about trees generated in standard NEWICK format or other. These files are compatible with standard bioinformatics tools like TreeView, MEGA, etc. to create, view, edit, and customize trees. 

```
tree <-write.tree(my_nj, file = "", append = FALSE, digits = 10, tree.names = FALSE)

ape::write.tree(my_nj, file='Newick_NJ_tree.txt') #for Newick format #write out trees in a text file
ape::write.nexus(my_nj, file='Nexus_NJ_tree.nex') ##for Nexus format


## reading tree from file 
 newick.tree <- ape::read.tree("Newick_NJ_tree.txt")  
 nexus.tree <- ape::read.nexus("Nexus_NJ_tree.nex")
```

The following tree is created in this pipeline and later modified/labelled using mega software. 

 <p align="center">
<img src="https://user-images.githubusercontent.com/45668229/195969475-b84d614e-e43d-4e67-ba25-416a5a102f44.png" width=46% height=400>&nbsp; &nbsp; &nbsp; &nbsp;
<img src="https://user-images.githubusercontent.com/45668229/196874720-fb5c4fbb-a687-436a-b636-eec6a75c0942.png" width=46% height=400>

</p>
