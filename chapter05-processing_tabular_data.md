# Processing Tabular Data
The examples here will help you get started using Apache Spark DataFrames with Scala. The new Spark DataFrames API is designed to make big data processing on tabular data easier. A Spark DataFrame is a distributed collection of data organized into named columns that provides operations to _filter_, _group_, or _compute_ aggregates, and can be used with Spark SQL. DataFrames can be constructed from structured data files, existing RDDs, or external databases.

## Sample Dataset
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

Using Spark DataFrames we will explore the eBay data with questions like:
- How many auctions were held?
- How many bids were made per item?
- What's the minimum, maximum, and average number of bids per item?
- Show the bids with price > 100

## Loading Data into Spark DataFrames
First, we will import some packages and instantiate a sqlContext, which is the entry point for working with structured data (rows and columns) in Spark and allows the creation of DataFrame objects.

```Scala
//  SQLContext entry point for working with structured data
val sqlContext = new org.apache.spark.sql.SQLContext(sc)

// this is used to implicitly convert an RDD to a DataFrame.
import sqlContext.implicits._

// Import Spark SQL data types and Row.
import org.apache.spark.sql._
```

Start by loading the data from the ebay.csv file into a Resilient Distributed Dataset (RDD). RDDs have **transformations** and **actions**; the _first()_ **action** returns the first element in the RDD:

```Scala
// load the data into a  new RDD
val ebayText = sc.textFile("ebay.csv")

// Return the first element in this RDD
ebayText.first()
```

Use a Scala _case class_ to define the Auction schema corresponding to the ebay.csv file. Then a _map()_ **transformation** is applied to each element of _ebayText_ to create the ebay RDD of Auction objects.

```Scala
//define the schema using a case class
case class Auction(auctionid: String, bid: Float, bidtime: Float, bidder: String, bidderrate: Integer, openbid: Float, price: Float, item: String, daystolive: Integer)

// create an RDD of Auction objects
val ebay = ebayText.map(_.split(",")).map(p => Auction(p(0),p(1).toFloat,p(2).toFloat,p(3),p(4).toInt,p(5).toFloat,p(6).toFloat,p(7),p(8).toInt ))
```

Calling _first()_ **action** on the ebay RDD returns the first element in the RDD:

```Scala
// Return the first element in this RDD
ebay.first()

// Return the number of elements in the RDD
ebay.count()
```

A DataFrame is a distributed collection of data organized into named columns. Spark SQL supports automatically converting an RDD containing case classes to a DataFrame with the method toDF():

```Scala
// change ebay RDD of Auction objects to a DataFrame
val auction = ebay.toDF()
```

## Exploring and Querying the eBay Auction Data
DataFrames provide a domain-specific language for structured data manipulation in Scala, Java, and Python; below are some examples with the auction DataFrame. The _show()_ **action** displays the top 20 rows in a tabular form:

```Scala
// Display the top 20 rows of DataFrame
auction.show()
```

DataFrame _printSchema()_ displays the schema in a tree format:

```Scala
// Return the schema of this DataFrame
auction.printSchema()
```

After a DataFrame is instantiated it can be queried. Here are some example using the Scala DataFrame API:

```Scala
// How many auctions were held?
auction.select("auctionid").distinct.count

// How many bids per item?
auction.groupBy("auctionid", "item").count.show

// What's the min number of bids per item? what's the average? what's the max?
auction.groupBy("item", "auctionid").count.agg(min("count"), avg("count"),max("count")).show

// Get the auctions with closing price > 100
val highprice= auction.filter("price > 100")

// display dataframe in a tabular format
highprice.show()
```

A DataFrame can also be registered as a temporary table using a given name, which can then have SQL statements run against it using the methods provided by sqlContext. Here are some example queries using sqlContext:

```Scala
// register the DataFrame as a temp table
auction.registerTempTable("auction")

// How many bids per auction?
val results = sqlContext.sql("SELECT auctionid, item,  count(bid) FROM auction GROUP BY auctionid, item")

// display dataframe in a tabular format
results.show()

val results = sqlContext.sql("SELECT auctionid, MAX(price) FROM auction GROUP BY item,auctionid")
results.show()
```

## Summary
You have now learned how to load data into Spark DataFrames, and explore tabular data with Spark SQL. These code examples can be reused as the foundation to solve any type of business problem.
