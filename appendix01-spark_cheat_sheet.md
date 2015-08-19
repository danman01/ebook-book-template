# Spark Programming Cheat Sheet

Feature         | spark-defaults.conf      | application properties     | command line
--------------- | ------------------------ | -------------------------- | ------------------
Executor Memory | spark.driver.memory = 5g | spark.executor.memory = 5g | --driver.memory 5g

Don't be afraid to use .cache() method on RDDs to avoid unnecessary recomputation.

Understanding what's lazy vs. eagerly evaluated.

## RDD Functions
### Transformations (create new RDD's)
Transformations only occur at the point in the code when actions are called.

### Actions (return values)
## Spark Command Line
## Types of RDD's
## Deploying Code??
Date Functions, Mathematical Functions String Functions Collection Functions Aggregation Functions Conditional Functions? Functions for Text Analytics
