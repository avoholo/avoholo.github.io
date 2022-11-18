---
title: "Spark Cluster Setup"
date: 2022-11-11 09:00:00 +07:00
modified: 2022-11-14 09:00:00 +07:00
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
echo "JAVA_HOME=/spark/jdk8
alias sparkenv='vi $SPARK_CONF/spark-env.sh'" >> spark-env.sh
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

`spark.driver.memory 24g` 드라이버 메모리 설정도 history 서버 구성 하는김에 하자. 

~~~bash
cd $SPARK_CONF
cp spark-defaults.conf.template spark-defaults.conf

# Log Directory 생성
mkdir -p $SPARK_HOME/history
echo "spark.history.fs.logDirectory file://$SPARK_HOME/history
spark.eventLog.enabled true
spark.eventLog.dir file://$SPARK_HOME/history
spark.driver.memory 24g" >> spark-defaults.conf
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

이렇게 Spark Application을 실행하게 되면:

- History Server(18080) 
- WebUI(4040) 에서 Stage 별 상태를 확인 할 수 있다.

<figure>
<img src="https://raw.githubusercontent.com/avoholo/avoholo.github.io/master/_posts/Spark_multinode_setup/event_timeline.png" alt="event_timeline">
<figcaption>Fig 2. Event Timeline of Spark Application.</figcaption>
</figure>

<br>

<figure>
<img src="https://raw.githubusercontent.com/avoholo/avoholo.github.io/master/_posts/Spark_multinode_setup/dag_visualization.png" alt="dag_visualization">
<figcaption>Fig 3. Dag Visualization of Spark Application.</figcaption>
</figure>
<br>

<br>

### 4. Hadoop Cluster 구성
<hr style="height:10px; visibility:hidden;" />

Hadoop Cluster를 구성하는 일은 언제나 귀찮다. 

해당 설정들이 무엇을 의미하는지 안다면 그냥 복사 + 붙혀넣기 하자.

<hr style="height:10px; visibility:hidden;" />
#### Hadoop Binary 설치
~~~bash
cd $SPARK_BASE
wget https://archive.apache.org/dist/hadoop/common/hadoop-3.3.3/hadoop-3.3.3.tar.gz && tar -zxvf hadoop-3.3.3.tar.gz
mv hadoop-3.3.3 hadoop3 && rm -rf hadoop-3.3.3.tar.gz
~~~

<br>

#### 환경 변수 설정

~~~bash
$ vi ~/.bashrc

# Hadoop
HADOOP_BASE=$SPARK_BASE
HADOOP_HOME=$HADOOP_BASE/hadoop3
HADOOP_CONF=$HADOOP_HOME/etc/hadoop
export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin

. ~/.bashrc
~~~

<br>

#### hadoop-env.sh

~~~bash
echo "export JAVA_HOME=$HADOOP_BASE/jdk8" >> $HADOOP_CONF/hadoop-env.sh
~~~

<br>

#### core-site.xml

~~~bash
echo "<configuration>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://spark-master01:9000/</value>
    </property>
</configuration>" > $HADOOP_CONF/core-site.xml
~~~

<br>

#### hdfs-site.xml

아래 예시 처럼 `$HADOOP_HOME` 으로 변수 처리 할 경우 `~/.bashrc` 에 실제 변수가 존재하는지 체크를 한번 더 하자. 안그럼 값이 빈칸으로 들어간다.

~~~bash
echo "<configuration>
    <property>
        <name>dfs.namenode.name.dir</name>
        <value>file://$HADOOP_HOME/dfs/name</value>
    </property>
    <property>
        <name>dfs.datanode.data.dir</name>
        <value>file://$HADOOP_HOME/dfs/data</value>
    </property>
    <property>
        <name>dfs.namenode.checkpoint.dir</name>
        <value>file://$HADOOP_HOME/dfs/namesecondary</value>
    </property>
</configuration>" > $HADOOP_CONF/hdfs-site.xml
~~~

