# Research of signaling pathways and transcriptional factors activity alteration associated with acute myeloid leukemia

Authors:
- Ekaterina Osintseva
- Iuliia Ruzhenkova
- Eleonora Belykh

## Introduction

Acute myeloid leukemia (AML) is a heterogeneous group of oncohematological neoplasms, which is characterized by uncontrolled proliferation of immature myeloid cells (blasts) and their accumulation in bone marrow, leading to inhibition of normal hematopoiesis. AML is the most common form of acute leukemia in adults having the shortest survival (5-year survival = 24%) and high rates of relapse ([Shallis et al., 2019](https://www.sciencedirect.com/science/article/abs/pii/S0268960X18301395?via%3Dihub)).

One of the possible reasons for the malignant transformation of hematopoietic cells are mutations, translocations, or aberrant activity of transcriptional factors ([Khan, I. et al., 2021](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC9052200/)). Therefore, a better understanding of the mechanisms of transcriptional regulation can help develop new therapeutic strategies and identify prognostic markers.


## Aim, objectives and data

The aim of the project was to investigate signalling pathways activity alteration and TFs expression in the AML patients cells while performing RNAseq analysis.
The following objectives were set in order to achieve the goal:


0. Explore the topic (literature review) 
1. Download and preprocess publicly available pseudo-bulk RNAseq data of AML patients and healthy donors (HDs) 
2. Perform the differential expression analysis of normalised pseudo-bulk RNAseq dataset
3. Run PROGENy and DoRothEA tools to estimate the signaling pathways and transcriptional factors activity 
4. Validate the findings on the scRNAseq data 
5. Provide the possible prognostic markers

Publicly available datasets from Gene Expression Omnibus (GEO) were used:
- bulk RNA-seq from AML patients' and HDs' bone marrow ([GSE138702](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE138702));
- single cell RNA-seq from AML patients' ([GSE116256](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi)) and HDs' bone marrow ([GSE120221](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi))

Also AML dataset with overall survival data was used to perform Kaplan-Meier analysis of survival([dbGaP phs001657.v1.p1](https://www.ncbi.nlm.nih.gov/projects/gap/cgi-bin/study.cgi?study_id=phs001657.v1.p1&phv=417530&phd=&pha=&pht=9391&phvf=&phdf=&phaf=&phtf=&dssp=1&consent=&temp=1)).

## Justification of the tools choice
At first we were planning to work with bulk RNA datasets performing the pathway  and TFs activity alteration in the cells of AML patients bone marrow compared with healthy donors and validate the findings on the scRNA data. For the research we chose the tools developed in the Saez lab for the pathway and TF activity investigation: PROGENy and DoRothEA, available as R packages ([Garcia-Alonso L et al, 2019](https://genome.cshlp.org/content/29/8/1363.full), [Schubert, M. et al, 2018](https://www.nature.com/articles/s41467-017-02391-6#MOESM1)). The authors describe the advantages of PROGENy as the ability to infer the transcriptomic consequences of the processes not by direct mapping of the expression levels of involved genes but by the ‘footprints’ -  consistently deregulated genes with the known impact (‘weight’) on the pathway, which also allows to take into account the post-translational protein modifications. In the case of DoRothEA development several resources of TFs activity estimation were compared and integrated regulons for each TF were derived. The authors proved these tools outperformed the existing methods being more accurate and informative. 

## Workflow
The workflow of the project is presented at the following scheme. 

![workflow](/Figures/workflow.png)

 \*  Checking the accordance of the obtained pathways activities in AML patients compared to HDs with the literature.

All the tutorials for bulk and pseudo-bulk RNA-seq data analysis were taken from the open GitHub page of [Saez Lab](https://github.com/saezlab/transcriptutorial).


### Bulk RNA-seq data

At the first stage of the work, we downloaded the RNA sequencing data (GSE138702), preprocessed the data according to the tutorials, and analyzed the activity of signaling pathways using the PROGENy method (detailed description of these steps will be provided below). However, we found a discrepancy with the literature data and subsequently found that there were not enough genes in this dataset for correct further processing (for calculating pathway activity scores). Moreover, the S7 AML sample showed extreme scores and could change the whole picture of pathways activity estimation, the heatmap illustrating this is given below. Therefore, we decided to change the dataset.

![progeny_bulk](/Figures/progeny_results_bulkRNA.png)

### Pseudo-bulk RNA-seq data
#### Preprocessing of the raw counts data

We downloaded open source bone marrow scRNA-seq data of AML patients and HDs. Untreated AML patients only were taken into the sample. Thus, the work was carried out with 16 AML patients and with 25 HDs. Firstly, scRNAseq data was transformed to pseudo-bulk. Then log2-transformed pseudo-bulk RNAseq counts were visualized using violin plots to choose the threshold for low expressed genes cutoff. We chose the threshold for log2-transformed gene expressions at level 1.5.

![violin_plots_pseudobulk](/Figures/violin_plots_pseudobulkRNA.png)

Genes with expression lower than the threshold in less than a half of patients from each group were removed. After low expressed genes removal, 9897 genes remained.
 
Then  pseudo-bulk RNAseq counts were normalized with the VSN package, in which the variance stabilization and calibration method of normalization was implemented ([Huber et al., 2002](https://pubmed.ncbi.nlm.nih.gov/12169536/)).
Then principal components analysis (PCA) was carried out, and then visualized. Plot below shows that AML patients and HDs are well separated.

![PCA_pseudobulk](/Figures/PCA_pseudobulk.png)

#### Differential expression analysis

Then we performed differential expression analysis (DEA) on normalized data using the limma R package ([Matthew E. Ritchie et al., 2015](https://www.researchgate.net/publication/271332965_LIMMA_powers_differential_expression_analyses_for_RNA-sequencing_and_microarray_studies/link/5639b7c808aecf1d92a9cbed/download)). We received a table of genes sorted by p-value, which contains DEA main statistics such as logFC, adjusted p-value (Table 1). We also plotted qqplot (observed p-value distribution plotted against a random baseline). The black line deviates from the diagonal strongly enough, which means that the data of healthy and AML samples differ quite a lot. 

![limma_pseudobulk](/Figures/limma_pseudobulk.png)

#### Pathways activity analysis with PROGENy

For estimating the pathway activity we run PROGENy (Pathway RespOnsive GENes), a program which allows to infer the pathway activity indirectly, based on consensus gene signatures ([Schubert et al., 2018](https://www.nature.com/articles/s41467-017-02391-6); [Holland et al., 2019](https://pubmed.ncbi.nlm.nih.gov/31525460/); [Holland et al., 2020](https://genomebiology.biomedcentral.com/articles/10.1186/s13059-020-1949-z)). PROGENy was installed as a Bioconductor package. At the first step we downloaded normalized counts and DEA table results obtained in the previous steps to compute PROGENy scores for each sample. The 200 most responsible genes per pathway were chosen for the program running. However, not all of these genes were found in the dataset (Table 2). This can explain the following possible misaccordance of the results with the literature data. Then we ran the enrichment analysis to assess the significance of the pathway activity scores and performed the results (Normalized enrichment scores, NES) in the bar plot below. 

![progeny_NES_pseudobulkRNA](/Figures/progeny_NES_pseudobulkRNA.png)

The obtained PROGENy scores for each AML patient and HDs were performed at the heatmap, which illustrated a good separation of pathway activities between two groups.

![progeny_heatmap_pseudobulkRNA](/Figures/progeny_heatmap_pseudobulkRNA.png)

The most activated (PI3K) and deactivated (Trail) pathways correspond with the literature data about pathway activity alteration during AML. PI3K, or, the phosphatidylinositol-3-kinase pathway, is described as important in normal and malignant hematopoiesis, involved in cell proliferation, differentiation and survival ([Salihanur Darici et al., 2020](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC7563273/)). PI3K pathway is often detected as constitutively activated in AML cells, with FLT3 mutations as one of the driving mechanisms. This matches our dataset genetic landscape (38% of patients carry FLT3 mutations). 

#### Transiption factors activity analysis with DoRothEA 

To determine the most activated and deactivated TFs in AML (compared to healthy controls), we used DoRothEA ([Garcia-Alonso et al., 2019](https://pubmed.ncbi.nlm.nih.gov/31340985/)). DoRothEA was installed as a Bioconductor package. We ran DoRothEA and received 249 TFs which activity significantly differed in AML patients compared with HDs. The NES of the top 75 most activated and deactivated TFs are displayed in a bar plot below.

![dorothea_NES_pseudobulk](/Figures/dorothea_NES_pseudobulk.png)

DoRothEA is a comprehensive resource containing a curated collection of TFs and their transcriptional targets. The set of genes regulated by a specific TF is known as regulon. Volcano plots for regulon genes of  two the most differentially activated TFs (FOXP1 and NCOA1) are provided below.

![volcano_dorothea](/Figures/volcano_dorothea.png)

 
#### TFs visualization on scRNA-seq data

We preprocessed the single cell data (detailed steps are described in the notebook [scRNAseq_AML.ipynb](/Scripts/scRNAseq_AML.ipynb)) and using the author's markup by cell types looked at the expression of transcription factors in different bone marrow cell types ([van Galen et al., 2019](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC6515904/)). The TFs having the most interesting pattern of expression between cell types are shown on the dotplot. 

 ![dotplot_scRNAseq](/Figures/dotplot_scRNAseq.PNG)
 
We also built UMAP plots to visualize the expression of transcription factors of interest in order to highlight their expression level in certain cell types. The UMAP plot for MAFB which is overexpressed in monocytes and monocyte-like cells is shown below as an example.

![MAFB_featureplot](/Figures/MAFB_featureplot.png)

#### Searching for possible prognostic markers

We began our literature research with studying publications that discuss the relationship between AML and TFs that we found using DoRothEA package. 

For the most activated TF, NCOA1 (nuclear receptor coactivator 1), along with other active NCOA3 and NCOA2, encoded by paralogous genes, no literature evidence about the expression during AML were found. Some authors describe the rearrangements involving these genes resulting in generating the functionally aberrant fusion proteins ([S Esteyries et al., 2009](https://www.nature.com/articles/2404930)). Also for NCOA1 TF the direct interaction with ASXL1 protein encoded by one of the most frequently mutated genes  in malignant myeloid diseases was described ([M Katoh, 2013](https://www.nature.com/articles/bjc2013281)).  

The controversial literature data was obtained about the most deactivated TF - FOXP1 - the transcriptional repressor involved in regulation of monocyte differentiation. By Nicolas Duployez et al., the loss-of-function of FOXP1 gene was proposed as likely promoting the leukemogenesis mostly in the cases of inv(16)-AML ([Duployez et al., 2018](https://www.oncotarget.com/article/24031/text/)). In our dataset there was one patient with inv(16) aberration (van Galen P et al., 2019, Table S1).  However, the increased FOXP1 gene expression was also considered as not favorable prognostic predictor in cases of intense chemotherapy ([Seipel Katja et al., 2020](https://www.sciencedirect.com/science/article/abs/pii/S0145212620300011)). 

One of the interesting chosen TFs was MAFB - a bZip transcriptional factor specific for the monocytic lineage differentiation. This corresponds to our results of MAFB visualization on dot plot and UMAP.  Li Yang et al showed the connection between DNMT3A R882 mutation and MAFB overexpression ([Li Yang et al., 2015](https://pubmed.ncbi.nlm.nih.gov/25721756/)), which correspond to our dataset genetic properties (44 % of AML patients carried DNMT3A mutations). Mutations in DNMT3A gene, coding the DNA methyltransferase and responsible for the de novo DNA methylation, considered as driver mutations of AML. This research also demonstrated a positive impact of elevated MAFB expression on the overall survival of the patients with DNMT3A R882 mutation. Nonetheless, MAFB was described playing a controversial role in leukemogenesis depending on the hematopoietic cells context. 

Among the top 100 differentially activated TFs we also selected AHR  - ligand-activated transcription factor involved in cellular metabolism, HSC differentiation and immune regulation. There is the literature evidence of the correlation of high AHR expression with FLT3-ITD mutation, as well as the association with monocytic phenotype ([Jennifer N.Saultz at al., 2021](https://ashpublications.org/blood/article/138/Supplement%201/1161/482413/Aryl-Hydrocarbon-Receptor-AHR-Gene-Expression-in)). The authors also showed that using the AHR antagonist can facilitate the NK cell mediated killing FLT3-ITD AML cells. 

NR4A1 activation can explain the downregulation of MYC and NFkB pathway activities which we observed after PROGENy and DoRothEA run ([Salix Boulet et al., 2022](https://www.sciencedirect.com/science/article/pii/S2590255522000014?via%3Dihub)). 

One of the most activated TF in our research (top 25 by DoRothEA) was MEIS1, a transcriptional regulator  parciticated in hematopoiesis, megakaryocyte lineage development that also can act as cofactor of HOX genes especially in the induction of myeloid leukemias. Both MEIS1 and HOXA9 were activated in our AML samples which conforms to most literature sources about their co-overexpression, associated with poor prognosis ([Cailin T. Collins and Jay L. Hess, 2016](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC5653247/)). 

It is worth noting that we did not find any controversial data about MEIS activation during AML. That’s why we chose MEIS1 for subsequent Kaplan-Meier survival analysis ([Kaplan_Meier_Analysis](Kaplan_Meier_Analysis.ipynb)). For doing this we took an AML dataset with overall survival (OS) data. Patients with NA values were excluded, and finally 123 patients were taken into analysis. Then the patients were divided in two groups depending on the level of MEIS1 expression (the median value was chosen as the threshold). 


<p align="center">
  <img src="/Figures/MEIS1_Kaplan_Meier.png" />
</p>

The Kaplan-Meier curves show the difference in survival between two groups with decreased survival for the group expressing MEIS1 above median (p-value = 0.005). Thus, MEIS1 can be considered as a possible prognostic marker at the significance level of $\alpha$ = 0.01. However, this result should be validated on the bigger AML patients cohort. 

Of course, not for each of the TFs there were references in the literature about its association with AML. Therefore, there is a large space for studying the involvement of not previously mentioned in AML context TFs in gene regulatory networks and signaling pathways in order to build hypotheses.  

## Literature

Cailin T. Collins and Jay L. Hess. Deregulation of the HOXA9/MEIS1 Axis in Acute Leukemia. Curr Opin Hematol. 2016 Jul; 23(4): 354–361.doi: 10.1097/MOH.0000000000000245

Duployez Nicolas, Boudry-Labis Elise, Roumier Christophe et al. SNP-array lesions in core binding factor acute myeloid leukemia. Oncotarget. 2018; 9:6478-6489. https://doi.org/10.18632/oncotarget.24031.

Esteyries S,  Perot C,  Adelaide J et al. NCOA3, a new fusion partner for MOZ/MYST3 in M5 acute myeloid leukemia. Leukemia. v. 22, p. 663–665 (2008)

Garcia-Alonso L, Holland CH, Ibrahim MM, Turei D, Saez-Rodriguez J. Benchmark and integration of resources for the estimation of human transcription factor activities. Genome Res. 2019 Aug;29(8):1363-1375. doi: 10.1101/gr.240663.118. Epub 2019 Jul 24. Erratum in: Genome Res. 2021 Apr;31(4):745. PMID: 31340985; PMCID: PMC6673718.

Holland CH, Szalai B, Saez-Rodriguez J. “Transfer of regulatory knowledge from human to mouse for functional genomics analysis.” Biochimica et Biophysica Acta (BBA) - Gene Regulatory Mechanisms. 2019. DOI: 10.1016/j.bbagrm.2019.194431.

Holland CH, Tanevski J, Perales-Patón J, Gleixner J, Kumar MP, Mereu E, Joughin BA, Stegle O, Lauffenburger DA, Heyn H, Szalai B, Saez-Rodriguez, J. “Robustness and applicability of transcription factor and pathway analysis tools on single-cell RNA-seq data.” Genome Biology. 2020. DOI: 10.1186/s13059-020-1949-z.

Huber W, von Heydebreck A, Sültmann H, Poustka A, Vingron M. Variance stabilization applied to microarray data calibration and to the quantification of differential expression. Bioinformatics. 2002;18 Suppl 1:S96-104. doi: 10.1093/bioinformatics/18.suppl_1.s96. PMID: 12169536.

Katoh M. Functional and cancer genomics of ASXL family members. British Journal of Cancer volume 109, p. 299–306 (2013).

Khan, I., Eklund, E. E., & Gartel, A. L. (2021). Therapeutic Vulnerabilities of Transcription Factors in AML. Molecular cancer therapeutics, 20(2), 229–237. https://doi.org/10.1158/1535-7163.MCT-20-0115

Matthew E. Ritchie, Belinda Phipson, Di Wu, Yifang Hu, Charity W. Law, Wei Shi and Gordon K. Smyth. limma powers differential expression analyses forRNA-sequencing and microarray studies. Nucleic Acids Research, 2015, Vol. 43, No. 7. doi: 10.1093/nar/gkv007

Salihanur Darici, Hazem Alkhaldi, Gillian Horne, et al. Targeting PI3K/Akt/mTOR in AML: Rationale and Clinical Evidence. J Clin Med. 2020 Sep; 9(9): 2934. doi: 10.3390/jcm9092934

Saultz Jennifer N. et al. Aryl Hydrocarbon Receptor (AHR) Gene Expression in AML Is Associated with FLT3-ITD+ AML and HLA-E Induced Immune Resistance Reversed By Ik-364. Blood (2021) 138 (Supplement 1): 1161.https://doi.org/10.1182/blood-2021-147589. 

Schubert, M., Klinger, B., Klünemann, M. et al. Perturbation-response genes reveal signaling footprints in cancer gene expression. Nat Commun 9, 20 (2018). https://doi.org/10.1038/s41467-017-02391-6

Seipel Katja, Messerli Christian, Wiedemann Gertrud et al. MN1, FOXP1 and hsa-miR-181a-5p as prognostic markers in acute myeloid leukemia patients treated with intensive induction chemotherapy and autologous stem cell transplantation. Leukemia Research, 89(), 106296–. doi:10.1016/j.leukres.2020.106296 

Shallis RM, Wang R, Davidoff A, Ma X, Zeidan AM. Epidemiology of acute myeloid leukemia: Recent progress and enduring challenges. Blood Rev. 2019 Jul;36:70-87. doi: 10.1016/j.blre.2019.04.005. Epub 2019 Apr 29. PMID: 31101526.
 
van Galen P, Hovestadt V, Wadsworth Ii MH, et al. Single-Cell RNA-Seq Reveals AML Hierarchies Relevant to Disease Progression and Immunity. Cell. 2019;176(6):1265-1281.e24. doi:10.1016/j.cell.2019.01.031

Yang Li,  Liu Ya'Nan, Zhu Li et al. DNMT3A R882 mutation is associated with elevated expression of MAFB and M4/M5 immunophenotype of acute myeloid leukemia blasts. Leuk Lymphoma. 2015;56(10):2914-22. doi: 10.3109/10428194.2015.1015123.
