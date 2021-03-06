Project 01: Genetic interactions and cancer cell survival
=========================================================

### *Project overview and guidelines*

-   [Introduction](#introduction)
-   [Objective](#objective)
-   [Description of datasets](#description-of-datasets)
-   [Literature review](#literature-review)
-   [How to structure your project](#how-to-structure-your-project)
    -   [Project proposal](#project-proposal)
    -   [Project](#project)

Supervisor:

-   Carl Herrmann
    ([carl.herrmann@uni-heidelberg.de](mailto:carl.herrmann@uni-heidelberg.de))
-   Ashwini Sharma
    ([ashwini.sharma@uni-heidelberg.de](mailto:ashwini.sharma@bioquant.uni-heidelberg.de))

Tutor:
- David Schwarzenbacher ([david.schw@sol.at](mailto:david.schw@sol.at))

Introduction
------------

Genes rarely act alone, but rather in concert with others. In a
signalling or a metabolic pathway, a single gene (and its translated
product - protein) is not reflective of the final signalling or
metabolic pathway activity. However, different genes act together (some
activating, some repressing while others neutral) resulting in the net
pathway output.

The aim of this project is to identify **genetic interactions** and
their consequent effect on cancer cell survival. Cancer is a disease of
accumulated defects (mutations, copy number changes, aneuploidies etc)
in the genome. While the number of genomic defects in different cancers
might vary from very few to thousands, only a handful of them are
driving the process of oncogenesis. These *driver mutations* are
observed in the highest frequencies, significantly enriched over the
background mutation rates while the rest are typically irrelevant
*passenger mutations*.

In practice, these driver genes can mostly not be targeted by drugs.
This is mainly because the driver genes are often highly essential for
normal cell survival too. As a result, the anti-cancer drugs cannot
discriminate between cancer and normal cells. An alternative strategy
consists in targeting some other genes that interact with the driver
genes in the cancer cell but not in the normal cells.

<img src="/img/GraphicalAbstractDataScience.png" alt="Fig. 1 - Genetic interactions in cancer" width="500">

Fig. 1 - Genetic interactions in cancer

In Fig.1, some of the possible genetic interactions occurring in a
cancer cell has been summarized. Lets imagine a driver mutation (red)
that transforms a normal cell into a malignant cell which divides
uncontrollably. However, its wild type (not mutated) counterpart (black)
divides normally. Now, we cannot target the driver mutation (red)
because it is also essential in the normal cells. So we instead try to
find another gene (yellow, green and blue) which, when perturbed in
combination with the driver mutation, reduces the cancer cell
proliferation while having no effect in the wild type condition upon the
same perturbation.

As a basis for this project, we will use a large dataset of cancer cell
lines including a combination of gene expression data, information about
mutations and copy number changes, as well as data on the sensitivity of
the cell lines when certain genes are knocked-down (see below the
detailled description).

Objective
---------

The objective of this project is to achieve the following:

-   Choose a cancer type of interest (**Fig. 2**) with a complete
    genomic profile i.e. a cancer type with gene expression, gene copy
    number, gene mutation and CRISPR/Cas9 based gene knockdown data. The
    cancer type of interest must have at lest 20 representative cell
    lines.

-   For the chosen cancer type, review recent literature to identify 3-5
    most prominent mutations or genomic alterations driving the cancer
    growth (for example mutations in p53, EGFR, RB etc). A good starting
    point might be the [publications from the TCGA
    consortium](https://www.cancer.gov/about-nci/organization/ccg/research/structural-genomics/tcga/publications)

-   Ideally, chose those driver alterations which are difficult to
    target clinically (for example p53)

-   Identify appropriate cell line models to study the specific genomic
    alteration in a cancer type of interest. For instance, if you chose
    to focus on p53 mutation for a particular cancer type, segregate
    cell lines for that cancer type with or without p53 mutations. This
    way you can check many of your hypothesis like
    -   What happens when there is p53 mutation vs no p53 mutation
    -   If I observe something in p53 mutated scenario, do I still
        observe this in a non p53 mutated scenario
    -   How confident am I to claim that something is exclusively p53
        mutation related effect etc
-   In the cancer type of your interest, for the chosen driver
    alteration, find which are the most correlated mutations or copy
    number changes associated with the driver alterations.

-   Find if there is functional/biological relationship between these
    correlated alterations. For example are they in the same pathways as
    the driver mutation, are they part of the same protein complex etc.
    Use relevant publicly available databases to get these information.

-   Find if there are clinically approved drugs for any of the
    correlated alterations. Use relevant publicly available databases to
    get these information.

-   Make a coherent report of your complete analysis and results using R
    markdown.

<img src="/img/SampleCounts.png" alt="Fig. 2 - Number of cell lines per cancer type" width="400">
Fig. 2 - Number of cell lines per cancer type

Description of datasets
-----------------------

We have provided a R list object (as a .RDS file). You can find the data
using this link: [https://figshare.com/s/caa3448439fcf6032df4](https://figshare.com/s/caa3448439fcf6032df4)


Once you have downloaded it, load it as below:

``` {.r}
allDepMapData = readRDS("path/to/your/directory/DepMap19Q1_allData.RDS")
names(allDepMapData)
[1] "expression" "copynumber" "mutation"   "kd.ceres"   "kd.prob"    "annotation"
```

The list named as `allDepMapData` consists of the following matrices -

-   Gene expression
    -   This matrix consist of gene
        [TPM](https://www.rna-seqblog.com/rpkm-fpkm-and-tpm-clearly-explained/)
        (transcripts per million) values. These values reflect the level
        of gene expression, higher values suggests over expression of
        genes and vice versa. The rows of this matrix are the gene names
        and the columns are the cancer cell line identifiers.
-   Gene copy number
    -   This matrix consist of gene copy number (CN) values. In absolute
        terms, CN = 2, since there are two alleles per genes. In cancer,
        genes might be amplified CN \> 2 or deleted CN \< 2. These
        values reflect the copy number level per gene, higher values
        means amplification of genes and vice versa. The rows of this
        matrix are the gene names and the columns are the cancer cell
        line identifiers.
-   Gene mutations
    -   Is the annotation file for the various mutations observed in a
        sample. The `isDeleterious` flag specifies if mutation has a
        functional effect or not.
-   Gene knockdown (CERES)
    -   This matrix consist of gene knockdown scores. The score is a
        measure of how essential/important is a particular gene for the
        cell survival. This score reflects whether upon knocking down
        that genes does the cell reduce its proliferation or increases
        it or has no change. Smaller values refers to higher
        essentiality. The rows of this matrix are the gene names and the
        columns are the cancer cell line identifiers.
-   Gene knockdown (Probability)
    -   The probability value for the effect of the gene knockdown. A
        higher probability signifies that knocking down that particular
        gene very likely reduces cell proliferation.
-   Annotation
    -   This matrix gives information regarding the cell lines. The
        `DepMap_ID` provides the uniform cell line identifiers used in
        all the data sets below. The `CCLE_Name` is encoded as *cell
        line name* \_ *Tissue of origin*. The columns `Primary.Disease`
        and `Subtype.Disease` refers to the tissue/tumor of origin.

Literature review
-----------------

#### Reviews

Read these reviews to gain some understanding of genetic interactions in
cancer.

-   Ashworth, Alan, Christopher J. Lord, and Jorge S. Reis-Filho.
    “Genetic interactions in cancer progression and treatment.” Cell
    145.1 (2011): 30-38.

-   O’Neil, Nigel J., Melanie L. Bailey, and Philip Hieter. “Synthetic
    lethality and cancer.” Nature Reviews Genetics 18.10 (2017): 613.

#### Experimental methods

An experimental example of killing ovarian cancer cells specifically
with ARID1A mutation by inhibiting BRD2.

-   Berns, Katrien, et al. “ARID1A mutation sensitizes most ovarian
    clear cell carcinomas to BET inhibitors.” Oncogene (2018): 1.

#### Computational methods

Read these papers to understand how we can computationally identify
novel genetic interactions in cancer

-   Jerby-Arnon, Livnat, et al. “Predicting cancer-specific
    vulnerability via data-driven detection of synthetic lethality.”
    Cell 158.5 (2014): 1199-120
-   Rauscher, Benedikt, et al. “Toward an integrated map of genetic
    interactions in cancer cells.” Molecular systems biology 14.2
    (2018): e7656.

How to structure your project
-----------------------------

### Project proposal

You first task wil we to define a **project proposal**, which should
include

-   summary of literature on this dataset
-   questions you want to address
-   approximate timetable

You will present this project proposal together with a literature review
on the subject 3 week after the begining of the semester (10 minute
presentation + 5 minutes discussion).

### Project

You project **MUST** contain the following elements:
- **descriptive statistics** about the datasets
- **graphical representations**
- **dimension reduction** analysis (PCA, clustering or k-means)
- **statistical tests** (t-test, proportion tests etc)
- **linear regression** analysis, either uni- or multivariate

#### Data cleanup

You will be analyzing multiple genomic data sets together. It is
essential that you explore each dataset and clean it. Cleaning can refer
to many things:

-   Removing missing values
-   Imputing missing values
-   Removing low variance columns/rows
-   Removing batch effects
-   Removing outlier samples (only if it is due to technical issues !!)
-   Making sure that data is in the correct format, for example, numbers
    should be encoded as numeric and not as characters. Categorical
    variables should be factors etc.
-   Re-ordering rows/columns in meaningful and useful ways

#### Data exploration

Now that you have cleaned data, explore your data to understand its
structure. Perform basic exploratory data analysis.

-   Look at the distribution of the overall data, specific samples or
    features.
-   Visualize the data distribution
-   Visualize the inter-dependencies among specific samples/features of
    interest
-   Check some of your hypothesis like - is something high/low between
    two conditions etc

#### Data reduction

You have a high dimension matrix, that is, you have way more features
(\~25000 genes) than observations (\~30 samples).

-   Try out methods to reduce the dimensionality of this data.
-   Cluster your samples to identify similar and dis-similar groups
-   Check how well the groups separate based on the features of your
    interest

#### Data modelling

Imagine how you could try to **predict** sensitivity of cell lines to
knockdown using other features as input. Test how well this might work.
Alternatively, can you **predict** the impact of gene mutations or copy
number alterations on the expression of that gene?
