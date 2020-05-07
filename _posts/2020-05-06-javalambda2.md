---
layout: post
title: 람다 - 2
comments: true
category: Java
---

# 람다 - 2

지난 람다글에 좀 더 보충을 하기위해서 글을 적는다.

## 람다 개념 보충
지난 글에서 람다의 개념에 좀 더 보충을 할 필요가 있어보인다. 자바에서의 람다의 개념을 정확히 이해를 할 필요가 있다. 일단 람다는 메서드를 정의하는 과정을 간단한 식으로 대체하는 일을 할 수 있는데 여기서 오해가 생길 수 있다. 람다가 메서드의 용도 즉, 함수로서 사용이 되기 때문에 함수와 동급이라고 생각을 할 수 있다. 하지만 **람다는 정확히 익명 클래스와 동급이다** 이게 아주 중요하다. 람다가 함수로 취급 받지 않고 클래스로 취급을 받기 때문에 매개변수로 사용될 수 있는 것이다. <br>
<br>
이게 정확히 이해 될려면 아래의 코드를 일단 보자

```java
public interface TestInterface {

    int max(int a,int b);
}

```

```java
public class Main {

    public static void main(String[] args) throws Exception{
        TestInterface testInterface = (int a,int b) -> {return a+b;};
        System.out.println(testInterface.max(2,5));
    }
}
```

만약 람다가 메서드로 취급을 받는다면 TestInterface에 대입연산을 하는 것이 불가능하다. 하지만 람다가 클래스로 취급을 받기에 TestInterface에 대입이 가능한 것이다. 여기서 또다른 오해가 생길 수 있는데 **람다는 익명 클래스 타입이다** 람다를 받을 수 있는 인터페이스의 타입이 아니다. 


## 람다 사용법 보충
우리가 만들고, 사용하는 메서드는 모두 비슷한 형태를 가진다. 매개변수를 가지거나 안가지거나, 반환이 있거나 없거나. 경우의 수는 총 4개이다. 이런 4가지의 형태의 메서드를 자바에서 미리 인터페이스로 만들어 두었다. **java.util.function**에 일반적으로 쓰이는 형식의 메서드를 함수형 인터페이스로 만들어 두었기 때문에 이 인터페이스를 이용하는 것이 좋다. 그러면 함수형 인터페이스에 정의된 메서드 이름도 통일이 되고, 재사용성이나 유지보수 측면에서도 좋다. 함수형 인터페이스의 종류는 아래와 같다.
<br>
<br>

|함수형 인터페이스|메서드|설명|
|---|---|---|
|Runnable| run()|매개변수,반환 X|
|Supplier< T >|T get()|매개변수 X, 반환 O|
|Consumer< T >| accept(T t)|매개변수 O, 반환 X|
|Function< T, R>| R apply(T t)|매개변수, 반환 O|

<br>

```java

        //Supplier
        Supplier<String> supplier = () -> {return "hi";};
        System.out.println(supplier.get());


        //Consumer
        Consumer<String> consumer = str -> {
            System.out.println(str);
        };
        
        consumer.accept("hello");
        

        //Runnable
        Runnable runnable = () -> {
            System.out.println("Runnable Function Call");
        };
        
        runnable.run();
        

        //Function
        Function<String, Integer> function  = (str) -> {
            return 55;
        };
        
        function.apply("hello");
```

위와 같이 4가지의 방식을 이용하는것이 좋다.

<br>
하지만 위의 4가지 방식 말고 좀 더 좋은 방식이 하나 더 있다.<br>
위의 방식의 단점은 제너릭을 위해 Wrapping을 해야하는 문제가 있다는 것이다. 그래서 보다 효울적으로 처리할 수 있는 기본형 방식을 보자.
<br>
<br>

|함수형 인터페이스|메서드|설명
|---|---|---|
|DoubleToIntFunction|applyAsInt|AToBFunction은 입력이 A타입 출력이 B타입|
|ToIntFunction< T >|applyAsInt|ToBFunction은 출력이 B타입이다 입력은 제너릭|
|IntFunction< R >|apply|AFunction은 입력이 A타입 출력이 제너릭|
|ObjIntConsumer< T >|accept|ObjAFunction은 입력이 T,A타입 출력은 없다|

위와 같이 기본형으로도 제공이 되기때문에 기본형을 사용하는 것이 더욱 효율적이다<br>
<br>
