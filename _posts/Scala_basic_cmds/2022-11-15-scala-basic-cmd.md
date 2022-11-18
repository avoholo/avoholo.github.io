---
title: "Scala 기초(1) - Functions"
date: 2022-11-15 00:00:00 +09:00
modified: 2022-11-15 00:00:00 +09:00
tags: [Scala]
description: Scala Basic Commands. 
image: "/_posts/Scala_basic_cmds/default_post_image.png"
---

<figure>
<img src="https://raw.githubusercontent.com/avoholo/avoholo.github.io/master/_posts/Template_concept-notes-detail/default_post_image.png" alt="default_post_image">
<figcaption>Fig 1. </figcaption>
</figure>




Template<sup id="medium">[[1]](#medium-ref)</sup>





### Functions

<hr style="height:20px; visibility:hidden;" />

#### Declaration

~~~scala
def functionName ([list of parameters]) : [return type]
~~~

<hr style="height:20px; visibility:hidden;" />

#### Definition

~~~scala
def functionName ([list of parameters]) : [return type] = {
   function body
   return [expr]
}
~~~

<hr style="height:20px; visibility:hidden;" />

##### Example 1 - Two Parameters

~~~scala
object add {
   def addInt( a:Int, b:Int ) : Int = {
      var sum:Int = 0
      sum = a + b
      return sum
   }
}

scala> add.addInt(1,2)
res15: Int = 3
~~~

<hr style="height:20px; visibility:hidden;" />

##### Example 2 - No Parameters

아래 예제를 보면 `Unit` 이란 Type을 return한다. 여기서 `Unit` 은 뭘까?

Java에서 `void` 와 비슷한 타입이다. 아무것도 return 하지 않지만, Scala의 Method는 반드시 값을 return해야한다.

따라서 아무것도 return 하고 싶지 않다면 저렇게 마지막에 `Unit`을 넣어줘야한다.

~~~scala
object Hello {
   def printMe( ) : Unit = {
      println("Hello, Scala!")
   }
}

scala> Hello.printMe()
Hello, Scala!
~~~




<hr style="height:20px; visibility:hidden;" />

#### 실습

~~~scala
~~~

<br>

### Function with Variable Arguments

<hr style="height:20px; visibility:hidden;" />

#### Multiple Arguments

~~~scala
object Demo {
   def main(args: Array[String]) {
      printStrings("Hello", "Scala", "Python");
   }
   
   def printStrings( args:String* ) = {
      var i : Int = 0;
      
      for( arg <- args ){
         println("Arg value[" + i + "] = " + arg );
         i += 1;
      }
   }
}

scala> Demo.printStrings("Hello", "Scala", "Python")
Arg value[0] = Hello
Arg value[1] = Scala
Arg value[2] = Python
~~~





##### Data Replication





##### Persistence of File System Metadata





##### The Communication Protocols





### Best Practice (Do & Don't)

##### &#9940;Don't - 

##### &#9940;Don't - 

##### &#9989;Do - 

##### &#9989;Do -



### Limitation&#10060;

#### 1. 



**Solution &#10004;** 

- **Merge the small files** - 
- **HAR** - 
- **Sequence files** -

#### 2. 



> Related :
> <a href="/concept-notes">Post 1, </a> 
> <a href="/concept-notes">Post 2</a> 




###### Notes
<small id="medium-ref"><sup>[[1]](#medium)</sup> </small>

###### Resources
1. [HDFS Architecture](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html)