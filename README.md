# Deep Learning Framework for Predicting Drug Mechanisms of Action
 

The goal of modern drug discovery and development is to find molecules that can alter biological pathways. Scientists identify target proteins associated with a disease which can be regulated through therapeutics. The specific biological activity through which a drug produces a pharmacological effect is defined as its Mechanism of Action (MOA). 

For example, the painkiller ibuprofen inhibits the production of cyclooxygenase (COX) enzymes, a protein that synthesizes prostaglandins. With fewer prostaglandins in the body, pain and fever decrease. Thus, ibuprofen’s MOA is classified as a COX inhibitor. While target-based approaches are efficient, it is still a resource-intensive process. Compounds must be validated with cell-based assays and high throughput testing, both of which are often slow and costly.

With the advent of computationally predictive models, deep learning can be applied to analyze patterns over large genomic datasets. The objective of this project is to predict MOAs of compounds given gene expression and cell viability data. 

The dataset provided consists of 772 gene expression levels using the L1000 assay method which measures human cell mRNA levels in 978 genes. For each compound, cell viability features are measured using the PRISM assay, which measures the proportion of healthy cells after 24, 48, and 72 hours the drug has been added. There are 23,814 training samples with 3,289 unique compounds and 1,866 control samples where no drug was added. While there are 206 possible MOA annotations, only 0.343% of samples have an active MOA. 

This illustrates the “curse of dimensionality” – with such high-dimensional data and sparse target classes, extrapolating traditional statistical techniques becomes less useful. To address this, Principal Component Analysis (PCA) was performed. The reduced-dimensionality features (50 gene expression and 15 cell viability) were augmented to the training dataset.

For data preprocessing, control samples were removed from the training set since they are irrelevant for predicting MOAs. Different normalization techniques were applied on the gene expression and cell viability features in order to preserve their inherent distributions. 

Gene expression features were normalized using the 25th and 75th percentiles instead of Z-score normalization because it’s comparatively less sensitive to outliers. After subtracting the quantile, cell viability features were divided by the upper quantile + 4 because of the increased variability between the values. 

An additional 402 non-scored MOAs were provided in another dataset with the same samples. A correlation matrix between each non-scored MOA and the scored MOAs was done to determine which non-scored MOAs had the strongest correlation with scored MOAs. This information can be valuable since it suggests shared biological pathways and functions. 

Non-scored MOAs that had a maximum correlation greater than 0.9 were added as a target class. Additionally, we determined the rarest MOAs by counting those with fewer than 19 samples having a non-zero value. This information is used to determine class weights during training.

The neural network architecture consists of 1 dense layer with 4,906 neurons and 3 convolutional layers. Since convolutional layers require inputs to be of image size, the training data is reshaped from (1,4096) to (1,256,16). Stratified 5-fold cross validation with 25 epochs is used to track performance. 

To help prevent overfitting, an early stopping with a patience of 10 steps, max 2x2 pooling, and class weights were all implemented. As the model completes backpropagation, a higher weight is assigned to the error term associated with a rare MOA if it was misclassified. This is done to help prevent the algorithm from only predicting the common target classes. 

To evaluate performance, the cross entropy loss function was used, yielding a final log loss of 0.01787.

This project could be expanded by implementing a 2-D and 3-D representation with different hidden dimensions to possibly capture more complex patterns that may exist among the features.




