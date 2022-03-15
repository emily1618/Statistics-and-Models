#### Use the K-NN machine learning algorithm to Classify Single Cell RNA-seq in Glioblastoma patients.


![dna-concept-illustration_114360-5582](https://user-images.githubusercontent.com/62857660/155894426-43d677c5-3212-4635-8dcb-0b1ea69a2111.jpg)


### Introduction:
Recent advances in single-cell RNA sequencing technologies enable deep insights
into cellular development, gene regulation, and phenotypic diversity by
measuring gene expression for thousands of cells in a single experiment. This
results in high-throughput datasets and requires the development of new types of
computational approaches to extract the useful and valuable underlying biological
information of individual cells in heterogeneous biological populations. This will begin with a K-NN and later use a more advance deep learning
method to identify the predictor variable.

### Data: Data_Glioblastoma5Patients_SC.csv 
- Data consist of 430 rows and 5949 columns
- ![11](https://user-images.githubusercontent.com/62857660/158415896-cbf2d7bf-8b36-408d-9b4a-28883f6d11fe.JPG)


### The Process:

- Exploratory Data Analysis. 
  - After running `df['Classes'].value_counts()`, I see there is class imbalance problem, which I will address in a subsequent notebook. 
  - There are many columns, we can use feature importance and feature selection to narrow down the columns with minimal importance
` 
import lightgbm as lgb

feature_importances = np.zeros(X_train.shape[1])

model = lgb.LGBMClassifier(objective='multiclass', boosting_type = 'goss', n_estimators = 10000, class_weight = 'balanced')
`
  - Dropping columns with correlation > 0.90
`
threshold = 0.90

corr_matrix =pd.DataFrame(X_train).corr(method='pearson').abs()

upper = corr_matrix.where(np.triu(np.ones(corr_matrix.shape), k=1).astype(np.bool))

to_drop = [column for column in upper.columns if any(upper[column] > threshold)]

print('There are %d columns to remove.' % (len(to_drop))
`

- The ML technique required and the performance evaluation
with and without parameter optimization.
  - Normalize the data
`
from sklearn.preprocessing import StandardScaler
scaler = StandardScaler()
scaler.fit(X_train)

X_train_scaled = scaler.transform(X_train)
X_test_scaled = scaler.transform(X_test)
`
  - Fit the data
  - Hyperparameter tuning
  - Run the models


### The result:
  - Using a confusion matrix, classficiation report and accuracy score to evaluate your model
`
from sklearn.metrics import classification_report
`

