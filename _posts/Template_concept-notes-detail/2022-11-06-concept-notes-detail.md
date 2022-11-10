---
title: "[Template] Concept Notes - First Note (3)"
date: 2022-11-06 19:58:47 +07:00
modified: 2022-11-06 23:49:47 +07:00
tags: [Template]
description: Concept Note for Specific Skill.
image: "/_posts/Template_concept-notes-detail/default_note_image.png"
---

<figure>
<img src="https://raw.githubusercontent.com/avoholo/avoholo.github.io/master/_posts/Template_concept-notes-detail/default_note_image.png" alt="default_note_image">
<figcaption>Fig 1. Hadoop File System Architecture</figcaption>
</figure>



이전 글이 특정 기술에 대한 ***Summary*** 였다면, 이 글에선 해당 기술의 세부 **컴포넌트**를 자세히 설명하는 Template<sup id="medium">[[1]](#medium-ref)</sup>을 작성하려고 한다. 

예를 들어, 이전 글이 `Hadoop`을 소개하는 글이였다면, 이번 글은 `Hadoop`의 중요 개념 중 하나인 `Map&Reduce`를 설명하는 포스트다. 



### Hadoop Distributed File System

The Hadoop Distributed File System (HDFS) is a `distributed file system` designed to run on commodity hardware. It has many similarities with existing distributed file systems. However, the differences from other distributed file systems are significant. HDFS is highly `fault-tolerant` and is designed to be deployed on `low-cost` hardware. HDFS **provides high throughput access** to application data and is suitable for applications that have `large data sets`. 



### Architecture

Attach Here.





### Components

##### The File System Namespace

HDFS supports a traditional hierarchical file organization. A user or an application can create directories and store files inside these directories.



##### Data Replication

HDFS is designed to reliably store very large files across machines in a large cluster. It stores each file as a sequence of blocks. The blocks of a file are replicated for fault tolerance. The block size and replication factor are configurable per file.



##### Persistence of File System Metadata

The HDFS namespace is stored by the NameNode. The NameNode uses a transaction log called the EditLog to persistently record every change that occurs to file system metadata.



##### The Communication Protocols

All HDFS communication protocols are layered on top of the TCP/IP protocol. A client establishes a connection to a configurable TCP port on the NameNode machine.



### Best Practice (Do & Don't)

##### &#9940;Don't - use views

Views are great for transactional systems, where data is frequently changing, and a programmer can consolidate sophisticated logic into a View. When source data is not changing, create a table instead of a View.



##### &#9940;Don't - use small partitions

In a transactional system, to reduce the query time, a simple approach is to partition the data based on a query where clause. While in Hadoop, the mapping is far cheaper than start and stop of a container. So use partition only when the data size of each partition is about a block size or more (64/128 MB).



##### &#9989;Do - Use ORC or Parquet File format

By changing the underneath file format to ORC or Parquet, we can get a significant performance gain.



##### &#9989;Do - Managed Table vs. External Table

Adopt a standard and stick to it but I recommend using Managed Tables. They are always better to govern. You should use External Tables when you are importing data from the external system. You need to define a schema for it after that entire workflow can be created using Hive scripts leveraging managed tables.



### Limitation&#10060;

#### 1. Issue with Small Files

Hadoop does not suit for small data. [**(HDFS)** **Hadoop distributed file system**](https://data-flair.training/blogs/comprehensive-hdfs-guide-introduction-architecture-data-read-write-tutorial/) lacks the ability to efficiently support the random reading of small files because of its high capacity design.

**Solution &#10004;** 

- **Merge the small files** - merge the small files to create bigger files and then copy bigger files to HDFS.
- **HAR** - The introduction of **HAR files** (Hadoop Archives) was for reducing the problem of lots of files putting pressure on the namenode’s memory. By building a layered filesystem on the top of HDFS, HAR files works. Using the Hadoop archive command, HAR files are created, which runs a **[MapReduce](https://data-flair.training/blogs/hadoop-mapreduce-introduction-tutorial-comprehensive-guide/)** job to pack the files being archived into a small number of HDFS files. Reading through files in a HAR is not more efficient than reading through files in HDFS. Since each HAR file access requires two index files read as well the data file to read, this makes it slower.
- **Sequence files** - they work very well in practice to overcome the ‘small file problem’, in which we use the filename as the key and the file contents as the value. By writing a program for files (100 KB), we can put them into a single Sequence file and then we can process them in a streaming fashion operating on the Sequence file. MapReduce can break the Sequence file into chunks and operate on each chunk independently because the Sequence file is splittable.

#### 2. Slow Processing Speed

In Hadoop, with a parallel and distributed algorithm, the MapReduce process large data sets. There are tasks that we need to perform: Map and Reduce and, MapReduce requires a lot of time to perform these tasks thereby increasing latency. Data is distributed and processed over the cluster in MapReduce which increases the time and reduces processing speed.

<br>



> Related :
> <a href="/concept-notes">Concept Note, </a> 
> <a href="/concept-notes">Python OOP</a> 




###### Notes
<small id="medium-ref"><sup>[[1]](#medium)</sup> 좋은 블로그 형식을 갖춘 미디엄에서 아이디어를 얻었다.</small>

###### Resources
1. [HDFS Architecture](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html)