Run these commands in Spark shell using Scala:

1. Total Number of Trips
val df = spark.read.option("header", "true").option("inferSchema", "true").csv("yellow.csv")
val totalTrips = df.count()
println(s"Total number of trips: $totalTrips")

2. Total Revenue Generated (sum of total_amount)
val totalRevenue = df.agg(sum("total_amount")).first().getDouble(0)
println(s"Total revenue: $totalRevenue")

3. Fraction Paid for Tolls
val totalTolls = df.agg(sum("tolls_amount")).first().getDouble(0)
val tollFraction = totalTolls / totalRevenue
println(s"Fraction paid for tolls: $tollFraction")

4. Fraction Given as Driver Tips
val totalTips = df.agg(sum("tip_amount")).first().getDouble(0)
val tipFraction = totalTips / totalRevenue
println(s"Fraction given as tips: $tipFraction")

5. Average Trip Amount
val avgTripAmount = df.agg(avg("total_amount")).first().getDouble(0)
println(s"Average trip amount: $avgTripAmount")

6. Average Trip Distance
val avgTripDistance = df.agg(avg("trip_distance")).first().getDouble(0)
println(s"Average trip distance: $avgTripDistance")

7. Number of Different Payment Types
val paymentTypes = df.select("payment_type").distinct().count()
println(s"Number of different payment types: $paymentTypes")

8. For Each Payment Type: Average Fare, Tip, and Tax
df.groupBy("payment_type")
  .agg(
    avg("fare_amount").alias("avg_fare"),
    avg("tip_amount").alias("avg_tip"),
    avg("mta_tax").alias("avg_tax")
  )
  .show()

9. Hour of Day Generating Highest Revenue
import org.apache.spark.sql.functions._

val dfWithHour = df.withColumn("pickup_hour", hour(to_timestamp(col("tpep_pickup_datetime"), "M/d/yyyy HH:mm")))
dfWithHour.groupBy("pickup_hour")
  .agg(sum("total_amount").alias("total_revenue"))
  .orderBy(desc("total_revenue"))
  .show(1)
