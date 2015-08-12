# Hadoop and Spark

Spark is a general-purpose data processing engine, suitable for use in a wide range of circumstances. In its current form, however, Spark is not designed to deal with the data management and cluster administration tasks associated with running data process and analysis workloads at scale.

Rather than invest effort in building these capabilities into Spark the project currently leverages the strengths of other open source projects, relying upon them for everything from cluster management and data persistence to disaster recovery and compliance.

Projects like Apache Mesos offer a powerful – and growing – set of capabilities around distributed cluster management, but most Spark deployments today still tend to select Apache Hadoop and its associated projects to fulfill these requirements.

## What Hadoop gives Spark

Apache Spark is often deployed in conjunction with a Hadoop cluster, and Spark is able to benefit from a number of capabilities as a result. On its own, Spark is a powerful tool for processing large volumes of data. But, on its own, Spark is not yet well suited to production workloads in the enterprise. Integration with Hadoop gives Spark many of the capabilities that broad adoption and use in production environments will require:
* **YARN resource manager**, which takes responsibility for scheduling tasks across available nodes in the cluster;
* **Distributed File System**, which stores data when the cluster runs out of free memory, and which persistently stores historical data when Spark is not running;
* **Disaster Recovery capabilities**, inherent to Hadoop, which enable recovery of data when individual nodes fail. These capabilities include basic (but reliable) data mirroring across the cluster and richer snapshot and mirroring capabilities such as those offered by the MapR Data Platform;
* **Data Security**, which becomes increasingly important as Spark tackles production workloads in regulated industries such as healthcare and financial services. Projects like Apache Knox and Apache Ranger offer data security capabilities to augment Hadoop. Hadoop’s core code, too, is increasingly recognizing the need to expose advanced security capabilities that Spark is able to exploit;
* **A distributed data platform**, benefiting from all of the preceding points, meaning that Spark jobs can be deployed on available resources anywhere in a distributed cluster, without the need to manually allocate and track those individual jobs.

## What Spark gives Hadoop

Hadoop has come a long way since its early versions, which were essentially concerned with facilitating the batch-processing of MapReduce jobs on large volumes of data stored in HDFS. Particularly since the introduction of the YARN resource manager, Hadoop has become better-able to manage a wide range of data processing tasks from batch processing to streaming data and graph analysis.

Spark is able to contribute, via YARN, to Hadoop-based jobs. In particular, Spark’s machine learning module delivers capabilities not easily exploited in Hadoop without the use of Spark. Spark’s original design goal, to enable rapid in-memory processing of sizeable data volumes, also remains an important contribution to the capabilities of a Hadoop cluster.

In certain circumstances, Spark’s SQL capabilities, streaming capabilities (otherwise available to Hadoop through Storm, for example) and graph processing capabilities (otherwise available to Hadoop through Neo4J or Giraph) may also prove to be of value. 

## Planning the coexistence of Spark and Hadoop

As discussed earlier, Spark can run on its own. It is more commonly deployed as part of a cluster, managed by Mesos or (more often) the YARN resource manager within Hadoop.

**this section needs to be spruced up since we are not linking out to the spark site**
Issues to consider in specifying hardware for the cluster on which Spark will run will be covered later in this book. Key points include:
* Run Spark as close to the cluster’s storage nodes as possible;
* Allocate local storage as well, ideally 4-8 disks per node;
* Ensure that nodes are connected with network speeds of 10 Gigabits or more, to minimize latency;
* Allocate no more than 75% of available RAM to Spark, leaving the rest for the operating system and the cache.

