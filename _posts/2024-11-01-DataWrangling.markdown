---
layout: post
title: Data Wrangling for Machine Learning – Preparing Data for AI Success
date: 2024-11-01 
description: This workshop presented as part of contribution to Wiempower Event
img: DATA&AI.png # Add image post (optional)
tags: [Programming, Learn,python,data] # add tag
---

# Workshop: Data Wrangling for Machine Learning – Preparing Data for AI Success

---

## Opening 

### Introduction
Welcome, everyone, to today’s workshop on data wrangling for machine learning! My name is **Ines**, and I work as a data engineer with a focus on transforming raw data into insights. Today, we’ll learn how to prepare data for AI applications, focusing on key wrangling techniques that can make or break a machine learning model.

### Workshop Objectives
Our goal is to understand the steps involved in preparing data for machine learning. We’ll cover cleaning, transforming, and engineering data using the Iris dataset. By the end, you’ll have a solid foundation in data wrangling, and we’ll even try a basic machine learning model to see how this data preparation impacts performance!

---

## 1. Why Data Wrangling? 

### Discussion
Let’s start with a question: **why do you think data preparation is crucial for machine learning?** What kinds of problems do we need to fix in data?

Data wrangling is a critical first step because machine learning models rely on clean, well-structured data to make accurate predictions. If we feed a model low-quality data, the results can be highly inaccurate, leading to unreliable outcomes.

### Example
For instance, think of how GPS systems rely on real-time, accurate data to provide directions. If the data is outdated or missing, the GPS will lead you astray. Similarly, for machine learning, inaccurate data can produce inaccurate predictions.

---
## 2.Understanding Data Wrangling

# Data Wrangling: Transforming Raw Data into Usable Formats

Data wrangling, also known as data munging, is the process of converting raw data into a usable format. This involves several key steps, including data cleaning, data transformation, and data enrichment. The goal is to ensure that the data is accurate, consistent, and ready for analysis.

---

## Key Steps in Data Wrangling

1. **Data Collection**: Gather data from various sources, such as databases, APIs, and web scraping. Ensure that the data is relevant to the problem at hand.

2. **Data Cleaning**: Identify and rectify errors in the dataset. This includes handling missing values, removing duplicates, and correcting inconsistencies.

3. **Data Transformation**: Convert data into a suitable format for analysis. This may involve normalizing values, encoding categorical variables, and aggregating data.

4. **Data Enrichment**: Enhance the dataset by adding additional information from external sources. This can provide more context and improve the quality of the analysis.

5. **Data Validation**: Ensure that the cleaned and transformed data meets the necessary quality standards. This step is crucial to avoid biases and inaccuracies in the model.

---

## Best Practices for Data Wrangling

- **Automate Where Possible**: Utilize tools and libraries that can automate repetitive tasks in data wrangling, such as Pandas in Python or dplyr in R.

- **Document Your Process**: Keep a record of the steps taken during data wrangling. This documentation will be invaluable for reproducibility and for future reference.

- **Iterate and Refine**: Data wrangling is not a one-time process. Continuously refine your approach based on feedback and new insights.

- **Engage with Domain Experts**: Collaborate with stakeholders who understand the data context. Their insights can guide the wrangling process and improve the relevance of the data.




## 3. Tools for Data Wrangling 

### Explanation
For this workshop, we’ll use Python’s `pandas` and `scikit-learn` libraries. `pandas` is popular for data manipulation, while `scikit-learn` provides a range of tools for preparing data and building models.

### Code Setup
Let’s start by importing the libraries and loading the Iris dataset, a small dataset of flower characteristics. If you’re coding along, you can use Jupyter Notebook or Google Colab.

```python
import pandas as pd
from sklearn.preprocessing import StandardScaler, LabelEncoder
from sklearn.datasets import load_iris
```

### Load Dataset
Let’s load the Iris dataset and display the first few rows.

```python
iris = load_iris()
data = pd.DataFrame(data=iris.data, columns=iris.feature_names)
data['species'] = iris.target
data.head()
```

The dataset includes measurements like sepal length, sepal width, petal length, and petal width, along with a target variable, species.

---

## 3. Data Cleaning 

**Objective**: Handle missing values, duplicates, and data type issues.


Let’s start with cleaning. Cleaning involves handling missing values, removing duplicates, and ensuring correct data types. Although our dataset is relatively clean, we’ll go through these steps for practice.

### 1. Identify Missing Values
First, let’s check for missing values.

```python
data.isnull().sum()
```

In this dataset, there are no missing values, but in real projects, you might handle missing data by removing rows, filling in values, or using algorithms to estimate missing information.

### 2. Remove Duplicates
Next, we’ll remove duplicates.

```python
data.drop_duplicates(inplace=True)
```

### 3. Correct Data Types
Finally, let’s check data types.

```python
data.info()
```

In other cases, correcting data types may be necessary.

---

## 4. Data Transformation 

**Objective**: Scale numerical data and encode categorical data.

### 1. Scaling Features
Scaling standardizes features to have a mean of 0 and standard deviation of 1.

```python
scaler = StandardScaler()
data[['sepal length (cm)', 'sepal width (cm)', 'petal length (cm)', 'petal width (cm)']] = scaler.fit_transform(
    data[['sepal length (cm)', 'sepal width (cm)', 'petal length (cm)', 'petal width (cm)']]
)
```

### 2. Encoding Categorical Data
We’ll encode the species column to numbers.

```python
label_encoder = LabelEncoder()
data['species'] = label_encoder.fit_transform(data['species'])
```

---

## 5. Feature Engineering 

**Objective**: Create new features to reveal hidden patterns.

### 1. Create a New Feature: Sepal Ratio
Create a feature called `sepal_ratio`.

```python
data['sepal_ratio'] = data['sepal length (cm)'] / data['sepal width (cm)']
```

### 2. Create Interaction Feature: Petal Ratio
Create a feature called `petal_ratio`.

```python
data['petal_ratio'] = data['petal length (cm)'] / data['petal width (cm)']
```

---

## 6. Preparing for Machine Learning 

**Objective**: Finalize the dataset and run a simple model.

### 1. Separate Features and Target Variable
Split the data into features and target variable.

```python
features = data.drop(columns=['species'])
target = data['species']
```

### 2. Train-Test Split and Simple Model
We’ll split the data into a training set and a test set.

```python
from sklearn.model_selection import train_test_split
from sklearn.ensemble import RandomForestClassifier
from sklearn.metrics import accuracy_score

X_train, X_test, y_train, y_test = train_test_split(features, target, test_size=0.2, random_state=42)
model = RandomForestClassifier()
model.fit(X_train, y_train)

predictions = model.predict(X_test)
accuracy = accuracy_score(y_test, predictions)
print(f'Accuracy: {accuracy * 100:.2f}%')
```

With this accuracy score, we can see how well the model performed.

---

## 7. Q&A and Wrap-Up 

### Summary
We’ve gone through each stage of data wrangling, from cleaning to feature engineering, and even applied a basic model to see the impact of our work. Data wrangling may seem tedious, but it’s essential for effective machine learning.
Access the workshop notebook [here](https://colab.research.google.com/drive/1nwIgxHmwwtP-i0kwEuxjVx-AwxdryITU?usp=sharing).

### Closing
Thank you for joining! I hope this workshop gave you a strong foundation in data wrangling. With practice, these skills will allow you to prepare data for any AI or machine learning project.

---

