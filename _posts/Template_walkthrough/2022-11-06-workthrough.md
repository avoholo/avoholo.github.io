---
title: "[Template] Walk Through"
date: 2022-11-07 20:00:47 +07:00
modified: 2022-11-07 20:28:47 +07:00
tags: [Template]
description: None.
image: "/_posts/Template_walkthroughl/default_post_image.png"
---

![default_post_image](default_post_image.png)



이번 포스트에선 가이드를 보며 따라 할 수 있는 **Walk Through Template**을 작성 할 예정이다. 

이전에 FastAPI를 사용해보며 작성 해놓은 가이드가 있는데, 설치 방법이나 어떤 Library를 Import 했는지 매번 까먹어서 Template 작성겸 여기다 정리하려고 한다.

Walk Through Template 사용시 해당 기술의 개념을 정리 하기보단, **실습 내역을 기록하는 목적**으로 사용한다.

따라서 개념 정리는 따로 **Concept Note**를 통해 일괄적으로 관리한다.



#### FastAPI Hands On

##### pip

~~~bash
pip install fastapi[all]
~~~

[all] 설정보단 내가 필요한 패키지만 골라서 설치하는 방향이 나아보인다.

##### Sample Python code

~~~python
from fastapi import FastAPI
from fastapi.params import Body

app = FastAPI()

@app.get("/")
def root():
	return {"message" : "Hello World."}


@app.get("/posts")
def get_posts():
	return {"data": "this is your post."}

@app.post("/createposts")
def create_posts(payload: dict = Body(...)):
	print(payload)
	return {"new_post": f"title {payload['title']} content: {payload['content']}"}
~~~

##### How to run
~~~bash
echo "uvicorn main:app --reload --host=0.0.0.0 --port=8000" > run.sh && chmod +x run.sh
~~~
