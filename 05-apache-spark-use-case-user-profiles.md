## Computing User Profiles with Spark
This use case will bring together the core concepts of Spark and use a large dataset to build a simple real-time dashboard that provides insight into customer behaviors.

Spark is an enabling technology in a wide variety of use cases across many industries. Spark is a great candidate anytime results are needed fast and much of the computations can be done in memory. The language used here will be Python, because it does a nice job of reducing the amount of boilerplate code required to illustrate these examples.

### Delivering Music
Music streaming is a rather pervasive technology which generates massive quantities of data. This type of service is much like people would use every day on a desktop or mobile device, whether as a subscriber or a free listener (perhaps even similar to a Pandora). This will be the foundation of the use case to be explored. Data from such a streaming service will be analyzed.

The basic layout consists of customers whom are logging into this service and listening to music tracks, and they have a variety of parameters:
- Demographic information (gender, location, etc.)
- Free / paid subscriber
- Listening history; tracks selected, when, and geolocation when they were selected

Python, PySpark and MLlib will be used to compute some basic statistics for a dashboard, enabling a high-level view of customer behaviors as well as a constantly updated view of the latest information.

### Looking at the Data
This service has users whom are continuously connecting to the service and listening to tracks. Customers listening to music from this streaming service generate events, and over time they represent the highest level of detail about customers' behaviors.

The data will be loaded directly from a CSV file. There are a couple of steps to perform before it can be analyzed. The data will need to be transformed and loaded into a _PairRDD_. This is because the data consists of arrays of (key, value) tuples.

The customer events-individual tracks dataset ([tracks.csv](data/tracks.csv)) consists of a collection of events, one per line, where each event is a client listening to a track. This size is approximately 1M lines and contains simulated listener events over several months. Because this represents things that are happening at a very low level, this data has the potential to grow very large.

Field Name  | Event ID      | Customer ID   | Track ID      | Datetime            | Mobile        | Listening Zip
----------- | ------------- | ------------- | ------------- | ------------------- | ------------- | -------------
**Type**    | **_Integer_** | **_Integer_** | **_Integer_** | **_String_**        | **_Integer_** | **_Integer_**
**Example** | 9999767       | 2597          | 788           | 2014-12-01 09:54:09 | 0             | 11003

The event, customer and track IDs show that a customer listened to a specific track. The other fields show associated information, like whether the customer was listening on a mobile device, and a geolocation. This will serve as the input into the first Spark job.

The customer information dataset ([cust.csv](data/cust.csv)) consists of all statically known details about a user.

Field Name  | Customer ID   | Name              | Gender        | Address              | Zip           | Sign Date    | Status        | Level         | Campaign      | Linked with apps?
----------- | ------------- | ----------------- | ------------- | -------------------- | ------------- | ------------ | ------------- | ------------- | ------------- | -----------------
**Type**    | **_Integer_** | **_String_**      | **_Integer_** | **_String_**         | **_Integer_** | **_String_** | **_Integer_** | **_Integer_** | **_Integer_** | **_Integer_**
**Example** | 10            | Joshua Threadgill | 0             | 10084 Easy Gate Bend | 66216         | 01/13/2013   | 0             | 1             | 1             | 1

The fields are defined as follows:
- **Customer ID**: a unique identifier for that customer
- **Name, gender, address, zip**: the customer's associated information
- **Sign date**: the date of addition to the service
- **Status**: indicates whether or not the account is active (0 = closed, 1 = active)
- **Level**: indicates what level of service -0, 1, 2 for Free, Silver and Gold, respectively
- **Campaign**: indicates the campaign under which the user joined, defined as the following (fictional) campaigns driven by our (also fictional) marketing team:
  - **NONE** no campaign
  - **30DAYFREE** a '30 days free' trial offer
  - **SUPERBOWL** a Super Bowl-related program
  - **RETAILSTORE** an offer originating in brick-and-mortar retail stores
  - **WEBOFFER** an offer for web-originated customers

Other datasets that would be available, but will not be used for this use case, would include:
- Advertisement click history
- Track details like title, album and artist

