# Decision Tree

*Use decision tree machine learning algorithm to classify Single Cell RNA-seq in Glioblastoma patients.*

#### Background:

Understanding cellular and molecular heterogeneity in glioblastoma (GBM), the most common and aggressive primary brain malignancy, is a crucial step towards the development of effective therapies. With the advances in single-cell RNA Sequencing (scRNA-Seq), tumors can now be dissected at the cell level, unveiling
information from their life history to their clinical implications.

Propose a classification setting based on GBM scRNA-Seq data with decision tree model, where different cell populations (neoplastic periphery and neoplastic core) are taken as classes. The goal is to identify gene features discriminating between the classe. 

### Data: 
Data S1_Originaldata.csv consist of 444 rows and 16384 columns

![Screenshot 2022-03-20 001351](https://user-images.githubusercontent.com/62857660/159149220-06f98cb9-f912-46a5-8289-5efeb4ffa05b.png)

### The Process:
Exploratory Data Analysis. 
  - After running `df['Classes'].value_counts()`, because there is 386 samples of Neoplastic_Core vs 58 samples of Neoplastic_Periphery, there is an imbalance problem and we will need to resample the data.
  - There are many columns, we can use feature importance and feature selection to narrow down the columns with 0 importance.

![download](https://user-images.githubusercontent.com/62857660/159149297-67d177f4-529f-4e9e-bae3-729447d033f7.png)



