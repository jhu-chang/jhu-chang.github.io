---
layout: post
title: "Spark Structure Streaming"
category: "BigData"
tags: [spark,code]
---

- Requirements

  ```scala
  import org.apache.spark.sql._
  import org.apache.commons.io.FileUtils._
  import org.apache.commons.io.filefilter._
  import collection.JavaConverters._
  
  
  def toFile(str : String) = {
    new java.io.File(str)
  }
  def r(str : String) = {
    iterateFilesAndDirs(toFile(str), FileFilterUtils.directoryFileFilter(),  new RegexFileFilter("^.*\\p{XDigit}+-\\p{XDigit}+-\\p{XDigit}+-\\p{XDigit}+-\\p{XDigit}+$")).asScala.toSeq.sortBy(_.lastModified()).foreach(x => if (new java.io.File(str).getCanonicalPath() != x.getCanonicalPath())sqlContext.read.parquet(x.getCanonicalPath()).show(100, false))
  }
  deleteDirectory(toFile("""G:\pipe\nerus\output"""))
  //deleteDirectory(toFile("""G:\Spark\src\spark.fork\spark\bin\metastore_db"""))
  deleteDirectory(toFile("""G:\Spark\src\spark.fork\spark\bin\checkpoint"""))
  deleteQuietly(toFile("""G:\pipe\nerus\tmp\narus2.json"""))
  deleteQuietly(toFile("""G:\pipe\nerus\tmp\narus3.json"""))
  //deleteQuietly(toFile("""G:\Spark\src\spark.fork\spark\bin\spark.log"""))
  ```

- Checkpoint

  ```scala
  sqlContext.setConf("spark.sql.streaming.checkpointLocation", "checkpoint")
  ```

  ```scala
  clientip.write.option("checkpointLocation", "checkpoint")
  ```

- Streaming

  ```scala
  sqlContext.setConf("spark.sql.streaming.checkpointLocation", "checkpoint")
  // val static_records = sqlContext.read.json("G:/pipe/nerus/tmp/*.json")
  val records = sqlContext.read.format("json").stream("G:/pipe/nerus/tmp")
  val clientip=records.select($"CLIENTIP")
  val flushed = clientip.write.trigger(ProcessingTime("5 seconds")).mode("Append").format("json").startStream("G:/pipe/nerus/output")
  flushed.isActive
  ```

- Aggregation

  ```scala
  //TODO: agg, aggregation can not work with append mode, use overwrite here
  import org.apache.spark.sql._
  // workaround the output mode check due spark bug
  sqlContext.setConf("spark.sql.streaming.unsupportedOperationCheck", "false")
  // val static_records = sqlContext.read.json("G:/pipe/nerus/tmp/*.json")
  val records = sqlContext.read.format("json").stream("G:/pipe/nerus/tmp")
  val clientip=records.select($"CLIENTIP").agg(count("*") as 'count)
  //val flushed = clientip.write.option("checkpointLocation", "checkpoint").mode("overwrite").trigger(ProcessingTime("5 seconds")).format("json").startStream("G:/pipe/nerus/output")
  val flushed = clientip.write.option("checkpointLocation", "checkpoint").mode("overwrite").trigger(ProcessingTime("5 seconds")).format("com.databricks.spark.csv").option("header", "true").startStream("G:/pipe/nerus/output")
  flushed.isActive
  ```

- Event time window

  ```scala
  //window, aggregation can not work with append mode, use overwrite here
  import org.apache.spark.sql._
  // workaround the output mode check due spark bug
  sqlContext.setConf("spark.sql.streaming.unsupportedOperationCheck", "false")
  // val static_records = sqlContext.read.json("G:/pipe/nerus/tmp/*.json")
  val records = sqlContext.read.format("json").stream("G:/pipe/nerus/tmp")
  val clientip = records.groupBy(window($"SESSION_START_TIME", "5 seconds", "1 seconds")).agg(count("*") as 'count).orderBy($"window.start".asc).select($"window.start", $"window.end", $"count")
  //val flushed = clientip.write.option("checkpointLocation", "checkpoint").mode("overwrite").trigger(ProcessingTime("5 seconds")).format("json").startStream("G:/pipe/nerus/output")
  val flushed = clientip.write.option("checkpointLocation", "checkpoint").mode("overwrite").trigger(ProcessingTime("5 seconds")).format("com.databricks.spark.csv").option("header", "true").startStream("G:/pipe/nerus/output")
  flushed.isActive
  ```

- System time window

  ```scala
  
  import org.apache.spark.sql._
  // workaround the output mode check due spark bug
  sqlContext.setConf("spark.sql.streaming.unsupportedOperationCheck", "false")
  val records = sqlContext.read.format("json").stream("G:/pipe/nerus/tmp")
  val clientip = records.groupBy(window(current_timestamp(), "5 seconds", "1 seconds")).agg(count("*") as 'count).orderBy($"window.start".asc).select($"window.start", $"window.end", $"count")
  //val flushed = clientip.write.option("checkpointLocation", "checkpoint").mode("overwrite").trigger(ProcessingTime("5 seconds")).format("json").startStream("G:/pipe/nerus/output")
  val flushed = clientip.write.option("checkpointLocation", "checkpoint").mode("overwrite").trigger(ProcessingTime("5 seconds")).format("com.databricks.spark.csv").option("header", "true").startStream("G:/pipe/nerus/output")
  flushed.isActive
  ```

- Join (static)

  ```scala
  //TODO kryo reports NPE on this
  import org.apache.spark.sql._
  // workaround the output mode check due spark bug
  sqlContext.setConf("spark.sql.streaming.unsupportedOperationCheck", "false")
  val static_records = sqlContext.read.json("G:/pipe/nerus/tmp2/*.json")
  val records = sqlContext.read.format("json").stream("G:/pipe/nerus/tmp")
  val clientip=records.join(static_records, records("SESSIONKEY") === static_records("SESSIONKEY"), "outer").groupBy(records("SESSIONKEY")).agg(sum("CONTENT") as 'count)
  //val flushed = clientip.write.option("checkpointLocation", "checkpoint").mode("overwrite").trigger(ProcessingTime("5 seconds")).format("json").startStream("G:/pipe/nerus/output")
  val flushed = clientip.write.option("checkpointLocation", "checkpoint").mode("overwrite").trigger(ProcessingTime("5 seconds")).format("com.databricks.spark.csv").option("header", "true").startStream("G:/pipe/nerus/output")
  flushed.isActive
  ```

- Join (Streaming)

  ```scala
  //TODO kryo reports NPE on this
  import org.apache.spark.sql._
  // workaround the output mode check due spark bug
  sqlContext.setConf("spark.sql.streaming.unsupportedOperationCheck", "false")
  val static_records = sqlContext.read.format("json").stream("G:/pipe/nerus/tmp2")
  val records = sqlContext.read.format("json").stream("G:/pipe/nerus/tmp")
  val clientip=records.join(static_records, records("SESSIONKEY") === static_records("SESSIONKEY"), "outer").groupBy(records("SESSIONKEY")).agg(sum("CONTENT") as 'count)
  val flushed = clientip.write.option("checkpointLocation", "checkpoint").mode("overwrite").trigger(ProcessingTime("5 seconds")).format("json").startStream("G:/pipe/nerus/output")
  flushed.isActive
  ```

- Show result

  ```scala
  // show result
  sqlContext.read.parquet("""G:\pipe\nerus\output""").orderBy($"start".asc).show(100, false)
  sqlContext.read.parquet("""G:\pipe\nerus\output""").show(100, false)
  r("""G:\pipe\nerus\output""")
  ```