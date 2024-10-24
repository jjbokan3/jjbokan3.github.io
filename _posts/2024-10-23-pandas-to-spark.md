# PySpark (.sql)
## Window
Used to apply rank, denserank, lag, and lead functions (similar to SQL statements).
<a href="https://spark.apache.org/docs/latest/api/python/reference/pyspark.sql/api/pyspark.sql.Window.html?highlight=window#pyspark.sql.Window">
<img src="https://upload.wikimedia.org/wikipedia/commons/thumb/c/c3/Python-logo-notext.svg/1920px-Python-logo-notext.svg.png" alt = "Python Logo" width="30" height="30">
</a>

Parts of window functions:
- **partitionBy** - Used to subset the data for more useful metrics pertaining to the subsets
- **orderBy** - Used to order a column because rank, lag, and lead need to have a certain order to work as expected

### Rank
```python
from pyspark.sql.window import Window

windowSpec = Window.partitionBy('col1').orderBy('col2') # Partition by col1 and order by col2
df_ranked = df.withColumn('rank').over(windowSpec) # Create a new column 'rank' based on partition above
```

### Lag / Lead
```python
# Define Window Specification 
windowSpec = Window.orderBy("Date") 
# Use lag and lead functions 
df_with_lag_lead = df.withColumn("PreviousDaySales", lag("Sales", 1).over(windowSpec)) \
						.withColumn("NextDaySales", lead("Sales", 1).over(windowSpec))
```

## spark.functions
### when
<a href="https://spark.apache.org/docs/latest/api/python/reference/pyspark.sql/api/pyspark.sql.functions.when.html?highlight=when">
<img src="https://upload.wikimedia.org/wikipedia/commons/thumb/c/c3/Python-logo-notext.svg/1920px-Python-logo-notext.svg.png" alt = "Python Logo" width="30" height="30">
</a>
Used similar to a case statement when assigning a value to a new column. Used along with otherwise(),

```python
df.withColumn('new_col', when(col('old_col') > 100, 'Yes').when(col('old_col') < 10, 'Kinda').otherwise('Nope'))
```


# Pandas to PySpark Cheat Sheet

## DataFrame Creation

### Pandas:
```python
import pandas as pd
pd.DataFrame({'A': [1, 2, 3], 'B': [4, 5, 6]})
```
### PySpark:
```python
from pyspark.sql import SparkSession
spark = SparkSession.builder.appName("example").getOrCreate()
df = spark.createDataFrame([(1, 4), (2, 5), (3, 6)], ["A", "B"])
```

## Reading Data

### Pandas (CSV):
```python
pd.read_csv("file.csv")
```
### PySpark (CSV):
```python
spark.read.csv("file.csv", header=True, inferSchema=True)
```
## Viewing Data

### Pandas:
```python
df.head()
```
### PySpark:
```python
df.show()
```
## Referencing Columns
### Pandas
```python
df['col_name']
```
### PySpark
```python
col('col_name') # Best Practice
'col_name'
df.col_name
```
## Data Selection

### Selecting Columns (Pandas):
```python
df['A']
```
### Selecting Columns (PySpark):
```python
df.select('A')
```
### Selecting Rows by Position (Pandas):
```python
df.iloc[0]
```
### Selecting Rows by Position (PySpark):
```python
df.take(1)
```
## Filtering Data

### Pandas:
```python
df[df['A'] > 2]
```
### PySpark:
```python
df.filter(df['A'] > 2)
```
## Grouping and Aggregating

### Pandas:
```python
df.groupby('A').sum()
```
### PySpark:
```python
from pyspark.sql import functions as F
df.groupBy('A').agg(F.sum('B'))
```
## Joining DataFrames

### Pandas:
```python
pd.merge(df1, df2, on='key')
```
### PySpark:
```python
df1.join(df2, df1.key == df2.key)
```
## Handling Missing Data

### Drop NA (Pandas):
```python
df.dropna()
```
### Drop NA (PySpark):
```scala
df.na.drop()
```
### Fill NA (Pandas):
```python
df.fillna(value)
```
### Fill NA (PySpark):
```python
df.na.fill(value)
```
## Sorting

### Pandas:
```python
df.sort_values(by='A')
```
### PySpark:
```python
df.sort(df.A) # Ascending (Default)
df.sort(df.A.desc()) # Descending
```
## Writing Data

### Pandas (CSV):
```python
df.to_csv("file.csv", index=False)
```
### PySpark (CSV):
```python
df.write.csv("file.csv", header=True)
```

> Very similar to Pandas
> - Sep
> - Encoding
> - Header
> - escapeQuotes
{: .prompt-note }

## Creating New Column
### Pandas
```python
df['new_col'] = df['old_col'].apply(lambda x: True if x == 'Joseph' else False)
```
### PySpark
```python
df = df.withColumn('new_col', when(col('old_col') == 'Joseph', True).otherwise(False))
```
Uses [[#when]] function to give various results based on condition
