# Introduction

A new name has entered many of the conversations around big data, recently. Some see popular newcomer Spark as a more accessible and more powerful replacement for big data’s original technology of choice, Hadoop. Others recognize Spark as a powerful complement to Hadoop and other more established technologies, with its own set of strengths and its own quirks and limitations. 

***Spark, like other big data tools, is powerful, capable, and well-suited to tackling a range of data challenges. Spark, like other big data technologies, is not necessarily the best choice for every data processing task.***

In this report, we introduce Spark and explore some of the areas in which its particular set of capabilities show most promise. We discuss the relationship to Hadoop and other key technologies, and provide some accessible pointers so that you can try Spark for yourself. 


## What is Spark?

The story most people here is that Spark began life in 2009 as a project within AMPLab at the University of California, Berkeley. More specifically it was born out of the necessity to prove out the concept of Mesos, which was also created in the AMPLab. Spark was first discussed in the Mesos white paper written-most notably-by Benjamin Hindman, and Matei Zaharia.

From the beginning, Spark was optimized to run in-memory, helping it process data far more quickly than alternative approaches like Hadoop’s MapReduce, which tend to write data to and from computer hard drives between each stage of processing. Its backers claim that Spark running in memory can be 100 times faster than Hadoop MapReduce, but also 10 times faster when processing disk-based data in a similar way to Hadoop MapReduce itself. This comparison is not entirely fair, not least because raw speed tends to be more important to Spark’s typical use cases than it is in the batch processing at which MapReduce-like solutions still excel.

Spark became an incubated project of the Apache Software Foundation in 2013, and early in 2014 Apache Spark was promoted to become one of the Foundation’s top-level projects. Spark is currently one of the most active projects managed by the Foundation, and the community that has grown up around the project includes both prolific individual contributors and well-funded corporate backers such as Databricks, IBM and China’s Huawei.

Spark is a general-purpose data processing engine, suitable for use in a wide range of circumstances. Interactive queries across large data sets, processing of streaming data from sensors or financial systems, and machine learning tasks tend to be most frequently associated with Spark. Developers can also use it to support other data processing tasks, benefiting from the project’s extensive set of developer libraries and APIs, and its comprehensive support for languages such as Java, Python, R and Scala. Spark is often used alongside Hadoop’s data storage module, HDFS, but can also integrate equally well with other popular data storage subsystems such as HBase, Cassandra, MapR-DB, MongoDB and Amazon’s S3.

