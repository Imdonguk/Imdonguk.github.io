---
title: "javascript 간단정리 "
layout: post
date: 2019-01-01 15:12
image: /assets/images/markdown.jpg
headerImage: false
star: true
category: blog
author: woogie
---



# Today Log

> 최근에는 react라이브러리를 공부중이다. react에 대해선 따로 글을 쓰겠다. 오늘은 코드스쿼드에서 간단하게 시험을 봤는데, 프론트엔드 개발자로서 필요한 개념을 간단하게 설명하는 것도 굉장히 좋겠다는 생각이 들었다. 오늘은 시험본 문제를 짧게 설명하도록 해보겠다.



### 1. 최근한 프로젝트에서 가장 어려웠던 점은 무엇이고, 어떻게 해결했는지!??

<b>첫번째</b>로는 이벤트 input이 일어났을 때 inputValue의 상태를 저장해야 했어야했다. 방법이 두가지가 있었는데 둘 다 명확한 단점이 있어서 어떤 방법을 선택해야할 지 고민이 많이 됐다.

*  첫번째 방법 : `this.inputValue` 처럼 클래스내의 property로 저장하기에는 input이 일어났을 때 한정적으로 쓴다는 단점이 있었다. 클래스내에는 input이벤트가 일어나지만, key이벤트, mouse이벤트 등등 다른 클래스내의 메서드들이 있었다. 
* 두번째 방법 : 실제 동작하는 메서드는 addEventListener의 콜백함수니깐 클로져를 이용해서 딱 한번만 선언하는 변수를 이용하는 것이었다. 

~~~js

input: () => {
    let inputValue;
    this._el.input.addEventListener('input', debounce(({ target }) => {
        if (inputValue === target.value) return;
        inputValue = target.value;
        this.inputEventHandler(inputValue)
    }, timer))
}
focus: () => {
    this._el.input.addEventListener('focus', this.focusEventHandler.bind(this))
}
click: () => {
    this._el.searchList.addEventListener('click',this.clickSearchListHandler.bind(this))
    this._el.submit.addEventListener('click', this.clickSubmitHandler.bind(this))
}
clickAnotherArea: () => {
    document.body.addEventListener('click', this.clickAnotherAreaHanlder.bind(this))
}
keyup: () => {
    this._el.searchbar.addEventListener('keyup', this.keyupHandler.bind(this))
}

~~~

이것은 실제 구현한 코드인데, 두번째 방법처럼 콜백함수에 추가된 동작이 들어가니깐 다른 메서드들과 일관성이 맞지않았다. 

this.inputValue를 써서 메서드들의 일관성을 맞출까, this.inputValue와 같은 property를 최대한 줄일까 고민을 했다. 

선택한 방법은 두번째 방법이였다. 아무리 보기좋은 코드가 좋다고는 하지만, 다른 메서드에서도 접근할 수 있는 `this.inputVlaue` 를 선언하는 것은 내가 보기엔 별로였다. 두방법 모두 명확한 장점과 단점이 있을 때 선택하는 게 힘들었다.



### 2. 브라우저의 렌더링 동작과정을 짧게 설명

1. DOM을 읽는다.
2. DOM을 파싱한다.
3. DOM Tree와 Style Rule을 만든다.
4. RenderTree를 만든다.
5. Layout
6. Painting
7. Composition



### 3. Object.create의 역할!?

프로토타입을 포함한 객체를 생성해줍니다. 

Woogie라는 함수와 Dali라는 함수가 있다고 가정한다.

Woogie의 __ proto __ 는 Object라하고, Dali도 __ proto __ 는 Object라고 한다.

~~~js
Woogie.prototype = Object.create(Dali.prototype)
~~~

이렇게 하면 

Woogie의 __ proto __ 는 Dali가 되고 Dali는 그대로 Object를 참조하게 된다.

Object.create를 사용하면 class extends처럼 상속효과를 쓸 수 있다.



### 4. JS에서 모듈내의 private한 속성을 만드는 법?

 함수를 이용한다. private한 것은 선언해서 사용만해주고, public한 것은 return 해주면 된다.

~~~js
function woogie(){
    let a = 'crong';
    return function (){
        //여기서 a라는 변수를 사용한다.
    }
}
~~~



### 5. 재귀호출로 stack overflow를 막는 방법

stack에 쌓지 않으면 된다. 비동기를 이용해서 이벤트큐에 쌓는 방법이 있다.



### 6. closure와 스코프관계를 설명하기!

함수안에 함수가 있을 경우 예외적인 closure스코프가 생성된다.

안에 있는 함수는 밖에 있는 함수에 선언된 변수를 사용할 수 있다.



### 7. 본인이 경험한 OOP관점에서의 객체분리를 설명, 느낀 장점!

상속, 캡슐화, 추상화 이런이야기는 안하겠다.

우선 역할에 따라서 객체를 분리한다는 자체가 너무 좋았다.

해당 기능단위로 클래스를 나누고, 기능을 받는 대상을 인자로 주면 

그 대상에 똑같은 기능을 줄 수 있는 게 좋았다. 한마디로 재사용을 할 수 있는 게 좋았다.

결론은 역할을 class단위로 나누고, 그것을 재사용하는게 제일 좋았다.

### 8. 객체탐색방법

for-in을 사용한다. hasownproperty를 사용하는게 좋다. 그 해당객체만 탐색을 해야한다면말이다.

Object.keys나 Object.values라는 메서드를 사용해 key나 value를 배열로 가져와 탐색한다.



### 9. ES6의 Class extends 내부 동작원리

javascript의 class는 프로토타입체이닝을 이용해서 동작된다.

javascript에서는 프로토타입으로 연결되어 참고하고 있는 

### 10 .arrow 함수의 this가 결정되는 방식

arrow의 함수의 내부엔 this, arguments가 없다.

그래서 this는 그 함수를 감싸고 있는 객체를 가르킨다.

### 11. undefined와 null의 차이점!

보통 변수를 선언했지만 값을 할당하지 않았을 경우가 undefined이다. 또 undefined라는 값을 할당할 수 도있다. 타입이면서 값이다.

null은 값이 비어있음을 나타냅니다. 아무것도 참조하고 있지 않아서 객체를 담을 변수를 초기화할 때 많이 사용합니다.

### 12. polyfill

만약 Array.from을 모든 브라우저에서 동작하도록 호환성을 맞춰준다면

1. 브라우저에 Array.from이 있는지없는 지 확인한다.

2. 있으면 그대로 쓰도록한다.
3. 없으면 Array.from = function () {} 해서 Array.from의 동작을 설정해준다.

### 13. 프로그래밍에서 요구사항을 받았을 때 구현전까지의 과정 ( 구체적 )

1. 요구사항을 파악하고, 역할을 분리한다. ( 클래스별로 나눈다. )

2. STEP을 정한다. 기능 구현할 순서를 정한다. ( 전체를 다정하기보단 우선순위를 몇개정도 정한다. )

3. 구현할 기능(클래스)을 정했으면, 구현되기위한 동작을 함수로 세분화시켜서 나눈다.
   * 함수명으로 동작을 정해주고(빈 함수에 이름만 정해준다.)
   * 최대한 한개의 함수에 한개의 동작하게끔 한다.

