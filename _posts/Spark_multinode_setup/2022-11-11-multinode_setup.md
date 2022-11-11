---
title: "Spark Cluster Setup"
date: 2022-11-11 09:00:00 +07:00
modified: 2022-11-11 09:00:00 +07:00
tags: [Spark]
description: Introduction to Spark Cluster Setup.
image: "/_posts/multinode_setup/default_post_image.png"
---

<figure>
<img src="https://raw.githubusercontent.com/avoholo/avoholo.github.io/master/_posts/Spark_multinode_setup/default_post_image.png" alt="default_post_image">
<figcaption>Fig 1. Apache Spark</figcaption>
</figure>


<hr style="height:10px; visibility:hidden;" />
이번 포스트에선 가이드를 보며 따라 할 수 있는 **Walk Through Template**을 작성 할 예정이다. 

이전에 FastAPI를 사용해보며 작성 해놓은 가이드가 있는데, 설치 방법이나 어떤 Library를 Import 했는지 매번 까먹어서 Template 작성겸 여기다 정리하려고 한다.

Walk Through Template 사용시 해당 기술의 개념을 정리 하기보단, **실습 내역을 기록하는 목적**으로 사용한다.

따라서 개념 정리는 따로 **Concept Note**를 통해 일괄적으로 관리한다.



### 1. 사전 환경 설정

<hr style="height:10px; visibility:hidden;" />

#### User 생성

~~~bash
sudo useradd spark -m -s /bin/bash
sudo passwd spark

Changing password for user spark.
New password: spark
passwd: all authentication tokens updated successfully.
~~~

<br>

#### Sudo 권한 부여

~~~bash
$ sudo visudo
>> spark ALL=(ALL) NOPASSWD:ALL

>> Ctrl + X (exit)
>> Y (Yes)
>> Enter
~~~

<br>

#### Base Directory 생성

~~~bash
sudo mkdir /spark
sudo chown spark:spark /spark
~~~

<br>

#### Utils 설치

~~~bash
sudo yum update -y
sudo yum install -y wget unzip bzip2 net-tools
~~~

<br>

#### Hosts 설정

~~~bash
$ sudo vi /etc/hosts
1**.***.**.*0 spark-master01
1**.***.**.*3 spark-worker01
1**.***.**.*4 spark-worker02
1**.***.**.*5 spark-worker03
~~~

<br>

#### SSH 설정

`~/.ssh/authorized_keys` 파일의 권한이 `-rw-------.`로 되어있어야 비밀번호 없이 ssh가 가능하다.

~~~bash
ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
ssh spark-master01

ECDSA key fingerprint is SHA256:cLfCeQyyIJACmHbJDGMt4wKMaiL+FVClhtwDZyqLEKw.
ECDSA key fingerprint is MD5:bc:a1:24:2c:d7:9f:40:10:f6:80:0f:d4:62:da:6f:66.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'spark-master01,1**.***.**.*0' (ECDSA) to the list of known hosts.
Last login: Fri Nov 11 13:26:27 2022 from 10.10.10.1
~~~

<br>

지금 같은 경우엔 `spark-master` --> `spark-master` 즉, 자기 자신에게 ssh 접근을 시도하지만,

`spark-master` --> `spark-worker`의 경우, master, worker의 **서로의 공개키**가 각 서버의 `authorized_keys` 에 등록이 되어야 **비밀번호 입력 없이 접근이 가능**하다.

따라서, 가장 간단한 방법은 `spark-master`의 `authorized_keys`에 모든 공개키를 입력한다음에, `scp`로 해당 파일을 각 노드에 복사만 해주면 된다.

~~~bash
scp ./authorized_keys spark-worker01:~/.ssh/authorized_keys
scp ./authorized_keys spark-worker02:~/.ssh/authorized_keys
scp ./authorized_keys spark-worker03:~/.ssh/authorized_keys

# spark-worker 1,2,3 접근 후 권한 변경
ssh spark-worker01 chmod 600 ~/.ssh/authorized_keys
ssh spark-worker02 chmod 600 ~/.ssh/authorized_keys
ssh spark-worker03 chmod 600 ~/.ssh/authorized_keys
~~~

<br>

<br>

### 2. Spark 설치 (각 노드별 실행)

<hr style="height:10px; visibility:hidden;" />

#### JDK8 설치

~~~bash
ssh spark-master01
cd /spark && wget https://github.com/ojdkbuild/contrib_jdk8u-ci/releases/download/jdk8u232-b09/jdk-8u232-ojdkbuild-linux-x64.zip && unzip jdk-8u232-ojdkbuild-linux-x64.zip
mv jdk-8u232-ojdkbuild-linux-x64 jdk8 && rm -rf jdk-8u232-ojdkbuild-linux-x64.zip
~~~

<br>

#### Spark Binary로 설치

~~~ bash
ssh spark-master01
cd /spark && wget https://archive.apache.org/dist/spark/spark-3.2.1/spark-3.2.1-bin-hadoop3.2.tgz && tar xvfz spark-3.2.1-bin-hadoop3.2.tgz
mv spark-3.2.1-bin-hadoop3.2 spark3 && rm -rf spark-3.2.1-bin-hadoop3.2.tgz
~~~

<br>

#### 환경변수 설정

~~~bash
cd /spark/spark3/conf
cp spark-env.sh.template spark-env.sh
echo "JAVA_HOME=/spark/jdk8" >> spark-env.sh
echo "export PATH=/spark/jdk8/bin:$PATH" >> ~/.bashrc
. ~/.bashrc

$ vi ~/.bashrc
# Linux
alias v='vi ~/.bashrc'
alias s='source ~/.bashrc'
alias c='clear'
alias cd2='cd ../../'
alias cd3='cd ../../../'

