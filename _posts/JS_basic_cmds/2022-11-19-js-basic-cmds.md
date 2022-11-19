---
title: "JavaScript 기초 (1)"
date: 2022-11-19 00:00:00 +07:00
modified: 2022-11-19 00:00:00 +07:00
tags: [Javascript]
description:
image: "/_posts/JS_basic_cmds/default_post_image.png"
---

![default_post_image](https://raw.githubusercontent.com/avoholo/avoholo.github.io/master/_posts/Template_walkthrough/default_post_image.png)



이번 포스트에선 



### Variables

<hr style="height:20px; visibility:hidden;" />

#### Syntax and Conventions

##### 'Number' as starter of variable name

~~~javascript
let 3years = 3;
Uncaught SyntaxError: Invalid or unexpected token
~~~

##### '&' as in variable name

~~~javascript
let john&kim = "john kim";
Uncaught SyntaxError: Unexpected token '&'
~~~

##### Reserved Keyword as variable name

~~~javascript
// Not Recommended.
let name = 'John'
~~~

##### All Uppercase for constants

~~~javascript
let PI = 3.14159;
~~~

##### Descriptive variable name

~~~javascript
let myFirstJob = 'Student'
let myCurrentJob = 'Programmer'

let job1 = 'programer..'
let jb2 = 'program?'
~~~



##### Summary

| **Type**                   | **Example**                      |
| -------------------------- | -------------------------------- |
| **Int**                    | 3                                |
| **Special Characters**     | !@#$%^&*()'/                     |
| *Reserved Keyword          | name, int, long, char, interface |
| *Uppercase Constants       | PI                               |
| *Descriptive variable name | let myCurrentJob = 'Programmer'  |

**\*** : **Convention**이기에 필수는 아니지만 지키길 권장한다.

<br>

#### Data Types

다른 언어와 달리 중요 포인트들이 있는것 같다.

##### Undefined vs Null

어떤 이유로 javascript에선 `null` 이 object로 인식이 되는지 궁금해서 찾아봤더니..

legacy issue로 인해 **알고는있지만 고치지않은 버그**라고 한다.

~~~javascript
let year;
console.log(year);
console.log(typeof year);
console.log("null:", typeof null);

undefined
undefined
undefined
null: object
~~~

아래 두줄로 명확하게 Null과 Undefined를 구분할 수 있다.

~~~javascript
alert(null !== undefined) //true
alert(null == undefined)  //true
~~~

- `null`과 `undefined`는 둘다 **Primitive Type**이 맞지만,
- `undefined`는 존재하는지 유/무 조차 알 수 없고
- `null`은 존재한다는건 알지만, 어떤 값이 assign 되었는지 모르는 상태이다.

<br>

##### Dynamic Typing

- Variable에게 Type을 지정하는것이아닌, Value의 타입으로 정해진다.



##### const vs let (ES6)

`let` 은 **reassignment**가 가능한 **mutable** 변수이고, `const`는 **immutable**이다.

immutable이라는 말은 즉슨, 필수적으로 initialized가 되어야한다.

~~~javascript
const birthYear;
Uncaught SyntaxError: Missing initializer in const 

const birthYear = 2000;
birthYear = 1990;
Uncaught TypeError: Assignment to constant variable.
~~~

***Best Practice***는 `const`를 default로 쓰되, 변경이 될만한 변수만 `let`으로 사용하는것이다.

**tip**: `ES6` 이전에는 `var`가 `let` 역할을 했었으나 엄연히 다르게 동작하므로 해당 개념은 다음 포스트에 설명 할 예정이다. 일단 `var`는 모르면 쓰지말자.

<br>

<br>

### Functions

<hr style="height:20px; visibility:hidden;" />

#### Declaration vs Expression

**tip:** javascript에선 function 또한 `object`가 아닌 `value`로 인식한다.

~~~javascript
function declarationCalculateBMI(weight, height) {
    return weight / (height * height)
}

const expressionCalculateBMI = function (weight, height) {
    return weight / (height * height)
}
~~~

`expression` 과 `declaration`의 차이는 아래 코드를 통해 바로 알 수 있다.

~~~javascript
const john = declarationCalculateBMI(70, 190)
function declarationCalculateBMI(weight, height) {
    return weight / (height * height)
}

const dave = expressionCalculateBMI(60, 170)
const expressionCalculateBMI = function (weight, height) {
    return weight / (height * height)
}

console.log(`John: ${john} and Dave: ${dave}`)
~~~

`expression`은 `value`를 생성하기 때문에 initialize전에 수행이 불가능하다. 따라서, `expressionCalculateBMI` 수행시 에러가 발생한다.



> Related :
> <a href="/concept-notes">Post 1, </a> 
> <a href="/concept-notes">Post 2</a> 



###### Notes

<small id="medium-ref"><sup>[[1]](#medium)</sup> </small>

###### Resources
1. [test](https://medium.com/about)
