---
title: "Airflow Operators"
subtitle: apache
date: 2022-11-21 00:00:47+0900
modified: 2022-11-21 00:28:47+0900
tags: [Airflow]
description: Airflow Operators
image: "/_posts/Airflow_operators/default_post_image.png"
---

![default_post_image](https://raw.githubusercontent.com/avoholo/avoholo.github.io/master/_posts/Airflow_operators/default_post_image.png)



<br>

## How it works

<figure>
<img src="https://raw.githubusercontent.com/avoholo/avoholo.github.io/master/_posts/Airflow_operators/how_it_works.png" alt="how_git_works">
<figcaption>Fig 1. Airflow Architecure</figcaption>
</figure>



***

<br>

`Airflow`의 장점을 하나 꼽으라면 다양한 종류의 Operator를 지원한다는 것이다. *여기서 Operator는 뭘까?*  일단 *간단히* 말하자면 Airflow의 `Task`를 실행시켜준다고 생각하면된다. **Airflow**에서 실행할 작업들을 순서대로 구성한 Workflow를 DAG<sup id="medium">[[1]](#medium-ref)</sup>라고 한다. 

`DAG`에서 수행되는 각 작업을 `Task`라고 하며, 이 **Task**에는 총 3가지의 종류가 있다:

- *Operator*
- *Sensor<sup id="medium">[[2]](#medium-ref)</sup>*
- *Hook*

이중 오늘 포스트에선 ***Operator*** 와 ***Sensor***에 대해 다뤄볼 예정이다. 참고로, Operator도:



1) Action - 파일을 실행하는 operator 

2) Transfer - Source to Destination으로 data를 transfer 해주는 operator

로 나뉘게 되지만 더 자세한 내용은 아래에서 다룰것이다.

<br>

### 1. Http Sensor

#### Init DAG

~~~python
from airflow import DAG
from airflow.providers.http.sensors.http import HttpSensor
from airflow.sensors.filesystem import FileSensor
from airflow.operators.python import PythonOperator
from airflow.operators.bash import BashOperator

from datetime import datetime, timedelta
import requests
import json

default_args = {
    "owner": "airflow",
    "email_on_failure": False,
    "email_on_retry": False,
    "email": "admin@localhost.com",
    "retries": 1,
    "retry_delay": timedelta(minutes=5)
}

with DAG("http_sensor_test", start_date=datetime(2022, 1 ,1), 
    schedule_interval="@daily", default_args=default_args, catchup=False) as dag:
~~~

<br>





> Related :
> <a href="/git-fundamental-walkthrough">Git Fundamental Walkthrough </a> 




###### Notes
<small id="medium-ref"><sup>[[1]](#medium)</sup> None.</small>

###### Resources
1. [Github Docs](https://docs.github.com/en)