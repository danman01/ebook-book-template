# Streaming Framework

Although now considered a key element of Spark, streaming capabilities were only introduced to the project with its 0.7 release (February 2013), emerging from the alpha testing phase with the 0.9 release (February 2014). Rather than being integral to the design of Spark, stream processing is a capability that has been added alongside Spark Core and its original design goal of rapid in-memory data processing.

Other stream processing solutions exist, including projects like Apache Storm and Apache Flink. In each of these, stream processing is a key design goal, offering some advantages to developers whose sole requirement is the processing of data streams. These solutions, for example, typically process the data stream event-by-event, while Spark adopts a system of chopping the stream into chunks (or micro-batches) to maintain compatibility and interoperability with Spark Core and Spark’s other modules.

## The Details of Spark Streaming

Spark’s real and sustained advantage over these alternatives is this tight integration between its stream and batch processing capabilities. For workloads in which streamed data must be combined with data from other sources, Spark remains a strong and credible option.

![Figure 5: Spark Streaming accepts data from a range of sources and is able to pass that data to various storage systems for safekeeping.](images/streaming-options.png)

A streaming framework is only as good as its data sources. A strong messaging platform is the best way to ensure solid performance for any streaming system.

Spark Streaming supports the ingest of data from a wide range of data sources, including live streams from Apache Kafka, Apache Flume, AWS Kinesis, Twitter, or sensors and other devices connected via TCP sockets. Data can also be streamed out of storage services such as HDFS and AWS S3. Data is processed by Spark Streaming, using a range of algorithms and high-level data processing functions like *map*, *reduce*, *join* and *window*. Processed data can then be passed to a range of external file systems, or used to populate live dashboards.

![Figure 6: Spark Streaming divides incoming streams of data into batches which can then be processed.](images/streaming-flow.png)

Logically, Spark Streaming represents a continuous stream of input data as a discretized stream, or DStream. Internally, Spark actually stores and processes this DStream as a sequence of RDDs. Each of these RDDs is a snapshot of all data ingested during a specified time period, which allows Spark’s existing batch processing capabilities to operate on the data.

![Figure 7: Spark Streaming divides an input data stream into discrete chunks of data from a specified time period.](images/streaming-dstream.png)

The data processing capabilities in Spark Core and Spark’s other modules are applied to each of the RDDs in a DStream in exactly the same manner as they would be applied to any other RDD: Spark modules other than Spark Streaming have no awareness that they are processing a data stream, and no need to know.

A basic RDD operation, *flatMap*, can be used to extract individual words from lines of text in an input source. When that input source is a data stream, *flatMap* simply works as it normally would, as shown below.

![Figure 8: Individual words are extracted from an input stream, comprising lines of text](images/streaming-dstream2.png)

### The Spark Driver

![Figure 9: Components of a Spark cluster](images/streaming-driver.png)

Activities within a Spark cluster are orchestrated by a driver program using the *SparkContext*. In the case of stream-based applications the *StreamingContext* is used. This exploits the cluster management capabilities of an external tool like Mesos or Hadoop’s YARN to allocate resources to the Executor processes that actually work with data.

In a distributed and generally fault-tolerant cluster architecture the driver is a potential point-of-failure, and a heavy load on cluster resources.

Particularly in the case of stream-based applications, there is an expectation and requirement that the cluster will be available and performing at all times. Potential failures in the Spark driver must therefore be mitigated, wherever possible. Spark Streaming introduced the practice of checkpointing, to ensure that data and metadata associated with RDDs containing parts of a stream are routinely replicated to some form of fault-tolerant storage. This makes it feasible to recover data and restart processing in the event of a driver failure.

## Processing Models

** Please elaborate with the pros / cons of each of these models because this can impact performance and complexity. Also, please add a note describing the value of algorithms that are idempotent, because within streaming concepts this is HUGE to understand.**

Spark Streaming itself supports commonly understood semantics for the processing of items in a data stream. These semantics ensure that the system is delivering dependable results, even in the event of individual node failures. Items in the stream are understood to be processed in one of the following ways:

* *At most once*: Each item will either be processed once or not at all;
* *At least once*: Each item will be processed one or more times, increasing the likelihood that data will not be lost but also introducing the possibility that items may be duplicated;
* *Exactly once*: Each item will be processed exactly once.

Different input sources to Spark Streaming will offer different guarantees for the manner in which data will be processed. With version 1.3 of Spark, a new API enables *exactly once* ingest of data from Apache Kafka, improving data quality throughout the workflow. This is discussed in more detail in an [Integration Guide](http://spark.apache.org/docs/latest/streaming-kafka-integration.html).

## Spark Streaming v Others

Spark streaming operates on the concept of micro-batches. This means Spark Streaming should not be considered a real-time stream processing engine. This is perhaps the single biggest difference between Spark Streaming and others like Apache Storm, or Apache Flink.

A micro-batch consists of a series of events batched together over a period of time. The batch interval generally ranges from as little as 500ms to about 5,000ms (can be higher). While the batch interval is completely customizable it is ***important to note*** that it is set upon the creation of the *StreamingContext*. To change this setting would require a restart of the streaming application due to it being a statically configured value.

The shorter time frame (500ms) the closer to real-time and also the more overhead the system will endure. This is due to the need of creating more RDDs for each and every micro-batch. The inverse is also true; the longer the time frame the further from real-time and the less overhead that will occur for processing each micro-batch.

An argument often made with the concept of a micro-batch in the context of streaming applications is that it lacks the time-series data from each discrete event. This can make it more difficult to know if events arrived out of order. This may or may not be relevant to a business use case.

An application built upon Spark Streaming cannot react to every event as they occur. This is not necessarily a bad thing, but it is instead very important to make sure that everyone, everywhere understands the limitations and capabilities of Spark Streaming.

### Performance Comparisons

Spark Streaming is fast, but to make comparisons between Spark Streaming and other stream processing engines-like Apache Storm-is a difficult task. The comparisons tend not to be apples-to-apples. Most benchmarks comparisons should be taken very lightly. Because Spark Streaming is micro-batch based, it is going to tend to appear faster than nearly every system that is not micro-batch based, but this trade off comes at the cost of latency to process the events in a stream. The closer to real-time that an event is processed the more overhead that occurs in Spark Streaming.

## Current Limitations

Two of the biggest complaints about running Spark Streaming in production are backpressure and dynamic scaling.

Back pressure occurs when the volume of events coming across a stream is more than a stream processing can handle. There are changes that will show up in version 1.5 of Spark to enable more dynamic ingestion rate capabilities.

Dynamic scaling is important in long running streaming applications. Sometimes scaling the ingestion rate isn't enough, like when there is a need to add more more compute to the cluster to enable processing the data. Spark Streaming is already architected in such a way that it supports processing small units of work which are well distributed. It is not currently considered easy to scale up and down. Because this is such an important thing, it is expected that more will be done to
