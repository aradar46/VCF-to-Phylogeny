## Project3: What Are The Phylogenetical Relationships Between The Taxa?

***By:  Arash Darzian***

```

Software Used:

BCFtools = bcftools 1.3.1 Using htslib 1.3.1

VCFtools= VCFtools (0.1.16)

vcf2phylip =https://github.com/edgardomortiz/vcf2phylip

iqThree= IQ-TREE multicore version 2.2.0.3 COVID-edition

FigTree= FigTree v1.4.4, 2006-2018

```

### 1- Preprocessing Data

To find how many individuals are in the file

```

$ bcftools query -l ProjTaxa.vcf

> 16 names will be appeared

```

How many chromosomes there are?

```

$ cat ProjTaxa.vcf | grep -v "^#" | cut -f1| uniq

> chr5 chrZ

```

To analysis mean deapth of coverage in each individuls

```

$  vcftools --vcf ProjTaxa.vcf --depth

$  cat out.idepth

```

## 2. Filter the data

The file we have is a not so tidy .vcf file. We need to filter it to get a better result.

We can use vcftools for filtering and we are going to do this 3 times:

- for ProjTaxa.vcf
- for chromosme 5 using --chr chr5 flag
- for chromosome Z  using --chr chrZ flag

for the original file

```

$ vcftools --vcf ProjTaxa.vcf --minQ 30  --min-meanDP 6 --max-meanDP 16 --max-missing 0.8  --recode --out ProjTaxa_all


> After filtering, kept 3520919 out of a possible 3816977 Sites

```

for chr5

```

$ vcftools --vcf ProjTaxa.vcf --minQ 30  --min-meanDP 6 --max-meanDP 16 --max-missing 0.8  --chr chr5 --recode --out ProjTaxa_chr5

```

for chrZ

```

$ vcftools --vcf ProjTaxa.vcf --minQ 30  --min-meanDP 6 --max-meanDP 16 --max-missing 0.8  --chr chrZ --recode --out ProjTaxa_chrZ

```

*`--vcf ProjTaxa.vcf`: The input VCF file to be filtered.

*`--minQ 30`: This option specifies the minimum base quality threshold.

*`--min-meanDP 6`: This option specifies the minimum mean depth of coverage.

*`--max-meanDP 16`: This option specifies the maximum mean depth of coverage.

*`--max-missing 0.8`: This option specifies the maximum fraction of missing data allowed for a sample.

*`--recode`: Tells VCFtools to generate a new VCF file with only the variants that passed the filtering criteria.

*`--out ProjTaxa_all`: This option specifies the name of the output file, which will be `ProjTaxa_all.recode.vcf`.

Mesureing depth of coverage after filtering:

```

$ vcftools --vcf ProjTaxa_all.recode.vcf --depth

$ cat out.idepth

```

Now we can see that the mean depth of coverage is better than before.

## 3. Generate a matrix of variants:

We need to generate a matrix of variants where each row represents a sample, and each column represents a variant. The matrix should contain binary values indicating whether a sample has a variant or not. To generate a matrix of variants, we can use a tool such as VCF2Phylip.

VCf2Phylip is a python script that converts a VCF file into a Phylip file using IUPAC ambiguity codes.

The script can be run from the command line as follows:

we run below line for all three filtered vcf files from previous stage as input and **Naxos2** as outgroup

```

$ python vcf2phylip.py --input <input> -o <outgroup> 


> will produce *.phy file

```

## 4. Build the phylogeny

iqTree is a fast, parallel, and user-friendly software for inferring maximum likelihood (ML) and Bayesian phylogenetic trees from molecular sequence alignments. (I've tried RAxML but it was so slow rather than iqTree(thanks to Zac))

Maximum likekihood is a method of estimating the parameters of a statistical model given observations. It is a special case of the method of maximum a posteriori (MAP) estimation, which is a method of estimating the parameters of a statistical model given observations, and a prior distribution for the parameters. The maximum likelihood method is used in statistics, econometrics, and machine learning. Now we can use it to build phylogenetic trees.

```

$ iqtree -s <input.phy file> -B 1000 -alrt 1000 -T 5 -st DNA


> produce different files but we will use .contree file for visulising

```

*`-B 1000`: This number of bootstrap replicates to perform.

*`-alrt 1000`: The number of ultrafast bootstrap replicates to perform.

*`-T 5`: This option specifies the number of threads to use.

*`-st DNA`: This option specifies the type of data being analyzed

## 5. Visualize the phylogeny

figtree--> open .contree files -->reroot to outgroup---> nodes lable

![img]()
