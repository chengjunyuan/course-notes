---
description: The Kaggle course named "Intro to Machine Learning"
---

# Intro to Machine Learning (Kaggle)

## Decision Tree

Decision trees partition data sets based on a given attribute possessed by the data. This step of capturing patterns is called **fitting** or **training** the model. The data used to **fit** the model is called the **training data**. After the model has been fitted, we can apply it to new data to **predict** features of the data based on the attribute that the model has been trained on. Our decision trees can be improved by increasing the number of layers, therefore increasing the granularity of our predictions.

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption><p>Copied from the Kaggle course.</p></figcaption></figure>

The point at the bottom of a tree where a prediction is made is called a **leaf**.

## Basic Exploratory Data Analysis

The first step in any ML project is to get to know the data better, which we do so using `pandas`. We first need to read and save our data file into a variable, and we can then use the `.describe()` function to get a quick summary of the data.

```python
import pandas as pd

file_path = "file_name.csv"
data = pd.read_csv(file_path)

data.describe()
```

The `.describe()` function gives us an eight-number summary, of the count, mean, standard deviation, minimum, maximum, median, and Q1 and Q3.

## Building Simple Models

We return to our data set to select both the variable to predict, as well as the variables to base the prediction off. We can then use the `scikit-learn` library to build a simple model. There are several steps we take to build a model:

1. Define: what type of model are we using? What parameters will it have?
2. Fit: train the model on some data
3. Predict: use the trained model to predict the values we want for new data.
4. Evaluate: determine how accurate the model's predictions are

```python
from sklearn.tree import DecisionTreeRegressor
import pandas as pd

file_path = "file_name.csv"
data = pd.read_csv(file_path)

feature_list = ['f1', 'f2', 'f3']
X = data[feature_list] # features for the decision tree
y = data.f4 # predicted variable

data_model = DecisionTreeRegressor(random_state=1) # random_state is a seed
data_model.fit(X, y)
predictions = data_model.predict(new_data)
```

## Model Validation

We want to validate our models, i.e., check that they actually make accurate predictions. One common problem people make is that they test their models on their training data, which is the data that was used to build the model. In such a case, they're obviously going to get ridiculously high test scores.

One metric we can use to summarize model quality is called **mean absolute error (MAE)**, which we can simply import from `sklearn` with the following code:

```python
from sklearn.metrics import mean_absolute_error

predictions = model.predict(X)
mean_absolute_error(y, predictions)
```

To resolve this problem, what we can do is to set aside a portion of our data during model building, which we'll use during our test phase. This set of data is known as the **validation data**. The `sklearn` library has a function called `train_test_split` that our data into pieces, which lets us generate our training and validation data easily.

```python
from sklearn.model_selection import train_test_split

train_X, val_Y, train_y, val_y = train_test_split(X, y, random_state=0)
model = DecisionTreeRegressor()
model.fit(train_X, train_y)
predictions = model.predict(val_X)
print(mean_absolute_error(val_y, predictions))
```

## Underfitting and Overfitting

**Overfitting** is what happens when our model matches the training data almost perfectly, but performs terribly on validation and real world data. **Underfitting** is what happens when our model fails to capture the underlying patterns in data, making it not much better than a random number generator when it comes to predictions.

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption><p>Taken from Kaggle course</p></figcaption></figure>

We therefore want to hit the sweet spot where the model can perform fairly well on both training and validation data. One way we can do so for our decision tree models is through limiting the tree depth, and the maximum number of leaves the tree can have. We can write a simple function that checks the MAE when we vary our tree depth, in order to find a nice tree depth to have.

```python
from sklearn.metrics import mean_absolute_error
from sklearn.tree import DecisionTreeRegressor

def get_mae(max_leaf_nodes, train_X, val_X, train_y, val_y):
    model = DecisionTreeRegressor(max_leaf_nodes=max_leaf_nodes, random_state=0)
    model.fit(train_X, train_y)
    preds_val = model.predict(val_X)
    mae = mean_absolute_error(val_y, preds_val)
    return(mae)
    
# compare MAE with differing values of max_leaf_nodes
for max_leaf_nodes in [5, 50, 500, 5000]:
    my_mae = get_mae(max_leaf_nodes, train_X, val_X, train_y, val_y)
    print("Max leaf nodes: %d  \t\t Mean Absolute Error:  %d" %(max_leaf_nodes, my_mae))
```

To actually limit the number of leaves, we use the `max_leaf_nodes` argument of the `DecisionTreeRegressor` function, such as:

```python
new_model = DecisionTreeRegressor(max_leaf_nodes=100)
```

## Random Forests

A **random forest** model is like a decision tree on crack - it runs many decision trees and averages their results in order to get a better predictive accuracy. Some sample code for a random forest would be:

```python
from sklearn.ensemble import RandomForestRegressor
from sklearn.metrics import mean_absolute_error

forest_model = RandomForestRegressor(random_state=1)
forest_model.fit(train_X, train_y)
melb_preds = forest_model.predict(val_X)
print(mean_absolute_error(val_y, melb_preds))
```
