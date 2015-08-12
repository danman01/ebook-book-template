# Putting Spark into Production

I wanted to ask you, when it comes to people implementing spark:

1. Did you see people trying to optimize one spark installation for multiple use cases?

2. Would the best practice to follow be to setup separate optimized instances of spark for different use cases?

I'm curious because the more people I talk about, who are putting / trying to put Spark into production seem to have problems getting it optimized, and I'm wondering if this is because of multiple use cases on the same cluster.

The larger orgs will be trying to optimize for multiple use cases. However, part of the Spark/Mesos lineage is that one assumes some cluster resources and can "build" a number of different SparkContext objects. JVM settings *might* become a problem, but it's reasonable to think of having an ETL cluster, an exploratory SQL cluster, a Streaming cluster, a batch cluster, etc., all atop the same underlying cluster.

My takeaway on people having production problems with Spark is that many tend to think of it as something else with which they're familiar... then don't bother to drill down into the details of the UI, resource usage, etc. They may be thinking like a DBA about SQL queries in Oracle, without understanding that repartitioning and serialization are big big concerns. Or they may be thinking about M/R in Hadoop, without understanding functional programming, lazy evaluation, how to leverage type safety for optimization, etc.  Or they are used to doing Python analytics and have no idea about predicate movement, column pruning, filter-scans, etc., for queries at scale.


## Teradata Use Case

Within a chapter, the first and highest heading level uses two pound signs.

### Second-Level Heading

The second-level heading uses three pound signs.

#### Third-level heading

The third-level heading uses four pound signs.
