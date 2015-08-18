
## Real-World Example #1 - Computing User Profiles with PySpark ##

Let's dive into an example of unifying the core concepts of Spark and use a large dataset to build a simple real-time dashboard giving us insight into customer behaviors.

Spark is becoming a key enabling technology in a wide variety of use cases across many industries – anything where the results are needed fast and much of the computations can be done in memory – but as we mentioned in the previous chapter, its fully developed support for at least three programming languages (Scala, Java, Python) and active community are key factors for why you might use it in into areas where MapReduce or other approaches may have been the first choice as recently as a few years ago.  In this first example we will use Python because reduces the amount of boilerplate code required to illustrate a few simple examples.

**The Scenario**

To give us a foundation and some things to analyze, we’ll use is a hypothetical music streaming site, much like the one you might use every day on your desktop or mobile device, either as a subscriber or a free listener (one such service has a name that refers a mystical box of troubles from Greek mythology).

The basic architecture is simple:  customers are logging into our service and listening to music tracks, and they have a variety of parameters associated with them -- things like the following:


- Basic demographic information (gender, location, etc.)
- Whether or not they are a paying member of the site
- Their listening history -- what tracks they select, when, and where they were when they selected them.

This (somewhat stylized) example provides us with a few different rich data sources for ingest and analysis, and shows some typical things you might do with Spark to help get you thinking about how they would map to business problems you see in your own world.
	
We’ll use Python, PySpark and a small part of MLlib to compute some basic statistics for a dashboard, that will give us a high-level view of customer behaviors and always contain the latest information.

