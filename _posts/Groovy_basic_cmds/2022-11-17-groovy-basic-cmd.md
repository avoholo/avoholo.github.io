---
title: "Groovy 기초 - String, Files, Iteration"
date: 2022-11-15 00:00:00 +09:00
modified: 2022-11-15 00:00:00 +09:00
tags: [Scala]
description: Scala Basic Commands. 
image: "/_posts/$folder_name/default_post_image.png"
---

<figure>
<img src="https://raw.githubusercontent.com/avoholo/avoholo.github.io/master/_posts/Template_concept-notes-detail/default_post_image.png" alt="default_post_image">
<figcaption>Fig 1. </figcaption>
</figure>




Template<sup id="medium">[[1]](#medium-ref)</sup>



[GroovyWebConsole](https://groovyconsole.appspot.com) 여기가 Groovy Online Compiler중에선 가장 빠른거같다.





### String

<hr style="height:20px; visibility:hidden;" />

#### Get firstline of string

~~~scala
def string = "hello!\nMyName Is\nJohn."

string.eachLine{ line, count ->
	def firstline = ""
	if(count==0){
		firstline = line
	}
	println(firstline)
}
~~~

<hr style="height:20px; visibility:hidden;" />

#### Substring & indexOf

~~~scala
status = "HTTP/1.1 200 OK"

int start = status.indexOf(' ')
int end = status.indexOf('OK')
String status_code = status.substring(start, end).trim()

println(status_code)
> 200
~~~

<hr style="height:20px; visibility:hidden;" />

#### Replace String

~~~scala
tmp = "/var/lib/conf/docker/knk/"

println("** FAILED! ** " + tmp.replace("$baseDir", "").replaceAll("/",""))
> knk
~~~

<hr style="height:20px; visibility:hidden;" />

#### Contains

~~~scala
str = "Hello World!"

println(str.contains("world"));
> false

println(str.contains("World"));
> true
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

### Jenkins Code

<hr style="height:20px; visibility:hidden;" />

#### API Test Automation in Scripted

~~~scala
import groovy.io.FileType

//pipeline {
node(){
    /*
    *** Please Setup targerDir.
    */
    def targetDir = "test"
    
    def baseDir = sh(script:"pwd", returnStdout: true).trim();
    def mydir = new File("$baseDir/$targetDir")
    
    stage('GetFiles'){
        file_list = traverseDir(mydir)
        sorted = file_list.sort()
    }
    
    stage('ListFiles'){
        def count = sh(script:"ls $targetDir | wc -l", returnStdout: true);
        println("Total Number of Files : " + count)
        for(int i=0; i < sorted.size(); i++){
            println("Index " + i + " : " +sorted[i].replace("$baseDir", "").replace("$targetDir", "").replaceAll("/",""))
        }
    }
    
    stage('000_get_auth'){
        getDto(sorted[0], targetDir)
    }
    stage('101_encrypt'){
        execute(sorted[1], targetDir)
    }
    stage('102_decrypt'){
        execute(sorted[2], targetDir)
    }
    stage('202_create_user') {
        execute(sorted[3], targetDir)
    }
    stage('204_get_user_info') {
        execute(sorted[5], targetDir)
    }
    stage('203_delete_user') {
        execute(sorted[4], targetDir)
    }
    
}

@NonCPS    
def traverseDir(dir){
    def list = []
    dir.traverse(type: groovy.io.FileType.FILES, nameFilter: ~/.*\.sh/){
        //println it.path
        list << it.path
        
    }
    return list
}


def getDto(tmp, targetDir){
    def baseDir = sh(script:"pwd", returnStdout: true).trim();
    def mydir = new File("$baseDir/$targetDir")
    token = sh(script: "sh $tmp | jq '.dto.token'", returnStdout: true)
    status = sh(script: "sh $tmp | awk 'NR==1{print \$1}'", returnStdout: true)
    println("** SUCCESS ** " + tmp.replace("$baseDir", "").replace("$targetDir", "").replaceAll("/",""))
    println(status)
}

def execute(tmp, targetDir){
    def baseDir = sh(script:"pwd", returnStdout: true).trim();
    def mydir = new File("$baseDir/$targetDir")
    try {
        status_code = sh(script: "sh $tmp | jq '.header.responseCode' | sed 's/\"//g' | sed 's/NON-0//g'", returnStdout: true)
        status = sh(script: "sh $tmp | jq '.dto'", returnStdout: true)
        if(status_code.toInteger() == 200){
            println(status_code)
            println("** SUCCESS ** " + tmp.replace("$baseDir", "").replace("$targetDir", "").replaceAll("/",""))
            println(status)
        }
    } catch (Exception e) {
        println("** FAILED! ** " + tmp.replace("$baseDir", "").replace("$targetDir", "").replaceAll("/","") + ":" + status_code)
        def fail = sh(script: "sh $tmp | jq '.exception'", returnStdout: true)
        println(fail)
        catchError(stageResult:'FAIL') {
        }
    }
}

/*
@NonCPS
def jsonParse(def json) {
  new groovy.json.JsonSlurperClassic().parseText(json)
}
*/
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