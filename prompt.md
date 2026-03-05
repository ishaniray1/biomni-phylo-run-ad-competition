# Biomni-AD Discovery Prize: Agent Prompt
## "DNA Repair Deficiency as a T-Cell Priming Mechanism for Alzheimer's Neuroinflammation"
### A Multi-Omics Integration Analysis

**Submitted by:** Ishani Ray, Fox Chase Cancer Center  
**Date:** March 2026  
**Competition:** Biomni-AD Discovery Prize / AlzInsights AI Prize for ADRD Research

---

## THE PROMPT

You are a biomedical research AI agent with expertise in genomics, immunology, and Alzheimer's disease (AD). Your task is to execute a multi-omics integration analysis to test the following novel hypothesis:

> **Core Hypothesis:** Germline DNA repair deficiency (in genes such as BRCA1, BRCA2, PALB2, ATM, CHEK2, RAD51C/D) creates a systemic T-cell dysfunction state — characterized by exhaustion, impaired DNA damage response, and pro-inflammatory signaling — that primes neuroinflammation and may accelerate Alzheimer's disease risk and progression. This represents a shared mechanistic axis linking hereditary cancer susceptibility and neurodegeneration.

This hypothesis is grounded in three converging lines of evidence:
1. DNA damage accumulates in AD neurons and is a pathological hallmark of the disease
2. Peripheral T cells show proteomic and metabolic dysfunction in AD patients, including mitochondrial impairment and exhaustion markers
3. Germline DNA repair deficiency (e.g., BRCA2, ATM) alters immune microenvironments in cancer — but this has never been systematically tested in the context of AD neuroinflammation

Your goal is to generate a **computationally supported, testable hypothesis** with multi-layered evidence, suitable for a peer-reviewed publication and grant application.

---

## ANALYSIS WORKFLOW

Execute the following steps **in order**. At each step, document your methods, statistical decisions, and any data quality issues encountered.

---

### STEP 1: Curate the DNA Repair Gene Universe

**Task:** Build a comprehensive, evidence-based list of germline DNA repair genes relevant to cancer predisposition.

**Instructions:**
1. Query the GWAS Catalog for all SNPs associated with Alzheimer's disease (p < 5×10⁻⁸, European ancestry, latest meta-analysis). Extract: rsID, chromosome, position, mapped gene, p-value, odds ratio, study PMID.
2. Independently compile a curated list of germline DNA repair genes across the following pathways:
   - Homologous Recombination (HR): BRCA1, BRCA2, PALB2, RAD51C, RAD51D, BRIP1, BARD1
   - Non-Homologous End Joining (NHEJ): LIG4, XRCC4, XRCC5, XRCC6, PRKDC
   - Mismatch Repair (MMR): MLH1, MSH2, MSH6, PMS2
   - Base Excision Repair (BER): OGG1, MUTYH, APEX1, XRCC1
   - Nucleotide Excision Repair (NER): XPA, XPC, ERCC1, ERCC2, ERCC3
   - ATM/ATR Checkpoint: ATM, ATR, CHEK1, CHEK2, TP53, MDM2
   - Fanconi Anemia: FANCA, FANCC, FANCD2, FANCG, FANCI
3. Cross-reference: Do any AD GWAS loci map to or are in LD (r² > 0.6) with DNA repair genes? Use the GWAS Catalog gene mapping and LD data.
4. Query MSigDB (C2:CGP and C5:GO gene sets) for "DNA repair" and "DNA damage response" gene sets to ensure completeness.

**Statistical considerations:**
- Use genome-wide significance threshold p < 5×10⁻⁸ for AD GWAS loci
- For LD-based overlap, use r² > 0.6 in European reference panel (1000 Genomes Phase 3)
- Report the number of AD loci, number of DNA repair genes, and any direct overlaps

**Output:** 
- `ddr_gene_universe.csv`: Full annotated list of DNA repair genes with pathway classification
- `ad_gwas_loci.csv`: AD GWAS significant loci with gene mappings
- `ddr_ad_overlap.csv`: Any direct or LD-based overlaps between the two sets

