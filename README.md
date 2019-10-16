# gbs_pipeline
This repository contains all of the scripts that were used to develop the genotyping-by-sequencing (GBS) pipeline for the wild rice (_Zizania palustris_ L.) breeding and conservation program at the University of Minnesota.

## awk_filtering_scripts (directory)
These scripts are short and simple awk commands to pre-filter the tab-separated (TSV) files that contain the output of the normalize.awk script. The normalize.awk script can be found in this repository and is used to extract SNP information from the VCF files.

## umgc_pilot_study (directory)
These scripts are for the (partial) re-analysis of the pilot GBS study conducted by the University of Minnesota Genomics Center (UMGC). I say partial because I start the re-analysis from the VCF files produced by their pipeline. For their pipeline, they used a program called stacks (http://catchenlab.life.illinois.edu/stacks/). The reference genome of _Zizania palustris_ was not used in this pipeline. I was mostly interested in pulling some stats out of the files and re-creating the PCA plot to meet my style preferences. The paper was accepted for publication in Conservation Genetics Resources.

## 190515_cutadapt_pilot_GBS
This file is the "complete" GBS pipeline that was initiated on 15 May 2019. It can be broken down into a few major steps:
1. Removing adapter content with cutadapt
2. Running fastQC on trimmed reads
3. Aligning trimmed fastq reads to the genome with bwa-mem
  - Indexing genome
  - Performing alignment
  - Sorting BAM files
4. SNP calling with samtools mpileup/bcftools call
  - There are two parameters to pay especially close attention to.
    - The -q option for samtools mpileup allows us to specify a _minimum_ mapping quality. A minimum mapping quality value of 20 might be "standard", but I used a value of 60 for some interations of the experiment which might be a little too stringent and results in otherwise useful SNPs being filtered out of the dataset. (Reference: http://www.htslib.org/doc/samtools-1.2.html)
    - The -m option for bcftools call stands for the _multiallelic caller_. It is an alternative method for multi-allelic and rare-varient calling that overcomes known limitations to the -c option which stands for _consensus_ caller. The consensus caller is the original option. Because these options conflict with each other, only one can be used at a time.(Reference: https://samtools.github.io/bcftools/bcftools.html)
5. Pulling the SNP information out of the VCF files and into usable format
  - The script "normalize.awk" extracts the following information from the VCF files: 1) scaffold, 2) position, 3) reference allele, 4) alternate allele, 5) base quality score, 6) sample identifier (path to BAM file), 7) genotype call (0, 1, or 2), 8) read depth (DP) at site, and 9) depth of the varient (DV) allele at the site.

## 190913_pilot_GBS_snp_filtering_wide_format_q60.R
This file contains R code that reformats SNP data from the pilot study. It loads a pre-existing Rdata file and outputs a comma-separated (CSV) file. Otherwise, this code is pretty simple. It takes data in long format and converts it to wide format before writing the CSV file.
  - The input Rdata file is: 190627_snp_filtering_q60.Rdata
  - The output Rdata file is: 190913_pilot_GBS_snip_filtering_wide_format_q60.Rdata
  - The CSV file is: 190913_pilot_GBS_snp_filtering_wide_format_q60.csv
  
## 190913_snp_calling_from_190607_samtools_pilot_GBS.R
This file contains R code for generating a SNP table for the pilot study (excluding _Z. latifolia_).
  - Input Rdata file: 190607_snp_filtering.Rdata
  - Output Rdata file: 190913_snp_calling_from_190607_samtools_pilot_GBS.Rdata
  - Output CSV file: 190913_snp_calling_from_190607_samtools_pilot_GBS.csv
  
## 191002_pca_from_vcf_subset_of_main_GBS_data.R
The purpose of this code is to create a PCA plot directly from the VCF files for the first 50 samples of the main_GBS project. It only considered Scaffold_48. I used this approach because I was having a hard time getting all of the data to load and run at once without the system crashing.
  - Input files: 1) '191001_samtools_Scaffold_48;HRSCAF=1451.vcf.gz', 2) 191002_first_fifty_lines.csv
  - Output files: 1) 191002_pca_from_vcf_subset_of_main_GBS_data.pdf, 2) 191002_pca_from_vcf_subset_of_main_GBS_data.Rdata
  
## 191010_main_GBS_snp_calling_troubleshooting
The purpose of this code is to play around with different parameters from the SNP calling portion of the pipeline (samtools mpileup and bcftools call). All code within this file is simply another version of the SNP calling portion from **190515_cutadapt_pilot_GBS**. Every iteration may not have been version-controlled (saved) because, as the title implies, this was all troubleshooting. The resulting files are contained within directories that follow the pattern: "YYMMDD_samtools". They can probably be deleted at some point after we have decided on our "best practices". Note: if that is done, this text should be changed to "strikethrough" format.

## 191015_main_GBS_snp_calling_troubleshooting.R
This is R code that is also part of the troubleshooting file section above. It reads SNP data contained in a tab-separated value (TSV) file (for example, **191010_normalize_filtered.tsv**) and processes the table in R. The result is a wide format SNP table. Data are saved in CSV and Rdata formats.

  **Example output files:**
  (1) 191015_main_GBS_SNPs_200_samples_DP5_filtered.csv
  (2) 191015_main_GBS_filtered_SNPs.Rdata
