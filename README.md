# CGR
CGR package for phylogeny


### Overview
Chaos Game Representation (CGR) is an iterative mapping technique to construct a two dimensional representation of genomic sequences (Jeffrey, 1990). CGRs have been conventionally used to visualize the large nucleotide sequences. However, apart from visualization, CGRs can be used to compare DNA sequences, construct cladograms and address various biological problems like HIV subtyping (Pandit and Sinha, 2010). CGAT is an integrated web server for multiple whole genome sequence comparison using Chaos Game Representation (CGR) based approach. The most important and significant idea behind this webserver is its ability to handle large DNA sequences (e.g., NJ tree of whole genome sequences of 11 Viruses each ~150 Kbp long was made in 2 min 30 sec), effectively classify very similar sequences (e.g., sub-subtypes of HIV-1 sequences) in less time and deliver better resolved classification trees when compared to standard Maximum Likelihood methods. It efficiently classifies sequences based on both inter-species and intra-species variation in a computationally less intense manner. It analyses whole genome variations using an alignment free and scale invariant method resulting in trees that can be used to interpret similarity between multiple whole genome sequences.

###
![image](https://user-images.githubusercontent.com/45668229/186615844-71da47d7-d15c-4da3-8495-86720fccf993.png)
![image](https://user-images.githubusercontent.com/45668229/186615878-f6bc04ee-9263-4d56-898e-a318d11c9427.png)

#### Input, Output and Processing
Input required is a set of two or more genome sequences in FASTA format, which can either be pasted on the webpage or uploaded as text file. The size limit for each input sequence is 10MB. The other input required by user is “word length” for which the frequencies of all the words in the sequences are calculated. User can also specify the Out-group for construction of Neighbor Joining Tree using Phylip (Felsenstein J, 2005). Sequences are converted to coordinates for plotting as CGR. The CGR for each sequence can be visualized on the browser and the image can be downloaded in PNG format. These coordinates can also be downloaded and CGRs can be visualized in any graph plotting softwares like GNUPLOT. These CGRs are then used to calculate frequencies of words at user specified word length. The frequencies of all the words in each sequence can be downloaded as text file for additional processing. Based on these word frequencies the pair-wise Eucledian distance is calculated between all pairs of sequences in input data. This distance matrix can again be either visualized on the browser or downloaded as text file. This distance matrix is automatically used as input for Phylip and the NJ tree file based on this distance matrix can be downloaded in Newick format. The web server also displays the tree on the browser using the software Notung (version 2.6) (Durand et. al, 2006). The web-server assigns unique job-id to each submission and the job id is displayed along with the link to the result. The results for each input can be accessed at a later time (up to 48 hrs) by using the job id.

##### Input
The DNA sequences to be analyzed can be either pasted in the text box on "Submit" page or uploaded in the form text file. All the sequences must be in FASTA format. The allowed DNA characters in file are A, T, G, C, R, Y and N.Whole genome sequences of HIV-1 subtypes A, B, C, D, F, G, SIV and HTLV (Dataset used in Pandit et al Mol. Phylo. Evol., 2012) have been provided as an example. Click on the "Example data" link on Submit page to upload this data for analysis.

##### Selection of "Word Length"
The clustering of the sequences is based on the distances calculated from the frequencies of DNA words. The word length to be used for the calculation can be specified by the user at the submission page. This default word length used is 6. After selecting the word length click on "Submit"

##### Selection of Out group
An out group needs to be specified for construction of Neighbor Joining Tree by Phylip. Select the out group from the dropdown menu and confirm the submission. The calculation is started immediately and each submission is provided a unique Job ID.

##### Visualization of CGR plot
CGRs for each sequence can be visualized by selecting the sequence from dropdown menu.

### Download Results
All the results can be downloaded from the same page. The file that can be downloaded are

1) Distance Matrix file- It is (.txt) text file that contains pairwise distance matrix generated using input sequences. We used Euclidean distance method to calculate pairwise distances between multiple whole genome sequences. It contains output in standard PHYLIP distance matrix format. Following is the screenshot of the file for example data.
 ![image](https://user-images.githubusercontent.com/45668229/186616689-8c4f8efa-06fa-4d4f-bffa-1b3fbe71cdf4.png)
 
 2) Word Frequency file- It is a (.txt) text file that contains frequencies of all different k-letter words corresponding to CGR map.

For example – At k=3 in example data set

![image](https://user-images.githubusercontent.com/45668229/186616875-97dcc3aa-0d9d-4f1b-a0f9-4db0f96f8390.png)


![image](https://user-images.githubusercontent.com/45668229/186616980-55dcef85-6164-496f-86c1-add97badadcf.png)

3) Outtree and Outfile – CGAT also lets users download outfile and outtree files generated by Phylip. These files contains information about trees generated in standard NEWICK format. These files are compatible with standard bioinformatics tools like TreeView , MEGA etc. to create, view , edit and customize trees.

4) NJ Tree-The tree generated by the Notung program using the Phylip Outtree input can be downloaded in PNG format.
