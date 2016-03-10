---
layout: post
title: "Dynamic generared class"
category: "Code"
tags: [scala,java,code]
---

Here is the sample code to dynamic create the class in `scala`

1. create an abstract class: `Test.scala`

   ```scala
   abstract class Test {
       def f(): Any
   }
   ```

2. import the necessary packages

   ```scala
   import org.codehaus.janino.ClassBodyEvaluator
   ```

3. use the code to parse the dynamic class

   ```scala
   val evaluator = new ClassBodyEvaluator()
   evaluator.setParentClassLoader(Thread.currentThread().getContextClassLoader)
   evaluator.setClassName("GeneratedClass")
   evaluator.setDefaultImports(Array(classOf[Test].getName))
   evaluator.setExtendedClass(classOf[Test])
   val code = """ 
            |    public Object f() {
            |       return new Integer(1);
            |    }
            |""".stripMargin
   evaluator.cook("generated.java", code)
   val instance = evaluator.getClazz().newInstance().asInstanceOf[GeneratedClass]
   instance.f()
   ```
