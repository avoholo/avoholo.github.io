---
title: "Groovy - String, Files, Iteration"
date: 2022-11-15 00:00:00 +09:00
modified: 2022-11-15 00:00:00 +09:00
tags: [Scala]
description: Groovy Basic Commands. 
image: "/_posts/$folder_name/default_post_image.png"
---

<figure>
<img src="https://raw.githubusercontent.com/avoholo/avoholo.github.io/master/_posts/Groovy_basic_cmds/default_post_image.png" alt="default_post_image">
<figcaption>Fig 1. Apache Groovy</figcaption>
</figure>





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


<hr style="height:20px; visibility:hidden;" />



> Related :
> <a href="/concept-notes">Post 1, </a> 
> <a href="/concept-notes">Post 2</a> 




###### Notes
<small id="medium-ref"><sup>[[1]](#medium)</sup> </small>

###### Resources
1. None.