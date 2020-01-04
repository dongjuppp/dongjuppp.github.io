---
layout: post
title: ApplicationContext
comments: true
category: Spring
---

## ApplicationContext
Application Context는 Spring의 IOC의 역할을 하는 부분이다.  즉, 빈 인스트너스 생성, 
의존 관계 생성, 빈 제공을 한다.
<br>

Spring IOC 컨테이너의 역할
* 빈 인스턴스 생성
* 의존 관계 설정
* 빈 제공

ApplicationContext를 생성하는 방법에는 다음과 같은 방법이 있다.

* XML Spring 초창기의 고전적인 방식
* Java XML을 사용하는것에 불편함을 느껴 만든 방식
* Annotation 최신 방식
<br>
<br>
### XML
XML 방식은 resource 폴더아래 XML파일을 생성해서 XML안에 Spring Bean을 정의하고 관계를 정의하는 방식<br>
이방식으로 생성한 IOC는 아래와 같이 사용한다.
```java
ApplicationContext applicationContext =
        new ClassPathXmlApplicationContext("application.xml");

    Book book = applicationContext.getBean("book");
 ```

### Java
이 방식은 XML을 사용하는 것에 불편함을 느끼고 좀 더 편하게 Bean 등록을 하기 위해 나온 방식
<br>

```java
@Configuration
public Class Config{
    @Bean
    public Book book(){
        return new Book();
    }
}
```
사용하는 방식은 아래와 같다
```java
ApplicationContext applicationContext2 = new AnnotationConfigApplicationContext(Config.class);

Book book = applicationContext2.getBean("book");
```
이방식도 XML처럼 설정 파일안에 Bean을 일일이 등록해야 하는 불편함이 있다. 하지만 이런 불편함을 위해 ComponentScan이라는 좋은 도구가 있다.
<br>
<br>
ComponentScan은 Spring Bean으로 등록할 클래스에 @Component를 붙인 Java Class를 자동으로 찾아내서 Spring Bean으로 등록한다.
<br>
<br>
@ComponentScan(basePackage = "패키지")는 해당 패키지안에 있는 클래스들을 대상으로 한다.
<br>
@CompnentScan(basePackageClasses = "클래스명")은
해당 클래스의 위치로 부터 하위 패키지까지 모두 찾는다.
<br>
Spring Boot는 basePacageClasses를 @SpringBootApplication가 자동으로 사용하여 Application.class 부터 찾는 방식이다.