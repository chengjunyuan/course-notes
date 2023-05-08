---
description: The Kaggle course named "Intermediate Machine Learning"
---

# Intermediate Machine Learning

## Introduction

In this section, we go through:

* Tackle data problems (e.g., missing values, categorical variables)
* Design pipelines to improve the quality of machine learning code
* Perform cross-validation
* Use XGBoost
* Avoid data leakage

## Missing Values

There are several approaches to data with missing entries.

1. Drop columns with missing values
2. Imputation: filling in missing values with some number (e.g., the mean or median of that column)
3. Augmented imputation: add an extra column to denote imputed entries - this helps occasionally.

If we drop values, we want to drop the same columns from both our training and validation data sets. Do take note that dropping columns will actually affect our results because it reduces the number of features that the model can learn from.

```python
# Get names of columns with missing values
cols_with_missing = [col for col in X_train.columns
                     if X_train[col].isnull().any()]

# Drop columns in training and validation data
reduced_X_train = X_train.drop(cols_with_missing, axis=1)
reduced_X_valid = X_valid.drop(cols_with_missing, axis=1)

print("MAE from Approach 1 (Drop columns with missing values):")
print(score_dataset(reduced_X_train, reduced_X_valid, y_train, y_valid))
```

We can otherwise use the inbuilt functions of `sklearn` to fill up the data.

```python
from sklearn.impute import SimpleImputer

# Imputation
my_imputer = SimpleImputer()
imputed_X_train = pd.DataFrame(my_imputer.fit_transform(X_train))
imputed_X_valid = pd.DataFrame(my_imputer.transform(X_valid))

# Imputation removed column names; put them back
imputed_X_train.columns = X_train.columns
imputed_X_valid.columns = X_valid.columns

print("MAE from Approach 2 (Imputation):")
print(score_dataset(imputed_X_train, imputed_X_valid, y_train, y_valid))
```

Thus far, we've learnt how to perform a little data cleaning. The conclusion for now is that if there is very little missing data from the column, we impute, and if there is a lot of missing data from the column, we remove the entire column.

## Categorical Variables

A **categorical variable** is a variable that takes in a limited number of inputs. There are several approaches to handling categorical variables in `sklearn`.

1. Drop categorical variables: simply remove them from the dataset
2. Ordinal encoding: assign an integer to each value the categorical variable can take on
3. One-hot encoding: Add new columns representing the different values of the categorical variable, and assign a boolean 0 or 1 to each column

One-hot encoding is usually used on **nominal variables**, which do not have an intrinsic ordering, as **ordinal variables** do. However, one-hot encoding should not be used on nominal variables which can take on many different values, as it will clog up the table and make it very messy.

We start by checking the data types of the columns in our data set, to find out which ones are categorical. We can then choose which columns to drop in order to train our model.

```python
# Get list of categorical variables
s = (X_train.dtypes == 'object')
object_cols = list(s[s].index)

print("Categorical variables:")
print(object_cols)

drop_X_train = X_train.select_dtypes(exclude=['object'])
drop_X_valid = X_valid.select_dtypes(exclude=['object'])

print("MAE from Approach 1 (Drop categorical variables):")
print(score_dataset(drop_X_train, drop_X_valid, y_train, y_valid))
```

In `sklearn`, we can also use the inbuilt `OrdinalEncoder` to encode our ordinal variables. We can either choose to let the function assign random integers to the different values, or provide our own custom labels for them.

```python
from sklearn.preprocessing import OrdinalEncoder

# Make copy to avoid changing original data 
label_X_train = X_train.copy()
label_X_valid = X_valid.copy()

# Apply ordinal encoder to each column with categorical data
ordinal_encoder = OrdinalEncoder()
label_X_train[object_cols] = ordinal_encoder.fit_transform(X_train[object_cols])
label_X_valid[object_cols] = ordinal_encoder.transform(X_valid[object_cols])

print("MAE from Approach 2 (Ordinal Encoding):") 
print(score_dataset(label_X_train, label_X_valid, y_train, y_valid))
```

Finally, we can use the inbuilt `OneHotEncoder` to perform one-hot encoding on our data as well.