---

### STEP 2: Brain Expression Profiling of DNA Repair Genes in AD

**Task:** Determine which DNA repair genes are differentially expressed in AD-affected brain regions.

**Instructions:**
1. Access publicly available AD brain transcriptomics data. Priority sources (use whichever is accessible):
   - **AMP-AD/ROSMAP** (via Synapse syn3219045 or AD Knowledge Portal): Dorsolateral prefrontal cortex (DLPFC) bulk RNA-seq, comparing AD vs. cognitively normal controls
   - **GEO datasets**: GSE5281 (Entorhinal cortex, hippocampus, frontal cortex), GSE48350, GSE132903
   - **GTEx v8**: Brain regions (frontal cortex, hippocampus, cerebellum) for baseline expression
2. For each dataset, perform differential expression analysis (AD vs. control) using DESeq2 (R) or equivalent. Apply:
   - Pre-filtering: Remove genes with < 10 counts across all samples
   - Normalization: DESeq2 median-of-ratios
   - Covariates: Age, sex, PMI (post-mortem interval), RIN (RNA integrity number) where available
   - Multiple testing correction: Benjamini-Hochberg FDR < 0.05
   - Effect size threshold: |log2FC| > 0.5
3. Subset results to the DNA repair gene universe from Step 1.
4. Identify: Which DNA repair genes are significantly dysregulated in AD brain? In which direction (up/down)?

**Critical statistical notes:**
- Use **padj (FDR-adjusted p-value)**, NOT raw p-value, for all significance calls
- Do NOT use inclusive inequalities incorrectly: padj ≤ 0.05 AND |log2FC| ≥ 0.5
- If multiple brain region datasets are available, report results per region and note consistency
- Clearly distinguish human data from any mouse model data — do not conflate
- Report sample sizes (N AD, N control) for each dataset used

**Output:**
- `ddr_brain_degs.csv`: Differentially expressed DNA repair genes in AD brain (with log2FC, padj, dataset source)
- `ddr_brain_heatmap.png/.svg`: Heatmap of DNA repair gene expression across brain regions and disease status
- `volcano_ddr_brain.png/.svg`: Volcano plot highlighting DNA repair genes

---

### STEP 3: Blood T-Cell Transcriptomic Signature of DNA Repair Deficiency

**Task:** Identify T-cell-specific gene expression signatures associated with DNA repair deficiency and cross-reference with AD blood transcriptomics.

**Instructions:**
1. **T-cell exhaustion gene set:** Compile a curated T-cell exhaustion/dysfunction signature including:
   - Inhibitory receptors: PDCD1 (PD-1), HAVCR2 (TIM-3), LAG3, TIGIT, CTLA4, CD244
   - Transcription factors of exhaustion: TOX, TOX2, NR4A1, NR4A2, EOMES (low), TBX21 (low)
   - Effector dysfunction: IFNG (low), TNF (low), IL2 (low), GZMB (low)
   - Terminally exhausted markers: CD57, KLRG1, CX3CR1
   - DNA damage response in T cells: γH2AX pathway genes (H2AX, ATM, TP53BP1, BRCA1, RAD51)
2. **AD blood transcriptomics:** Access blood/PBMC gene expression data from AD patients vs. controls:
   - GEO datasets: GSE63060, GSE63061 (AddNeuroMed cohort, blood), GSE97760, GSE140829
   - If accessible: ADNI blood gene expression data via AD Workbench
