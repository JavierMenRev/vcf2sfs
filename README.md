# vcf2sfs
This R script contains a series of functions for generating site frequency spectra (SFS) from a VCF file. It also contains functions for manipulating and plotting SFS.

# Updates
This is version 2.0. The original script used in the publication (see citation below) is, hereafter, tagged as version 1.0 and can be found in releases.

Major improvement of this version is the introduction of two types of simple objects. One is the "gt" object which stores the genotype information from the VCF file and the population affinities of the individuals. The other is the "sfs" object which is an array representation of an SFS in R environment. All the functions are designed to generate and manipulate (e.g., subset, filter, plot, input and output) these objects. This contrasts with the previous version which directly interacted with files, hence increasing the flexibility and the efficiency.

# How to use
Descriptions for each function can be found in the script file. Here I present a quick view of how to use it.

# Input files
Two input files are required. One is the VCF file, and the other is a so-called popmap file. The popmap file is a tab-delimited two-column text file. I inherited this file type from Stacks. Its first column specifies the IDs of the individuals included in the VCF file. The individuals should be arranged in the same order as in the VCF file, and the IDs do not need to be the same as those in the header line of the VCF file. The second column specifies the populaton ID of each individual and can be either integers or characters.

I provide an example VCF file and an example popmap file. The dataset includes 2265 SNPs from 197 individuals belonging to 11 populations.

# Read in the VCF file and the popmap file and create a gt object
> mygt<-vcf2gt("example.vcf", "example_popmap.txt")

NOTE: This function requires that the FORMAT column of the VCF file start with GT tag. All the SNPs should be biallelic and use "0", "1" and "." to represent reference allele, alternative allele and missing. Some VCF files generated using GATK do not start with GT tag in the FORMAT column, hence unable to make use of this function. However, a gt object is a simple list of two elements. You can view the structure of this object by running "str(mygt)". Therefore, even with an incompatible VCF file, if you can manage to parse the file to get the genotype information, you can still manage to create a gt object by yourself.

# Functions for manipulating the gt object
choose.pops: Subset the gt object by populations.
samSize: Calculate sample sizes of the populations.
minSamSize: Calculate minimum sample sizes of the populations accounting for the missing values.
viewMissing: View the distribution of the missing values to help decide whether some individuals or SNPs should be filtered out.
filter.gt: Filter the gt object for the missing values.
gt2snp: Transform the gt object to a headered data.frame of dadi SNP data format.

# Generate a SFS (a sfs object)
> mysfs1<-gt2sfs.raw(mygt, “Kap1”)      # 1-dimensional
> mysfs2<-gt2sfs.raw(mygt, c(“Kap1”, “Nor”))      # 2-dimensional

This function works well when the number of missing genotypes is low in the gt object. When the number is high, you can choose one of the follwing four options.
1. Visualize the distribution of the missing genotypes using "vewMissing" and filter the gt object to decrease the number of missing using "filter.gt".
2. Transform the gt object to dadi SNP data format using "gt2snp", output it to a file using "write.table", and input the file to dadi. Let dadi cope with the missing by downsizing the sample sizes.
3. Project the SFS by downsizing the sample sizes using "project.sfs". This function uses the same algorithm (hypergeometric distribution) as dadi in coping with missing. However, the resulting SFS shows very slight difference from the one by dadi. This function needs to be tested more vigorously.
4. Impute the missing values using binomial distribution. This is a simple method I developed myself and can be implemented using "gt2sfs.impute". You can choose either downsize the sample sizes or not. The SFS generated by this function is more diffused compared to the one generated through hypergeometric distribution. But the parameter estimates upon running dadi do not show difference. However, caution is recommended when using this function, and more vigorous tests are needed.

# Functions for manipulating the sfs object
fold.sfs: Fold the SFS.
sample.sfs: Resampling from the SFS for the purpose of bootstrapping.
plot.sfs: Plot the SFS (barplot for 1D, image for 2D)
write.sfs.dadi: Output a SFS in dadi format.
read.sfs.dadi: Read a SFS file in dadi format.
write.1D.fsc: Write a 1D-SFS to a file in fastSimCoal format.
write.2D.fsc: Write a 2D-SFS to a file in fastSimCoal format.
read.1D.fsc: Read a 1D-SFS from a fastSimCoal format file.
read.2D.fsc: Read a 2D-SFS from a fastSimCoal format file.

# Citation
Liu S, Ferchaud AL, Grønkjær P, Nygaard R, Hansen MM (2018) Genomic parallelism and lack thereof in contrasting systems of three-spined sticklebacks. Molecular Ecology, 27: 4725-4743.
