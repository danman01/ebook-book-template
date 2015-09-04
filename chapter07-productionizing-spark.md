# Putting Spark into Production
"Spark is like a fighter jet that you have to build yourself. Once you have it built though, you have a fighter jet. Pretty awesome. Now you have to learn to fly it."

This analogy came from a conversation I had with someone at Strata London in 2015. Let's break down this quote to see the value it serves in discussing Spark, and explain why this analogy may or may not be accurate.

## Breaking it Down
### Spark and Fighter Jets
Fighter jets are a phenomenal feat of engineering, but how is this relevant to Spark? Well, building scalable applications can be difficult. Putting them into production is even more difficult. Spark scales out of the box nearly as simply as it is to install. A lot of work has gone into the thoughts and concepts of Spark as a scalable platform.

Spark is powerful, not only in the terms of scalability, but in ease of building applications. Spark has an API that is available in multiple languages and allows nearly any business application to be built on top of it.

However, just because Spark can scale easily doesn't mean everything written to run in Spark can scale as easily.

### Learning to Fly
While the API is similar or nearly identical between languages, this doesn't solve the problem of understanding the programming language of choice. While a novice programmer may be able to write Java code with minimal effort, it doesn't mean they understand the proper constructs in the language to optimize for a use case.

Let's consider analytics in Python on Spark. While a user may understand Python analytics, they may have no experience with concepts like predicate movement, column pruning or filter scans. These features could have significant impact when running queries at scale. Here are a few other topic areas where people may overestimate how Spark works by drawing on experiences with other technologies:
- Spark supports MapReduce, but people with a lot of experience with Hadoop MapReduce might try to transfer over ideas that don't necessarily translate over to Spark, such as functional programming constructs, type safety, or lazy evaluation;
- Someone with database administration experience with any popular RDBMS system may not be thinking of partitioning and serialization in the same terms that would be useful in Spark;

Another thing that could cause problems would be trying to run multiple use cases on a single Spark cluster. Java Virtual Machine configuration settings for a Spark cluster could be optimized for a single use case. Deploying an alternate use case on the same Spark cluster may not be optimized with the same settings. However, with technologies like Mesos and YARN, this shouldn't be a real problem. Multiple Spark clusters can be deployed to cover specific use cases. It could even be beneficial to create an ETL cluster and perhaps a cluster dedicated to streaming applications, all while running on the the same underlying hardware.

While these examples are not intended to be exhaustive, they hopefully clarify the concept that any given language or platform still needs to be well understood in order to get the most from it. Thus, really learning to fly.

### Assessment
This analogy is pretty good, and hopefully it doesn't scare anyone away from using Spark. The fact is that building, deploying and managing distributed systems are complicated. Even though Spark tries to simplify as much as possible with good default configuration settings, it is no exception to the level of complication that distributed systems bring.

## Planning for the Coexistence of Spark and Hadoop
As discussed earlier, Spark can run on its own. It is more commonly deployed as part of a cluster, managed by Mesos or the YARN resource manager within Hadoop.

Spark should always run as close to the cluster's storage nodes as possible. Much like configuring Hadoop, network I/O is likely to be the biggest bottleneck in a deployment. Deploying with 10Gb+ networking hardware will minimize latency and yield the best results. Never allocate more than 75% of available RAM to Spark. The operating system needs to use it as well, and going higher could cause paging. If a use case is so severely limited by 75% of available RAM, it might be time to add more servers to the cluster.

## Advice and Considerations
Nearly any business out there can benefit from utilizing Spark to solve problems. Thinking through taking Spark into production is usually the tough part. Some others in the industry have been kind enough to share some ideas on how to successfully take Spark into production to solve business problems. With any luck, the information provided here will help you be more successful on your own journey to success.

{% include 'pepperdata.html' %}
