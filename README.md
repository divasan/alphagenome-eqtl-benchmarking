# alphagenome-eqtl-benchmarking
Predicting and evaluating regulatory effects of COPD-associated variants using AlphaGenome, Hi-C, and eQTL data.

## 1. Preparation of AlphaGenome Input Variants

### Selection of candidate Hi-C loops

Hi-C data from six human lung samples (3 healthy controls and 3 COPD) were processed using Juicer, followed by loop calling with HiCCUPS. The Juicer processing workflow is described in the [Lung Hi-C Analysis](https://github.com/divasan/Lung-HiC-Analysis) repository.

Loops were filtered to retain those with at least one anchor overlapping both ATAC-seq and enhancer peaks. We then selected loops connecting lung function-associated LD-expanded SNPs at one anchor to a gene transcription start site (TSS) at the other anchor.

This resulted in 189 candidate loops used for AlphaGenome input preparation.

### Step 1: Extract SNP–gene pairs

- Selected loops connecting SNP-containing enhancer anchors to gene TSSs.
- Retained 139 loops with 1,421 SNP–gene relationships, involving 711 unique SNPs and 201 genes.

### Step 2: Annotate reference and alternate alleles

- Obtained REF and ALT alleles from UCSC dbSNP155.
- Excluded chromosome annotation mismatches and retained SNVs.
- Split multiallelic SNPs into separate REF/ALT entries.
- Obtained 1,174 variant definitions from 710 SNPs. One SNP lacked a usable allele annotation.

### Step 3: Prepare AlphaGenome input files

Generated two files:

- `alphagenome_variant_input.clean.tsv` — Contains chromosome, position, REF, and ALT for AlphaGenome predictions.
- `alphagenome_SNP_loop_gene_input.tsv` — Contains 2,295 SNP–loop–gene rows linking variants to candidate target genes.

**Notebook:** [01_prepare_alphagenome_variants_commented.ipynb](notebooks/01_prepare_alphagenome_variants_commented.ipynb)
