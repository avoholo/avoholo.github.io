---
title: "Spark SQL Setup"
subtitle: apache
date: 2022-11-16 09:00:00 +07:00
modified: 2022-11-18 09:00:00 +07:00
tags: [Spark]
description: Spark SQL Commands.
image: "/_posts/Spark_basic_cmd/default_post_image.png"
---

<figure>
<img src="https://raw.githubusercontent.com/avoholo/avoholo.github.io/master/_posts/Spark_multinode_setup/default_post_image.png" alt="default_post_image">
<figcaption>Fig 1. Apache Spark</figcaption>
</figure>

이번 포스트에선 `SparkSQL`을 통해  **Structured Processing**을 수행해 보고 Spark RDD API와 **어떤 차이점이 있는지 알아볼 예정**이다.

### Scala

#### 1. SparkSession

~~~bash
import org.apache.spark.sql.SparkSession

val path = "/data/data/SQL/Data/views/views.csv"

val spark = SparkSession
  .builder()
  .appName("Spark SQL basic example")
  .config("spark.some.config.option", "some-value")
  .getOrCreate()

// For implicit conversions - converting RDDs to DataFrames
import spark.implicits._
~~~
*`Spark-Shell` 실행시 **SparkContext**는 `sc`라고 정의되어있고 **Spark-Session**은 `spark`라고 되어 있다.

<hr style="height:30px; visibility:hidden;" />

#### 2. Create Dataframes

~~~scala
val df = spark.read.format("csv").option("header", "true").load(path)
df.show()
+----------+---------+---------+----------+
|article_id|author_id|viewer_id| view_date|
+----------+---------+---------+----------+
|         1|        3|        5|2019-08-01|
|         1|        3|        6|2019-08-02|
|         2|        7|        7|2019-08-01|
|         2|        7|        6|2019-08-02|
|         4|        7|        1|2019-07-22|
|         3|        4|        4|2019-07-21|
|         3|        4|        4|2019-07-21|
+----------+---------+---------+----------+

// 사전에 정의한 Schema를 사용하고 싶다면 아래 방법을 사용한다.
import org.apache.spark.sql.types.{StructType, StructField, StringType, IntegerType, DateType, BooleanType, YearMonthIntervalType};

val schema = StructType(
  List(
    StructField("article_id", IntegerType, true),
    StructField("author_id", IntegerType, true),
	StructField("viewer_id", IntegerType, true),
	StructField("view_date", DateType, true)
  )
)

var df = spark.read.format("csv").schema(schema).option("header", "true").load(path)
~~~

<hr style="height:30px; visibility:hidden;" />

<figure>
<img src="https://raw.githubusercontent.com/avoholo/avoholo.github.io/master/_posts/Spark_sparksql/1.png" alt="cheat_sheet">
<figcaption>Fig 2. Spark Read Write Cheat Sheet</figcaption>
</figure>

<br>

#### 3. Dataframe vs Scala Datasets

Spark에서 `Dataframe`은 단순히 Row로 이루어진 Scala & Java API의 Dataset 이기에 아래 예시는 **Untyped Transformation**이다. Scala `Dataset`은 아래에서 추가로 다룰거지만 RDD와 비슷한 개념이다. 

대신 다른점이 한가지 있는데:

- `RDD`는 `Java Serializer`(kyro)를 사용하는대신 
- `Dataset`은 `Specialized Encoder`를 사용하여 직렬화한다.

~~~scala
import spark.implicits._

// Spark SQL Method #1
df.printSchema()
root
 |-- article_id: integer (nullable = true)
 |-- author_id: integer (nullable = true)
 |-- viewer_id: integer (nullable = true)
 |-- view_date: date (nullable = true)

// Scala Method #2
df.dtypes.foreach(f=>println(f._1+","+f._2))
// article_id,IntegerType
// author_id,IntegerType
// viewer_id,IntegerType
// view_date,DateType

// Select only the "article_id" column
 df.select("article_id").show()
+----------+
|article_id|
+----------+
|         1|
|         1|
|         2|
|         2|
|         4|
|         3|
|         3|
+----------+

// Select all, but increment the article by 1
df.select($"view_date", $"article_id" + 1).show()
+----------+----------------+
| view_date|(article_id + 1)|
+----------+----------------+
|2019-08-01|               2|
|2019-08-02|               2|
|2019-08-01|               3|
|2019-08-02|               3|
|2019-07-22|               5|
|2019-07-21|               4|
|2019-07-21|               4|
+----------+----------------+

// Select article_id larger than 2
df.filter($"article_id" > 2).show()
+----------+---------+---------+----------+
|article_id|author_id|viewer_id| view_date|
+----------+---------+---------+----------+
|         4|        7|        1|2019-07-22|
|         3|        4|        4|2019-07-21|
|         3|        4|        4|2019-07-21|
+----------+---------+---------+----------+

// Count article by id
df.groupBy("article_id").count().show()
+----------+-----+
|article_id|count|
+----------+-----+
|         1|    2|
|         3|    2|
|         4|    1|
|         2|    2|
+----------+-----+
~~~

<hr style="height:30px; visibility:hidden;" />

#### 4. With SQL

~~~scala
// Temporary
df.createOrReplaceTempView("views")

