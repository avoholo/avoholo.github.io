---
title: "[Template] Post Naming Rules"
date: 2022-11-09 09:00:00 +09:00
modified: 2022-11-09 09:01:00 +09:00
tags: [Template]
description: Setup Naming Rules for blog posts.
image: "/_posts/Template_naming_rules/default_post_image.png"
---

<figure>
<img src="https://raw.githubusercontent.com/avoholo/avoholo.github.io/master/_posts/Template_naming_rules/default_post_image.png" alt="default_note_image">
<figcaption>Fig 1. Naming Rule Image.</figcaption>
</figure>



이번 포스트에선 블로그 포스트의 **가독성, 간결함, 일관성**을 유지 할 수 있도록 Naming Rule을 정의를 할 예정이다. 본격적으로 블로그를 시작하기 전에 Naming Rule 정의를 해야 내가 원하는 정보를 블로그에서 빠르게 찾을 수 있을것 같다.

#### 1. Square Brackets

**[ ] :** 포스트 제목을 보면 대괄호 (square brackets) `[]`가 포함되어 있는 경우가 있다. 대괄호는 Tag만으로 Category를 구분 할 수 없을 때 사용한다.

예시) *Refactoring* 이라는 책에서 Encapsulation 이란 주제를 읽었다면:

>  [Books] 'Refactoring' by Martin Fowler- Encapsulation

이렇게 제목을 짓는게 정보를 찾는데도 빠르고, '책에 대한 포스트다' 라는 걸 명시하여 단순히 인터넷에서 찾은 정보가 아닌 **정독하여 읽은 글**이라는걸 알 수 있게된다.



#### 2. Category

개인적으로 Tag 남발이나 하나의 주제에 대해 여러가지 Category를 부여하는 것을 좋아하지 않는다. 한개의 포스트에 여러개의 Category를 부여하게 된다면 UI 상으론 중복된 Post가 보일것이다. 심지어 포스트가 1000개 이상으로  늘어나게 될 경우, 관리가 더욱 어려워 진다.

한 주제에 대해 **하나의 Cateogry만 부여하자**.



#### 3. Title Naming Rule

파일 이름을 잘 정의하는것 만으로도 정리하기가 쉬워진다.

`Spark`에 대한 Introduction Post를 작성한다고 가정해보자. Spark를 구성하는 여러 요소가 있다:

- Spark Context
- Resource Manager (Yarn, Mesos)
- DAG
- Executors
- ...etc

하나의 포스트안에 이 모든 정보를 담으려면 **파일이 너무 커지게 되고**, **가독성도 떨어지게된다.** 

따라서, Spark에 대한 전반적인 내용을 `Spark Introduction` 이란 제목으로 쓰고, 나머지 주제들을 `Spark Context`, `Spark Resource Manager`, `Spark Executors`, `DAG in Spark` 이런식으로 제목을 지을 수 있을것 같다.

`DAG`라는 단어를 특별히 `in Spark`와 같이 묶은 이유는, DAG는 Spark에만 존재하는 개념이 아니기에, 뒤에 in ~ 를 붙혀준다.



> Related :
> <a href="/concept-notes">Concept Notes - First Note (1) </a>,
> <a href="/walkthrough">Walk Through</a> 



###### Notes
<small id="medium-ref"><sup>[[1]](#medium)</sup> None.</small>

###### Resources
1. [None](https://medium.com/about)