### Customer Analysis
All the right information is in place and a lot of micro-level detail is available that describes what customers listen to and when. The quickest way to get this data to a dashboard is by leveraging Spark to create summary information for each customer as well as basic statistics about the entire user base. After the results are generated, they can be persisted to a file which can be easily used for visualization with BI tools such as Tableau, or other dashboarding frameworks like C3.js or D3.js.

Step one in getting started is to initialize a Spark context. Additional parameters could be passed to the _SparkConf_ method to further configure the job, such as setting the master and the directory where the job executes.
<pre data-code-language="python" data-not-executable="true" data-type="programlisting">
from pyspark import SparkContext, SparkConf
from pyspark.mllib.stat import Statistics
import csv

conf = SparkConf().setAppName('ListenerSummarizer')
sc = SparkContext(conf=conf)
</pre>

The next step will be to read the CSV records with the individual track events, and make a _PairRDD_ out of all of the rows. To convert each line of data into an array, the _map()_ function will be used, and then _reduceByKey()_ is called to consolidate all of the arrays.
<pre data-code-language="python" data-not-executable="true" data-type="programlisting">
trackfile = sc.textFile('/home/jovyan/work/datasets/spark-ebook/tracks.csv')

def make_tracks_kv(str):
    l = str.split(",")
    return [l[1], [[int(l[2]), l[3], int(l[4]), l[5]]]]

    # make a k,v RDD out of the input data
    tbycust = trackfile.map(lambda line: make_tracks_kv(line))
      .reduceByKey(lambda a, b: a + b)
</pre>

The individual track events are now stored in a _PairRDD_, with the customer ID as the key. A summary profile can now be computed for each user, which will include:
- Average number of tracks during each period of the day (time ranges are arbitrarily defined in the code)
- Total unique tracks, i.e., the set of unique track IDs
- Total mobile tracks, i.e., tracks played when the mobile flag was set

By passing a function to _mapValues_, a high-level profile can be computed from the components. The summary data is now readily available to compute basic statistics that can be used for display, using the _colStats_ function from _pyspark.mllib.stat_.
<pre data-code-language="python" data-not-executable="true" data-type="programlisting">
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
</pre>

The last line provides meaningful statistics like the mean and variance for each of the fields in the per-user RDDs that were created in _custdata_.

Calling _collect()_ on this RDD will persist the results back to a file. The results could be stored in a database such as MapR-DB, HBase or an RDBMS (using a Python package like _happybase_ or _dbset_). For the sake of simplicity for this example, using CSV is the optimal choice. There are two files to output:
- _live_table.csv_ containing the latest calculations
- _agg_table.csv_ containing the aggregated data about all customers computed with _Statistics.colStats_

<nobr/>
<pre data-code-language="python" data-not-executable="true" data-type="programlisting">
for k, v in custdata.collect():
    unique, morn, aft, eve, night, mobile = v
    tot = morn + aft + eve + night

    # persist the data, in this case write to a file
    with open('/home/jovyan/work/datasets/spark-ebook/live_table.csv', 'wb') as csvfile:
        fwriter = csv.writer(csvfile, delimiter=' ',
            quotechar='|', quoting=csv.QUOTE_MINIMAL)
        fwriter.writerow(unique, morn, aft, eve, night, mobile)

    # do the same with the summary data
    with open('/home/jovyan/work/datasets/spark-ebook/agg_table.csv', 'wb') as csvfile:
        fwriter = csv.writer(csvfile, delimiter=' ',
            quotechar='|', quoting=csv.QUOTE_MINIMAL)
        fwriter.writerow(aggdata.mean()[0], aggdata.mean()[1],
            aggdata.mean()[2], aggdata.mean()[3], aggdata.mean()[4],
            aggdata.mean()[5])
</pre>

After the job completes, a summary is displayed of what was written to the CSV table and the averages for all users.

### The Results
With just a few lines of code in Spark, a high-level customer behavior view was created, all computed using a dataset with millions of rows that stays current with the latest information. Nearly any toolset that can utilize a CSV file can now leverage this dataset for visualization.

This use case showcases how easy it is to work with Spark. Spark is a framework for ensuring that new capabilities can be delivered well into the future, as data volumes grow and become more complex.

{% include "thebe_py.js" %}
