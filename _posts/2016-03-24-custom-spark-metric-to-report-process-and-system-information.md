---
layout: post
title: "Custom spark metric to report process and system information"
category: "Doc"
tags: [scala,spark,code]
---

The [**spark**](http://spark.apache.org/) has an advanced metrics system which uses [codahale metrcis](https://github.com/dropwizard/metrics) to report many system status like memory, jvm, backlog, stages information to outside like csv, graphite, ganglia. It is very convinient to draw some figure with current status. But it lacks some system/process information like cpu, memory and network information.

Here are  the steps to custum a source to report this kind of information using [sigar](https://github.com/hyperic/sigar)

1. Add a class to extends the `org.apache.spark.metrics.source.Source`, which is private class for spark package, so we need to put the new class to package `org.apache.spark`, in the sample, we put into `org.apache.spark.metrics.source`

2. Create a class `SystemMonitor` into `org.apache.spark.metrics.source`, which get cpu, memory, disk and network infomation

   ```scala
   class SystemMonitor(val sigar : Sigar) {
     val CACHED_DURATION = 1000
     val fileSystemNames : Seq[String] = Try(sigar.getFileSystemList().filter(
       _.getType() == FileSystem.TYPE_LOCAL_DISK).map(_.getDirName).toSeq).getOrElse(Seq())
   
     val nics : Seq[String] = Try(sigar.getNetInterfaceList().filter(
       x => (sigar.getNetInterfaceConfig(x).getFlags() &  NetFlags.IFF_UP) != 0
     ).toSeq).getOrElse(Seq())
   
     def getCPUUsage() : Double = {
       sigar.getCpuPerc.getCombined
     }
   
     def getUsedMem() : Long = {
       sigar.getMem.getActualUsed
     }
   
     def getFreedMem() : Long = {
       sigar.getMem.getActualFree
     }
     /* unit: bytes */
     def getDiskReadBytes() : Long = {
       fileSystemNames.map(
           x => sigar.getFileSystemUsage(x).getDiskReadBytes).reduceOption(_ + _).getOrElse(0L)
     }
   
     /* unit: bytes/second */
     def getDiskWriteBytes() : Long = {
      fileSystemNames.map(
           x => sigar.getFileSystemUsage(x).getDiskWriteBytes).reduceOption(_ + _).getOrElse(0L)
     }
   
     def getNICTotalRxBytes() : Long = {
       nics.map(
           x => sigar.getNetInterfaceStat(x).getRxBytes).reduceOption(_ + _).getOrElse(0L)
     }
   
     def getNICTotalTxBytes() : Long = {
       nics.map(
         x => sigar.getNetInterfaceStat(x).getTxBytes).reduceOption(_ + _).getOrElse(0L)
     }
   
     def getNICTotalSpeed() : Long = {
       nics.map(
         x => sigar.getNetInterfaceStat(x).getSpeed).reduceOption(_ + _).getOrElse(0L)
     }
   
     def getNICRxBytes(nicName : String) : Long = {
       sigar.getNetInterfaceStat(nicName).getRxBytes
     }
   
     def getNICTxBytes(nicName : String) : Long = {
       sigar.getNetInterfaceStat(nicName).getTxBytes
     }
   
     def getNICSpeed(nicName : String) : Long = {
       sigar.getNetInterfaceStat(nicName).getSpeed
     }   
   }
   ```

3. Create a `SystemMetrics` under `org.apache.spark.metrics.source`

   ```scala
   class SystemMetrics extends Source {
     override val sourceName: String = "system"
   
     override val metricRegistry: MetricRegistry = new MetricRegistry()
   
   
     val systemMonitor = new SystemMonitor(SigarHolder.sigar)
   
     metricRegistry.register(MetricRegistry.name("cpu_usage"), new Gauge[Double] {
       override def getValue: Double = systemMonitor.getCPUUsage()
     })
   
     metricRegistry.register(MetricRegistry.name("used_mem"), new Gauge[Long] {
       override def getValue: Long = systemMonitor.getUsedMem()
     })
   
     metricRegistry.register(MetricRegistry.name("free_mem"), new Gauge[Long] {
       override def getValue: Long = systemMonitor.getFreedMem()
     })
   
     metricRegistry.register(MetricRegistry.name("disk_read_bytes"), new Gauge[Long] {
       override def getValue: Long = systemMonitor.getDiskReadBytes()
     })
   
     metricRegistry.register(MetricRegistry.name("disk_write_bytes"), new Gauge[Long] {
       override def getValue: Long = systemMonitor.getDiskWriteBytes()
     })
   
     metricRegistry.register(MetricRegistry.name("total_rx_bytes"), new Gauge[Long] {
       override def getValue: Long = systemMonitor.getNICTotalRxBytes()
     })
   
     metricRegistry.register(MetricRegistry.name("total_tx_bytes"), new Gauge[Long] {
       override def getValue: Long = systemMonitor.getNICTotalTxBytes()
     })
   
     def registerNIC(nicName : String) = {
       metricRegistry.register(MetricRegistry.name( nicName  + "_tx_bytes"), new Gauge[Long] {
         override def getValue: Long = systemMonitor.getNICTxBytes(nicName)
       })
   
       metricRegistry.register(MetricRegistry.name( nicName  + "_rx_bytes"), new Gauge[Long] {
         override def getValue: Long = systemMonitor.getNICRxBytes(nicName)
       })
   
     }
   
     systemMonitor.nics.map(registerNIC)
   }
   ```
 
4. Add the new class to `metrics.properties` under `$SPARK_HOME/conf`, which monitors all the instance system information

   > *.source.system.class=org.apache.spark.metrics.source.SystemMetrics
   
   Of course, you can add a new metrics properties file, then submit using following arguments.

   > --files /path/to/your/metrics.properties
   > --conf spark.metrics.conf=metrics.properties


   You can set this properties via spark configuration which begins with `spark.metrics.conf.`

5. Proc cpu can use the same method.
 
