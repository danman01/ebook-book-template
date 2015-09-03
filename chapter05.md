# Applying Spark to Solve Actual Business Problems
Now that you have learned how to get Spark up and running, it's time to put some of this practical knowledge to use. The use cases and code examples described in this chapter are reasonably short and to the point. They are intended to provide enough context on the problem being described so they can be leveraged for solving many more problems.

If these use cases are not complicated enough for your liking, don't fret, as there are more in-depth use cases provided at the end of the book. Those use cases are much more involved, and get into more details and capabilities of Spark.

The first use case walks through loading and querying tabular data. This example is a foundational construct of loading data in Spark. This will enable you to understand how Spark gets data from disk, as well as how to inspect the data and run queries of varying complexity.

The second use case here is about building user profiles from a music streaming service. User profiles are used across almost all industries. The concept of a customer 360 is based on a user profile. The premise behind a user profile is to build a dossier about a user. Whether or not the user is a known person or just a number in a database is usually a minor detail, but one that would fall into areas of privacy concern. User profiles are also at the heart of all major digital advertising campaigns. One of the most common Internet-based scenarios for leveraging user profiles is to understand how long a user stays on a particular website. All-in-all, building user profiles with Spark is child's play.  

## Processing Tabular Data
The examples here will help you get started using Apache Spark DataFrames with Scala. The new Spark DataFrames API is designed to make big data processing on tabular data easier. A Spark DataFrame is a distributed collection of data organized into named columns that provides operations to _filter_, _group_, or _compute_ aggregates, and can be used with Spark SQL. DataFrames can be constructed from structured data files, existing RDDs, or external databases.

### Sample Dataset
The dataset to be used is from eBay online auctions. The eBay online auction dataset contains the following fields:
- **auctionid** - unique identifier of an auction
- **bid** - the proxy bid placed by a bidder
- **bidtime** - the time (in days) that the bid was placed, from the start of the auction
- **bidder** - eBay username of the bidder
- **bidderrate** - eBay feedback rating of the bidder
- **openbid** - the opening bid set by the seller
- **price** - the closing price that the item sold for (equivalent to the second highest bid + an increment)

The table below shows the fields with some sample data:

auctionid  | bid | bidtime  | bidder   | bidderrate | openbid | price | item | daystolive
---------- | --- | -------- | -------- | ---------- | ------- | ----- | ---- | ----------
8213034705 | 95  | 2.927373 | jake7870 | 0          | 95      | 117.5 | xbox | 3

Using Spark DataFrames, we will explore the eBay data with questions like:
- How many auctions were held?
- How many bids were made per item?
- What's the minimum, maximum, and average number of bids per item?
- Show the bids with price > 100

### Loading Data into Spark DataFrames
First, we will import some packages and instantiate a sqlContext, which is the entry point for working with structured data (rows and columns) in Spark and allows the creation of DataFrame objects.

```scala
//  SQLContext entry point for working with structured data
val sqlContext = new org.apache.spark.sql.SQLContext(sc)

// this is used to implicitly convert an RDD to a DataFrame.
import sqlContext.implicits._

// Import Spark SQL data types and Row.
import org.apache.spark.sql._
```

Start by loading the data from the ebay.csv file into a Resilient Distributed Dataset (RDD). RDDs have **transformations** and **actions**; the _first()_ **action** returns the first element in the RDD:

```scala
// load the data into a  new RDD
val ebayText = sc.textFile("ebay.csv")

// Return the first element in this RDD
ebayText.first()
```

Use a Scala _case class_ to define the Auction schema corresponding to the ebay.csv file. Then a _map()_ **transformation** is applied to each element of _ebayText_ to create the ebay RDD of Auction objects.

```scala
//define the schema using a case class
case class Auction(auctionid: String, bid: Float, bidtime: Float,
  bidder: String, bidderrate: Integer, openbid: Float, price: Float,
  item: String, daystolive: Integer)

// create an RDD of Auction objects
val ebay = ebayText.map(_.split(",")).map(p => Auction(p(0),
  p(1).toFloat,p(2).toFloat,p(3),p(4).toInt,p(5).toFloat,
  p(6).toFloat,p(7),p(8).toInt))
```

Calling _first()_ **action** on the ebay RDD returns the first element in the RDD:

```scala
// Return the first element in this RDD
ebay.first()

// Return the number of elements in the RDD
ebay.count()
```

A DataFrame is a distributed collection of data organized into named columns. Spark SQL supports automatically converting an RDD containing case classes to a DataFrame with the method toDF():