// Global Temporary
df.createGlobalTempView("views")

// SQL
var sql=("""
    SELECT * 
    FROM VIEWS
""")

// Option 1. show results directly
spark.sql(sql).show()

// Option 2. save output and show()
dfsql = spark.sql(sql)
dfsql.show()
~~~

<br>

### Python

#### 1. SparkSession

~~~python
from pyspark.sql import SparkSession

spark = SparkSession\
        .builder\
        .appName('Python Spark SQL example')\
        .config('spark.some.config.option', 'some-value')\
        .getOrCreate()

# sparkContext 객체 생성
sc = spark.sparkContext

path = "/data/data/SQL/Data/colors/multiline.json"
df = spark.read.option("multiline", "true").json(path)
~~~

<hr style="height:30px; visibility:hidden;" />

#### 2. Create Dataframes

~~~python
# show schema with printSchema()
df.printSchema()
root
 |-- City: string (nullable = true)
 |-- RecordNumber: long (nullable = true)
 |-- State: string (nullable = true)
 |-- ZipCodeType: string (nullable = true)
 |-- Zipcode: long (nullable = true)
    
df.show()
+-------------------+------------+-----+-----------+-------+
|               City|RecordNumber|State|ZipCodeType|Zipcode|
+-------------------+------------+-----+-----------+-------+
|PASEO COSTA DEL SUR|           2|   PR|   STANDARD|    704|
|       BDA SAN LUIS|          10|   PR|   STANDARD|    709|
+-------------------+------------+-----+-----------+-------+
~~~



json type은 데이터가 `[]` 안에 들어가있지 않으면 **multiline**으로 인식하지 않아 1개의 Row만 읽는다.

~~~scala
[{
  "RecordNumber": 2,
  "Zipcode": 704,
  "ZipCodeType": "STANDARD",
  "City": "PASEO COSTA DEL SUR",
  "State": "PR"
},
{
  "RecordNumber": 10,
  "Zipcode": 709,
  "ZipCodeType": "STANDARD",
  "City": "BDA SAN LUIS",
  "State": "PR"
}]
~~~

<hr style="height:30px; visibility:hidden;" />

#### 3. Dataframe

~~~python
df.select("Zipcode").show()
+-------+
|Zipcode|
+-------+
|    704|
|    709|
+-------+

df.select(df['City'], df['RecordNumber'] + 1).show()
+-------------------+------------------+
|               City|(RecordNumber + 1)|
+-------------------+------------------+
|PASEO COSTA DEL SUR|                 3|
|       BDA SAN LUIS|                11|
+-------------------+------------------+


df.filter(df['RecordNumber'] > 9).show()
+------------+------------+-----+-----------+-------+
|        City|RecordNumber|State|ZipCodeType|Zipcode|
+------------+------------+-----+-----------+-------+
|BDA SAN LUIS|          10|   PR|   STANDARD|    709|
+------------+------------+-----+-----------+-------+

df.groupBy("RecordNumber").count().show()
~~~

<br>

#### 4. With SQL

~~~scala
// Temporary
df.createOrReplaceTempView("multi")

// Global Temporary
df.createGlobalTempView("multi")

// SQL
sql=("""
    SELECT * 
    FROM MULTI
""")

// Option 1. show results directly
spark.sql(sql).show()

// Option 2. save output and show()
dfsql = spark.sql(sql)
dfsql.show()
~~~

<br>



### Java

#### 1. SparkSession

~~~java
import org.apache.spark.sql.SparkSession;

SparkSession spark = SparkSession
  .builder()
  .appName("Java Spark SQL example")
  .config("spark.some.config.option", "some-value")
  .getOrCreate();
~~~
<hr style="height:30px; visibility:hidden;" />

#### 2. Create Dataframes

~~~java
import org.apache.spark.sql.Dataset;
import org.apache.spark.sql.Row;

String path = "/data/data/SQL/Data/colors/multiline.json";
Dataset<Row> df = spark.read().csv(path);

// Displays the content of the DataFrame to stdout
df.show();
~~~

<br>

#### 3. Dataframe

~~~java
df.select("Zipcode").show();
+-------+
|Zipcode|
+-------+
|    704|
|    709|
+-------+

df.select(col("City"), col("RecordNumber").plus(1)).show();
+-------------------+------------------+
|               City|(RecordNumber + 1)|
+-------------------+------------------+
|PASEO COSTA DEL SUR|                 3|
|       BDA SAN LUIS|                11|
+-------------------+------------------+


df.filter(col("RecordNumber").gt(9)).show();
+------------+------------+-----+-----------+-------+
|        City|RecordNumber|State|ZipCodeType|Zipcode|
+------------+------------+-----+-----------+-------+
|BDA SAN LUIS|          10|   PR|   STANDARD|    709|
+------------+------------+-----+-----------+-------+

df.groupBy("RecordNumber").count().show()
~~~

<br>

> Related :
> <a href="/multinode_setup">Spark Cluster Setup, </a> 
> <a href="/concept-notes">Post 2</a> 



###### Notes

<small id="medium-ref"><sup>[[1]](#medium)</sup> </small>

###### Resources
1. [test](https://medium.com/about)
