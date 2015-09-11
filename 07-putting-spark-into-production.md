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

<nobr/>
<aside data-type="sidebar">

<p class="partner">Advice from our friends: Pepperdata</p>

<h2>Reliability and Performance through Monitoring</h2>

<p>As more organizations begin to deploy Spark in their production clusters, the need for fine-grained monitoring tools becomes paramount. Having the ability to view Spark resource consumption, and monitor how Spark applications are interacting with other workloads on your cluster, can help you save time and money by:</p>

<ul>
    <li>troubleshooting misbehaving applications;</li>
    <li>monitoring and improving performance;</li>
    <li>viewing trend analysis over time</li>
</ul>

<p>When deploying Spark in production, here are some crucial considerations to keep in mind:</p>

<h3>Monitoring the Right Metrics?</h3>

<p>How granular is your visibility into Spark's activity on your cluster? Can you view all the relevant variables you need to? These are important questions, especially for troubleshooting errant applications or behavior.</p>

<p>With an out-of-the-box installation, Spark's Application Web UI can display basic, per-executor information about memory, CPU, and storage. By accessing the web instance on port 4040 (default), you can see statistics about specific jobs, like their duration, number of tasks, and whether they've completed.</p>

<p>But this default monitoring capability isn't necessarily adequate. Take a basic scenario: suppose a Spark application is reading heavily from disk, and you want to understand how it's interacting with the file subsystem because the application is missing critical deadlines. Can you easily view detailed information about file I/O (both local file system and HDFS)? No, not with the default Spark Web UI. But this granular visibility would be necessary to see how many files are being opened concurrently, and whether a specific disk is hot-spotting and slowing down overall performance. With the right monitoring tool, discovering that the application attempted to write heavily to disk at the same time as a MapReduce job could take seconds instead of minutes or hours using basic Linux tools like Top or Iostat.</p>

<p>These variables are important, and without them you may be flying blind. Having deep visibility helps you quickly troubleshoot and respond in-flight to performance issues. Invest time in researching an add-on monitoring tool for Spark that meets your organization's needs.</p>

<h3>Is Your Monitoring Tool Intuitive?</h3>

<p>It's great to have lots of data and metrics available to digest, but can you navigate that data quickly? Can you find what you need, and once you do, can you make sense of it? How quantitative information is displayed makes a difference. Your monitoring tool should allow you to easily navigate across different time periods, as well as to zoom in on a few seconds' worth of data. You should have the option to plot the data in various ways—line charts, by percentile, or in a stacked format, for example. Note whether you can filter the data easily by user, job, host, or queue. In short, can you use your monitoring tool intuitively, complementing your mental line of questioning? Or do you have to work around the limitations of what the tool presents?</p>

<p>If you have all the data, but can't sort it easily to spot trends or quickly filter it to drill down into a particular issue, then your data isn't helping you. You won't be able to effectively monitor cluster performance or take advantage of the data you do have. So make sure your tool is useful, in all senses of the word.</p>

<h3>Can You See Global Impact?</h3>

<p>Even if you are able to see the right metrics via an intuitive dashboard or user interface, being limited in vantage point to a single Spark job or to a single node view is not helpful. Whatever monitoring tool you choose should allow you to see not just one Spark job, but all of them—and not just all your Spark jobs, but everything else happening on your cluster, too. How is Spark impacting your HBase jobs or your other MapReduce workloads?</p>

<p>Spark is only one piece in your environment, so you need to know how it integrates with other aspects of your Hadoop ecosystem. This is a no-brainer from a troubleshooting perspective, but it's also a good practice for general trend analysis. Perhaps certain workloads cause greater impact to Spark performance than others, or vice versa. If you anticipate an increase in Spark usage across your organization, you'll have to plan differently than if you hadn't noticed that fact.</p>

<p>In summary, the reliability and performance of your Spark deployment depends on what's happening on your cluster, including both the execution of individual Spark jobs and how Spark is interacting (and impacting) your broader Hadoop environment. To understand what Spark's doing, you'll need a monitoring tool that can provide deep, granular visibility as well as a wider, macro view of your entire system. These sorts of tools are few and far between, so choose wisely.</p>

</aside>
