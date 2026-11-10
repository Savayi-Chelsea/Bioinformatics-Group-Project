# Cell-cycle gene expression in lung adenocarcinoma (TCGA-LUAD)

**Computing Bioinformatics – Group 4 Project**

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/Savayi-Chelsea/Bioinformatics-Group-Project/blob/main/Bioinformatics_Group_Project.ipynb)

## Research question

Do lung adenocarcinoma (LUAD) tumours differ from matched adjacent normal lung tissue in the expression of cell-cycle genes, and can the expression of these genes alone classify a sample as tumour or normal?

**Hypothesis:** Cell-cycle genes are upregulated in tumours, reflecting increased proliferation, and more strongly than genes in general.

## Objectives

1. Acquire, document and quality-control TCGA-LUAD RNA-seq expression and clinical data from UCSC Xena.
2. Test for differential expression of cell-cycle genes between tumour and matched normal samples, using paired tests with Benjamini–Hochberg FDR correction, at both the gene and the gene-set level.
3. Interpret the results biologically using pathway/GO enrichment and a STRING protein-interaction network.
4. Build machine-learning classifiers of tumour vs normal and evaluate them against dummy baselines and against random gene sets of the same size.

## Dataset

| File | Source | Contents |
|---|---|---|
| `HiSeqV2.gz` | UCSC Xena TCGA hub, `TCGA.LUAD.sampleMap/HiSeqV2` | Illumina HiSeq RNA-seq, 20,530 genes × 576 samples, log2(RSEM normalised count + 1) |
| `LUAD_clinicalMatrix` | UCSC Xena TCGA hub, `TCGA.LUAD.sampleMap/LUAD_clinicalMatrix` | 147 clinical variables (sample type, sex, age, stage, smoking, survival) |

The notebook downloads both files automatically. No manual download is needed.

## How to run

1. Click the **Open in Colab** badge above.
2. Select **Runtime → Run all**.
3. A full run takes about 5–10 minutes on a free Colab runtime. Results tables are saved to `results/`.

To run it locally, use Python 3.10+ with:

```
pip install numpy pandas scipy scikit-learn matplotlib seaborn networkx requests statsmodels
```

The enrichment step (Enrichr), the STRING network and the KEGG gene list are fetched from web APIs, so an internet connection is needed. A random seed (42) is fixed for reproducibility.

## Pipeline

| Stage | What it does |
|---|---|
| 1. Question and objectives | Research question, hypotheses, objectives |
| 2. Data acquisition | Downloads and documents the expression and clinical files |
| 3. Preprocessing and QC | Removes duplicates, keeps primary tumour (`-01`) and normal (`-11`) samples, filters lowly expressed genes, updates old gene symbols, biological sanity checks (lung markers, inferred vs reported sex, MKI67), outlier screen, matched pairs |
| 4. Exploratory analysis | Expression distributions, PCA (all genes vs cell-cycle genes), heatmap of matched pairs, cell-cycle score |
| 5. Differential expression | Paired Wilcoxon signed-rank tests with BH FDR (gene level); self-contained and competitive gene-set tests against 10,000 random gene sets |
| 6. Biological interpretation | Enrichr (GO Biological Process, KEGG, Reactome) and a STRING network analysed with NetworkX |
| 7. Machine learning | Logistic regression, random forest and SVM with patient-grouped, stratified 5-fold CV ×3; dummy baselines, label permutation, random same-size gene sets |
| 8. Conclusions | Answers to each objective |

## Key results

| | Result |
|---|---|
| Samples after QC | 574 (515 tumour, 59 normal), 58 matched pairs, 17,944 expressed genes |
| Gene level | 47 of 152 KEGG cell-cycle genes up and 7 down (FDR < 0.05, ≥ 2-fold); strongest: CDC25C, CDC20, CDC45, BUB1B, PLK1 |
| Gene-set level | Cell-cycle score higher in the tumour in 55/58 patients; more changed than all 10,000 random gene sets (p = 0.0001); 30.9% of cell-cycle genes up vs 9.4% of other genes |
| Enrichment | Top up-regulated genes: mitosis and cell-cycle terms (20 of top 300 are cell-cycle genes vs 2.5 expected); down-regulated: surfactant metabolism, PPAR signalling |
| Network | 52 of 54 changed genes connected by 460 high-confidence interactions; hubs CDK1, CCNA2, CCNB1, CDC45 |
| Classification | Logistic regression balanced accuracy 0.99, MCC 0.95 (dummy: 0.50 / 0.00), but random gene sets of the same size perform as well (MCC 0.96) |

**Conclusion:** Cell-cycle genes are strongly and specifically upregulated in LUAD tumours, which supports the hypothesis. They can classify tumour vs normal almost perfectly, but random genes do equally well, so this ability is not unique to the cell cycle.

## Limitations

- Only 59 normal samples, and they were taken next to tumours.
- Bulk RNA-seq mixes cell types, and RNA levels do not always reflect protein activity.
- Results come from one cohort and have not yet been validated in an independent dataset.



## Group 4

| Name | Adm No |
|---|---|
| Savayi Chelsea | 150765 | 
| Shawn James Kuria | 158657 | 
| Charles Murage | 165948 | 
| Timothy Munyiri Kimani | 166095 | 
| Erika Diang'a | 168661 | 
| Nyokabi Priscillah Njoroge | 169532 | 

## Data sources and tools

- The Cancer Genome Atlas (TCGA) Research Network, data via [UCSC Xena](https://xenabrowser.net/)
- [KEGG](https://www.kegg.jp/) pathway hsa04110 (Cell cycle)
- [Enrichr](https://maayanlab.cloud/Enrichr/)
- [STRING](https://string-db.org/) v12
