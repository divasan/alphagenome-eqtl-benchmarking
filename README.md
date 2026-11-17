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

## 2. AlphaGenome RNA Variant Predictions

### Running AlphaGenome

We used AlphaGenome to predict how the 1,174 REF/ALT variants affect RNA expression.

Each variant was scored using the RNA-seq variant scorer with a 1 Mb window centered on the variant. We ran the predictions in batches of 25 and kept the scores for genes linked to the variants through Hi-C.

### Step 1: Run RNA predictions

- Ran all 1,174 variants in 47 batches.
- Found RNA scores for 1,103 variants and 179 target genes.
- Had 740,887 RNA score rows across all available biosamples.

### Step 2: Look at lung-related RNA scores

We kept scores from lung-related tissues and cells, including lung fibroblasts, alveolar epithelial cells, and lung endothelial cells.

This gave us 29,955 lung RNA score rows, covering 1,976 unique REF/ALT variant–gene pairs and 179 target genes.

### Step 3: Look at predicted RNA effects

Most predicted RNA effects were close to zero, with relatively few larger positive or negative scores.

![Distribution of predicted RNA effects in lung](figures/alphagenome_lung_rna_distribution.png)

To examine effects by gene, we selected the largest absolute RNA score for each gene across its linked variants and lung tracks.

The table below shows selected examples of genes with the largest absolute predicted RNA effects from the AlphaGenome rerun.

| Gene | SNP | Lung biosample | Raw score |
|---|---|---|---:|
| CARMIL2 | rs13334205 | Lung microvascular endothelial cell | -0.023065 |
| SERTAD2 | rs2302643 | Fibroblast of lung | -0.018789 |
| ZBTB16 | rs648116 | Lung microvascular endothelial cell | +0.016066 |
| PKD1 | rs3087631 | Fibroblast of lung | +0.015186 |
| RPS12P4 | rs3764906 | Upper lobe of left lung | -0.014387 |
| PHGR1 | rs8040086 | Upper lobe of left lung | +0.013117 |
| HMGN1P4 | rs504895 | Upper lobe of left lung | -0.012978 |
| TSPAN14 | rs7922621 | Left lung | +0.011761 |
| SCGB1A1 | rs2509967 | Left lung | +0.011102 |

Among these examples, CARMIL2 (−0.0231) and SERTAD2 (−0.0188) had the largest negative scores, while ZBTB16 (+0.0161) and PKD1 (+0.0152) had the largest positive scores.

**Notebook:** [02_alphagenome_variant_predictions_all_HPC.ipynb](notebooks/02_alphagenome_variant_predictions_all_HPC.ipynb)
