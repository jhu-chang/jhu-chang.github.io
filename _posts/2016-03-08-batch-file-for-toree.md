---
layout: post
title: "Batch file for toree"
category: "Code"
tags: [misc,jupyter,python,spark, windows]
---

[Toree](http://toree.incubator.apache.org/) ([source](https://github.com/apache/incubator-toree)) is a spark notebook based on [Jupyter](http://www.jupyter.org), here is the batch file for windows, put this to the `bin` directory of `jupyter toree kernals home`

```
@echo off
set PROG_HOME=%~dp0
REM set PROG_HOME=C:\ProgramData\jupyter\kernels\toree\bin

if "%SPARK_HOME%" == "" (
  echo SPARK_HOME must be set to the location of a Spark distribution!
  exit 1
)

echo Starting Spark Kernel with SPARK_HOME: %SPARK_HOME%

pushd %PROG_HOME%..\lib
for /r %%f in (toree-kernel-assembly*) do set KERNEL_ASSEMBLY=%%f
popd

echo Starting Spark Kernel with: %KERNEL_ASSEMBLY%
REM disable randomized hash for string in Python 3.3+
set PYTHONHASHSEED=0
set SPARK_SUBMIT_OPTS=-Dscala.usejavacp=true
set SPARK_CLASSPATH=%KERNEL_ASSEMBLY%
"%SPARK_HOME%\bin\spark-submit2.cmd" %SPARK_OPTS% --class org.apache.toree.Main "%KERNEL_ASSEMBLY%" %*

:END
```

