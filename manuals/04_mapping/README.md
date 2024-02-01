# *Mycobacterium tuberculosis* example dataset


## Table of contents
1. [Introduction & Aims](#introduction)
2. [Run Snippy](#exercise1)
3. [Looking at SNPs](#exercise2)
4. [Looking for resistance SNPs](#exercise3)

## 1. Introduction <a name="introduction"></a>

The goal of this exercise is look map short reads (Illumina) to a reference genome and call variants. Our dataset are two *M. tuberculosis* samples that have been sequenced via Illumina paired-end. We will be using the trimmed reads from the QC module as input for the mapping.

### We will

- [X] Use `snippy` to map and call variants for each dataset
- [x] Look at SNPs



## Map and call variants using `snippy` <a name="exercise1"></a>
First make sure we are in the `TB_module` folder:
```bash
cd ~/modules/TB_module
```

Check snippy is installed properly and to get an idea of how to run the program:
```bash
snippy -h
```
![](figures/map_fig2.png)

## 2. Run `snippy` to map reads to reference and call variants <a name="exercise2"></a>

We are going to use `snippy` which is a very popular pipeline for mapping and calling variants for bacterial genomes.  
You can learn more about the pipeline here : https://github.com/tseemann/snippy  

Let's run snippy on the first TB sample:
```bash
snippy --cpus 4 --outdir TBsample1_snippy --ref Mtb_H37Rv.gb --R1 TBsample1_1_val_1.fq.gz --R2 TBsample1_2_val_2.fq.gz
```
`--cpus 4` : run using 4 CPUs. Default is 8.  
`--outdir` : name the output directory  
`--ref` : reference genome file in GenBank or fasta format  
`--R1` : Forward read files (remember we are using the trim_galore output files)  
`--R2` : Reverse read files (remember we are using the trim_galore output files)  

`snippy` will take a few minutes to complete. There will be quite a bit of output on the terminal -- and that is OKAY!

### When finished look at the output files:
```bash
ll TBsample1_snippy
```
![](figures/map_fig3.png)

## **Now modify the command to run date for TBsample2**  
How do you need to change the above `snippy` command so that you use that data?  

Set `--outdir TBsample2_snippy`. What read data (`--R1` and `--R2`) do we need to add?

After you have run both `snippy` commands you should see the following files is you run:
```bash
ls *snippy
```
![](figures/map_fig4.png)
You should see both datasets that have the same files.

## Looking at SNPs <a name="exercise2"></a>
There are many different files produced by `snippy`.

## Output Files

Extension | Description
----------|--------------
.tab | A simple [tab-separated](http://en.wikipedia.org/wiki/Tab-separated_values) summary of all the variants
.csv | A [comma-separated](http://en.wikipedia.org/wiki/Comma-separated_values) version of the .tab file
.html | A [HTML](http://en.wikipedia.org/wiki/HTML) version of the .tab file
.vcf | The final annotated variants in [VCF](http://en.wikipedia.org/wiki/Variant_Call_Format) format
.bed | The variants in [BED](http://genome.ucsc.edu/FAQ/FAQformat.html#format1) format
.gff | The variants in [GFF3](http://www.sequenceontology.org/gff3.shtml) format
.bam | The alignments in [BAM](http://en.wikipedia.org/wiki/SAMtools) format. Includes unmapped, multimapping reads. Excludes duplicates.
.bam.bai | Index for the .bam file
.log | A log file with the commands run and their outputs
.aligned.fa | A version of the reference but with `-` at position with `depth=0` and `N` for `0 < depth < --mincov` (**does not have variants**)
.consensus.fa | A version of the reference genome with *all* variants instantiated
.consensus.subs.fa | A version of the reference genome with *only substitution* variants instantiated
.raw.vcf | The unfiltered variant calls from Freebayes
.filt.vcf | The filtered variant calls from Freebayes
.vcf.gz | Compressed .vcf file via [BGZIP](http://blastedbio.blogspot.com.au/2011/11/bgzf-blocked-bigger-better-gzip.html)
.vcf.gz.csi | Index for the .vcf.gz via `bcftools index`)

Let's take a look at a summary file to see how many variants were called in total:
```bash
cat TBsample1_snippy/snps.txt
```
![](figures/map_fig5.png)
How many variants total were called against for this sample?

### Look at the `snps.txt` for TBSample2.
```bash
cat TBsample2_snippy/snps.txt
```

Which TB dataset has more variants called?


### Looking at the `snps.html` file
One of the most useful files for us to look at is the `snps.html` file. This file will list each of the mutations found and describe what gene and what type of mutation -- for instance whether it is a synonymous or non-synonymous mutation. If a non-synonymous mutation it will list the amino acid difference and the gene effected.

Open the following file:
```bash
firefox TBsample1_snippy/snps.html
```

## Columns in the TAB/CSV/HTML file formats
Name | Description
-----|------------
CHROM | The sequence the variant was found in eg. the name after the ```>``` in the FASTA reference
POS | Position in the sequence, counting from 1
TYPE | The variant type: snp msp ins del complex
REF | The nucleotide(s) in the reference
ALT | The alternate nucleotide(s) supported by the reads
EVIDENCE | Frequency counts for REF and ALT

If you supply a Genbank file as the `--reference` rather than a FASTA
file, Snippy will fill in these extra columns by using the genome annotation
to tell you which feature was affected by the variant:

Name | Description
-----|------------
FTYPE | Class of feature affected: CDS tRNA rRNA ...
STRAND | Strand the feature was on: + - .
NT_POS | Nucleotide position of the variant withinthe feature / Length in nt
AA_POS | Residue position / Length in aa (only if FTYPE is CDS)
LOCUS_TAG | The `/locus_tag` of the feature (if it existed)
GENE | The `/gene` tag of the feature (if it existed)
PRODUCT | The `/product` tag of the feature (if it existed)
EFFECT | The `snpEff` annotated consequence of this variant (ANN tag in .vcf)

## Variant Types

Type | Name | Example
-----|------|-------------
snp  | Single Nucleotide Polymorphism |  A => T
mnp  | Multiple Nuclotide Polymorphism | GC => AT
ins  | Insertion | ATT => AGTT
del  | Deletion | ACGG => ACG
complex | Combination of snp/mnp | ATTC => GTTA



# 3. Looking for resistance mutations <a name="exercise3"></a>

There are several antibiotics that are used to treat TB. Treatment typically involves a combination of antibiotics, as this can help to reduce the risk of antibiotic resistance and improve treatment outcomes.

The antibiotics most commonly used to treat TB include:

1. **Isoniazid (INH)**: This is a first-line antibiotic that is highly effective against M. tuberculosis. It works by inhibiting the synthesis of mycolic acids, which are essential components of the bacterial cell wall.

2. **Rifampin (RIF)**: This is another first-line antibiotic that is used in combination with INH. It works by inhibiting the activity of RNA polymerase, which is required for bacterial replication.

3. **Pyrazinamide (PZA)**: This is a first-line antibiotic that is used in combination with INH and RIF. It works by disrupting the bacterial cell membrane and reducing the acidity within the cell.

4. **Ethambutol (EMB)**: This is a first-line antibiotic that is used in combination with INH, RIF, and PZA. It works by inhibiting the synthesis of arabinogalactan, which is another component of the bacterial cell wall.

5. **Streptomycin**: This is a second-line antibiotic that is used in cases of drug-resistant TB. It works by inhibiting protein synthesis in the bacterial cell.

**Let's look for a few well known resistance mutations in these datasets**

### **Rifampin resistance**
A primary drug for treating TB is rifampin. One of the main genes that has mutations that confers resistance to rifampin is rpoB. Let's take a look to see if we have any mutations in rpoB within our two samples.

```bash
grep 'rpoB' TBsample1_snippy/snps.tab
grep 'rpoB' TBsample2_snippy/snps.tab
```
`grep` is the command to search within a file  
Here, we are searching for `rpoB` then we follow it by the file we wish to search.


Does one sample have more mutations than the other?

One particular mutation in rpoB that is known to confer resistance to rifampin is the S450L mutation. Does one of our samples have this mutation?

### **Isoniazid resistance**

Take a look at this paper here: https://academic.oup.com/jid/article/182/6/1788/916586?login=false

## *Questions*
1. What gene and what mutation within that gene are typical for isoniazid resistance with TB?

Use `grep` to search for this gene within the `TBsample1_snippy/snps.tab` and `TBsample2_snippy/snps.tab`


2. Based on these findings, which TB sample(s) are resistant and which are sensitive?
