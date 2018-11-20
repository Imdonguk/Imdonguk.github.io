---
title: "Prototype"
layout: post
date: 2018-11-20 22:40
image: /assets/images/markdown.jpg
headerImage: false
star: true
category: blog
author: woogie
---



### 1. 사전학습

> https://eloquentjavascript.net/06_object.html

* object literal & Encapsulation

* call & this & apply

* new

* constructor

* prototype

* __ proto __

* Object.getPrototypeOf

* Object.prototype (root prototype)

* Object.create

* ES Classes

* own properties -> own prototype -> prototype... > Object.prototype

물론 여기있는 키워드에 대해 다 알아야한다. 그런데 어중간하게 알고있는 개념이 여럿 있었다. 개념을 확실히 잡고가겠다.



1. function, constructor, prototype

   함수를 선언하면 해당함수에는 prototype도 존재한다. prototype에는 constructor가 존재하고 prototype 체이닝을 통해 참조하고 있는 prototype이 존재한다. 예를 들어보자

   ~~~js
   function Woogie(){}
   
   Woogie.prototype.constructor //f Woogie()
   Woogie.prototype.__proto__ //Object.prototype
   
   Woogie.__proto__.__proto__ === Object.prototype //true
   ~~~

   최상위 Object의 prototype으로 모든 타입은 연결되어있다. 물론 primitive values(원시값)도 예외는 아니다.

   ~~~js
   'woogie'.__proto__.__proto__ === Object.prototype //true
   ~~~

2. 객체리터럴과 인스턴스객체의 차이

   * 객체리터럴

   key와 value의 구조로 되어있는 일반 객체를 의미한다.

   ~~~js
   const codesquad= {
       frontMaster : 'crong',
       backMaster : 'pobi'
   }
   ~~~

   * 인스턴스객체

   new키워드를 통해 생성되어 constructor함수와 prototype을 가지는 객체를 의미한다.

   ~~~js
   function Woogie(){
       this.value = 10;
   }
   Woogie.prototype.getValue = function (){
       return this.value;
   }
   
   const woogie = new Woogie();
   woogie.value //10
   woogie.getValue() //10
   ~~~


3. new의 동작