```python
from sklearn.preprocessing import OneHotEncoder

# Apply one-hot encoder to each column with categorical data
OH_encoder = OneHotEncoder(handle_unknown='ignore', sparse=False)
OH_cols_train = pd.DataFrame(OH_encoder.fit_transform(X_train[object_cols]))
OH_cols_valid = pd.DataFrame(OH_encoder.transform(X_valid[object_cols]))

# One-hot encoding removed index; put it back
OH_cols_train.index = X_train.index
OH_cols_valid.index = X_valid.index

# Remove categorical columns (will replace with one-hot encoding)
num_X_train = X_train.drop(object_cols, axis=1)
num_X_valid = X_valid.drop(object_cols, axis=1)

# Add one-hot encoded columns to numerical features
OH_X_train = pd.concat([num_X_train, OH_cols_train], axis=1)
OH_X_valid = pd.concat([num_X_valid, OH_cols_valid], axis=1)

# Ensure all columns have string type
OH_X_train.columns = OH_X_train.columns.astype(str)
OH_X_valid.columns = OH_X_valid.columns.astype(str)

print("MAE from Approach 3 (One-Hot Encoding):") 
print(score_dataset(OH_X_train, OH_X_valid, y_train, y_valid))
```

Overall, one way of processing the data would be:

```python
from sklearn.preprocessing import OneHotEncoder

OH_encoder = OneHotEncoder(handle_unknown='ignore', sparse=False)
OH_cols_train = pd.DataFrame(OH_encoder.fit_transform(X_train[low_cardinality_cols]))
OH_cols_valid = pd.DataFrame(OH_encoder.transform(X_valid[low_cardinality_cols]))

# One-hot encoding removed index; put it back
OH_cols_train.index = X_train.index
OH_cols_valid.index = X_valid.index

# Remove categorical columns (will replace with one-hot encoding)
num_X_train = X_train.drop(object_cols, axis=1)
num_X_valid = X_valid.drop(object_cols, axis=1)

# Add one-hot encoded columns to numerical features
OH_X_train = pd.concat([num_X_train, OH_cols_train], axis=1)
OH_X_valid = pd.concat([num_X_valid, OH_cols_valid], axis=1)

# Ensure all columns have string type
OH_X_train.columns = OH_X_train.columns.astype(str)
OH_X_valid.columns = OH_X_valid.columns.astype(str)
```

## Pipelines

We usually use pipelines to organize our data preprocessing and model building. There are three components to our data processing pipeline.

1. Define preprocessing steps
2. Define the model
3. Create and evaluate pipeline

During preprocessing, the standard things we want to do will be impute missing values and to apply one-hot encoding to categorical data. This transforms all our data into nice to work with numerical variables. We then initialize a random forest model, and then write a `Pipeline` class to put everything together.

```python
from sklearn.compose import ColumnTransformer
from sklearn.pipeline import Pipeline
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import OneHotEncoder

# Preprocessing for numerical data
numerical_transformer = SimpleImputer(strategy='constant')

# Preprocessing for categorical data
categorical_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='most_frequent')),
    ('onehot', OneHotEncoder(handle_unknown='ignore'))
])

# Bundle preprocessing for numerical and categorical data
preprocessor = ColumnTransformer(
    transformers=[
        ('num', numerical_transformer, numerical_cols),
        ('cat', categorical_transformer, categorical_cols)
    ])
    
##################

from sklearn.ensemble import RandomForestRegressor

model = RandomForestRegressor(n_estimators=100, random_state=0)

##################

from sklearn.metrics import mean_absolute_error

# Bundle preprocessing and modeling code in a pipeline
my_pipeline = Pipeline(steps=[('preprocessor', preprocessor),
                              ('model', model)
                             ])

# Preprocessing of training data, fit model 
my_pipeline.fit(X_train, y_train)

# Preprocessing of validation data, get predictions
preds = my_pipeline.predict(X_valid)

# Evaluate the model
score = mean_absolute_error(y_valid, preds)
print('MAE:', score)
```

## Cross-Validation

In **cross-validation**, we run our modeling process on different subsets of the data to get multiple measures of model quality. For example, we could split the data equally into five groups, known as **folds**. For smaller datasets, where extra computational burden doesn't matter so much, you should run cross-validation. For larger datasets, a single validation set is sufficient.

We can use the `cross_val_score` function in `sklearn`, and set the number of folds with the `cv` parameter.

```python
from sklearn.model_selection import cross_val_score

# Multiply by -1 since sklearn calculates *negative* MAE
scores = -1 * cross_val_score(my_pipeline, X, y,
                              cv=5,
                              scoring='neg_mean_absolute_error')

print("MAE scores:\n", scores)
```

## XGBoost



## Data Leakage
