# Research of signaling pathways and transcriptional factors activity alteration associated with acute myeloid leukemia

Authors:
- Ekaterina Osintseva
- Iuliia Ruzhenkova
- Eleonora Belykh

## Introduction

Acute myeloid leukemia (AML) is a heterogeneous group of oncohematological neoplasms, which is characterized by uncontrolled proliferation of immature myeloid cells (blasts) and their accumulation in bone marrow, leading to inhibition of normal hematopoiesis. AML is the most common form of acute leukemia in adults having the shortest survival (5-year survival = 24%) and high rates of relapse ([Shallis et al., 2019](https://www.sciencedirect.com/science/article/abs/pii/S0268960X18301395?via%3Dihub)).

One of the possible reasons for the  malignant transformation of hematopoietic cells is mutations, translocation, or abberant activity of transcriptional factors.
Therefore, a better understanding of the mechanisms of transcriptional regulation can help develop new therapeutic strategies and identify prognostic markers.

## Aim, tasks and data

The aim of the project was to investigate signalling pathways activity alteration and TFs expression in the AML patients cells while performing RNAseq analysis.
The following objectives were set in order to achieve the goal:Thus, a better understanding of the mechanisms of transcriptional regulation can help develop new therapeutic strategies and identify prognostic markers.


0. Explore the topic (literature review) 
1. Download and preprocess publicly available pseudo-bulk RNAseq data of AML patients and healthy donors (HDs) 
2. Perform the differential expression analysis of normalised pseudo-bulk RNAseq dataset
3. Run PROGENy and DoRothEA tools to estimate the signaling pathways and transcriptional factors activity 
4. Validate the findings on the scRNAseq data 
5. Provide the possible prognostic markers

Publicly available datasets from Gene Expression Omnibus (GEO) were used:
- bulk RNA-seq from AML patients' and HDs' bone marrow ([GSE138702](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE138702));
- single cell RNA-seq from AML patients' ([GSE116256](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi)) and HDs' bone marrow ([GSE120221](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi)).

## Workflow

![workflow](workflow.png)

### Preprocessing of the raw counts data

We downloaded open source bone marrow scRNA-seq data of AML patients and HDs. Untreated AML patients only were taken into the sample. Thus, the work was carried out with 16 specimens of AML patients and with 25 specimens of HDs.

Tutorial provided by Saez Lab was used for data preprocessing.

## Literature

Shallis RM, Wang R, Davidoff A, Ma X, Zeidan AM. Epidemiology of acute myeloid leukemia: Recent progress and enduring challenges. Blood Rev. 2019 Jul;36:70-87. doi: 10.1016/j.blre.2019.04.005. Epub 2019 Apr 29. PMID: 31101526.