3. Perform differential expression analysis on blood data (same DESeq2 pipeline as Step 2).
4. **Key question:** Are T-cell exhaustion genes AND DNA damage response genes co-dysregulated in AD blood?
5. Compute a **T-cell exhaustion score** per sample: mean normalized expression of exhaustion signature genes. Test: Is this score elevated in AD vs. control? (Wilcoxon rank-sum test, report effect size Cohen's d)
6. Correlate T-cell exhaustion score with available clinical variables: MMSE score, amyloid status, tau levels, APOE4 status.

**Statistical considerations:**
- For correlation analyses: Use Spearman correlation (non-parametric, robust to outliers)
- Report 95% confidence intervals for all effect size estimates
- Correct for multiple comparisons when testing multiple clinical correlates (Bonferroni or FDR)
- Clearly label which samples are AD, MCI, and control — do not pool MCI with AD without justification

**Output:**
- `tcell_exhaustion_score_ad.csv`: Per-sample exhaustion scores with clinical metadata
- `tcell_ddr_blood_degs.csv`: DEGs in blood overlapping T-cell exhaustion and DDR gene sets
- `exhaustion_score_boxplot.png/.svg`: Boxplot of exhaustion score by disease status
- `exhaustion_clinical_correlation.png/.svg`: Correlation matrix with clinical variables

---

### STEP 4: Single-Cell Validation — T-Cell Subtype Resolution

**Task:** Validate findings at single-cell resolution using published AD single-cell atlases.

**Instructions:**
1. Access published AD single-cell RNA-seq data:
   - **CellxGene Census** (cellxgene.cziscience.com): Query for Homo sapiens, brain tissue, Alzheimer's disease, T cell annotations
   - **Key datasets to prioritize:**
     - Mathys et al. 2019 (Nature, snRNA-seq, ROSMAP): syn18485175
     - Leng et al. 2021 (Nature Neuroscience): entorhinal cortex
     - Zhou et al. 2020 (Nature Medicine): snRNA-seq multi-region
     - Allen Brain Cell Atlas AD data if available
2. For blood/PBMC single-cell data:
     - Olst et al. 2024 (Mol Neurodegeneration): CyTOF peripheral immune profiling in AD
     - Any available PBMC scRNA-seq from AD patients on CellxGene
3. **Analysis:**
   - Identify T cell clusters (CD3E+, CD4+, CD8+ populations)
   - Score each T cell for: (a) exhaustion signature, (b) DNA damage response gene expression
   - Compare scores between AD and control T cells using Wilcoxon test per cluster
   - Identify which T cell subtype shows the strongest DDR-exhaustion co-expression
4. **Microglia-T cell crosstalk:** In brain datasets, test whether microglia activation state (DAM vs. homeostatic) correlates spatially or transcriptionally with T cell infiltration markers

**Statistical considerations:**
- Use pseudobulk aggregation for differential expression in scRNA-seq (not single-cell-level DE, which inflates significance)
- Report number of cells per cluster per condition
- Use Seurat or Scanpy — document version and parameters
- Clearly distinguish brain-infiltrating T cells from peripheral T cells

**Output:**
- `scrnaseq_tcell_ddr_scores.csv`: Per-cluster DDR and exhaustion scores
- `umap_tcell_exhaustion.png/.svg`: UMAP colored by exhaustion score, split by AD/control
- `pseudobulk_de_tcells.csv`: Pseudobulk DE results for T cell clusters

---

### STEP 5: GWAS-to-Function — Do AD Risk Variants Regulate DNA Repair or T-Cell Genes?

**Task:** Determine whether AD GWAS risk variants have functional effects on DNA repair or T-cell gene expression through eQTL and TWAS analysis.

**Instructions:**
1. Using the AD GWAS loci from Step 1, query GTEx v8 eQTL data for:
   - Brain tissues: Frontal cortex, hippocampus, anterior cingulate cortex
   - Blood/immune tissues: Whole blood, EBV-transformed lymphocytes, transformed fibroblasts
2. **Key question:** Do any AD risk SNPs act as eQTLs for DNA repair genes or T-cell function genes in brain or blood?
3. Run S-PrediXcan (fast TWAS) using:
   - AD GWAS summary statistics (use Bellenguez et al. 2022, Nature Genetics — largest European AD GWAS, N > 788,000)
   - GTEx v8 MASHR expression weights
   - Tissues: Whole blood, brain frontal cortex, brain hippocampus
4. Apply colocalization (coloc) for top TWAS hits: PP.H4 > 0.8 indicates shared causal variant
5. **Filter results** to genes in: (a) DNA repair gene universe, (b) T-cell exhaustion signature
6. Interpret therapeutic directionality: For genes with positive TWAS Z-score in a risk direction → INHIBIT; negative Z-score → ACTIVATE

**Statistical considerations:**
- TWAS significance: Bonferroni correction across all genes tested per tissue
- Colocalization: Only report PP.H4 > 0.8 as high-confidence; PP.H4 0.5–0.8 as suggestive
- Up to 50% of TWAS hits may be LD artifacts — colocalization is mandatory before claiming causal evidence
- Report genomic inflation factor (λGC) for GWAS input; flag if > 1.1

**Output:**
- `twas_results_all_tissues.csv`: Full TWAS results
- `twas_ddr_tcell_hits.csv`: Filtered to DDR and T-cell genes with PP.H4 scores
- `twas_manhattan.png/.svg`: Manhattan plot of TWAS results
- `coloc_summary.csv`: Colocalization results for top hits

---

### STEP 6: Network Integration — The DNA Repair–T Cell–AD Axis

**Task:** Synthesize all findings into an integrated molecular network and generate a testable mechanistic model.

**Instructions:**
1. **Build a multi-layer network** connecting:
   - Layer 1: AD GWAS risk genes (from Step 1)
   - Layer 2: DNA repair genes dysregulated in AD brain (from Step 2)
   - Layer 3: T-cell exhaustion/DDR genes dysregulated in AD blood (from Step 3)
   - Layer 4: TWAS-supported genes with eQTL evidence (from Step 5)
   - Edges: Protein-protein interactions (STRING database, confidence > 0.7), shared pathways (Reactome), co-expression (from Steps 2–4)
2. **Pathway enrichment** on the integrated gene set:
   - Run ORA (over-representation analysis) separately for: upregulated genes, downregulated genes
   - Gene sets: GO Biological Process, Reactome, KEGG, Hallmark (MSigDB)
   - Significance: FDR < 0.05 (Benjamini-Hochberg)
   - Do NOT pre-filter pathways by keyword — assess all enriched pathways for biological relevance
3. **Identify hub genes:** Nodes with highest degree centrality in the integrated network — these are the most promising mechanistic candidates
4. **Mendelian Randomization (if data permits):** Test whether genetically predicted expression of top DDR hub genes causally affects AD risk (using MR-Base or TwoSampleMR package)
5. **Generate the mechanistic hypothesis statement** with supporting evidence tiers:
   - Tier 1 (Genetic): GWAS/TWAS evidence
   - Tier 2 (Transcriptomic): Brain and blood expression evidence
   - Tier 3 (Cellular): Single-cell T-cell evidence
   - Tier 4 (Network): PPI and pathway convergence

**Statistical considerations:**
- For network hub analysis: Report degree, betweenness centrality, and clustering coefficient
- For MR: Use IVW as primary method; Egger and weighted median as sensitivity analyses
- Report heterogeneity (Cochran's Q) and pleiotropy (MR-Egger intercept) for MR
- Distinguish correlation from causation throughout — be explicit about evidence levels

**Output:**
- `integrated_network.csv`: Edge list with evidence source annotations
- `network_visualization.png/.svg`: Network plot with nodes colored by evidence layer
- `pathway_enrichment_results.csv`: Full ORA results
- `hub_genes_ranked.csv`: Top 20 hub genes with centrality metrics
- `mr_results.csv`: MR causal estimates (if run)

---

### STEP 7: Synthesis and Hypothesis Formalization

**Task:** Generate a structured scientific output suitable for submission.

**Instructions:**
1. **Write a 500-word scientific abstract** covering:
   - Background and gap in knowledge
   - Methods summary (data sources, key analyses)
   - Key findings (with specific statistics: effect sizes, p-values, gene names)
   - Novel hypothesis generated
   - Proposed experimental validation strategy
2. **Generate a summary figure** (conceptual diagram as a structured table or network) showing the proposed mechanism: Germline DDR deficiency → T-cell dysfunction → peripheral inflammation → neuroinflammation → AD acceleration
3. **Prioritize top 5 candidate genes** for experimental follow-up, ranked by:
   - Strength of genetic evidence (GWAS/TWAS)
   - Consistency across brain and blood data
   - T-cell specificity
   - Druggability (existing compounds targeting the gene/pathway)
   - Novelty (not previously reported in AD context)
4. **Propose 3 experimental validation strategies:**
   - In vitro: What cell model, what assay, what readout?
   - Ex vivo: What patient population (BRCA/ATM carriers vs. non-carriers), what biospecimen?
   - In vivo: What mouse model, what intervention?
5. **Identify key limitations and confounders:**
   - Population stratification in GWAS
   - Age as a confounder in both DDR and AD
   - Reverse causation (does AD cause T-cell dysfunction, or vice versa?)
   - Mouse vs. human data discordance
   - Tissue specificity of eQTL effects

**Output:**
- `scientific_abstract.md`: Structured abstract
- `top5_candidate_genes.csv`: Prioritized gene list with evidence summary
- `experimental_validation_plan.md`: Proposed follow-up experiments
- `final_report_ddr_tcell_ad.md`: Comprehensive report integrating all findings

---

## REPRODUCIBILITY REQUIREMENTS

All analyses must meet the following standards:

### Code and Environment
- All code must be saved to a Jupyter notebook (`execution_trace.ipynb`) with outputs preserved
- Document all package versions used (Python: `pip freeze`, R: `sessionInfo()`)
- Set random seeds for any stochastic analyses: `np.random.seed(42)`, `set.seed(42)`
- Use relative file paths; avoid hardcoded absolute paths

### Data Provenance
- For every dataset used, record: source URL/accession, version/date accessed, sample size, preprocessing steps applied
- Clearly label all figures with: dataset name, N (AD), N (control), analysis method
- Distinguish human vs. mouse data in all outputs — never conflate

### Statistical Rigor
- Always use **adjusted p-values (FDR/Bonferroni)** — never report raw p-values as primary evidence
- Report effect sizes (log2FC, Cohen's d, odds ratio) with 95% confidence intervals
- For all differential expression: use padj ≤ 0.05 AND |log2FC| ≥ 0.5 as thresholds
- For correlations: report both r and p-value; use Spearman for non-normal data
- Pre-register analysis decisions before seeing results where possible (document in notebook)

### Data Quality Checks (run before every analysis)
- Check for duplicate sample IDs or gene names — remove duplicates and document
- Check for batch effects — visualize PCA colored by batch/dataset; apply ComBat if needed
- Check for outlier samples — flag samples > 3 SD from mean PC1/PC2
- Check for missing data — report % missing per variable; exclude samples with > 20% missing
- Validate gene ID mapping — confirm Ensembl/Entrez/symbol conversions are correct

### Known Confounders to Control
| Confounder | Why It Matters | How to Handle |
|------------|---------------|---------------|
| Age | Both DDR dysfunction and AD increase with age | Include as covariate in all regression models |
| Sex | BRCA1/2 prevalence differs by sex; AD risk differs by sex | Stratify or include as covariate |
| APOE4 status | Strongest AD genetic risk factor | Always include as covariate; stratify in sensitivity analyses |
| PMI (post-mortem interval) | Affects RNA quality in brain tissue | Include as covariate in brain DEG analyses |
| Batch/dataset | Technical variation across studies | Apply ComBat or include as covariate; validate with PCA |
| Cell type composition | Bulk RNA-seq confounded by cell type proportions | Estimate cell type fractions (CIBERSORT/MuSiC) and include as covariates |

---

## KNOWN ROADBLOCKS AND MITIGATION STRATEGIES

| Roadblock | Likelihood | Mitigation |
|-----------|-----------|------------|
| AMP-AD/ROSMAP data requires Synapse login | High | Use GEO alternatives (GSE5281, GSE48350, GSE132903); document access attempt |
| ADNI data requires application | High | Use AddNeuroMed (GSE63060/61) as blood transcriptomics alternative |
| Low statistical power for DDR-specific subgroup analyses | Medium | Use gene set scoring (ssGSEA) rather than individual gene tests to boost power |
| No direct BRCA/ATM carrier status in AD cohorts | High | Use genetically predicted expression (TWAS) as proxy; propose carrier cohort as future work |
| Batch effects across multi-dataset integration | Medium | Apply ComBat-seq; validate with PCA before and after correction |
| T cells rare in brain snRNA-seq | High | Focus on peripheral blood scRNA-seq; use brain data for microglia-T cell crosstalk inference |
| TWAS LD artifacts | Medium | Mandatory colocalization (PP.H4 > 0.8) before claiming causal evidence |
| Reverse causation (AD → T cell dysfunction) | Medium | Address with MR (genetic instruments are not affected by reverse causation) |

---

## EXPECTED OUTPUTS SUMMARY

| File | Description |
|------|-------------|
| `ddr_gene_universe.csv` | Curated DNA repair gene list with pathway annotations |
| `ad_gwas_loci.csv` | AD GWAS significant loci |
| `ddr_ad_overlap.csv` | DDR genes overlapping AD GWAS loci |
| `ddr_brain_degs.csv` | DDR DEGs in AD brain |
| `ddr_brain_heatmap.png/.svg` | Brain expression heatmap |
| `tcell_exhaustion_score_ad.csv` | T-cell exhaustion scores per sample |
| `tcell_ddr_blood_degs.csv` | Blood DEGs in DDR/T-cell gene sets |
| `exhaustion_score_boxplot.png/.svg` | Exhaustion score by disease status |
| `scrnaseq_tcell_ddr_scores.csv` | Single-cell T-cell DDR/exhaustion scores |
| `umap_tcell_exhaustion.png/.svg` | UMAP visualization |
| `twas_ddr_tcell_hits.csv` | TWAS hits in DDR/T-cell genes |
| `integrated_network.csv` | Multi-layer network edge list |
| `hub_genes_ranked.csv` | Top hub genes with centrality metrics |
| `pathway_enrichment_results.csv` | ORA results |
| `scientific_abstract.md` | 500-word abstract |
| `top5_candidate_genes.csv` | Prioritized candidates for follow-up |
| `experimental_validation_plan.md` | Proposed wet-lab validation |
| `final_report_ddr_tcell_ad.md` | Comprehensive integrated report |
| `execution_trace.ipynb` | Full reproducible Jupyter notebook |

---

## SCIENTIFIC CONTEXT AND NOVELTY

This analysis is novel because:

1. **Unexplored intersection:** While DNA damage in AD neurons is established [1], and T-cell dysfunction in AD blood is documented [22, 25], the hypothesis that *germline* DDR deficiency creates a *pre-existing* T-cell dysfunction state that accelerates AD has never been tested.

2. **Cancer-AD bridge:** DSB repair pathogenic variant carriers show altered tumor immune microenvironments [17] — this analysis asks whether the same immune perturbation occurs systemically and contributes to neuroinflammation.

3. **Interception opportunity:** If confirmed, this would identify BRCA/ATM/CHEK2 carriers as a high-risk AD subpopulation amenable to immune-based interception strategies — directly relevant to cancer prevention programs.

4. **Genetic evidence layer:** Using TWAS + colocalization provides causal genetic evidence that goes beyond correlation, addressing the reverse causation concern.

5. **Therapeutic implications:** Top hub genes may be targetable with existing DDR inhibitors (ATR/ATM/PARP inhibitors) or immune checkpoint modulators already in clinical development.

---

*This prompt was designed for the Biomni-AD Discovery Prize and AlzInsights AI Prize for ADRD Research. The analysis leverages publicly available data and open-source tools to ensure full reproducibility and community benefit.*
