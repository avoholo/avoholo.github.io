---
title: "객체지향 프로그래밍의 4요소 5원칙"
date: 2022-11-09 10:00:00 +09:00
modified: 2022-11-09 10:00:00 +09:00
tags: [OOP]
description:
image: "/_posts/OOP_4elements_5principles/default_post_image.png"
---

<figure>
<img src="https://raw.githubusercontent.com/avoholo/avoholo.github.io/master/_posts/OOP_4elements_5principles/default_post_image.png" alt="default_post_image">
<figcaption>Fig 1. Principles of Object-Oriented Design.</figcaption>
</figure>

이번 포스트에선 객체지향 디자인의 뼈대가 되는 4요소 5원칙을 알아볼 예정이다. 

요소들과 원칙들을 정의시 최대한 **나의 워딩으로 사용**하고, **두줄로 요약** 해보려고한다.



### 4 Core Concepts in OOP

##### Abstraction.  추상화


##### Encapsulation. 캡슐화

##### Polymorphism. 다형성

##### Inheritance. 추상화



<br>

### SOLID Principle
##### Single Responsibility Principle. 단일  책임 원칙
~~~bash
모든 클래스는 각각 하나의 책임만 가져야 한다.
~~~
<p>&#8618;   특수한 목적을 수행하도록 만든 클래스는 해당 목적 외에 다른 기능을 수행하면 안된다.</p>
###### 🗝️ Keyword
- SRP
- Refactoring
- Shotgun Surgery

<br>

##### Open Closed Principle. 개방 폐쇄 원칙

~~~bash
클래스는 확장에는 열려있고 수정에는 닫혀있어야 한다.
~~~

<p>&#8618; 기존의 코드를 변경하지 않으면서 기능을 추가할 수 있도록 설계되어야 한다.</p>
###### 🗝️ Keyword
- OCP

<br>

##### Liskov Substitution Principle. 리스코프 치환 원칙

~~~bash
자식 클래스는 언제나 자신의 부모 클래스를 대체할 수 있어야 한다.
~~~

<p>&#8618; 자식 클래스는 부모 클래스의 책임을 무시하거나 재정의하지 않고 확장만 수행하도록 해야한다.</p>
###### 🗝️ Keyword
- LSP
- Design By Contract

<br>

##### Interface Segregation Principle. 인터페이스 분리 원칙

~~~bash
한 클래스는 자신이 사용하지 않는 인터페이스는 구현하지 말아야 한다.
~~~

<p>&#8618; 하나의 일반적인 인터페이스보다 여러 개의 구체적인 인터페이스가 낫다.</p>
###### 🗝️ Keyword
- ISP
- Delegation 

<br>

##### Dependency Inversion Principle. 의존성 역전 원칙

~~~bash
하위 레벨 모듈의 변경이 상위 레벨 모듈의 변경을 요구하는 위계관계를 형성하지 말아야한다.
~~~

<p>&#8618; 구체적인 클래스보다는 인터페이스나 추상 클래스와 관계를 맺어야 한다.</p>
###### 🗝️ Keyword
- DIP
- IOC
- Hook Method



> Related :
> <a href="/concept-notes">Post 1, </a> 
> <a href="/concept-notes">Post 2</a> 



###### Notes
<small id="medium-ref"><sup>[[1]](#medium)</sup> </small>

###### Resources
1. [[Java] 객체지향프로그래밍 (OOP)란?](https://limkydev.tistory.com/30)
1. [객체지향 개발 5대 원리: SOLID](https://www.nextree.co.kr/p6960/)