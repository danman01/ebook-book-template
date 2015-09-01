<div class="pagebreak-before" />

## Spark Programming Cheat Sheet

Feature         | spark-defaults.conf      | application properties     | command line
--------------- | ------------------------ | -------------------------- | ------------------
Executor Memory | spark.driver.memory = 5g | spark.executor.memory = 5g | --driver.memory 5g

Don't be afraid to use .cache() method on RDDs to avoid unnecessary recomputation.

Understanding what's lazy vs. eagerly evaluated. Transformations only occur at the point in the code when actions are called.

### Using the Shell
### RDD Functions
#### Transformations (return new RDD's)
#### Actions (return values)
### RDD Persistence and Caching

Storage Level                                     | Meaning
------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
MEMORY_ONLY (default for _cache()_ & _persist()_) | Store RDD as deserialized Java objects in the JVM. If the RDD does not fit in memory, some partitions will not be cached and will be recomputed on the fly each time they're needed. This is the default level.
MEMORY_AND_DISK                                   | Store RDD as deserialized Java objects in the JVM. If the RDD does not fit in memory, store the partitions that don't fit on disk, and read them from there when they're needed.
MEMORY_ONLY_SER                                   | Store RDD as serialized Java objects (one byte array per partition). This is generally more space-efficient than deserialized objects, especially when using a fast serializer, but more CPU-intensive to read.
MEMORY_AND_DISK_SER                               | Similar to MEMORY_ONLY_SER, but spill partitions that don't fit in memory to disk instead of recomputing them on the fly each time they're needed.
DISK_ONLY                                         | Store the RDD partitions only on disk.
MEMORY_ONLY_2, MEMORY_AND_DISK_2, etc.            | Same as the levels above, but replicate each partition on two cluster nodes.

### Spark Command Line
### Specialty RDD's
### Deploying Code??
Date Functions, Mathematical Functions String Functions Collection Functions Aggregation Functions Conditional Functions? Functions for Text Analytics
