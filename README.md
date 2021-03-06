#TKrelated
##**Estimating relatedness from low-coverage genomes using "forced homozygous" SNP data**

Copyright (c) Daniel Fernandes - dani.mag.fernandes@gmail.com

    This program is free software: you can redistribute it and/or modify
    it under the terms of the GNU Affero General Public License as
    published by the Free Software Foundation, either version 3 of the
    License, or (at your option) any later version.
    
    This software is supplied without any warranty or guaranteed support.
    
    For more details on this license, see http://www.gnu.org/licenses/
***

###Citation

Fernandes D, Sirak K, Novak M, Finarelli J, Byrne J, Connolly E, Carlsson J, Ferretti E, Pinhasi R, Carlsson J. The Identification of a 1916 Irish Rebel: New Approach for Estimating Relatedness From Low Coverage Homozygous Genomes. Sci. Rep. 7, 41529; doi: 10.1038/srep41529 (2017)
***

###Description

This R script is composed by different functions that can be used to:

1. estimate relatedness from *in silico* generated, and plot histograms for,
  * a) unrelated individuals (Queller and Goodnight's Rxy coefficient of relationship = 0%)
  * b) first order (Queller and Goodnight's Rxy coefficient of relationship = 50%)
  * c) 3/4 siblings (Queller and Goodnight's Rxy coefficient of relationship = 37.5%)
  * d) second order (Queller and Goodnight's Rxy coefficient of relationship = 25%)
  * e) third order (Queller and Goodnight's Rxy coefficient of relationship = 12.5%)

The main advantage of this script is the requirement of low amounts of genetic data for accurate estimations. This is possible due to the application of a "forced homozygous" approach, and has been shown to work with as few as 1400 common SNPs. It can be used for both forensic or archaeological applications, where reduced DNA amounts are frequently an issue. More information on the paper where this method was first applied: [http://www.nature.com/articles/srep41529 ](http://www.nature.com/articles/srep41529)

DISCLAIMER & WORKING NOTES: This software is supplied without any warranty or guaranteed support. The authors can not be responsible for its use or misuse.
Please be aware of the inherent existence of false-positive, and false-negative, results. This behaviour can be expected to happen with higher frequencies on Third Order estimations, due to reduced coefficient separations between this order and others, namely the Unrelated one.
The current version can not estimate probabilities when the used number of common SNPs is too high. This is due to an increased influence of population structure and the inability of the estimator to correct for it using haploid data. 
***

###Third-Party Software Requirements

[**SPAGeDI**](http://ebe.ulb.ac.be/ebe/SPAGeDi.html "SPAGeDI")

    "SPAGeDi (Spatial Pattern Analysis of Genetic Diversity) is a computer package primarily 
    designed to characterise the spatial genetic structure of mapped individuals and/or
    mapped populations using genotype data of any ploidy level."
    http://ebe.ulb.ac.be/ebe/SPAGeDi.html

[**PLINK**](http://pngu.mgh.harvard.edu/~purcell/plink/ "PLINK")

    PLINK is a free, open-source whole genome association analysis toolset, designed to perform
    a range of basic, large-scale analyses in a computationally efficient manner.
    http://pngu.mgh.harvard.edu/~purcell/plink/
***

###Usage and Data Requirements

TKrelated requires individual data and allele frequencies both in non-binary PLINK format (.ped/.map and .frq). Each PED file can only contain one individual.
More detailed input requirements and formats are described within the script.

####Example (v1.6.2) - Estimating relatedness on two given samples

We suggest using this script by sourcing it into your current R session environment. All functions will then be available to the user:

    source("/path/to/script/TKrelated_vx.x.x.R")

For comparing two individuals one should start by having their PLINK files and an appropriate file with the allele frequencies. For this example, we will say that those frequencies were extracted from a PLINK file from the 1000 Genomes Project.
As a note, too large samples will either crash SPAGeDI or take too much time to complete. It is suggested to use the PLINK command *thin* to reduce the sample to desired, smaller, sizes (recommended between 50 and 100K SNPs).

    ind1.ped
    ind1.map
    
    ind2.ped
    ind2.map
    
    1000genomes_European.bed
    1000genomes_European.bim
    1000genomes_European.fam
    1000genomes_allele_frequencies_European.frq

Use **relatedHomozSNP** function to convert the PLINK data into SPAGeDI format.

    relatedHomozSNP(sample1="ind1",sample2="ind2",freqs="1000genomes_allele_frequencies_European.frq",run.spagedi=TRUE)

This should produce two files to be used with SPAGeDI.

    ind1_ind2_spagedi_input.txt
    ind1_ind2_spagedi_frequencies.txt

If argument *run.spagedi=TRUE*, it will also create an extra file in order to run the program automatically.

    SP_CMDS_ind1_ind2_spagedi_in.txt

The relatedness estimation generated by SPAGeDI will be found on the following section of *ind1_ind2_spagedi_out.txt*:
    
    Pairwise RELATIONSHIP coefficients (Queller & Goodnight, 1989)
    
    ALL LOCI	ind1	ind2
    ind1		0.1233
    ind2	0.1233

Due to the "forced homozygous" approach, the coefficients are expected to be the half-values of the originals, ie: a Queller and Goodnight coefficient of 50% under our approach will be presented as 25%, or 0.25. In the above example, 0.1233 would correspond to a Queller and Goodnight coefficient of 25%.

####Estimating relatedness on virtual individuals

At this point, one can use the frequencies of the common SNPs between the two samples to generate virtual individuals and simulate different degrees of relatedness. This way one can confirm the ranges for which the found coefficient of relatedness can vary.

The function **relatedHomozSNP** outputs two extra files: one with the ID of the common SNPs, and another with their frequencies in .frq format. This last one should be used for the next steps.

    commons_freqs_ind1_ind2.frq

For each of the five coefficients, 0, 12.5, 25, 37.5 and 50%, there are two functions. The first one generates the individuals and estimates their relatedness, and the second compiles the data from the previous and plots the corresponding histograms.

    unrelatedfunc = function(file,samples.tag,numUnrelatedPairs,identifier,run.spagedi=TRUE,reduce.SNPs=FALSE)
    unrelatedplot = function(samples.tag)
    
    fullsibsfunc = function(file,samples.tag,numFullSiblingsPairs,identifier,run.spagedi=TRUE,reduce.SNPs=FALSE)
    fullsibsplot = function(samples.tag) 
    
    halfsibsfunc = function(file,samples.tag,numHalfSiblingsPairs,identifier,run.spagedi=TRUE,reduce.SNPs=FALSE)
    halfsibsplot = function(samples.tag)
    
    threequarterfunc = function(file,samples.tag,num34SiblingsPairs,identifier,run.spagedi=TRUE,reduce.SNPs=FALSE)
    threequarterplot = function(samples.tag)
    
    firstcousfunc = function(file,samples.tag,numfirstcousPairs,identifier,run.spagedi=TRUE,reduce.SNPs=FALSE)
    firstcousplot = function(samples.tag)

A detailed description of each argument exists within the function.

Below is a suggested set of commands to run the simulations, taking into consideration that SPAGeDI can become unstable due to memory issues.
 
    unrelatedfunc("commons_freqs_ind1_ind2.frq","ind1_ind2",200,1,run.spagedi = TRUE)
    unrelatedfunc("commons_freqs_ind1_ind2.frq","ind1_ind2",200,2,run.spagedi = TRUE)
    unrelatedfunc("commons_freqs_ind1_ind2.frq","ind1_ind2",200,3,run.spagedi = TRUE)
    unrelatedfunc("commons_freqs_ind1_ind2.frq","ind1_ind2",200,4,run.spagedi = TRUE)
    unrelatedfunc("commons_freqs_ind1_ind2.frq","ind1_ind2",200,5,run.spagedi = TRUE)
    unrelatedplot("ind1_ind2")
    fullsibsfunc("commons_freqs_ind1_ind2.frq","ind1_ind2",200,1,run.spagedi = TRUE)
    fullsibsfunc("commons_freqs_ind1_ind2.frq","ind1_ind2",200,2,run.spagedi = TRUE)
    fullsibsfunc("commons_freqs_ind1_ind2.frq","ind1_ind2",200,3,run.spagedi = TRUE)
    fullsibsfunc("commons_freqs_ind1_ind2.frq","ind1_ind2",200,4,run.spagedi = TRUE)
    fullsibsfunc("commons_freqs_ind1_ind2.frq","ind1_ind2",200,5,run.spagedi = TRUE)
    fullsibsplot("ind1_ind2")
    halfsibsfunc("commons_freqs_ind1_ind2.frq","ind1_ind2",200,1,run.spagedi = TRUE)
    halfsibsfunc("commons_freqs_ind1_ind2.frq","ind1_ind2",200,2,run.spagedi = TRUE)
    halfsibsfunc("commons_freqs_ind1_ind2.frq","ind1_ind2",200,3,run.spagedi = TRUE)
    halfsibsfunc("commons_freqs_ind1_ind2.frq","ind1_ind2",200,4,run.spagedi = TRUE)
    halfsibsfunc("commons_freqs_ind1_ind2.frq","ind1_ind2",200,5,run.spagedi = TRUE)
    halfsibsplot("ind1_ind2")
    threequartersibsfunc("commons_freqs_ind1_ind2.frq","ind1_ind2",200,1,run.spagedi = TRUE)
    threequartersibsfunc("commons_freqs_ind1_ind2.frq","ind1_ind2",200,2,run.spagedi = TRUE)
    threequartersibsfunc("commons_freqs_ind1_ind2.frq","ind1_ind2",200,3,run.spagedi = TRUE)
    threequartersibsfunc("commons_freqs_ind1_ind2.frq","ind1_ind2",200,4,run.spagedi = TRUE)
    threequartersibsfunc("commons_freqs_ind1_ind2.frq","ind1_ind2",200,5,run.spagedi = TRUE)
    threequartersibsplot("ind1_ind2")
    firstcousfunc("commons_freqs_ind1_ind2.frq","ind1_ind2",200,1,run.spagedi = TRUE)
    firstcousfunc("commons_freqs_ind1_ind2.frq","ind1_ind2",200,2,run.spagedi = TRUE)
    firstcousfunc("commons_freqs_ind1_ind2.frq","ind1_ind2",200,3,run.spagedi = TRUE)
    firstcousfunc("commons_freqs_ind1_ind2.frq","ind1_ind2",200,4,run.spagedi = TRUE)
    firstcousfunc("commons_freqs_ind1_ind2.frq","ind1_ind2",200,5,run.spagedi = TRUE)
    firstcousplot("ind1_ind2")
    readAll("ind1_ind2")

This will generate estimations for 1000 unrelated, 1000 first order, 1000 3/4 siblings, 1000 second order, and 1000 third order virtual individuals, in batches of 200. This way the script and SPAGeDI run faster and should not crash. It, however, is dependant on the amount of RAM available.

The **readAll** function will recognize the data from the five different orders and use it to calculate the posterior probabilities of each of the orders for the given relatedness coefficient. Default options output a plot and a tab-spaced text file with the posterior probabilities, number of SNPs used, and homozygous relatedness coefficient. Note that this function requires the previous five plot functions to have run.

***

