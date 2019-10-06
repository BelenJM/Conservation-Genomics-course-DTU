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

*R: the programming language for today*

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

*Packages needed for today's exercises*
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

*Loading the dataset*
There are many ways to load a dataset in R. Because the file type is a VCF file, we'll use the vcfR package we just downloaded and loaded. To do this, we will introduce the path where you copied the dataset to your folder.
```
vcf <- read.vcfR("data/test_vcf.vcf")
```
We will convert it to a special format to analyse large genomic datasets, a "genlight" object.
```
data <- vcfR2genlight(vcf)
```
For following analysis, we need to prepare the dataset a little bit. This means, for example, that we need to extract the population name for each individual of the dataset and make it a factor. This is not really important right now (please, ask me if you want to know exactly what the following commands are doing). Otherwise, just type:
```
Samples_names <- data@ind.names
pop <- t(data.frame(str_split(Samples_names, "_", n=2)))
Pop_ID <- paste(pop[,1])
data@pop <- as.factor(Pop_ID)
```

*First look*
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

