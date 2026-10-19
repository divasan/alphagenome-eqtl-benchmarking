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

Most of the predicted RNA effects were close to zero, with only a small number of larger positive or negative scores.

![Distribution of predicted RNA effects in lung](figures/alphagenome_lung_rna_distribution.png)

To look at the effects by gene, we took the largest absolute RNA score for each gene across its linked variants and lung tracks.

Using a raw score cutoff of ±0.01:

- 170 genes had scores between −0.01 and +0.01.
- 4 genes had scores of −0.01 or lower.
- 5 genes had scores of +0.01 or higher.

The table below shows the 9 genes with absolute RNA scores of 0.01 or higher.

| Gene | SNP | Lung biosample | Raw score |
|---|---|---|---:|
| CARMIL2 | rs13334205 | Lung microvascular endothelial cell | -0.027391 |
| SERTAD2 | rs2302643 | Fibroblast of lung | -0.019648 |
| HMGN1P4 | rs1129723 | Upper lobe of left lung | -0.011451 |
| PIMREGP2 | rs111873045 | Lower lobe of right lung | -0.010622 |
| TSPAN14 | rs7922621 | Left lung | +0.012575 |
| ATP5F1EP1 | rs17036142 | Left lung | +0.012873 |
| PHGR1 | rs2034650 | Lung | +0.012877 |
| ZBTB16 | rs648116 | Lung microvascular endothelial cell | +0.014892 |
| PKD1 | rs3087631 | Fibroblast of lung | +0.015164 |

CARMIL2 (−0.0274) and SERTAD2 (−0.0196) had the largest negative scores, while PKD1 (+0.0152) and ZBTB16 (+0.0149) had the largest positive scores.

Overall, most genes had small predicted RNA effects. These are AlphaGenome predictions, so we will compare them with eQTL data in the next part of the analysis. The ±0.01 cutoff was used to identify genes with relatively larger predicted effects in our dataset, not as a measure of statistical significance.

**Notebook:** `notebooks/02_alphagenome_variant_predictions_all_HPC.ipynb`