<br>

#### yarn-site.xml

~~~bash
echo "<configuration>
    <property>
        <name>yarn.resourcemanager.hostname</name>
        <value>spark-master01</value>
    </property>
    <property>
        <name>yarn.resourcemanager.webapp.address</name>
        <value>spark-master01:8188</value>
    </property>
</configuration>" > $HADOOP_CONF/yarn-site.xml
~~~

<br>

#### hadoop workers

~~~bash
echo "spark-worker01
spark-worker02
spark-worker03" > $HADOOP_CONF/workers
~~~

<br>

#### HDFS Init & Start

~~~bash
spark-worker01: datanode is running as process 8693.
Stop it first and ensure /tmp/hadoop-spark-datanode.pid file is empty before retry.
~~~

이런 워닝이 뜨고 있다면 이미 Datanode가 실행 중인거다. `start-dfs.sh` 을 실행한 후, 실패 했다면 **반드시** `stop-dfs.sh` 실행 하고, pid 파일 지우고 다시 실행하자.

~~~bash
ssh spark-master01

# Init (초기에만 실행 필요)
$HADOOP_HOME/bin/hdfs namenode -format

# Start (at spark-master01)
$HADOOP_HOME/sbin/stop-dfs.sh
ssh spark-worker01 rm -rf /tmp/hadoop-spark-datanode.pid
ssh spark-worker02 rm -rf /tmp/hadoop-spark-datanode.pid
ssh spark-worker03 rm -rf /tmp/hadoop-spark-datanode.pid

$HADOOP_HOME/sbin/start-dfs.sh
>> Starting namenodes on [spark-master01]
>> Starting datanodes
>> Starting secondary namenodes [spark-master01]

[spark@spark-master01 hadoop]$ jps
1607 HistoryServer
3592 NameNode
3821 SecondaryNameNode
~~~

<br>

#### Yarn Start

~~~bash
$HADOOP_HOME/sbin/start-yarn.sh
>> Starting resourcemanager
>> Starting nodemanagers

[spark@spark-master01 sbin]$ jps
5701 SecondaryNameNode
1607 HistoryServer
5469 NameNode
6063 ResourceManager
~~~

<br>

#### WebUI

WebUI로 들어가서 Overview 탭 > Summary > Live Nodes 개수 확인한다. Live Node가 3개가 아니면:

`tail -f  $HADOOP_HOME/logs/hadoop-spark-datanode-spark-worker-02.log` 로 로그확인.

- **Hadoop WebUI** : http://spark-master01:9870
- **Yarn WebUI** : http://spark-master01:8188
- **Spark WebUI** : http://spark-master01:4040
- **Spark History** : http://spark-master01:18080

<br>

나같은 경우엔 `spark-worker02` 서버의 ip가 잘못 잡혀있어 namenode와 통신을 못했었다.

