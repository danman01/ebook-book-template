<div class="pagebreak-before" />

## Spark Programming Cheat Sheet

Feature         | spark-defaults.conf      | application properties     | command line
--------------- | ------------------------ | -------------------------- | ------------------
Executor Memory | spark.driver.memory = 5g | spark.executor.memory = 5g | --driver.memory 5g

Don't be afraid to use .cache() method on RDDs to avoid unnecessary recomputation.

Understanding what's lazy vs. eagerly evaluated. Transformations only occur at the point in the code when actions are called.

### Using the Shell
### Running Jobs
### Deploying Code??
Date Functions, Mathematical Functions String Functions Collection Functions Aggregation Functions Conditional Functions? Functions for Text Analytics

### DataFrames
### SQL
### MLlib
### Streaming
