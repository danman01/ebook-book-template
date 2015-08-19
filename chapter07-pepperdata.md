<section data-type="partner">

# Spark Reliability and Performance Through Monitoring
As more organizations begin to deploy Spark in their production clusters, the need for fine-grained monitoring tools becomes paramount. Having the ability to view Spark resource consumption, and monitor how Spark applications are interacting with other workloads on your cluster can help you save time and money by:
- troubleshooting misbehaving applications;
- monitoring and improving performance;
- viewing trend analysis over time

When deploying Spark in production, here are some crucial considerations to weigh.

## Monitoring the right metrics?
How granular is your visibility into Spark's activity on your cluster? Can you view all the relevant variables you need to? These are important questions, especially for troubleshooting errant applications or behavior.

With an out-of-the-box installation, Spark's Application Web UI can display basic, per-executor information about memory, CPU, and storage. By accessing the web instance on port 4040 (default), you can see statistics about specific jobs, like their duration, number of tasks, and whether they've completed.

But this default monitoring capability isn't necessarily adequate. Take a basic scenario: suppose a Spark application is reading heavily from disk and you want to understand how it's interacting with the file subsystem because the application is missing critical deadlines. Can you easily view detailed information about file I/O (both local file system and HDFS)? No, not with the default Spark Web UI. But this granular visibility would be necessary to see how many files are being opened concurrently, and whether a specific disk is hotspotting and slowing down overall performance. With the right monitoring tool, discovering that the application attempted to write heavily to disk at the same time as a MapReduce job could take seconds instead of minutes or hours using basic Linux tools like Top or Iostat.

These variables are important, and without them you may be flying blind. Having deep visibility helps you quickly troubleshoot and respond in-flight to performance issues. Invest time in researching an add-on monitoring tool for Spark that meets your organization's needs.

## Is your monitoring tool intuitive?
It's great to have lots of data and metrics available to digest, but can you navigate that data quickly? Can you find what you need, and once you do, can you make sense of it? How quantitative information is displayed makes a difference. Your monitoring tool should allow you to easily navigate across different time periods, as well as to zoom in on a few seconds' worth of data. You should have the option to plot the data in various ways -- line charts, by percentile, or in a stacked format, for example. Note whether you can filter the data easily by user, job, host, or queue. In short, can you use your monitoring tool intuitively, complementing your mental line of questioning? Or do you have to work around the limitations of what the tool presents?

If you have all the data, but can't sort it easily to spot trends or quickly filter it to drill down into a particular issue, then your data isn't helping you. You won't be able to effectively monitor cluster performance or take advantage of the data you do have. So make sure your tool is useful, in all senses of the word.

## Can you see global impact?
Even if you are able to see the right metrics via an intuitive dashboard or user interface, being limited in vantage point to a single Spark job or to a single node view is not helpful. Whatever monitoring tool you choose should allow you to see not just one Spark job, but all of them -- and not just all your Spark jobs, but everything else happening on your cluster, too. How is Spark impacting your HBase jobs or your other MapReduce workloads?

Spark is only one piece in your environment, so you need to know how it integrates with other aspects of your Hadoop ecosystem. This is a no-brainer from a troubleshooting perspective, but it's also a good practice for general trend analysis. Perhaps certain workloads cause greater impact to Spark performance than others, or vice-a-versa. If you anticipate an increase in Spark usage across your organization, you'll have to plan differently than if you hadn't noticed that fact.

In summary, the reliability and performance of your Spark deployment depends on what's happening on your cluster. Both the execution of individual Spark jobs and how Spark is interacting (and impacting) your broader Hadoop environment. To understand what Spark's doing, you'll need a monitoring tool that can provide deep, granular visibility as well as a wider, macro view of your entire system. These sorts of tools are few and far between, so choose wisely.

</section>
