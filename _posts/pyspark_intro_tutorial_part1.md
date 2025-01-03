
# **Introduction to PySpark: Part 1 - Setting Up and Understanding the Basics**

## **Overview**

Apache Spark is a powerful open-source engine for large-scale data processing, and PySpark is the Python API for Spark. This tutorial will give you a solid foundation in PySpark, starting with an introduction to the Spark ecosystem and leading into hands-on exercises that demonstrate PySpark's core functionalities.

### **In this Part 1 Tutorial, we will cover:**
1. What is PySpark and Why Use It?
2. Setting Up PySpark
3. Understanding the SparkSession
4. Working with DataFrames in PySpark

> **Prerequisites**: Basic knowledge of Python and data analysis concepts.

---

## **1. What is PySpark and Why Use It?**

Apache Spark is a framework for distributed data processing that allows you to process large datasets quickly by distributing the data across multiple nodes in a cluster. PySpark lets you use Spark’s power with Python, which is especially beneficial if you're familiar with Python’s data analysis libraries, such as Pandas.

### **Key Benefits of PySpark:**
- **Speed**: PySpark can process data in-memory, making it much faster than traditional disk-based processing systems.
- **Scalability**: Spark is designed to work on massive datasets and can scale horizontally across clusters.
- **Ease of Use**: Python API makes it accessible to Python developers.

---

## **2. Setting Up PySpark**

### **Option 1: Local Installation**

If you want to set up PySpark on your local machine, you’ll need:
- **Java Development Kit (JDK)** (version 8 or later)
- **Apache Spark**
- **PySpark** (can be installed via `pip install pyspark`)

#### **Basic Setup Steps**:

1. **Install Java**:
   ```bash
   sudo apt update
   sudo apt install openjdk-8-jdk
   ```
2. **Install PySpark**:
   ```bash
   pip install pyspark
   ```
3. **Verify Installation**:
   Open a Python terminal and type:
   ```python
   import pyspark
   from pyspark.sql import SparkSession

   spark = SparkSession.builder.master("local").appName("Introduction to PySpark").getOrCreate()
   print("SparkSession created:", spark)
   ```

### **Option 2: Using Google Colab**

If you prefer not to install anything locally, you can use Google Colab, which is pre-configured with PySpark. Run the following code in a Colab cell:

```python
!pip install pyspark
```

---

## **3. Understanding the SparkSession**

In PySpark, the `SparkSession` is the entry point to interact with Spark functionalities. It allows us to create and manipulate Spark DataFrames, perform operations, and manage the Spark cluster.

```python
from pyspark.sql import SparkSession

# Initialize SparkSession
spark = SparkSession.builder.master("local").appName("Introduction to PySpark").getOrCreate()

# Check the SparkSession
print("SparkSession Created:", spark)
```

### **Key Parameters of SparkSession**:
- `master`: Defines where the Spark job will run (e.g., “local” means it runs locally).
- `appName`: Sets a name for the application.

#### **Exercise: Creating a SparkSession**

1. **Open a Python script or Jupyter Notebook**.
2. **Create a SparkSession using the code above**.
3. **Print the Spark version** to verify the setup:
   ```python
   print("Spark version:", spark.version)
   ```

---

## **4. Working with DataFrames in PySpark**

PySpark DataFrames are similar to Pandas DataFrames, but they are optimized for distributed data processing. They are structured collections of data that allow you to perform SQL-like queries.

### **Basic DataFrame Operations**

#### 1. **Creating a PySpark DataFrame**

Let’s create a sample DataFrame from a Python dictionary:

```python
data = [("Alice", 29), ("Bob", 31), ("Cathy", 24)]
columns = ["Name", "Age"]

df = spark.createDataFrame(data, columns)
df.show()
```

#### 2. **Viewing DataFrame Schema**

Use `.printSchema()` to display the structure of your DataFrame:

```python
df.printSchema()
```

#### 3. **Basic DataFrame Operations**

   - **Selecting Columns**:
     ```python
     df.select("Name").show()
     ```
   - **Filtering Data**:
     ```python
     df.filter(df["Age"] > 25).show()
     ```
   - **Adding a New Column**:
     ```python
     df = df.withColumn("AgeAfter5Years", df["Age"] + 5)
     df.show()
     ```

#### 4. **Writing SQL Queries**

You can use SQL queries with Spark DataFrames by registering the DataFrame as a temporary view:

```python
df.createOrReplaceTempView("people")

result = spark.sql("SELECT Name, Age FROM people WHERE Age > 25")
result.show()
```

---

## **Hands-On Exercise**

### Objective: Create and manipulate a DataFrame of employees with the following data:

| Name     | Age | Department  |
|----------|-----|-------------|
| John     | 35  | Sales       |
| Sarah    | 40  | Engineering |
| Michael  | 30  | Marketing   |
| Jessica  | 28  | HR          |

1. **Create a DataFrame** using the above data.
2. **Add a new column** called `Seniority` that classifies employees as `"Senior"` if they are 35 or older, otherwise `"Junior"`.
3. **Filter** for employees in the `"Engineering"` department.

### Solution:

```python
# Step 1: Define data and columns
data = [("John", 35, "Sales"), ("Sarah", 40, "Engineering"), ("Michael", 30, "Marketing"), ("Jessica", 28, "HR")]
columns = ["Name", "Age", "Department"]

# Step 2: Create DataFrame
employees = spark.createDataFrame(data, columns)

# Step 3: Add 'Seniority' column
from pyspark.sql.functions import when

employees = employees.withColumn("Seniority", 
                                 when(employees["Age"] >= 35, "Senior").otherwise("Junior"))

# Step 4: Filter by 'Engineering' department
engineering_team = employees.filter(employees["Department"] == "Engineering")

# Show results
engineering_team.show()
```

---

## **Summary**

In this tutorial, you learned:
- What PySpark is and why it’s used.
- How to set up and configure PySpark.
- Basic operations with SparkSession and DataFrames.

In **Part 2**, we’ll dive deeper into DataFrame transformations and actions, essential PySpark functions, and performance optimizations.

---

This concludes Part 1 of the PySpark tutorial series.
