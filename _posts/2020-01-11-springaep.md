---
layout: post
title: ApplicationEventPublisher
comments: true
category: Spring
---

# ApplicationEventPublisher
스프링에서 Event가 발생했을 때 이 Event를 받는 객체들에게 일괄적으로 알려주는 기능을 하는 ApplicationEventPublisher이라는 인터페이스가 ApplicationContext에 구현 되어 있다.<br>
Event를 일괄적으로 알려주는것은 GOF의 디자인 패턴에 나온 옵저버 패턴과 같은 것이다. <br><Br>
옵저버 패턴은
예를들어 사용자의 요청이 들어와서 DataBase에 저장하면 A,B,C객체의 상태가 변해야 하는 로직이 있을때 리스너를 따로 두어서 DataBase에 저장할 때 이 리스너에게 알려주면 리스너가 A,B,C에게 알려주는 것이다. <br><br>
이건 코드와 실행결과를 보면 이해가 갈것이다.

```java

public class AClass {

    private int data;

    public int getData(){
        return data;
    }
}
```
우선 AClass이다 이클래스는 Event가 발생했다는 것을 받는 역할을한다. 코드를 보면 스프링 Annotation을 안썼는데 이게 스프링에서 추구하는 Java POJO이다<br>
<br>

```java
@Component
public class DataBase {


    public void insert(int data){
        /*
        * 데이터 저장
        * */
    }


    @EventListener
    public void publisherEvent(AClass aClass){
        System.out.println("AClass에게 알림");
    }
}
```
그다음은 DataBase인데 insert메서드로 데이터를 입력한다고 가정하고 밑에 @EventListener Annotation은 해당 메서드가 옵저버 패턴을 구현한 것이라고 알리는 것이다<br><br>

```java
@Component
public class AppRunner implements ApplicationRunner {

    @Autowired
    ApplicationContext applicationContext;


    @Override
    public void run(ApplicationArguments args) throws Exception {
        DataBase dataBase =applicationContext.getBean(DataBase.class);
        dataBase.insert(23);
        applicationContext.publishEvent(new AClass());
    }
}
```
이부분은 DataBase의 insert메서드를 통해 데이터를 입력하고 바로 ApplicationEventPublisher로 Event를 알리는 것이다.

실행결과는 "AClass에게 알림"이라고 나온다