~~~bash
service to spark-master01/1********:9000 Datanode denied communication with namenode because hostname cannot be resolved (ip=192.****.11, hostname=?): 
DatanodeRegistration(0.0.0.0:9866, datanodeUuid=3e3bebbe-12cb-4bf7-a704-a6aa35c11cea, infoPort=9864, infoSecurePort=0, ipcPort=9867, storageInfo=lv=-57;cid=CID-
~~~

spark 서버 IP로 설정 후 기동시키자 live-node가 3개로 정상 기동했었다.

역시 항상 **로그를 먼저 봐야** 문제가 쉽게 해결된다. 문제가 뭔지도 모르는데 대충 예상해서 트러블 슈팅하는 습관은 좋지 못한거같다.

<br>

<br>

### 5. Spark-Yarn 연동

Spark의 `Resource Manager`를 `Yarn`으로 설정 하려면 `HADOOP_CONF` , `YARN_CONF` 설정을 추가로 해줘야한다.

<hr style="height:10px; visibility:hidden;" />
#### spark-shell with yarn

`$HADOOP_CONF/core-site.xml` , `$HADOOP_CONF/yarn-site.xml`  을 `SPARK_CONF2`에 복사해주자.

~~~bash
mkdir -p $SPARK_HOME/conf2
$ vi ~/.bashrc
SPARK_CONF2=$SPARK_HOME/conf2
. ~/.bashrc

cp $HADOOP_CONF/core-site.xml $SPARK_CONF2
cp $HADOOP_CONF/yarn-site.xml $SPARK_CONF2
~~~

<br>

#### spark-shell with yarn 실행

~~~bash
cd $SPARK_HOME
YARN_CONF_DIR=$SPARK_CONF2 ./bin/spark-shell --master yarn

Using Spark's default log4j profile: org/apache/spark/log4j-defaults.properties
Setting default log level to "WARN".
To adjust logging level use sc.setLogLevel(newLevel). For SparkR, use setLogLevel(newLevel).
22/11/11 17:55:36 WARN NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
22/11/11 17:55:38 WARN Client: Neither spark.yarn.jars nor spark.yarn.archive is set, falling back to uploading libraries under SPARK_HOME.
Spark context Web UI available at http://spark-master01:4040
Spark context available as 'sc' (master = yarn, app id = application_1668156169104_0001).
Spark session available as 'spark'.
Welcome to
      ____              __
     / __/__  ___ _____/ /__
    _\ \/ _ \/ _ `/ __/  '_/
   /___/ .__/\_,_/_/ /_/\_\   version 3.2.1
      /_/
         
Using Scala version 2.12.15 (OpenJDK 64-Bit Server VM, Java 1.8.0_232)
Type in expressions to have them evaluated.
Type :help for more information

scala> sc
scala> spark
scala> sc.master
res2: String = yarn
scala> sc.uiWebUrl
res3: Option[String] = Some(http://spark-master-01:4040)
~~~

<br>

#### Executor 코어수 증설을 위한 설정

~~~bash
stop-yarn.sh
cd $HADOOP_CONF
$ vi capacity-scheduler.xml

# 추가
....
  <property>
    <name>yarn.scheduler.capacity.resource-calculator</name>
    <!--<value>org.apache.hadoop.yarn.util.resource.DefaultResourceCalculator</value>-->
    <value>org.apache.hadoop.yarn.util.resource.DominantResourceCalculator</value>
    <description>
	  The ResourceCalculator implementation to be used to compare
	  Resources in the scheduler.
	  The default i.e. DefaultResourceCalculator only uses Memory while
	  DominantResourceCalculator uses dominant-resource to compare
	  multi-dimensional resources such as Memory, CPU etc.
    </description>
  </property>
....

scp $HADOOP_CONF/capacity-scheduler.xml spark@spark-worker01:$HADOOP_CONF
scp $HADOOP_CONF/capacity-scheduler.xml spark@spark-worker02:$HADOOP_CONF
scp $HADOOP_CONF/capacity-scheduler.xml spark@spark-worker03:$HADOOP_CONF
~~~

<br>

#### Executor 메모리와 코어수 증설

~~~bash
cd $SPARK_HOME && YARN_CONF_DIR=$SPARK_CONF2 ./bin/spark-shell --master yarn --executor-memory 20G --executor-cores 7 --num-executors 3cd
~~~

부여한 설정대로 적용되었는지 아래 WebUI에서 확인:

- **Yarn WebUI** : http://spark-master01:8188
- **Spark WebUI** : http://spark-master01:4040

혹은 `Standalone-Mode`라면, `spark-env.sh`에 환경변수를 포함하여 Spark Standalone Cluster 기동시 적용 할 수 있다<sup id="standalone">[[1]](#standalone-ref)</sup>.  

~~~bash
JAVA_HOME=/spark/jdk8
SPARK_MASTER_HOST=spark-master01
SPARK_MASTER_PORT=7177  # default: 7077
SPARK_MASTER_WEBUI_PORT=8180  # default: 8080
SPARK_WORKER_PORT=7178  # default: random
SPARK_WORKER_WEBUI_PORT=8181  # default: 8081
#SPARK_WORKER_CORES=8  # default: all available
#SPARK_WORKER_MEMORY=8G  # default: machine's total RAM minus 1 GiB
SPARK_PUBLIC_DNS=${HOSTNAME}

[spark@spark-master01 ~]$ source $SPARK_CONF/spark-env.sh
~~~



RM이 `Yarn` 으로 설정되어 있다면<sup id="yarn">[[2]](#yarn-ref)</sup>, **각 노드별** `yarn-site.xml` 에:

- `yarn.nodemanager.resource.memory-mb`
- `yarn.nodemanager.resource.cpu-vcores`
- `yarn.scheduler.maximum-allocation-mb`
-  `yarn.scheduler.maximum-allocation-vcores`

그리고 `mapred-site.xml` 에 :

- `mapreduce.map.memory.mb`
- `mapreduce.map.cpu.vcores`
- `mapreduce.map.java.opts`
- `mapreduce.reduce.memory.mb`
- `mapreduce.reduce.cpu.vcores`
- `mapreduce.reduce.java.opts`
- `yarn.app.mapreduce.am.resource.mb`

설정으로 메모리와 vCores 수를 늘릴 수 있다. 클러스터 리소스 튜닝은 **다른 포스트에서 다룰 예정**이다.

<br>

<figure>
<img src="https://raw.githubusercontent.com/avoholo/avoholo.github.io/master/_posts/Spark_multinode_setup/1.png" alt="1">
<figcaption>Fig 4. Executor별 할당된 리소스 내역</figcaption>
</figure>

<br>

<figure>
<img src="https://raw.githubusercontent.com/avoholo/avoholo.github.io/master/_posts/Spark_multinode_setup/2.png" alt="2">
<figcaption>Fig 5. Applciation에 할당된 리소스 내역</figcaption>
</figure>

<br>
<figure>
<img src="https://raw.githubusercontent.com/avoholo/avoholo.github.io/master/_posts/Spark_multinode_setup/3.png" alt="3">
<figcaption>Fig 6. Application Summary 페이지</figcaption>
</figure>

<br>
<figure>
<img src="https://raw.githubusercontent.com/avoholo/avoholo.github.io/master/_posts/Spark_multinode_setup/4.png" alt="4">
<figcaption>Fig 7. Container별 리소스 할당 내역</figcaption>
</figure>

<br>
<figure>
<img src="https://raw.githubusercontent.com/avoholo/avoholo.github.io/master/_posts/Spark_multinode_setup/5.png" alt="5">
<figcaption>Fig 8. 하나의 Container에 할당된 리소스 Summary</figcaption>
</figure>



<br>
<br>

### 6. Spark Standalone vs Spark Cluster (Yarn)

이제부터 중요해진다. (Chapter2-28을 다시 봐야할거같다...) 

<br>

#### Spark Cluster로 word count 수행

##### Error 1 

~~~bash
scala > val rdd_wc = sc.textFile("README.md").flatMap(_.split(" ")).map((_,1)).reduceByKey(_+_)
~~~

위와 같이 `READMD.md` 파일을 읽으려고 하면 아래와 같은 에러가 발생한다.

~~~bash
org.apache.hadoop.mapred.InvalidInputException: 
Input path does not exist: hdfs://spark-master01:9000/user/spark/README.md
~~~

<br>

##### Error 2 (local file)

~~~bash
scala > val rdd_wc = sc.textFile("file:///spark/spark3/README.md").flatMap(_.split(" ")).map((_,1)).reduceByKey(_+_)
scala > rdd_wc.collect.take(10).foreach(println)
~~~
위와 같이 `READMD.md` 파일을 읽으려고 하면 아래와 같은 에러가 발생한다.
~~~bash
WARN TaskSetManager: Lost task 0.0 in stage 0.0 (TID 0) (spark-worker-01 executor 1): 
java.io.FileNotFoundException: File file:/spark/spark3/README.md does not exist
~~~



<br>



##### Success

**driver 노드와 executor 노드 모두에 있는** Hadoop의 README.txt 파일로 수행.

~~~bash
scala > val rdd_wc = sc.textFile("file:///spark/hadoop3/README.txt").flatMap(_.split(" ")).map((_,1)).reduceByKey(_+_)
scala > rdd_wc.collect.foreach(println)

(information,1)
(http://hadoop.apache.org/,1)
(our,2)
(,9)
(wiki,,1)
(please,1)
(For,1)
(visit,1)
(about,1)
(website,1)
(Hadoop,,1)
(https://cwiki.apache.org/confluence/display/HADOOP/,1)
(at:,2)
(and,1)
(latest,1)
(the,1)
~~~

<br>

<br>

### 7. Summary

#### Commands

~~~bash
ls -al /tmp | grep pid
ssh spark-worker01 ls -al /tmp | grep pid
ssh spark-worker02 ls -al /tmp | grep pid
ssh spark-worker03 ls -al /tmp | grep pid

rm -rf /tmp/*.pid
ssh spark-worker03 rm -rf /tmp/*.pid

source $SPARK_CONF/spark-env.sh
start-dfs.sh
start-yarn.sh
start-master.sh
$SPARK_HOME/sbin/start-history-server.sh
ssh spark@spark-worker01 sh $SPARK_HOME/sbin/start-worker.sh $SPARK_MASTER_HOST:$SPARK_MASTER_PORT
ssh spark@spark-worker02 sh $SPARK_HOME/sbin/start-worker.sh $SPARK_MASTER_HOST:$SPARK_MASTER_PORT
ssh spark@spark-worker03 sh $SPARK_HOME/sbin/start-worker.sh $SPARK_MASTER_HOST:$SPARK_MASTER_PORT

cd $SPARK_HOME && YARN_CONF_DIR=$SPARK_CONF2 ./bin/spark-shell --master yarn --driver-memory 30g --executor-memory 24G --executor-cores 6 --num-executors 3
scala> sc
scala> spark
scala> sc.master
scala> sc.uiWebUrl


netstat -antup | grep LISTEN | sort -n
~~~

<br>

#### WebUI

- **Hadoop WebUI** : http://spark-master01:9870
- **Yarn WebUI** : http://spark-master01:8188
- **Spark WebUI** : http://spark-master01:4040
- **Spark History** : http://spark-master01:18080

<br>

#### Stop

~~~bash
ssh spark@spark-worker01 sh $SPARK_HOME/sbin/stop-worker.sh 
ssh spark@spark-worker02 sh $SPARK_HOME/sbin/stop-worker.sh 
ssh spark@spark-worker03 sh $SPARK_HOME/sbin/stop-worker.sh 
stop-master.sh
stop-yarn.sh
stop-dfs.sh
~~~





<br>

> Related :
> <a href="/concept-notes">None</a> 



###### Notes
<small id="standalone-ref"><sup>[[1]](#standalone)</sup> Mesos와 Standalone 모드는 spark-env.sh 파일로 설정 가능하다. 자세한 내용은 <a href="https://spark.apache.org/docs/latest/configuration.html#environment-variables">여기서</a> 확인 가능하다.</small>

<small id="yarn-ref"><sup>[[2]](#yarn)</sup> 아무 설정이 없을 경우, yarn-default.xml로 yarn이 기동된다. yarn-default.xml 기본 설정은 <a href="https://hadoop.apache.org/docs/r3.1.1/hadoop-yarn/hadoop-yarn-common/yarn-default.xml">여기서</a> 확인 가능하다.</small>

###### Resources
1. [Spark Official API Docs](https://spark.apache.org/docs/latest/api/scala/org/apache/spark/index.html)
