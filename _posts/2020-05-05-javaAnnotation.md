---
layout: post
title: Annotation
comments: true
category: Java
---

# Annotation

자바를 사용하다 보면 @Override를 자주 사용할 것이다. @Override는 주로 상속받은 클래스의 메서드를 오버라이딩할 때 붙이는데 @Override같이 @가 붙은 것이 Annotation(어노테이션)이다. 처음 자바를 접했을때는 어노테이션이 단순히 코드의 주석과 같은 것이라고 생각했다. 하지만 어노테이션은 더 많은 일을한다.

## Annotation의 역할
우선 어노테이션의 역할을 한줄로 요약하면 **프로그램의 소스코드 안에 다른 프로그램을 위한 정보를 미리 약속된 형식으로 포함시킨것**이다.<br>
이게 무슨말 인지 @Override를 예로 들자면 @Override를 메소드 위에 붙이면 컴파일러는 해당 메서드를 사용할때 이 메서드가 다른 클래스로부터 상속받은 메서드인지 검사하고 아니다면 메세지를 날린다.<br> 즉, @Override를 붙혀서 컴파일러에게 정보를 알리는 것이다.
<br> 이 외에도 다른 역할이 있는데 역할에 따른 종류를 알아보자

## Annotation의 종류
어노테이션을 크게 3가지로 분류하면 아래와 같다.

|종류|역할|
|------|------|
|표준 어노테이션|자바에서 기본으로 제공하는 것|
|메타 어노테이션|어노테이션을 위한 어노테이션|
|사용자 어노테이션|개발자가 만든 어노테이션|

우선 표준 어노테이션은 자바에서 기본으로 제공하는 어노테이션이다. 대표적인것이 위에서본 @Override이다. 나머지는 아래와 같다.

|어노테이션| 설명|
|-----|-------|
|@Override|컴파일러에게 오버라이딩하는 메서드라는것을 알린다|
|@Deprecated|앞으로 사용하지 않을 것을 권장하는 것을 알린다|
|@SupppressWarnings|컴파일러의 특정 경고 메세지를 생략할 것을 알린다|
|@SafeVarags|지네릭스 타입의 가변인자에 사용한다|
|@FunctionalInterface|람다식 인터페이스임을 알린다|
|@Native|native메서드에서 참조되는 상수 앞에 붙인다|

<br>
다음으로 메타 어노테이션은 어노테이션을 만들기 위해 사용되는 기본 어노테이션을 의미한다.

|어노테이션|설명|
|---|---|
|@Target|어노테이션이 적용가능한 대상을 지정하는데 사용|
|@Documented|어노테이션 정보가 doc문서로 포함되게 한다|
|@Ingerited|어노테이션이 자손 클래스에 상속되도록 한다|
|@Retention|어노테이션이 유지되는 범위를 지정하는데 사용한다|
|@Repeatable|어노테이션을 반복해서 적용할 수 있게 한다|

그럼 다음으로 어노테이션을 직접 만들어서 사용하는 것을 해보자

## 어노테이션 만들기
우선 어노테이션을 만들기 전에 메타 어노테이션의 Target,Retetion의 세부 종류를 알아야한다.(개인적으로 이 2개가 메타 어노테이션에서 중요하다고 생각한다.)

### Target
|대상 타입|의미|
|---|---|
|ANNOTATION_TYPE|어노테이션|
|CONSTRUCTOR|생성자|
|FIELD|필드|
|LOCAL_VARIABLE|지역 변수|
|METHOD|메서드|
|PACKAGE|패키지|
|PARAMETER|매개 변수|
|TYPE|타입|
|TYPE_PARAMETER|타입 매개변수|
|TYPE_USE|타입이 사용되는 모든곳|

## Retention
|유지 정책|의미|
|---|---|
|SOURCE|소스 파일에만 존재, 클래스 파일에는 존재하지 않음|
|CLASS|클래스 파일에 존재, 실행시에 사용불가, 기본값|
|RUNTIME|클래스 파일에 존재, 실행시에 사용가능|

위의 2개를 알았다면 기본적인 어노테이션을 만들 수 있다.

```java
@Target({ElementType.FIELD, ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
public @interface MyAnnotation {
    String name() default "default Name";

}
```
위의 어노테이션을 설명하면 우선 Target으로 필드와 타입선언부에 어노테이션을 적용할 수 있게 했다. 그리고 Retention으로 실행가능하게 했다.

<br>

```java
class Suzy{

    @MyAnnotation(name = "suzy")
    String name;

}
```
직접 만든 어노테이션을 이렇게 사용했다. 그렇다면 name에 바로 suzy라는 값을 저장해서 가져올 수 있을까? 아니다. 어노테이션으로 적용한 값을 가져오기 위해서는 다른 방법이 필요하다.

```java
public class Main {

    public static void main(String[] args) throws Exception{
        Suzy me = new Suzy();
        Field field = me.getClass().getDeclaredField("name");
        MyAnnotation myAnnotation = field.getAnnotation(MyAnnotation.class);
        System.out.println(field.getName());
        System.out.println(myAnnotation.name());
    }
}
```
위처럼 실행 가능한 main메서드를 만들고 getDeclaredField메서드로 가져올 요소를 적어서 사용해야 한다.