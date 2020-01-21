---
layout: post
title: 에러 핸들링
comments: true
category: JavaScript
---

# JS 에러 핸들링
자바를 공부했다면 에러 핸들링을 기억할 것이다. 에러 핸들링이란 우리가 작성한 코드에서 에러가 발생했을경우 대처하는 코드를 의미한다. 예를 들어 컴퓨터에서는 자연수를 0으로 나눌경우 에러가 발생하거나 예기치 못한 값이 나온다. JS같은 경우는 Infinity라는 값이 나온다. 이런 경우를 막기위해 미리 체크를 해서 에러라고 경고를 하거나, 다른 코드로 대처하는 것을 에러 핸들링이라고 한다.
<br>

우선 간단한 문법을 보자

```javascript
try{
    /*
    실행할 코드
    */
}catch(e){
    /*
    에러가 발생했을 경우 실행할 코드
    */
}
```
이 문법은 흔히 try,catch문이라고 한다. 영어 뜻 그대로 try: 시도를 해보고, catch:문제가 생겼을경우 잡아서 실행을 한다. 여기서 catch안의 'e'는 try문에서 발생한 에러의 정보가 자동으로 들어간다. 그럼 한번 로그로 어떻게 나오는지 확인해보자.

```javascript
try{
    console.log(t) // 변수 t는 정의되지 않았다.
}catch(e){
    console.log('에러 발생')
}
```
이렇게하면 변수 t는 정의되지 않아서 에러가 발생하고 이 발생한 에러는 catch문에의해 잡혀서 catch문의 로그가 나온다.

<br>그렇다면 우리가 직접 에러를 만들고 이것을 catch로 잡는 방법을 알아보자.

<br>우선 우리가 직접 에러타입을 만들어야한다. 에러타입을 만드는 방법은 class로 만드는 방법이있다.

```javascript
class MyError{
    construct(msg,type){
        this.msg
        this.type
    }
}
```
이렇게 MyError라는 에러를 만든다. 여기서 msg는 에러가 발생했을때 보여줄 에러 메세지이고 type은 에러 타입을 정한다.

<br>이제 이것을 사용하는 방법을 보자. 우선 시나리오는 사용자의 입력을 받았는데 이 입력의 끝이 .txt가 아니라면 에러가 발생하는 것이다.

```javascript
class MyError{
    constructor(msg,type){
        this.msg = msg
        this.type = type
    }
}

try{
    const userText = 'hello.doc'

    const isTxt = userText.endsWith('.txt')

    if(!isTxt){
        throw new MyError('txt파일이 아닙니다.','NotTextFile')
    }
}catch(e){
    console.error(e.msg)
    console.error(e.type)
}
```
위의 코드의 실행결과는 

* txt파일이 아닙니다
* NotTextFile

이렇게 나온다. 이런식으로 우리가 직접 에러를 만들어서 사용이 가능하다.