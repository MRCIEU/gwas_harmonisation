# GWAS Harmonisation

Tool to map GWAS summary statistics to VCF/BCF with on the fly harmonisation to a supplied reference FASTA.

### Install

```
# Src
git clone https://github.com/MRCIEU/gwas_harmonisation
cd gwas_harmonisation
```

```
# Native
pip install --user -r ./requirements.txt

# Docker
docker build -t gwas_harmonisation .
docker create -v /data:/data -name gwas_harmonisation gwas_harmonisation
```

### Reference FASTA

```
# GRCh36/hg18/b36
wget ftp://gsapubftp-anonymous@ftp.broadinstitute.org/bundle/b36/human_b36_both.fasta.gz
wget ftp://gsapubftp-anonymous@ftp.broadinstitute.org/bundle/b36/human_b36_both.fasta.fai.gz

# GRCh37/hg19/b37
wget ftp://gsapubftp-anonymous@ftp.broadinstitute.org/bundle/b37/human_g1k_v37.fasta.gz
wget ftp://gsapubftp-anonymous@ftp.broadinstitute.org/bundle/b37/human_g1k_v37.fasta.fai.gz

# GRCh38/hg38/b38
wget https://storage.googleapis.com/genomics-public-data/resources/broad/hg38/v0/Homo_sapiens_assembly38.fasta
wget https://storage.googleapis.com/genomics-public-data/resources/broad/hg38/v0/Homo_sapiens_assembly38.fasta.fai
```

## Running the tests

Unit tests:

```
cd gwas_harmonisation
python -m unittest discover test
```

End-to-end tests:

```
bash test/example.sh
```

## Usage

```
Map GWAS summary statistics to VCF/BCF

-h, --help            show this help message and exit
-v, --version         show program's version number and exit
--out OUT             Path to output VCF/BCF
--data GWAS           Path to GWAS summary stats
--ref FASTA           Path to reference FASTA
--json JSON           Path to parameters JSON
--id ID               Study identifier
--cohort_controls COHORT_CONTROLS
                      Total study number of controls (if case/control) or
                      total sample size if continuous
--cohort_cases COHORT_CASES
                      Total study number of cases
--rm_chr_prefix       Remove chr prefix from GWAS chromosome
--log {DEBUG,INFO,WARNING,ERROR,CRITICAL}
                      Set the logging level
```

See param.py for JSON specification

## Validate VCF file

```
gatk ValidateVariants \
-V harmonised.bcf \
-R ref.fasta \
--validation-type-to-exclude ALLELES
```

## Add variant frequency

Add variant frequency from 1000 genomes (or similar)

```
gatk VariantAnnotator \
-R ref.fasta \
-V harmonised.vcf \
-O harmonised_af.vcf \
--resource 1kg:1kg.vcf.gz \
-E 1kg.EUR_AF \
--resource-allele-concordance
``` 

## Known issues

VCF v4.2 cannot accommodate double precision floats. Therefore the smallest effect size and SE etc is:
```
1.1754944e-38
```
