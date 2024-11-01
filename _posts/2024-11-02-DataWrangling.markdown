---
layout: post
title: Build a Paraphrasing web application using flask and Pegasus model
date: 2022-09-21 
description: You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. # Add post description (optional)
img: how-to-start.jpg # Add image post (optional)
tags: [Programming, Learn,flask,transformers] # add tag
---
# Workshop: Data Wrangling for Machine Learning – Preparing Data for AI Success

---

## Opening (5 mins)

### Introduction
Welcome, everyone, to today’s workshop on data wrangling for machine learning! My name is **[Your Name]**, and I work as a data engineer with a focus on transforming raw data into insights. Today, we’ll learn how to prepare data for AI applications, focusing on key wrangling techniques that can make or break a machine learning model.

### Workshop Objectives
Our goal is to understand the steps involved in preparing data for machine learning. We’ll cover cleaning, transforming, and engineering data using the Iris dataset. By the end, you’ll have a solid foundation in data wrangling, and we’ll even try a basic machine learning model to see how this data preparation impacts performance!

---

## 1. Why Data Wrangling? (10 mins)

### Discussion
Let’s start with a question: **why do you think data preparation is crucial for machine learning?** What kinds of problems do we need to fix in data?

Data wrangling is a critical first step because machine learning models rely on clean, well-structured data to make accurate predictions. If we feed a model low-quality data, the results can be highly inaccurate, leading to unreliable outcomes.

### Example
For instance, think of how GPS systems rely on real-time, accurate data to provide directions. If the data is outdated or missing, the GPS will lead you astray. Similarly, for machine learning, inaccurate data can produce inaccurate predictions.

---

## 2. Tools for Data Wrangling (10 mins)

### Explanation
For this workshop, we’ll use Python’s `pandas` and `scikit-learn` libraries. `pandas` is popular for data manipulation, while `scikit-learn` provides a range of tools for preparing data and building models.

### Code Setup
Let’s start by importing the libraries and loading the Iris dataset, a small dataset of flower characteristics. If you’re coding along, you can use Jupyter Notebook or Google Colab.

```python
import pandas as pd
from sklearn.preprocessing import StandardScaler, LabelEncoder
from sklearn.datasets import load_iris
