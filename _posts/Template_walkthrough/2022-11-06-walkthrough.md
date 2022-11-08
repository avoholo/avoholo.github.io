---
title: "[Template] Walk Through"
date: 2022-11-07 20:00:47 +07:00
modified: 2022-11-09 20:28:47 +07:00
tags: [Template]
description: None.
image: "/_posts/Template_walkthroughl/default_post_image.png"
---

![default_post_image](../images/2022-11-06-walkthrough/default_post_image-16679204354976.png)



이번 포스트에선 가이드를 보며 따라 할 수 있는 **Walk Through Template**을 작성 할 예정이다. 

이전에 FastAPI를 사용해보며 작성 해놓은 가이드가 있는데, 설치 방법이나 어떤 Library를 Import 했는지 매번 까먹어서 Template 작성겸 여기다 정리하려고 한다.

Walk Through Template 사용시 해당 기술의 개념을 정리 하기보단, **실습 내역을 기록하는 목적**으로 사용한다.

따라서 개념 정리는 따로 **Concept Note**를 통해 일괄적으로 관리한다.



### FastAPI Hands On

#### 1. Install Package

[all] 설정보단 내가 필요한 패키지만 골라서 설치하는 방향이 나아보인다.

~~~bash
pip install fastapi[all]
~~~

<br>

#### 2.  Sample Python code

별도의 설정 없이 복사 + 붙혀넣기 하면 바로 실행이 가능한 코드다.

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

@app.post("/posts")
def create_posts(payload: dict = Body(...)):
	print(payload)
	return {"new_post": f"title {payload['title']} content: {payload['content']}"}
~~~

<br>

#### 3. How to run

- uvicorn - 파이썬을 위한 async server gateway interface (asgi)이다.
- host - 0.0.0.0은 *any host* 라는 의미다.
- port - predefined port도 8000번이지만, 설정하는 방법을 까먹기 싫어서 명시해줬다.
- 별도의 쉘을 만들어 매번 명령어를 실행하는것이 아닌, 쉘을 실행 시켜 사용성을 높인다.

~~~bash
echo "uvicorn main:app --reload --host=0.0.0.0 --port=8000" > run.sh && chmod +x run.sh
~~~

<br>



### Pydantic

Python Type Hint를 사용한 데이터 유효성 검사 및 설정 관리를 Pydantic 패키지를 통해 쉽게 할 수 있다.

~~~python
from fastapi import FastAPI
from fastapi.params import Body
from pydantic import BaseModel

app = FastAPI()

class Post(BaseModel):
	title: str
	content: str
	puiblished: bool = True
	rating: int = None

@app.get("/")
def root():
	return {"message" : "Hello World."}


@app.get("/posts")
def get_posts():
	return {"data": "this is your post."}

@app.post("/createposts")
def create_posts(new_post: Post):
	print(new_post.dict)
	return {"data": new_post}
~~~

