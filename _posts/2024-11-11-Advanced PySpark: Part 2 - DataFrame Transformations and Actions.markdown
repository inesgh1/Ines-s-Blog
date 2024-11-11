---
layout: post
title:Advanced PySpark: Part 2 - DataFrame Transformations and Actions
date: 2024-11-11 
description: This first part covers foundational concepts, basic setup, and hands-on exercises to give users a practical feel for working with PySpark.
img:  PysparkSeries.png # Add image post (optional)
tags: [Programming, Learn,pyspark] # add tag
---
# **Advanced PySpark: Part 2 - DataFrame Transformations and Actions**

Welcome back to our PySpark tutorial series! In Part 1, we covered the basics of setting up PySpark, creating a SparkSession, and performing basic operations on DataFrames. In **Part 2**, we’ll dive deeper into **DataFrame transformations** and **actions**—two core concepts for working with data in PySpark.

### **In this Part 2 Tutorial, you will learn:**
1. The Difference Between Transformations and Actions
2. Common DataFrame Transformations
3. Frequently Used DataFrame Actions
4. Hands-On Exercises

Let’s get started!

---

## **1. Transformations vs. Actions in PySpark**

- **Transformations** create new DataFrames from existing ones but are *lazy*, meaning they don’t execute until an action is called. Examples include `select()`, `filter()`, `groupBy()`, and `withColumn()`.
- **Actions** trigger the execution of transformations and return a result. Examples include `collect()`, `show()`, and `count()`.

The *lazy evaluation* design allows Spark to optimize the data processing pipeline and improve performance.

---

## **2. Common DataFrame Transformations**

### a. **select()**

`select()` is used to choose specific columns from a DataFrame.

```python
# Select specific columns
df.select("Name", "Age").show()
```

### b. **filter()**

`filter()` allows you to retrieve rows based on conditions. This is similar to SQL’s `WHERE` clause.

```python
# Filter rows where Age is greater than 30
df.filter(df["Age"] > 30).show()
```

### c. **withColumn()**

`withColumn()` adds or updates a column in the DataFrame.

```python
from pyspark.sql.functions import col

# Add a new column with incremented Age by 5
df = df.withColumn("AgePlus5", col("Age") + 5)
df.show()
```

### d. **groupBy() and agg()**

`groupBy()` is used for grouping data, often followed by `agg()` for aggregate functions.

```python
# Calculate average Age per Department
df.groupBy("Department").agg({"Age": "avg"}).show()
```

### e. **join()**

`join()` combines DataFrames based on a common column.

```python
# Assuming another DataFrame 'df2' with a common column 'ID'
df.join(df2, on="ID", how="inner").show()
```

---

## **3. Common DataFrame Actions**

### a. **show()**

`show()` is useful for quickly displaying rows in a DataFrame.

```python
# Show the first 5 rows
df.show(5)
```

### b. **collect()**

`collect()` retrieves all data as a list of rows, often used when working with smaller datasets.

```python
# Collect all rows
data = df.collect()
```

### c. **count()**

`count()` returns the number of rows in the DataFrame.

```python
# Get the row count
row_count = df.count()
```

### d. **first() and head()**

`first()` and `head()` are used to retrieve the first row or a specific number of rows.

```python
# Get the first row
first_row = df.first()

# Get the first 3 rows
first_three_rows = df.head(3)
```

### e. **describe()**

`describe()` generates summary statistics for numeric columns.

```python
# Display summary statistics
df.describe().show()
```

---

## **4. Hands-On Exercise**

Let’s practice using transformations and actions on a sample dataset. Create a DataFrame and apply the following operations.

### **Dataset: Employee Details**

| Name     | Age | Department  | Salary |
|----------|-----|-------------|--------|
| John     | 35  | Sales       | 70000  |
| Sarah    | 40  | Engineering | 120000 |
| Michael  | 30  | Marketing   | 60000  |
| Jessica  | 28  | HR          | 75000  |
| David    | 45  | Engineering | 130000 |

```python
# Sample data
data = [("John", 35, "Sales", 70000), ("Sarah", 40, "Engineering", 120000),
        ("Michael", 30, "Marketing", 60000), ("Jessica", 28, "HR", 75000),
        ("David", 45, "Engineering", 130000)]
columns = ["Name", "Age", "Department", "Salary"]

# Create DataFrame
employees = spark.createDataFrame(data, columns)
employees.show()
```

### **Tasks:**

1. **Select** only the "Name" and "Salary" columns.
   ```python
   employees.select("Name", "Salary").show()
   ```

2. **Filter** employees with a salary greater than 80,000.
   ```python
   high_earners = employees.filter(employees["Salary"] > 80000)
   high_earners.show()
   ```

3. **Add a new column** called `Seniority`, classifying employees as "Senior" if they are older than 40, otherwise "Junior."
   ```python
   from pyspark.sql.functions import when

   employees = employees.withColumn("Seniority", when(employees["Age"] > 40, "Senior").otherwise("Junior"))
   employees.show()
   ```

4. **Group by** department and calculate the **average salary**.
   ```python
   avg_salary_by_dept = employees.groupBy("Department").agg({"Salary": "avg"})
   avg_salary_by_dept.show()
   ```

5. **Sort** the DataFrame by salary in descending order.
   ```python
   employees.sort(employees["Salary"].desc()).show()
   ```

---

## **Summary**

In Part 2, you learned about:
- Key **DataFrame transformations** like `select()`, `filter()`, and `join()`.
- Essential **DataFrame actions** such as `show()`, `count()`, and `collect()`.

This hands-on practice should give you a good foundation in PySpark transformations and actions, enabling you to perform complex data manipulations efficiently.

---

In **Part 3**, we’ll focus on **performance optimization** and explore **RDDs** (Resilient Distributed Datasets) for advanced data processing. Stay tuned!
