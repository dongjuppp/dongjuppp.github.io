---
layout: post
title: Environment
comments: true
category: Spring
---

## Environment

Spring에는 Environment 즉, 환경에 따라 앱을 바꿀 수 있는 기능이 있다.
<br>
무슨 소린가 하면 한가지 앱을 만들다보면 이런 상황에서는 이 Bean들만 사용되었으면 좋을 텐데 이런 생각이 들 수 있는데 그걸 해준다.<br>
또는 IOC 컨테이너를 좀 분리한다고 생각하면 될 것 같다.<br>

```java
@Configuration
@Profile("test") // test라는 Profile에서만 유효한 설정
public class TestConfiguration {

    @Bean
    public BookRepository bookRepository(){
        return new TestBookRepository();
    }
}
```

<br>이렇게 하면 위의 클래스는 test라는 Profile에서만 유효한 설정 클래스가 된다. 
<br>다른 방식으로는 @Component를 붙이 Spring Bean에 똑같이 Profile을 붙이는 방식이 있다.