All of the source code and data sets are available in this [github repo](https://github.com/mapr/mapr-demos/tree/master/spark_music_demo), and using a one of the preconfigured Hadoop environments (such as the MapR Sandbox) you can easily setup and modify this example on a small scale.

**Reading data from MapR-FS (or HDFS) into a Spark RDD**

The data coming in is raw (in this case, directly from a CSV file), so we have to perform a couple of steps before we can start analyzing it.  In this example we will apply transformations to “massage” the data into a *pair RDD*, which means the data consists of an array of (key, value) tuples.

**Introducing the Example Data**

Users are continuously connecting to the service and listening to tracks that they like.  This generates our main data set. The behaviors captured in these events, over time, represent the highest level of detail about actual behaviors of customers as they consume the service by listening to music. In addition to the events of listening to individual tracks, we have a few other data sets representing all the information we might normally have in such a service. In this example we will make use of the following data:

- **Events relating to individual customers listening to individual tracks (tracks.csv)**.  This dataset consists of a collection of events, one per line, where each event is a client listening to a track.  This size is approximately 1M lines and contains simulated listener events over several months.  Because this represents things that are happening at a very low level, this data has the potential to grow very large.

![](https://www.mapr.com/sites/default/files/blogimages/blog_RealTimeUser-table1.png)


The event, customer and track IDs tell us what occurred (a customer listened to a certain track), while the other fields tell us some associated information, like whether the customer was listening on a mobile device and a guess about their location while they were listening.  This will serve as the input into our first Spark job.

- **Customer information: (cust.csv) information about individual customers.**

![](https://www.mapr.com/sites/default/files/blogimages/blog_RealTimeUser-table2.png)

The fields are defined as follows:

- Customer ID: a unique identifier for that customer
- Name, gender, address, zip: the customer’s associated information
- Sign date: the date of addition to the service
- Status: indicates whether or not the account is active (0 = closed, 1 = active)
- Level: indicates what level of service -0, 1, 2 for Free, Silver and Gold, respectively
- Campaign: indicates the campaign under which the user joined, defined as the following (fictional) campaigns driven by our (also fictional) marketing team:
	- NONE no campaign
	- 30DAYFREE a ‘30 days free’ trial offer
	- SUPERBOWL a Superbowl-related program
	- RETAILSTORE an offer originating in brick-and-mortar retail stores
	- WEBOFFER an offer for web-originated customers

There are some additional data sets, such as history about clicking on advertisements, and of course information about music tracks (title, etc.), but we won't use those in this example. You will find these additional data sets as also part of the github repo.

**What are customers doing?**

We’ve got all the right information in place and a lot of micro-level detail about what customers are selecting for music and when... but for most of us, our eyelids would get a little heavy looking at it. What’s the quickest way from here to a dashboard? Here’s where we put Spark to work computing summary information for each customer as well as some basic statistics about the entire user base.  After we have the results, we can persist them to a file that can be easily picked up for visualization with our favorite BI tool, such as Tableau, or other software packages with dashboarding capabilities like C3.js.

Let’s jump into some Python code. First, we’ll initialize a Spark context.  Optionally, you can pass additional parameters to the `SparkConf` method to further configure the job, such as setting the master and the directory where the job executes.

    from pyspark import SparkContext, SparkConf  
    from pyspark.mllib.stat import Statistics
    import csv  
      
    conf = SparkConf().setAppName('ListenerSummarizer')  
    sc = SparkContext(conf=conf)  

Next we’ll read the CSV rows with individual track events, and make a pair RDD out of all of the rows. Pair RDDs have several advantages -- one of them being gaining the use of a set of library functions for manipulating the data that we wouldn’t have otherwise (i.e. we would have to write equivalent code by hand).

We’ll use `map()` to convert each line of the data into an array, then `reduceByKey` to consolidate all of the arrays.

    trackfile = sc.textFile('tracks.csv')  
    
    def make_tracks_kv(str):
    	l = str.split(",")
    	return [l[1], [[int(l[2]), l[3], int(l[4]), l[5]]]]
    
   	# make a k,v RDD out of the input data  
    tbycust = trackfile.map(lambda line: make_tracks_kv(line)).reduceByKey(lambda a, b: a + b) 

The individual track events are now stored in a pair RDD, with the customer ID as the key.

Now we’re ready to compute a summary profile for each user. 

- Average number of tracks listened during each period of the day: morning, afternoon, evening, and night. We arbitrarily define the time ranges in the code.  This is good for our dashboard, but is also a new feature of the data set which we can use for making predictions later.

- Total unique tracks listened by that user, i.e. the set of unique track IDs.

- Total mobile tracks listened by that user, i.e. the count of tracks that were listened that had their mobile flag set.

By passing a function we’ll write to mapValues, we can compute these components of high-level profile data.  Since we have the summary data readily available we compute some basic statistics on it that we can use for display, using the `colStats` function from `pyspark.mllib.stat`.

    def compute_stats_byuser(tracks):
    	mcount = morn = aft = eve = night = 0
	    tracklist = []
    	for t in tracks:
    		trackid, dtime, mobile, zip = t
    		if trackid not in tracklist:
    			tracklist.append(trackid)
    		d, t = dtime.split(" ")
    		hourofday = int(t.split(":")[0])
    		mcount += mobile
    		if (hourofday < 5):
    			night += 1
    		elif (hourofday < 12):
    			morn += 1
    		elif (hourofday < 17):
    			aft += 1
    		elif (hourofday < 22):
    			eve += 1
    		else:
    			night += 1
    		return [len(tracklist), morn, aft, eve, night, mcount]
 
    # compute profile for each user  
    custdata = tbycust.mapValues(lambda a: compute_stats_byuser(a))  

    # compute aggregate stats for entire track history  
    aggdata = Statistics.colStats(custdata.map(lambda x: x[1]))  

This last line gives us access to the mean, variance, and other statistics for each of the fields in the per-user RDD we created in `custdata`.

Calling `collect()` on this new RDD, we can now persist the results back to a file.  We use a CSV file for output in this example, but the results could easily be stored in a database such as MapR-DB, HBase or an RDBMS (maybe using a Python package like `happybase` or `dbset`).  There are two files to output, one called `live_table.csv` with the latest calculations, and another named `agg_table.csv` with the aggregated data about all customers computed with `Statistics.colStats`.

    for k, v in custdata.collect():
    	unique, morn, aft, eve, night, mobile = v
    	tot = morn + aft + eve + night
    
    	# persist the data, in this case write to a file
		with open('live_table.csv', 'wb') as csvfile:
    		fwriter = csv.writer(csvfile, delimiter=' ',
         	                quotechar='|', quoting=csv.QUOTE_MINIMAL)
    		fwriter.writerow(unique, morn, aft, eve, night, mobile)
    
   		# do the same with the summary data
		with open('agg_table.csv', 'wb') as csvfile:
    		fwriter = csv.writer(csvfile, delimiter=' ',
         	                quotechar='|', quoting=csv.QUOTE_MINIMAL)
    		fwriter.writerow(aggdata.mean()[0], aggdata.mean()[1], aggdata.mean()[2],
                            aggdata.mean()[3], aggdata.mean()[4], aggdata.mean()[5])

To run the code on the MapR cluster we run the command-line utility `spark-submit`:

    [mapr@ip-172-31-42-51 d2]$ /opt/mapr/spark/spark-1.2.1/bin/spark-submit ./rt_profile_dash.py 
    Spark assembly has been built with Hive, including Datanucleus jars on classpath
    wrote 5000 lines of profiles
    averages:  unique: 112 morning: 32 afternoon: 25 evening: 27 night: 31 mobile: 64
    done
    [mapr@ip-172-31-42-51 d2]$

After the job completes, a summary is printed of what was written to the database and the averages for all users (the printing code is not included here, this is present in the github version). 

**Summary and What's Next?  Using the Results**

By persisting our newly computed data about individual customer listening habits into a file or database, we could  then pull the information into a BI tool like Tableau.  We want to show you a few more Spark examples so this is left as a further exercise for the reader, but firing up Apache Drill on this data is a great place to start.  Coupled with a SQL-on-Hadoop technology like Drill, we can take our analysis in new directions and use it to increase revenue, through actions such as:

- Developing custom-tailored advertisements (i.e. ad targeting)
- Extending special offers to customers based on their listening habits
- Giving the support team a real-time view of a specific customer when they call support

With just a few lines of code in Spark, we can get a high-level view of our customer base and their listening habits, all computed using a dataset with millions of rows that stays current with latest information.  In this example you can see the beginnings of how Spark works well as a framework for ensuring that capabilities like this can be delivered well into the future, as your data grows.
