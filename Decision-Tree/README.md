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
Exploratory Data Analysis: 
  - After running `df['Classes'].value_counts()`, because there is 386 samples of Neoplastic_Core vs 58 samples of Neoplastic_Periphery, there is an imbalance problem and we will need to resample the data.
![download](https://user-images.githubusercontent.com/62857660/159149297-67d177f4-529f-4e9e-bae3-729447d033f7.png)

Split Data
```
X = df.drop(['class'], axis=1)
y = df['class']

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size= 0.2, random_state= 42, stratify=y)
```

Feature Selection:
- There are many columns, I use feature importance and feature selection with LGB to narrow down the columns with 0 importance.
```
model = lgb.LGBMClassifier()
model.fit(X_train, y_train, early_stopping_rounds=100, eval_set = [(X_test, y_test)], verbose = 200)

feature_importances += model.feature_importances_

zero_features = list(feature_importances[feature_importances['importance'] == 0.0]['feature'])
```
- After I dropped the columns in `X_train` and `X_test` with 0 importance, I scaled the data with `scaler = StandardScaler()`.

Fit the model:
```
tree = DecisionTreeClassifier() 
tree.fit(X_train, y_train)
y_pred = tree.predict(X_test) 
```
Resample the Data:
- Resample the data, then run the same steps as above. I will compare the results later.
```
df_class1 = resample(df_resample[df_resample['class']=='Neoplastic_Core'],
               replace=True,
               n_samples=386,
               random_state=123)

df_class2 = resample(df_resample[df_resample['class']=='Neoplastic_Periphery'],
               replace=True,
               n_samples=386,
               random_state=123)

df_resample= pd.concat([df_class1, df_class2])
df_resample['class'].value_counts()
```
![download (1)](https://user-images.githubusercontent.com/62857660/159149532-906c6d5c-ceb6-4dcb-b146-27288169a60d.png)

Comparision:
- Use feature selection and fit the decision tree model with the resampled data.
- Now let's compare the two models.
![download (2)](https://user-images.githubusercontent.com/62857660/159149823-14b6114e-8898-4eff-bcac-f1dd752607b7.png)
![download (3)](https://user-images.githubusercontent.com/62857660/159149830-6a6ae6b0-6a5c-45f8-8154-f20158b79d63.png)


- Confusion matrix
![download (4)](https://user-images.githubusercontent.com/62857660/159149898-69d0ecf1-753d-4f08-8447-4949dbf4a4f2.png)
![download (5)](https://user-images.githubusercontent.com/62857660/159149900-9edc65d2-6d8b-48b6-be9f-06b34ffaf817.png)


![download (6)](https://user-images.githubusercontent.com/62857660/159149936-8c3264d6-dadc-49f9-a877-478b24d5b1c4.png)
![download (7)](https://user-images.githubusercontent.com/62857660/159149937-2c24bec7-9a7e-466b-aacc-3cf181094ded.png)