```scala
// change ebay RDD of Auction objects to a DataFrame
val auction = ebay.toDF()
```

### Exploring and Querying the eBay Auction Data
DataFrames provide a domain-specific language for structured data manipulation in Scala, Java, and Python; below are some examples with the auction DataFrame. The _show()_ **action** displays the top 20 rows in a tabular form:

```scala
// Display the top 20 rows of DataFrame
auction.show()
```

DataFrame _printSchema()_ displays the schema in a tree format:

```scala
// Return the schema of this DataFrame
auction.printSchema()
```

After a DataFrame is instantiated it can be queried. Here are some example using the Scala DataFrame API:

```scala
// How many auctions were held?
auction.select("auctionid").distinct.count

// How many bids per item?
auction.groupBy("auctionid", "item").count.show

// What's the min number of bids per item?
// what's the average? what's the max?
auction.groupBy("item", "auctionid").count
  .agg(min("count"), avg("count"),max("count")).show

// Get the auctions with closing price > 100
val highprice= auction.filter("price > 100")

// display dataframe in a tabular format
highprice.show()
```

A DataFrame can also be registered as a temporary table using a given name, which can then have SQL statements run against it using the methods provided by sqlContext. Here are some example queries using sqlContext:

```scala
// register the DataFrame as a temp table
auction.registerTempTable("auction")

// How many bids per auction?
val results = sqlContext.sql(
  "SELECT auctionid, item,  count(bid) FROM auction
    GROUP BY auctionid, item"
  )

// display dataframe in a tabular format
results.show()

val results = sqlContext.sql(
  "SELECT auctionid, MAX(price) FROM auction
    GROUP BY item,auctionid"
  )
results.show()
```

### Summary
You have now learned how to load data into Spark DataFrames, and explore tabular data with Spark SQL. These code examples can be reused as the foundation to solve any type of business problem.

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

```python
from pyspark import SparkContext, SparkConf
from pyspark.mllib.stat import Statistics
import csv

conf = SparkConf().setAppName('ListenerSummarizer')
sc = SparkContext(conf=conf)
```

The next step will be to read the CSV records with the individual track events, and make a _PairRDD_ out of all of the rows. To convert each line of data into an array, the _map()_ function will be used, and then _reduceByKey()_ is called to consolidate all of the arrays.

```python
trackfile = sc.textFile('/tmp/data/tracks.csv')

def make_tracks_kv(str):
    l = str.split(",")
    return [l[1], [[int(l[2]), l[3], int(l[4]), l[5]]]]

    # make a k,v RDD out of the input data
    tbycust = trackfile.map(lambda line: make_tracks_kv(line))
      .reduceByKey(lambda a, b: a + b)
```

The individual track events are now stored in a _PairRDD_, with the customer ID as the key. A summary profile can now be computed for each user, which will include:
- Average number of tracks during each period of the day (time ranges are arbitrarily defined in the code)
- Total unique tracks, i.e., the set of unique track IDs
- Total mobile tracks, i.e., tracks played when the mobile flag was set

By passing a function to _mapValues_, a high-level profile can be computed from the components. The summary data is now readily available to compute basic statistics that can be used for display, using the _colStats_ function from _pyspark.mllib.stat_.

```python
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
```

The last line provides meaningful statistics like the mean and variance for each of the fields in the per-user RDDs that were created in _custdata_.

Calling _collect()_ on this RDD will persist the results back to a file. The results could be stored in a database such as MapR-DB, HBase or an RDBMS (using a Python package like _happybase_ or _dbset_). For the sake of simplicity for this example, using CSV is the optimal choice. There are two files to output:
- _live_table.csv_ containing the latest calculations
- _agg_table.csv_ containing the aggregated data about all customers computed with _Statistics.colStats_

```python
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
        fwriter.writerow(aggdata.mean()[0], aggdata.mean()[1],
            aggdata.mean()[2], aggdata.mean()[3], aggdata.mean()[4],
            aggdata.mean()[5])
```

After the job completes, a summary is displayed of what was written to the CSV table and the averages for all users.

### The Results
With just a few lines of code in Spark, a high-level customer behavior view was created, all computed using a dataset with millions of rows that stays current with the latest information. Nearly any toolset that can utilize a CSV file can now leverage this dataset for visualization.

This use case showcases how easy it is to work with Spark. Spark is a framework for ensuring that new capabilities can be delivered well into the future, as data volumes grow and become more complex.
