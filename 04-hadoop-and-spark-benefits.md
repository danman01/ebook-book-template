# Benefits of Hadoop and Spark
Spark is a general-purpose data processing engine, suitable for use in a wide range of circumstances. In its current form, however, Spark is not designed to deal with the data management and cluster administration tasks associated with running data processing and analysis workloads at scale.

Rather than investing effort in building these capabilities into Spark, the project currently leverages the strengths of other open source projects, relying upon them for everything from cluster management and data persistence to disaster recovery and compliance.

Projects like Apache Mesos offer a powerful and growing set of capabilities around distributed cluster management. However, most Spark deployments today still tend to use Apache Hadoop and its associated projects to fulfill these requirements.

## Hadoop vs. Spark - An Answer to the Wrong Question
Spark is not, despite the hype, a replacement for Hadoop. Nor is MapReduce dead.

Spark can run on top of Hadoop, benefiting from Hadoop's cluster manager (YARN) and underlying storage (HDFS, HBase, etc.). Spark can also run completely separately from Hadoop, integrating with alternative cluster managers like Mesos and alternative storage platforms like Cassandra and Amazon S3.

Much of the confusion around Spark's relationship to Hadoop dates back to the early years of Spark's development. At that time, Hadoop relied upon MapReduce for the bulk of its data processing. Hadoop MapReduce also managed scheduling and task allocation processes within the cluster; even workloads that were not best suited to batch processing were passed through Hadoop's MapReduce engine, adding complexity and reducing performance.

MapReduce is really a programming model. In Hadoop MapReduce, multiple MapReduce jobs would be strung together to create a data pipeline. In between every stage of that pipeline, the MapReduce code would read data from the disk, and when completed, would write the data back to the disk. This process was inefficient because it had to read all the data from disk at the beginning of each stage of the process. This is where Spark comes in to play. Taking the same MapReduce programming model, Spark was able to get an immediate 10x increase in performance, because it didn't have to store the data back to the disk, and all activities stayed in memory. Spark offers a far faster way to process data than passing it through unnecessary Hadoop MapReduce processes.

Hadoop has since moved on with the development of the YARN cluster manager, thus freeing the project from its early dependence upon Hadoop MapReduce. Hadoop MapReduce is still available within Hadoop for running static batch processes for which MapReduce is appropriate. Other data processing tasks can be assigned to different processing engines (including Spark), with YARN handling the management and allocation of cluster resources.

Spark is a viable alternative to Hadoop MapReduce in a range of circumstances. Spark is not a replacement for Hadoop, but is instead a great companion to a modern Hadoop cluster deployment.

## What Hadoop Gives Spark
Apache Spark is often deployed in conjunction with a Hadoop cluster, and Spark is able to benefit from a number of capabilities as a result. On its own, Spark is a powerful tool for processing large volumes of data. But, on its own, Spark is not yet well-suited to production workloads in the enterprise. Integration with Hadoop gives Spark many of the capabilities that broad adoption and use in production environments will require, including:
- **YARN resource manager**, which takes responsibility for scheduling tasks across available nodes in the cluster;
- **Distributed File System**, which stores data when the cluster runs out of free memory, and which persistently stores historical data when Spark is not running;
- **Disaster Recovery capabilities**, inherent to Hadoop, which enable recovery of data when individual nodes fail. These capabilities include basic (but reliable) data mirroring across the cluster and richer snapshot and mirroring capabilities such as those offered by the MapR Data Platform;
- **Data Security**, which becomes increasingly important as Spark tackles production workloads in regulated industries such as healthcare and financial services. Projects like Apache Knox and Apache Ranger offer data security capabilities that augment Hadoop. Each of the big three vendors have alternative approaches for security implementations that complement Spark. Hadoop's core code, too, is increasingly recognizing the need to expose advanced security capabilities that Spark is able to exploit;
- **A distributed data platform**, benefiting from all of the preceding points, meaning that Spark jobs can be deployed on available resources anywhere in a distributed cluster, without the need to manually allocate and track those individual jobs.

## What Spark Gives Hadoop
Hadoop has come a long way since its early versions which were essentially concerned with facilitating the batch processing of MapReduce jobs on large volumes of data stored in HDFS. Particularly since the introduction of the YARN resource manager, Hadoop is now better able to manage a wide range of data processing tasks, from batch processing to streaming data and graph analysis.

Spark is able to contribute, via YARN, to Hadoop-based jobs. In particular, Spark's machine learning module delivers capabilities not easily exploited in Hadoop without the use of Spark. Spark's original design goal, to enable rapid in-memory processing of sizeable data volumes, also remains an important contribution to the capabilities of a Hadoop cluster.

In certain circumstances, Spark's SQL capabilities, streaming capabilities (otherwise available to Hadoop through Storm, for example), and graph processing capabilities (otherwise available to Hadoop through Neo4J or Giraph) may also prove to be of value in enterprise use cases.
