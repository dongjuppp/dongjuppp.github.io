---
layout: post
title: Test - 1
comments: true
category: Spring
---

# 테스트 - 1
이번에는 스프링에서 Test를 어떤식으로 하는지 알아 보겠다. 우선 테스트에 관한 글은 길어질것같아 여러차례 나눠서 쓸 예정이다. 이번 글에서는 스프링에서 사용하는 테스트 Tool중 하나인 Junit, AssertJ를 알아보자.

## Junit & AssertJ
Junit같은 경우 스프링을 위한 Test Tool이 아니다. 자바기반 프로젝트라면 모두 사용할 수 있다. 그런데 Spring Boot의 경우 의존성에 spring-boot-starter-test를 추가하면 Junit이 같이 들어온다.
```xml
<dependency>
        <groupId>org.springframework.boot</groupId>
         <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
</dependency>
```

![image](https://user-images.githubusercontent.com/47367509/74941481-6e141b00-5436-11ea-8e66-9915ce2f4c21.png)

밑줄 친 부분을 보면 프로젝트의  의존성으로 Junit이 들어와 있고 다른 여러 Tool들도 같이 들어와있는 것을 알 수 있다.<br>

또한 AssertJ도 같이 들어와 있다. AssertJ는 테스트 코드를 가독성있고 유지보수가 용이하게 만들어 준다. Junit과 AssertJ는 단위 테스트를 위해 사용되고 둘은 같이 사용해서 테스트 코드를 만든다.


## 간단한 예제
그럼 바로 간단한 예제를 보자<br>

우선 Junit과 AssertJ를 사용하는 방법은 스프링의 의존성을 추가한뒤 src/test밑에 테스트 코드를 만들 자바 파일을 만드는 것이다

![image](https://user-images.githubusercontent.com/47367509/74951347-47101600-5443-11ea-8c2c-581cbe879dbc.png)

그리고 이 자바 파일의 클래스에 아래와 같이 적는다 

```java
@SpringBootTest
class DemoApplicationTests{

    @Test
    public void test1(){
        /*
        테스트 내용
        */
    }
}
```
여기까지 했다면 이제 이 클래스안에 메서드를 만들고 메서드 위에 @Test로 이 메서드가 Test라는 것을 설정하면 테스트 코드가 된다.

1. 문자열 Test
문자열 Test는 값이 Null인지, 특정 단어를 포함하는지, 특정 단어로 시작하는지 등등 여러 검증이 있다. 이런 검증은 AssertJ에 메서드로 구현이 되어 있다.

```Java
@Test
void contextLoads() {
    String str = "hello world!";
    assertThat(str).isNotNull()
            .isEqualTo("hello world!")
            .contains("world!")
            .startsWith("hello")
            .endsWith("!");
 }
```
위 코드는 hello world!라는 문자열은 검사한다. 우선 AssertJ는 검사하는 대상을 assertThat안에 넣고 그 후에 체이닝을 통해 검사하고자하는 것들을 넣을 수 있다. isNotNull부터 이름만 봐도 이 메서드가 무엇을 검증하는지 알 수 있다.
<br>

2. Collection Test

Collection은 자바에서 가장만이 사용되기 때문에 잘 알아 둘 필요가 있다 우선 이번에는 가장 많이 사용되는 List에 대해 알아보자

```Java
@Test
    void contextLoads() {
        ArrayList<Student> list = new ArrayList<>();
        Student s1 = new Student("jason",15);
        Student s2 = new Student("jane",18);
        Student s3 = new Student("steve",25);
        Student s4 = new Student("kim",30);
        Student s5 = new Student("robert",12);
        list.add(s1);
        list.add(s2);
        list.add(s3);
        list.add(s4);
        list.add(s5);

        assertThat(list).filteredOn(student -> student.name.contains("j"))
                .containsOnly(s1,s2);

        assertThat(list).extracting("name")
                .contains("jason","jane","steve")
                .hasSize(5);

    }
```
우선 Student라는 클래스의 프로퍼티는 name,age가 있다. 그리고 이 student를 list에 5개를 만들어 넣었다.<br>

그 다음 첫번째 assertThat에서는 filteredOn으로 안에 있는 조건에 만족한는 것들을 필터링한 후 containsOnly로 s1,s2가 조건에 맞는 인스턴스인지 검사 했다.

<br>
그 다음  assertThat에서는 extracting으로 name프로퍼티에 대해 검사를 하겠다고 정한뒤 contains로 name프로퍼티 중 jason, jane, steve가 있는지 검사 했다.<br>
또한 두번째 assertThat처럼 하나늬 프로퍼티에 대해 검증할때는 프로퍼티이름 옆에 클래스 타입을 넣어주어서 Type Safe하게 해줄 수 있다.
<br>
<br>
여기까지만 봐도 검증하는 대상에 대한 메서드들의 이름이 매우 직관적이라 IDE에서 나오는 자동완성 대상에서 필요한 것을 이름만 봐도 사용할 수 있을 정도이다. 
 
<br>
<br>

 아래 주소는 assertJ의 공식 문서이다. 참고하면 기본적인 사용법이 모두 나와있다.
 <br>
 
[https://assertj.github.io/doc/](https://assertj.github.io/doc/)