# Getting started with Spark

Although cluster-based installations of Spark can become large and relatively complex, integrated with Mesos, Hadoop, Cassandra, or other systems, it is straightforward to download Spark and configure it in standalone mode on a laptop or server for learning and exploration. This low barrier to entry makes it relatively easy for individual developers, or data scientists to get started with Spark, and for businesses to launch pilot projects that do not require complex re-tooling or interference with production systems.

Apache Spark is open source software, and can be freely [downloaded](https://spark.apache.org/downloads.html) from the Apache Software Foundation. Spark requires at least version 6 of Java and at least version 3.0.4 of Maven. Other dependencies, such as Scala and Zinc, are automatically installed and configured as part of the installation process.

[Build options](http://spark.apache.org/docs/latest/building-spark.html), including optional links to data storage systems such as Hadoop’s HDFS or Hive, are discussed in more detail in Spark’s online documentation.

## Getting started with Spark

A [Quick Start](https://spark.apache.org/docs/1.4.1/quick-start.html) guide, optimized for developers familiar with either Python or Scala, provides an accessible introduction to working with Spark. MapR also provide a [tutorial](https://www.mapr.com/products/mapr-sandbox-hadoop/tutorials/spark-tutorial) linked to their simplified deployment of Hadoop, the [MapR Sandbox](https://www.mapr.com/products/mapr-sandbox-hadoop).

### A very simple Spark installation

The following steps are all that’s needed to download Java, Spark and Hadoop and get them running on a laptop, in this case one running Mac OS X.
If you do not currently have the [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) (version 7 or higher) installed, download it and follow the steps to install it for your operating system.

<img src="images/download-spark.png" alt="Figure 1: Apache Spark download page, with a pre-built package selected for download" width="640px" />
<!--![Figure 1: Apache Spark download page, with a pre-built package selected for download](images/download-spark.png)-->

Visit the [Spark downloads page](https://spark.apache.org/downloads.html), select a pre-built package, and download Spark. Double-click the archive file, to expand its contents ready for use.

Open a text console, and navigate to the newly created directory. Start Spark’s interactive shell:

    ./bin/spark-shell

A series of messages will scroll past, as Spark and Hadoop are configured. Once the scrolling stops, you should see a simple prompt.

<img src="images/console-messages.png" alt="Figure 2: A Terminal window, once Spark starts running for the first time" width="640px" />
<!--![Figure 2: A Terminal window, once Spark starts running for the first time](images/console-messages.png)-->

**At this prompt, let’s create some data; a simple sequence of numbers from 1 to 50,000.**

    scala> val data = 1 to 50000

**Now, let’s place these 50,000 numbers into a Resilient Distributed Dataset (RDD) which we’ll call sparkSample. It is this RDD upon which we can have Spark perform analysis.**

    scala> val sparkSample = sc.parallelize(data)

**Now we can filter the data in the RDD, to find any values of less than 10.**

    scala> sparkSample.filter(_ < 10).collect()

<img src="images/console-result.png" alt="Figure 3: Values less than 10, selected from a set of 50,000 numbers" width="640px" />
<!--![Figure 3: Values less than 10, selected from a set of 50,000 numbers](images/console-result.png)-->

**Spark should report the result, with an array containing any values less than 10. Richer and more complex examples are available in resources mentioned elsewhere in this guide.**
