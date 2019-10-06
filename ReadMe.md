# 25334 Genomic methods in breeding and management of aquatic living resources. 
## Lecture 6: Introduction to Population Genetics & tools for conservation and management
### Teachers: Romina Henriques & Belen Jimenez-Mena

Wednesday 09/10/2019
 - 8am - 10pm: Introduction to population genetics (theory + practical)
 - 10am - 12: Tools for conservation and management (theory + practical)

## Material

Slides can be found [here](https://github.com/belenjm/).

The data for practicals has been already downloaded and it is provided in '/home/EXERCICES/lecture6'.
These instructions, including all relevant files and scripts, can be found at '/home/EXERCISES/lecture6'.
In short, you don't have to worry about anything for the practicals.

### Data

As an illustration, we will use a VCF file from the flat oyster, published in a recent article: Detailed insights into pan-European population structure and inbreeding in wild and hatchery Pacific oyster (Crassostrea gigas) populations revealed by genome-wide SNP data by [Vendrami et al. (2018)](https://onlinelibrary.wiley.com/doi/full/10.1111/eva.12736).
We extracted the PED files from the [article's repository](https://datadryad.org/stash/dataset/doi:10.5061/dryad.6d778b6.), and transformed it into a VCF file.
The oyster data represents a SNP-chip from variation all over the genome.

## Preparation

We'll be making some exercises on this dataset to study how we can give some insights for conservation/management in this population.
However, before doing anything else, please create a folder where you will put all the results and some temporary data.
```
mkdir Results
mkdir Data
```
You can make a copy of the dataset by using the following command in the terminal, from your directory.
```
cp ... Data\.
```
That's it.

## Case study

*MOTIVATION*

The flat oyster 



## Analysis

* R: the programming language for today

We are going to use the programming language R for the analysis and visualization. R is a programming language suited for statistical computing that has been developed by the scientific community and it is widely used for data analysis. 

We can call R from the terminal. We do this by simply using
```
R
```

We need a few packages in order to do the analysis. In R, appart from coding our own functions and programs, we can download packages and use the functions that have been developed and saved there. This is one of the advantages of using R, its active user community. To install an R package, we have just to type (remember to call R before, aka. the previous command, otherwise, the following commands won't work in the BASH terminal):
```
install.packages("<the package's name>")
```
R will then download the package from Internet. To load the package into your current session, you run:
user community. To install an R package, we have just to type:
```
library("<the package's name>")
```

* Packages needed for today's exercises

There are thousands of helpful R packages for you to use. For the analysis at this lecture, we will be using the following packages:
```
install.packages("vcfR")
require(vcfR)
install.packages("adegenet")
require(adegenet)
install.packages("dartR")
require(dartR)
install.packages("assertthat")
require(tidyverse)
```

* Loading the dataset

There are many ways to load a dataset in R. Because the file type is a VCF file, we'll use the vcfR package we just downloaded and loaded. To do this, we will introduce the path where you copied the dataset to your folder.
```
vcf <- read.vcfR("data/test_vcf.vcf")
```
We will convert it to a special format to analyse large genomic datasets, a "genlight" object.
```
data <- vcfR2genlight(vcf)
```
For the following analysis, we need to prepare the dataset a little bit. This means, for example, that we need to extract the population name for each individual of the dataset and make it a factor. This is not really important right now (please, ask me if you want to know exactly what the following commands are doing). Otherwise, just type:
```
Samples_names <- data@ind.names
pop <- t(data.frame(str_split(Samples_names, "_", n=2)))
Pop_ID <- paste(pop[,1])
data@pop <- as.factor(Pop_ID)
```

* First look
First, let's look how our data looks like. This is the first step when analysing a genomic dataset. Genomic datasets are quite large and therefore it is a bit difficult ("humanly impossible") to check each data one by one, by hand. That's why we write scripts and use functions, so we can automate the checks and the analysis.
```
data
```
This command will show us several lines of information, to summarize all the dataset. 
```
> data
  /// GENLIGHT OBJECT /////////

 // 232 genotypes,  21,499 binary SNPs, size: 3.5 Mb
 62767 (1.26 %) missing data

 // Basic content
   @gen: list of 232 SNPbin

 // Optional content
   @ind.names:  232 individual labels
   @loc.names:  21499 locus labels
   @chromosome: factor storing chromosomes of the SNPs
   @position: integer storing positions of the SNPs
   @pop: population of each individual (group size range: 6-12)
   @other: a list containing: elements without names 
```
What does this mean? The first row is telling us that the data file is a genlight object, and inside we can find 232 genotypes (so 232 individuals with genotype data). Each of the 232 individuals have genotypes on 21499 SNPs, from which there is an average of 1.26% of missing data. The optional content (introduced by "@") correspond to another additional matrix with further information about the SNPs or the individuals.

* Filtering our dataset

As a second step in a genomic analysis, we would need to filter the data. There are many ways of filtering a dataset, and it all depends what we are interested in. One thing to remember is that whatever we choose for filtering steps, we would need to report all the steps, so other scientists can replicate our analysis and understand why the results are the way they are. Some of the parameters that genomicists filter their data on is in the % of missing data, or minor allele frequencies. For the sake of simplicity, we won't be doing any filtering process for our exercises today, for two reasons: (1) the dataset is of very good quality and it has already been filtered for some parameters (see the original article for details), and (2) for the sake of simplicity for today's main key messages (and one can spend lots of time in filtering - and one should! Unfortunately it is not our priority for today).

* Principal Component Analysis (PCA)

For the principal component analysis (PCA), we will use the package adegenet which handles genlight objects very quickly. The following lines tells adegenet the type of analysis we want to use, and the number of axes that we want to take into account for the variation. 
```
pca_genlight <- adegenet::glPca(data,nf = 100) # 5 axes selected
```
It will take a few minutes to run (~21000 SNPs are a lot of SNPs/columns!). After the function has run, we will be asked how many axes we want. Let's say 5 for the moment (you can play with this parameter later when you have time).

We will then visualize the plot with this:
```
myCol <- adegenet::colorplot(pca_genlight$scores,pca_genlight$scores, transp=TRUE, cex=4)
abline(h=0,v=0, col="grey")
add.scatter.eig(pca_genlight$eig[1:40],2,1,2, posi="topright", inset=.05, ratio=.3)
scatter(pca_genlight, xax = 1, yax = 2, posi = "bottomleft", bg = "white",
        ratio = 0.3, label = rownames(pca_genlight$scores))
```
Which populations do you see differentiated? Can you identify some populations that are less differentiated than others within themselves, and some others that are more differentiated? In other words, how many clusters do you see in this oyster population?

Which loci seem to be driving the differenciation in the PCA? For looking into this, we can make a loading analysis.
```
selection_criteria_PC1_1 <- loadingplot(pca_genlight, axis=1)
selection_criteria_PC1_2_95 <- quantile(selection_criteria_PC1_1$var.values, 0.99) #I also tested 0.95 and we can see the clear pattern of plots in PC1

loadingplot(pca_genlight, at=NULL, threshold=selection_criteria_PC1_2_95, axis=1)
alleles_1_contribPC1_95 <- loadingplot(pca_genlight, threshold=selection_criteria_PC1_2_95, lab.jitter=1, axis=1)
head(alleles_1_contribPC1_95)
invers_snp1_PC1_95 <- alleles_1_contribPC1_95$var.idx
snpsPC1_snp1_names_95 <- data$loc.names[c(invers_snp1_PC1_95)]
```

Is there any special loci that seem to be driving the differentiation?

* Degree of differentiation (Fst)

We will measure Fst with the package dartR. It will take a few minutes to run. 
```
fst_all <- dartR::gl.fst.pop(data,nboots = 1000)
fst_all$Fsts
fst_all$Pvalues
```
Remember that the Fst is a measure of how different a population is from another one. Which populations have the highest Fst between each other? And which ones have the lowest?
