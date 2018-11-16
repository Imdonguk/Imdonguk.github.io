---
title: "[개발로그]2018-11-16(금)"
layout: post
date: 2018-11-16 17:30
image: /assets/images/markdown.jpg
headerImage: false
star: true
category: blog
author: woogie
---

#자바스크립트 : function declaration과 Arrow Function의 this 스코프차이



ES6가 나옴에 따라 많은 변화가 생겼는데 그 중 하나인 `Arrow Function`에 대해서 간단하게 설명하자면 `() => {}`이런 형태를 가지고 있습니다. 기능도 함수의 기능을 하고 있습니다. 

하지만 `function () {}` 과 `()=>{}` 는 같다고 할 수는 없습니다.

우선 두 함수의 차이를 설명하기 이전에 `Arrow Function` 의 바뀐점에 대해서 예제를 통해서 설명해보겠습니다.



 ### 1. 사용법

~~~js
//기존함수
function woogie(crong){
    console.log(crong);
}
//Arrow Function
const woogie = crong => {
    console.log(crong);
}

//한 줄의 동작일 경우
const woogie = crong => console.log(crong)

//return할 경우
const woogie = crong => 'hi ,'+crong
woogie('crong') //리턴값 'hi ,crong'

//인자가 두 개 이상일 경우
const woogie => (crong, pobi) => crong + ' and ' + 'pobi'
woogie('crong','pobi') //리턴값 'crong and pobi'
~~~



`Array Function` 이 기존 `function` 을 완전히 대신 할 수 없습니다.

콜백함수에서 사용하면 간단히 표현할 수 있다는 장점이 있다. 콜백함수 사용시 다른 점이 있는데 뒤에 설명하겠습니다.



### 2. arguments가 없다.

실제로 구현하면서 힘들었던 경험을 토대로 설명을 해보겠습니다.

`Arrow Function` 을 많이 쓰면서도 arguments를 쓸 일이 거의 없다가 throttle이라는 함수를 구현 중에 arguments가 없다는 사실을 뒤늦게 알아서 구현중에 머리 아팠습니다.



~~~js
function throttle(func, timer){
    let shutter;
    return function (){
        if(!shutter){
            func.apply(null, arguments);
            setTimeout(()=>{shutter = null}, timer);
        }
    }
}
~~~

이렇게 구현된 함수이다. 간단하게 설명하자면 일정시간동안에 연속되게 함수를 호출하지 않게 위한 코드입니다.

실제로 사용할 땐 

~~~js
addEventListner('click', throttle(()=>{//...}), timer )
~~~

이렇게 사용했습니다.



다시 내가 실수한 상황

~~~js
function throttle(func, timer){
    let shutter;
    return ()=>{		//Arrow Function
        if(!shutter){
            func.apply(null, arguments);
            setTimeout(()=>{shutter = null}, timer);
        }
    }
}
~~~



이렇게 반환하는 함수를 `Arrow Function` 으로 하다보니깐 클로져상황때문에 arguments가 throttle의 인자인 func와 timer를 받게 되었다. 이 땐 arguments가 없는 줄 모르고 실수를 저질렀습니다..



이번엔 arguments가 없지만  `ES6의 spread operator` 를 사용해서 간단하게 구현해보겠습니다.



~~~js
//ES5
function sum_es5(){
    var arr = Array.prototype.slice.call(arguments);
    return arr.reduce(function (prev, curr){
        return prev + curr
    })
}
console.log(sum_es5(1,2,3,4))

//ES6
const sum_es6 = (...args) => args.reduce((prev,curr) => prev+curr)
console.log(sum_es6(1,2,3,4))
~~~



###3. this가 다르다

제일 중요한 점입니다. 이것을 위해서 글을 쓰게 됐습니다. 

`Arrow Function` 의 또 다른 특징은 bind나 call을 사용하여 this를 변경할 수 없습니다. 

그리고 기존의 함수와 this가 다릅니다.

 `Arrow Function` 은 this를 계승받습니다.  보통 `function declaration` 의 this는 그 함수 자신이 포함되어 있는 해당 객체, 포함되어 있지 않을 땐 window를 가르킵니다. 



<b>(1) function declaration을 사용했을 때</b>

~~~js
const codesquad = {
    crong(){
        console.log('첫번째 this : ' + this);
        return (function (){ console.log('두번째 this : ' + this) })()
    }
}
codesquad.crong();
//첫번째 this : [object Object]
//두번째 this : [object Window]
~~~

첫번째 this는 codesquad라는 객체를 가르키고 두번째 this는 window를 가르킵니다.

이렇게 때문에 보통 객체안의 메서드에서 콜백함수를 인자로 받는 메서드를 사용할 때 (예를 들면 addEventlListener)

bind를 해주어서 호출될 때의 scope를 정해주어야합니다.

~~~js
//bind
const codesquad = {
    name : 'woogie',
    crong(){
        window.addEventListener('click', function (){
            console.log('my name is ' + this.name);
        }.bind(this))
    }
}
codesquad.crong();
//my name is woogie
~~~



<b>(2) Arrow Function을 사용할 때</b>

위와 똑같은 예제를 사용해보겠습니다.

~~~js
const codesquad = {
    crong(){
        console.log('첫번째 this : ' + this);
        return (()=>{ console.log('두번째 this : ' + this) })()
    }
}
codesquad.crong();
//첫번째 this : [object Object]
//두번째 this : [object Object]
~~~

첫번째 this와 두번째 this 둘 다 codesquad라는 객체를 가르키게 됩니다. 이렇게 함수안에 함수가 있을 때에도 this가 다르지 않고 this를 계승받게 됩니다. ( Lexical Scope의 특징을 가집니다. )

<b>Lexical Scope는 함수를 어디서 호출했는지에 따라 scope가 결정되는가 아니라 선언했을 때 scope가 결정됩니다.</b>



그렇게 때문에 콜백함수를 사용할 때 `Arrow Function` 을 써주면 좋습니다.



~~~js
function normalize() {
  console.log(this.coords.map(n => n / this.length));
}
normalize.call({coords: [0, 2, 3], length: 5});
// → [0, 0.4, 0.6]
~~~

이 예시를 보시면 함수 normalize의 scope를 call메서드를 사용하여 변경시켜주었습니다. 

배열메서드 map의 콜백함수 스코프는 normalize함수의 스코프를 얻기 때문에 값이 생각대로 나오는 것을 확인할 수 있습니다.



<b>이렇게 화살표함수는 콜백함수를 사용할 때 편리합니다. 따로 bind를 안해줘도 되기 때문이죠. 하지만 오히려 스코프를 혼동할 수 있으니 주의해서 쓰는게 좋을 듯 합니다.</b>





### Arrow Function을 정리하자면

1. arguments가 없다.
2. bind나 call로 this를 변경시킬 수 없다.
3. this가 기존 function declaration과 다르다. (Lexical Scope의 특징을 가지고 있다.)



### 추가내용

1. `Arrow Function` 은 new로 호출 할 수 없습니다.
2. `Arrow Function` 을 이용하여 prototype에 메서드를 할당할 수 없습니다.
3. `Arrow Function` 은 prototype 프로퍼티를 가지지 않는다. 



### 참조

* [Poiemaweb](https://poiemaweb.com/es6-arrow-function)
* [Element Javascript](https://eloquentjavascript.net/06_object.html)