# Spark
SPARK_BASE=/spark
SPARK_HOME=$SPARK_BASE/spark3
SPARK_CONF=$SPARK_HOME/conf

. ~/.bashrc
~~~

<br>

#### Spark Shell 실행

Cluster Setup 전 Standalone 모드로 먼저 Test한다.

~~~bash
cd $SPARK_HOME
$ ./bin/spark-shell

Using Spark's default log4j profile: org/apache/spark/log4j-defaults.properties
Setting default log level to "WARN".
To adjust logging level use sc.setLogLevel(newLevel). For SparkR, use setLogLevel(newLevel).
22/11/11 14:56:54 WARN NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
Spark context Web UI available at http://spark-master01:4040
Spark context available as 'sc' (master = local[*], app id = local-1668146215383).
Spark session available as 'spark'.
Welcome to
      ____              __
     / __/__  ___ _____/ /__
    _\ \/ _ \/ _ `/ __/  '_/
   /___/ .__/\_,_/_/ /_/\_\   version 3.2.1
      /_/
         
Using Scala version 2.12.15 (OpenJDK 64-Bit Server VM, Java 1.8.0_232)
Type in expressions to have them evaluated.
Type :help for more information.

scala> 
~~~

<br>

#### Spark Web-UI 실행

~~~scala
scala> sc
res0: org.apache.spark.SparkContext = org.apache.spark.SparkContext@7db72209

scala> spark
res1: org.apache.spark.sql.SparkSession = org.apache.spark.sql.SparkSession@1df1bd44

scala> sc.master
res2: String = local[*]

scala> sc.uiWebUrl
res3: Option[String] = Some(http://spark-master01:4040)
                            
# spark-master01 IP가 172.12.64.23일 경우 http://172.12.64.23:4040 접근
[spark@spark-master01 ~]# netstat -nlp | grep 4040
tcp6       0      0 :::4040           :::*             LISTEN      1715/java 
~~~

<br>

<br>

### 3. Spark History Server 설치

Spark은 기본적으로 로그를 적재하지 않으므로 별도의 설정이 필요하다.

<hr style="height:10px; visibility:hidden;" />

#### Spark-defaults.conf 설정

~~~bash
cd $SPARK_CONF
cp spark-defaults.conf.template spark-defaults.conf

# Log Directory 생성
mkdir -p $SPARK_HOME/history
echo "spark.history.fs.logDirectory file://$SPARK_HOME/history" >> spark-defaults.conf
echo "spark.eventLog.enabled true" >> spark-defaults.conf
echo "spark.eventLog.dir file://$SPARK_HOME/history" >> spark-defaults.conf
~~~

<br>

#### History 서버 시작

~~~bash
$SPARK_HOME/sbin/start-history-server.sh

# 주소는 http://spark-master01:18080
[spark@spark-master01 conf]$ jps
1995 Jps
1950 HistoryServer

# 필요시 History 서버 종료
$SPARK_HOME/sbin/stop-history-server.sh
~~~

<br>

#### Spark App 별 실습 (word count)

##### 1. spark-shell

~~~bash
sh $SPARK_HOME/bin/spark-shell
scala> sc.uiWebUrl
scala> val rdd = sc.textFile("README.md")
scala> val rdd_word = rdd.flatMap(line => line.split(" "))
scala> val rdd_tuple = rdd_word.map(word => (word, 1))
scala> val rdd_wordcount = rdd_tuple.reduceByKey((v1, v2) => v1 + v2)
scala> val arr_wordcount = rdd_wordcount.collect()
scala> arr_wordcount.take(10).foreach(tuple => println(tuple))

# 종료되면 history의 .inprogress 확장자 사라짐
scala> :quit
~~~

##### 2. pyspark

~~~bash
sh $SPARK_HOME/bin/pyspark
>>> rdd = sc.textFile("README.md")
>>> rdd_word = rdd.flatMap(lambda line: line.split(" "))
>>> rdd_tuple = rdd_word.map(lambda word: (word, 1))
>>> rdd_wordcount = rdd_tuple.reduceByKey(lambda v1,v2: v1 + v2)
>>> arr_wordcount = rdd_wordcount.collect()
>>> for wc in arr_wordcount[:10]:
...     print(wc)
# 종료되면 history의 .inprogress 확장자 사라짐
>>> exit()
~~~

##### 3. spark-sql

~~~bash
sh $SPARK_HOME/bin/spark-sql
spark-sql> select word, count(*) from (select explode(split(*, ' ')) as word from text.`README.md`) group by word limit 10;

# 종료되면 history의 .inprogress 확장자 사라짐
spark-sql> exit;
~~~

<br>

이렇게 Spark Application을 실행하게 되면, History Server(18080), WebUI(4040) 에서 Stage 별 상태를 확인 할 수 있다.

<figure>
<img src="https://raw.githubusercontent.com/avoholo/avoholo.github.io/master/_posts/Spark_multinode_setup/dag_visualization.png" alt="dag_visualization">
<figcaption>Fig 2. Dag Visualization of Spark Application.</figcaption>
</figure>

<br>

<figure>
<img src="https://raw.githubusercontent.com/avoholo/avoholo.github.io/master/_posts/Spark_multinode_setup/event_timeline.png" alt="event_timeline">
<figcaption>Fig 3. Event Timeline of Spark Application.</figcaption>
</figure>


> Related :
> <a href="/concept-notes">Post 1, </a> 
> <a href="/concept-notes">Post 2</a> 



###### Notes

<small id="medium-ref"><sup>[[1]](#medium)</sup> None.</small>

###### Resources
1. [test](https://medium.com/about)
