# GWAS-02: Quality control of SNP data

## Table of Contents
- [GWAS-02: Quality control of SNP data](#gwas-02-quality-control-of-snp-data)
    - [Table of Contents](#table-of-contents)
    - [Dataset](#dataset)
    - [Task 1](#task-1)
    - [Task 2](#task-2)
        - [Data input](#data-input)
        - [Data output](#data-output)
    - [Task 3: filtering the dataset with PLINK](#task-3-filtering-the-dataset-with-plink)
    - [Task 4: filtering the Dataset with VCFtools](#task-4-filtering-the-dataset-with-vcftools)
        - [Data input](#data-input-1)
        - [Data output](#data-output-1)
        - [Try QC options](#try-qc-options)
    - [Task 5: Optional Visualization in R](#task-5-optional-visualization-in-r)
        - [Load data to R](#load-data-to-r)
        - [Genotype frequencies](#genotype-frequencies)


We recommend you to work on the server today. For the first part of this practice, you will need to run PLINK and vcftools.
both are software built to handle variant data. [Vcftools](https://vcftools.github.io/) is a tool built to handle ```.vcf``` files.
[PLINK](https://www.cog-genomics.org/plink/1.9/general_usage) is another very common and extensive tool that uses it's own data format.

You can download this sheet as a .Rmd file [here 💾](https://raw.githubusercontent.com/troe27/UU-GWAS02/refs/heads/main/worksheet.Rmd).


## Dataset
Copy the dataset to your working directory from ```/proj/g2020004/nobackup/tilman/gwas02/human.tar.gz``` on Rackham.
Unzip the human.tar.gz
After unzipping the folder, you will have three files: ```human.bed```, ```human.bim```, and ```human.fam```. These file formats are PLINK binary files.

```bash
### YOUR CODE HERE ###
```

## Task 1
Before we start the QC jobs, let’s have a quick look at our data. Try to find answers to the following questions on PLINK’s website:

#### .FAM file:
Have a look at human.fam in the terminal.

- _Which column is the family ID (FID)? What is the prefix?_
- _Which column is the Within-family ID (IID)? What is the prefix?_
- _Does this dataset provide parental information? What does 0 stands for?_
- _How many samples are included in this dataset?_

#### BIM file:
Have a look at human.bim in terminal.

- _What does each column mean?_
- _How does the missing value present in the columns for reference and alternative alleles?_
- _How many markers are included in this dataset?_

## Task 2

### Data input
Several data formats are available for PLINK. The dataset we provide for today’s practice is the PLINK 1 binary format. Have a look at the [manual](https://www.cog-genomics.org/plink/1.9/input#bed) on how to load the dataset.

Here are some tricks to make your work easier: 
- The prefix of the three files should be the same. 
- Make sure all three files are placed in the same directory.

### Data output
One powerful function of PLINK is converting data to formats used in other software. [Have a look at the help manual for the output format option](https://www.cog-genomics.org/plink/1.9/data). 

#### Input the PLINK binary file and generate a block-gzipped VCF file.
 (this will be used in the next section)
- _What’s the function of ```--out``` option?_
- _Have a look at the message in the terminal. How many variants are loaded from the BIM file? How many males and females sample were loaded?_
- _Do you find the output file, ```human.vcf```?_

```bash
### YOUR CODE HERE ###
```


## Task 3: filtering the dataset

Try to filter the dataset by the following steps and write down the number of markers and samples passed in each step. (Note: output name needs to be changed in each run)

**Minor allele frequency (0.01)**

```bash
### YOUR CODE HERE ###
```

**Individual genotyping rate (0.05 missingness)**

```bash
### YOUR CODE HERE ###
```

**SNP genotyping rate (0.05 missingness)**

```bash
### YOUR CODE HERE ###
```

**Hardy-Weinberg _P_ (0.001)**

```bash
### YOUR CODE HERE ###
```

After doing all steps, 
- _How many markers remain in the dataset?_
- _How many samples remain in the dataset?_

**All in a run**
Perform all steps from the raw data in one run by PLINK. 
- _Does the number of remaining markers and samples differ from the step-by-step result? Why?_

```bash
plink --bfile human --maf 0.01 --mind 0.05 --geno 0.05 --hwe 0.001 --make-bed --out human_all
```

## [VCFtools](https://vcftools.github.io/index.html)

Use the human.vcf.gz you generated in the previous section.

#### Data input
- What are the available input formats for this software? Which option should we use for our dataset?
#### Data output
- are the output options?
- What is the command for outputting data in the VCF file format? or a 012 matrix for genotype? or in PLINK format?

### Try QC options
Try to filter your dataset by the following instructions and write down the number of remaining markers and samples after filtering.

#### Minor allele frequency (0.01)

```bash
### YOUR CODE HERE ###
```
#### Include only bi-allelic sites
```bash
### YOUR CODE HERE ###
```
**SNP genotype rate (0.05 missingness)**
```bash
### YOUR CODE HERE ###
```

**Hardy-Weinberg P (0.001)**
```bash
### YOUR CODE HERE ###
```

**Perform all QC steps in one run and compare the result with the step-wise method.**
```bash
### YOUR CODE HERE ###
```

## Task 4: Optional Visualization in R 

#### Load data to R
```read_plink()``` function in the ```genio``` package is a powerful tool to read PLINK binary files.
Use the PLINK binary files that passed all the QC-steps in one run in the previous section.

```R
library(genio)
### YOUR CODE HERE ###
```

#### Genotype frequencies
Calculate genotype (0: A1A1, 1: A1A2, 2: A2A2) frequencies of each loci 
- Generate an empty data frame (allele_frequency) with three columns (A1A1, A1A2, A2A2).   
- Number of rows = number of markers.  

```R

### YOUR CODE HERE ###

```

#### Use a for loop to count the number of genotypes in each marker and divide by the number of samples.

```R
### YOUR CODE HERE ###
```

#### Bind the frequency table with the marker information (HINT: ```cbind()``` function can bind two data-frames)

```R
### YOUR CODE HERE ###
```
#### Make a plot with your frequency data using the following script:

```R
library(tidyverse)

# prepare plotting data
plotDat = freq %>% 
    as_tibble() %>%
    mutate(chr = as.numeric(.$chr), pos = as.numeric(.$pos))

plotDat = plotDat %>% 
    group_by(chr) %>%
    summarise(chr_len = max(pos)) %>%
    mutate(tol = cumsum(chr_len) - chr_len) %>%
    left_join(plotDat, .) %>%
    arrange(chr, pos) %>%
    mutate(pos.cum = pos + tol) %>%
    select(chr, id, ref, alt, A1A1, A1A2, A2A2, pos.cum) %>%
    ungroup()
plotDat = pivot_longer(plotDat, cols = 5:7, names_to = "genotype", values_to = "frequency")

axisdf = plotDat %>%
    group_by(chr) %>%
    summarise(center = (max(pos.cum) + min(pos.cum))/2)

# Plot
p = ggplot(data = plotDat, aes(x = pos.cum, y = frequency, fill = genotype)) +
    geom_area(position = 'fill') +
    labs(x = "Chromosome", y = 'Frequency') +
    scale_x_continuous(label = axisdf$chr, breaks = axisdf$center) +
    theme_bw() +
    theme(panel.grid.major.x = element_blank(), panel.grid.minor.x = element_blank())
#ggsave('images/GTfrequency.jpeg', p)
```

Questions:

- Why is visualizing datasets important?
- Try calculating the raw allele counts for each site using VCFtools.
- What is the problem with low MAF? Is it a good idea to have a stricter rule for MAF (a higher MAF threshold)? Why?