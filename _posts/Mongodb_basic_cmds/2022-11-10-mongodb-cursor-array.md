---
title: "MongoDB Cursor and Array"
date: 2022-11-10 00:00:00 +07:00
modified: 2022-11-10 00:00:00 +07:00
tags: [MongoDB]
description:
image: "/Mongodb_basic_cmds/default_post_image.png"
---

![default_post_image](https://raw.githubusercontent.com/avoholo/avoholo.github.io/master/_posts/Mongodb_basic_cmds/default_post_image.png)

<hr style="height:30px; visibility:hidden;" />

이번 포스트에선 NoSQL인 MongoDB 기본기의 핵심인 **Cursor**와 **Array**를 빠르게 Summary하려고한다.



### Quick Setup

**Cursor**와 **Array**를 알아보기전에 MongoDB에선 DB & Collections 생성, CRUD를 어떻게 수행하는지 알아보자.

<hr style="height:10px; visibility:hidden;" />

#### 1. Quick Example with insert

##### JSON Data

~~~json
  {
    "departureAirport": "MUC",
    "arrivalAirport": "SFO",
    "aircraft": "Airbus A380",
    "distance": 12000,
    "intercontinental": true
  },
  {
    "departureAirport": "LHR",
    "arrivalAirport": "TXL",
    "aircraft": "Airbus A320",
    "distance": 950,
    "intercontinental": false
  }
~~~

&nbsp;

##### switch db & insert

~~~javascript
shop> use flights
switched to db flights

flights> db.flightData.insertOne({
...     "departureAirport": "MUC",
...     "arrivalAirport": "SFO",
...     "aircraft": "Airbus A380",
...     "distance": 12000,
...     "intercontinental": true
...   })
{
  acknowledged: true,
  insertedId: ObjectId("637f0fcccc1248595ce1c248")
}
~~~

&nbsp;

##### .find() & .pretty()

최신 버전의 `mongosh` 은 `pretty()` 나 `find()` 둘다 가독성 좋게 값을 출력한다.

~~~javascript
flights> db.flightData.find()
[
  {
    _id: ObjectId("637f0fcccc1248595ce1c248"),
    departureAirport: 'MUC',
    arrivalAirport: 'SFO',
    aircraft: 'Airbus A380',
    distance: 12000,
    intercontinental: true
  }
]
~~~

&nbsp;

##### more .find()

- `{$gt}` & `{$lt}`

~~~javascript
flights> db.flightData.find({distance: {$gt: 900}})
[
  {
    _id: ObjectId("637f0fcccc1248595ce1c248"),
    departureAirport: 'MUC',
    arrivalAirport: 'SFO',
    aircraft: 'Airbus A380',
    distance: 12000,
    intercontinental: true,
    marker: 'deleted'
  },
~~~

&nbsp;

- **Projection**으로 원하는 데이터만 출력 가능하다 `.find({},{name :1})`

1대신 0을 넣어서 `aircraft: 0` 으로 조회한다면 `aircraft`field만 빼고 반환이 가능하다.

~~~javascript
flights> db.flightData.find({}, {aircraft: 1})
[
  {
    _id: ObjectId("637f52399ed8d2bb69a58387"),
    aircraft: 'Airbus A380'
  },
  {
    _id: ObjectId("637f52399ed8d2bb69a58388"),
    aircraft: 'Airbus A320'
  }
]
~~~



<br>

### 2. Fundamentals

위 실습을 통해 알 수 있었던 `MongoDB` 만의 **장점**을 알아보자.

##### JSON vs BSON

우리가 넣은 `JSON` 데이터는 `MongoDB Driver`를 통해 `BSON` 으로 변환된다. 이게 왜 중요할까?

`.find()`로 찾은 아래 형식의 데이터는 JSON 형식이 아니며, 고유 ID가 포함된걸 볼 수 있다. 많은 데이터를 효율적으로 적재하기 위해 `JSON`을 `Binary` 형식으로 변환한 것이다. 

~~~javascript
  {
    _id: ObjectId("637f0fcccc1248595ce1c248"),
    departureAirport: 'MUC',
  ...
  ...
~~~

&nbsp;

##### No Schema

`MongoDB` 에선 **Schema**가 없다. 이말인 즉슨, Data 를 넣을때 매우 **Flexible** 하다는것이다. 이미 정의된 **Schema**대로 없던 컬럼을 만들어서 넣거나, 구조를 새로 맞출 필요가 없다는것이다.

~~~javascript
db.flightData.insertOne({"_id": "my-id-txl", // Custom-ID 추가
"departureAirport": "LHR",
"arrivalAirport": "TXL",
"aircraft": "Airbus A320",
"distance": 950,
"intercontinental": false,
"airline": "Asiana"}) // airline 또한 추가하였다.

flights> db.flightData.find()
[
  {
    _id: ObjectId("637f0fcccc1248595ce1c248"),
    departureAirport: 'MUC',
    arrivalAirport: 'SFO',
    aircraft: 'Airbus A380',
    distance: 12000,
    intercontinental: true
  },
  {
    _id: 'my-id-txl',
    departureAirport: 'LHR',
    arrivalAirport: 'TXL',
    aircraft: 'Airbus A320',
    distance: 950,
    intercontinental: false,
    airline: 'Asiana'
  },
  {
    _id: 'my-id-txl2',
    departureAirport: 'LHR',
    arrivalAirport: 'TXL',
    aircraft: 'Airbus A320',
    distance: 950,
    intercontinental: false,
    airline: 'Asiana'
  }
]
~~~

<hr style="height:10px; visibility:hidden;" />

하지만.. **ID**만 다르면 똑같은 데이터를 넣어도 `insert`가 된다.. 중복된 데이터 관리를 어떻게 관리하는지 더 알아봐야겠다.

&nbsp;

### 3. CRUD in MongoDB

<figure>
<img src="1.png" alt="MongoDB CRUD">
<figcaption>Fig 1. Types of MongoDB CRUD Operations</figcaption>
</figure>
<br>

#### Delete

##### .deleteOne()

LIFO 형식으로 Document가 삭제된다.

~~~javascript
flights> db.flightData.deleteOne({departureAirport: "LHR"})
{ acknowledged: true, deletedCount: 1 }
~~~

<br>

#### Update

##### .updateOne() with reserved operator ($)

아래와 같이 데이터 형식이 맞지 않을때 에러가 발생한다.

~~~javascript
flights> db.flightData.updateOne({distance: 12000}, {marker: "deleted"})
MongoInvalidArgumentError: Update document requires atomic operators
~~~

&nbsp;

이런 경우엔, **Reserved Operator**를 사용해서 `marker`라는 값이 존재한다면 수행하고, 없으면 replace 할 수 있다.

~~~javascript
flights> db.flightData.updateOne({distance: 12000}, {$set: {marker: "deleted"}})
{
  acknowledged: true,
  insertedId: null,
  matchedCount: 1,
  modifiedCount: 1,
  upsertedCount: 0
}

flights> db.flightData.find()
[
  {
    _id: ObjectId("637f0fcccc1248595ce1c248"),
    departureAirport: 'MUC',
    arrivalAirport: 'SFO',
    aircraft: 'Airbus A380',
    distance: 12000,
    intercontinental: true,
    marker: 'deleted'
  },
~~~

&nbsp;

##### updateOne() vs updateMany() vs update()

`updateOne()`

~~~javascript
flights> db.flightData.updateOne({ _id: 'my-id-txl2' }, { airline: 'Deleted' })
MongoInvalidArgumentError: Update document requires atomic operators
~~~

`updateMany()`

~~~javascript
flights> db.flightData.updateMany({ _id: 'my-id-txl2' }, { airline: 'Deleted' })
MongoInvalidArgumentError: Update document requires atomic operators
~~~

`update()`

원래는 `$set`옵션을 주지 않아도 `update`가 되었지만 v6.7 이후로는 이 명령어가 **deprecated** 되었다고 한다.

- `.replaceOne()` 을 쓰도록 하자.

<br>

### 4. Cursor Object

30개의 element가 있는 json을 mongoDB에 넣고 `find()`해보자.

~~~javascript
[
  {
    "name": "Max Schwarzmueller",
    "age": 29
  },
 ...
 ...
   {
    "name": "Gordon Black",
    "age": 38
  }
]
~~~

&nbsp;

아래 결과를 보면 모든 결과를 출력하지 않고 결과가 잘린것을 볼 수 있다.  ***왜 그런것일까?***

~~~javascript
passengers> db.passengers.find()
...
  {
    _id: ObjectId("637f49a79ed8d2bb69a58384"),
    name: 'Klaus Arber',
    age: 53
  },
  {
    _id: ObjectId("637f49a79ed8d2bb69a58385"),
    name: 'Albert Twostone',
    age: 68
  }
]
Type "it" for more
~~~

&nbsp;

**MongoDB**의 `find()` 는 결과로 **Document**를 반환하지 않고 **Cursor** **Object**를 반환한다.

이는 엄청나게 큰 Document를 출력하지 않기 위함이며, cursor method로 내가 원하는 결과를 **filter** 해서 볼 수 있다. `toArray()`나 `forEach()` 같은 **arguement**를 넣어서 결과를 출력할 수 있다.

~~~javascript
passengers> db.passengers.find().forEach((myargs) => {printjson(myargs)})
...
{
  _id: ObjectId("637f49a79ed8d2bb69a58386"),
  name: 'Gordon Black',
  age: 38
}
~~~



<br>

### Arrays

MongoDB에서는 Embed Documents 와 Array를 활용해 다양한 형태의 데이터를 저장할 수 있다.

~~~javascript
passengers> db.passengers.updateOne({name: "Albert Twostone"}, {$set: {hobbies: ["sports", "cooking"]}})

passengers> db.passengers.find({name: "Albert Twostone"})
[
  {
    _id: ObjectId("637f49a79ed8d2bb69a58385"),
    name: 'Albert Twostone',
    age: 68,
    hobbies: [ 'sports', 'cooking' ]
  }
]
~~~

&nbsp;

##### find array objects

특정 인원의 **Array**를 찾을때는 `findOne()` 을 사용하고, `sports`가 **hobbies**인 경우는 `find()`를 사용한다.

~~~javascript
passengers> db.passengers.findOne({name: "Albert Twostone"}).hobbies
[ 'sports', 'cooking' ]

passengers> db.passengers.find({hobbies: "sports"})
[
  {
    _id: ObjectId("637f49a79ed8d2bb69a58385"),
    name: 'Albert Twostone',
    age: 68,
    hobbies: [ 'sports', 'cooking' ]
  }
]
~~~



##### nested documents

~~~javascript
passengers> db.passengers.updateOne({name: "Albert Twostone"}, {$set: {status: {description: "on-time", lastUpdate:"19:00:00"} }})

passengers> db.passengers.find({"status.description": "on-time"})
[
  {
    _id: ObjectId("637f49a79ed8d2bb69a58385"),
    name: 'Albert Twostone',
    age: 68,
    hobbies: [ 'sports', 'cooking' ],
    status: { description: 'on-time', lastUpdate: '19:00:00' }
  }
]
~~~



> Related :
> <a href="/concept-notes">Post 1, </a> 
> <a href="/concept-notes">Post 2</a> 



###### Notes

<small id="medium-ref"><sup>[[1]](#medium)</sup> </small>

###### Resources
1. [test](https://medium.com/about)