There are many reasons to choose Spark, but three are key:
* Simplicity: Spark’s capabilities are accessible via a set of rich APIs, all designed specifically for interacting quickly and easily with data at scale. These APIs are well documented, and structured in a way that makes it straightforward for data scientists and application developers to quickly put Spark to work;
* Speed: Spark is designed for speed, operating both in-memory and on disk. In 2014, Spark was used to win the [Daytona Gray Sort benchmarking challenge](https://spark.apache.org/news/spark-wins-daytona-gray-sort-100tb-benchmark.html), processing 100 terabytes of data stored on solid-state drives in just 23 minutes. The previous winner used Hadoop and a different cluster configuration, but took 72 minutes. This win was the result of processing a static data set. Spark’s performance can be even greater when supporting interactive queries of data stored in memory, with claims that Spark can be 100 times faster than Hadoop’s MapReduce in these situations;
* Support: Spark supports a range of programming languages, including Java, Python, R and Scala. Although often closely associated with Hadoop’s underlying storage system, HDFS, Spark includes native support for tight integration with a number of leading storage solutions in the Hadoop ecosystem and beyond. The Apache Spark community is large, active, and international. A growing set of commercial providers including Databricks, IBM, and all of the main Hadoop vendors deliver comprehensive support for Spark-based solutions.

## Who uses Spark?

A wide range of technology vendors have been quick to support Spark, recognizing the opportunity to extend their existing big data products into areas such as interactive querying and machine learning where Spark delivers real value. Well-known technology companies such as IBM and Huawei have invested significant sums, and a growing number of startups are building businesses that depend in whole or in part upon Spark. In 2013, for example, the Berkeley team responsible for creating Spark founded a new company; Databricks. 

The company is well funded, receiving $47 million across two rounds of investment in 2013 and 2014, and Databricks employees continue to play a prominent role in improving and extending the open source code of the Apache Spark project.

The major Hadoop vendors, including MapR, Cloudera and Hortonworks, have all moved to support Spark alongside their existing products, and each is working to add value for their customers.

Elsewhere, IBM, Huawei and others have all made significant investments in Apache Spark, integrating it into their own products and contributing enhancements and extensions back to the Apache project.

Web-based companies like Chinese search engine Baidu, e-commerce operation Alibaba Taobao and social networking company Tencent all run Spark-based operations at scale, with Tencent’s 800 million active users reportedly generating over 700 TB of data per day for processing on a cluster of more than 8,000 compute nodes.

Away from the domain of web-based giants, pharmaceutical company Novartis depends upon Spark to reduce the time required to get modeling data into the hands of researchers, while ensuring that ethical and contractual safeguards are maintained.

## What is Spark used for?

Spark is a general-purpose data processing engine, an API-powered toolkit which data scientists and application developers incorporate into their applications to rapidly query, analyze and transform data at scale. Spark’s flexibility makes it well-suited to tackling a range of use cases, and it is capable of handling several petabytes of data at a time, distributed across a cluster of thousands of cooperating physical or virtual servers. Typical use cases include:
* **Stream processing**: From log files to sensor data, application developers increasingly have to cope with ‘streams’ of data. Data arrives in a steady stream, often from multiple sources simultaneously. While it is certainly feasible to allow these data streams to be stored on disk and analyzed retrospectively, it can sometimes be sensible or important to process and act upon the data as it arrives. Streams of data related to financial transactions, for example, can be processed in real-time to identify – and refuse – potentially fraudulent transactions. 
* **Machine learning**: As data volumes grow, machine learning approaches become increasingly feasible, and increasingly accurate. Software can be trained to identify and act upon triggers within well-understood data sets, before applying the same solutions to new and unknown data. Spark’s ability to store data in memory and rapidly run repeated queries makes it well-suited to training machine learning algorithms. Running broadly similar queries again and again, at scale, significantly reduces the time required to iterate through a set of possible solutions in order to find the most efficient algorithms.
* **Interactive analytics**: Rather than running pre-defined queries to create static dashboards of sales or production line productivity or stock prices, business analysts and data scientists increasingly want to explore their data, asking a question, viewing the result, and then either altering the initial question slightly or drilling deeper into results. This interactive query process requires systems – like Spark – that are able to respond and adapt quickly.
* **Data integration**: Data produced by different systems across a business is rarely clean or consistent enough to simply and easily be combined for reporting or analysis. Extract Transform Load (ETL) processes are often used to pull data from different systems, clean and standardize it, and then load it into a separate system for analysis. Spark (and Hadoop) are increasingly being used to reduce the cost and time required for this ETL process.

## Hadoop v Spark – an answer to the wrong question

Spark is not, despite the hype, a replacement for Hadoop. 

Spark can run on top of Hadoop, benefiting from Hadoop’s cluster manager (YARN) and underlying storage (HDFS, HBase, etc.). Spark can also run completely separately from Hadoop, integrating with alternative cluster managers like Mesos and alternative storage platforms like Cassandra and Amazon S3.

Hadoop’s relationship to Spark is discussed in more detail, later in the book.

Much of the confusion around Spark’s relationship to Hadoop dates back to the early years of Spark’s development. At this time, Hadoop relied upon MapReduce for the bulk of its data processing. Hadoop MapReduce also managed scheduling and task allocation processes within the cluster: even workloads that were not best suited to batch processing were passed through Hadoop’s MapReduce engine, adding complexity and reducing performance. 

Spark offered (and still offers) a far faster way to process data than passing it through unnecessary Hadoop MapReduce processes. But Hadoop has moved on, with development of the YARN cluster manager freeing the project from its early dependence upon MapReduce. Hadoop MapReduce is still available within Hadoop, but need only be used when working with the types of data – log files, static batch processes, etc. – for which MapReduce is appropriate. Other data processing tasks can be assigned to different processing engines (including Spark), with YARN handling the management and allocation of cluster resources.

Spark is a viable alternative to Hadoop MapReduce in a range of circumstances. Spark is a useful adjunct to a modern Hadoop cluster deployment, once the dependence upon Hadoop MapReduce has been removed by YARN.

## Getting started with Spark

Apache Spark is open source software, and can be freely [downloaded](https://spark.apache.org/downloads.html) from the project website. A [Quick Start](https://spark.apache.org/docs/1.4.1/quick-start.html) guide, optimized for developers familiar with either Python or Scala, provides an accessible introduction to working with Spark. MapR also provide a [tutorial](https://www.mapr.com/products/mapr-sandbox-hadoop/tutorials/spark-tutorial) linked to their simplified deployment of Hadoop, the [MapR Sandbox](https://www.mapr.com/products/mapr-sandbox-hadoop).

## A very simple Spark installation

The following steps are all that’s needed to download Java, Spark and Hadoop and get them running on a laptop, in this case one running Mac OS X.
If you do not currently have the [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) (version 7 or higher) installed, download it and follow the steps to install it for your operating system.

![Figure 1: Apache Spark download page, with a pre-built package selected for download](images/download-spark.png)

Visit the [Spark downloads page](https://spark.apache.org/downloads.html), select a pre-built package, and download Spark. Double-click the archive file, to expand its contents ready for use.

Open a text console, and navigate to the newly created directory. Start Spark’s interactive shell:

    ./bin/spark-shell

A series of messages will scroll past, as Spark and Hadoop are configured. Once the scrolling stops, you should see a simple prompt.

![Figure 2: A Terminal window, once Spark starts running for the first time](images/console-messages.png)

**At this prompt, let’s create some data; a simple sequence of numbers from 1 to 50,000.**

    scala> val data = 1 to 50000

**Now, let’s place these 50,000 numbers into a Resilient Distributed Dataset (RDD) which we’ll call sparkSample. It is this RDD upon which we can have Spark perform analysis.
**

    scala> val sparkSample = sc.parallelize(data)

**Now we can filter the data in the RDD, to find any values of less than 10.
**

    scala> sparkSample.filter(_ < 10).collect()

![Figure 3: Values less than 10, selected from a set of 50,000 numbers](images/console-result.png)

**Spark should report the result, with an array containing any values less than 10. Richer and more complex examples are available in resources mentioned elsewhere in this guide.**
