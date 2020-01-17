---
layout: post
title: 콜백 함수
comments: true
category: JavaScript
---

# 콜백 함수

자바스크립트에서 콜백함수란 함수의 인자로 함수를 넣어서 받는쪽 함수가 인자로 넘어온 함수의 제어권을 가지고 필요한 시점에 인수로 넘어온 함수를 실행 시키는것을 의미합니다. 간단하게 말하면 함수를 인자로 받아서 자신의 함수 기능의 일부로 사용하는 것 입니다.<br>
<br>
또한 콜백 함수는 자바스크립트에서 setTimeout같은 내장 함수에서도 자주 이용이 됩니다.

```javascript
function callBack(){
    console.log('콜백함수 호출')
}

setTimeout(callBack,1000)
```
위의 코드드는 '콜백함수 호출'이라는 문자열을 1초단위로 로그에 기록합니다. 여기서 setTimeout의 첫번째 인자로 callBack이라는 이름의 함수를 넘겨줬습니다. 이렇게 자바스크립트의 내장 함수에 콜백 함수는 자주 이용이 됩니다.

<br><br>
## 콜백과 this
콜백함수를 사용할때 this에 대해 주의 하여야 합니다. 우선 자바스크립트의 this는 호출한 호출자가 this가 됩니다. 즉, 콜백함수를 사용할때 콜백함수 그자체의 호출자가 달라지는것 입니다.

```javascript
var name = '전역 이름'
class Student{
    constructor(name){
        this.name=name
    }

    setName(name){
        this.name = name
    }
}



thisCallBack = (name,setName)=>{
    setName(name)
}
const st = new Student('생성자 이름');
console.log(st.name)
st.setName('논 콜백')
console.log(st.name)
console.log(name)
thisCallBack(name,st.setName)
```
위의 코드와 실행결과를 보면 콜백에서의 this의 문제점이 나온다<br>
우선 thisCallBack함수를 주석처리하고 실행하면
* 생성자 일름
* 논 콜백
* 전역 이름
<br>

위의 순서대로 나온다 하지만 주석을 지우고 실행하면 이 코드는 Cannont set property 'name' of undefined라는 에러를 일으킨다 왜일가? 우선 의도는 콜백함수로 Student의 setName을 줘서 st의 이름을 바꾸기를 원했다. 하지만 이때 setName의 this는 st의 this가 아니기 때문이다. 위에 말한것 처럼 this는 호출하는 것의 this이다 즉, thisCallBack의 this가 들어가서 this에 name이라는 property가 없는 것이다.<br>

위의 문제를 해결하기 위해 우리는 call을 이용할 수 있다. 이 call은 호출한 것을 바꾸는 용도이다. 위에서 호출하는게 thisCallBack이였던것을 call을 이용해서 st인스턴스로 바꿀 수 있다.

```javascript
var name = '전역 이름'
class Student{
    constructor(name){
        this.name=name
    }

    setName(name){
        this.name = name
    }
}



thisCallBack = (name,setName)=>{
    setName.call(st,name)
}
const st = new Student('생성자 이름');
console.log(st.name)
st.setName('논 콜백')
console.log(st.name)
console.log(name)
thisCallBack(name,st.setName)
console.log(st.name)
```

이렇게 하면 아래와 같이 나온다

* 생성자 일름
* 논 콜백
* 전역 이름
* 전역 이름

<br> thisCallBack에 name이 '전역 이름'이기 때문에 정상적으로 기대한 대로 된것이다. 바뀐점은 thisCallBack에 setName을 call로서 호출하는 것을 바꾼것이다. 물론 위에서 thisCallBack의 인자로 이 call의 주인을 넣어서 해는게 재사용성있게 하는 것이다.