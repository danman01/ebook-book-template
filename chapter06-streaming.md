# Streaming framework

Although now considered a key element of Spark, streaming capabilities were only introduced to the project with its 0.7 release (February 2013), emerging from the alpha testing phase with the 0.9 release (February 2014). Rather than being integral to the design of Spark, stream processing is a capability that has been added alongside Spark Core and its original design goal of rapid in-memory data processing.

Other stream processing solutions exist, including projects like Apache Storm and Apache Flink. In each of these, stream processing is a key design goal, offering some advantages to developers whose sole requirement is the processing of data streams. These solutions, for example, typically process the data stream as a whole, while Spark adopts a system of chopping the stream into chunks (or micro-batches) to maintain compatibility and interoperability with Spark Core and Spark’s other modules.

Spark’s real and sustained advantage over these alternatives is this tight integration between its stream and batch processing capabilities. For workloads in which streamed data must be combined with data from other sources, Spark remains a strong and credible option.

![Figure 5: Spark Streaming accepts data from a range of sources and is able to pass that data to various storage systems for safekeeping.](images/streaming-options.png)

Spark Streaming supports the ingest of data from a wide range of data sources, including live streams from Apache Kafka, Apache Flume, AWS Kinesis, Twitter, or sensors and other devices connected via TCP sockets. Data can also be streamed out of storage services such as HDFS and AWS S3. Data is processed by Spark Streaming, using a range of algorithms and high-level data processing functions like *map*, *reduce*, *join* and *window*. Processed data can then be passed to a range of external file systems, or used to populate live dashboards.

![Figure 6: Spark Streaming divides incoming streams of data into batches which can then be processed.](images/streaming-flow.png)

Logically, Spark Streaming represents a continuous stream of input data as a discretized stream, or DStream. Internally, Spark actually stores and processes this DStream as a sequence of RDDs. Each of these RDDs is a snapshot of all data ingested during a specified time period, which allows Spark’s existing batch processing capabilities to operate on the data.

![Figure 7: Spark Streaming divides an input data stream into discrete chunks of data from a specified time period.](images/streaming-dstream.png)

The data processing capabilities in Spark Core and Spark’s other modules are applied to each of the RDDs in a DStream in exactly the same manner as they would be applied to any other RDD: Spark modules other than Spark Streaming have no awareness that they are processing a data stream, and no need to know.

Spark Streaming itself supports commonly understood semantics for the processing of items in a data stream. These semantics ensure that the system is delivering dependable results, even in the event of individual node failures. Items in the stream are understood to be processed in one of the following ways:

** Please elaborate with the pros / cons of each of these models because this can impact performance and complexity. Also, please add a note describing the value of algorithms that are idempotent, because within streaming concepts this is HUGE to understand.**

* *At most once*: Each item will either be processed once or not at all;
* *At least once*: Each item will be processed one or more times, increasing the likelihood that data will not be lost but also introducing the possibility that items may be duplicated;
* *Exactly once*: Each item will be processed exactly once.

Different input sources to Spark Streaming will offer different guarantees for the manner in which data will be processed. With version 1.3 of Spark, a new API enables *exactly once* ingest of data from Apache Kafka, improving data quality throughout the workflow. This is discussed in more detail in an [Integration Guide](http://spark.apache.org/docs/latest/streaming-kafka-integration.html).

A basic RDD operation, *flatMap*, can be used to extract individual words from lines of text in an input source. When that input source is a data stream, *flatMap* simply works as it normally would, as shown below.

![Figure 8: Individual words are extracted from an input stream, comprising lines of text](images/streaming-dstream2.png)

## The Spark Driver

![Figure 9: Components of a Spark cluster](images/streaming-driver.png)

Activities within a Spark cluster are orchestrated by a driver program, SparkContext. This exploits the cluster management capabilities of an external tool like Mesos or Hadoop’s YARN to allocate resources to the Executor processes that actually manipulate data.

In a distributed and generally fault-tolerant cluster architecture the driver is a potential point of failure, and a heavy load on cluster resources.

Particularly in the case of stream-based applications, there is an expectation and requirement that the cluster will be available and performing at all times. Potential failures in the Spark driver must therefore be mitigated, wherever possible. Spark Streaming introduces the practice of checkpointing, to ensure that data and metadata associated with RDDs containing parts of a stream are routinely replicated to some form of fault-tolerant storage. This makes it feasible to recover data and restart processing in the event of a driver failure.
