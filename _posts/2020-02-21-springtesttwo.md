---
layout: post
title: Test - 2
comments: true
category: Spring
---

# 테스트 - 2

이번글에서는 지난번 test에 이어서 mock에 대해 알아보겠다. <br>
우선 mock을 이용해 테스트 코드를 작성하기 전에 mock이 무엇인지 부터 알아보자<br>

## Mock 이란?
mock이란 위키백과에 나와있는 의미에 의하면 가짜 객체이다.
<br>

>모의 객체(Mock Object)란 주로 객체 지향 프로그래밍으로 개발한 프로그램을 테스트 할 경우 테스트를 수행할 모듈과 연결되는 외부의 다른 서비스나 모듈들을 실제 사용하는 모듈을 사용하지 않고 실제의 모듈을 "흉내"내는 "가짜" 모듈을 작성하여 테스트의 효용성을 높이는데 사용하는 객체이다. 사용자 인터페이스(UI)나 데이터베이스 테스트 등과 같이 자동화된 테스트를 수행하기 어려운 때 널리 사용된다. 

<br>
즉, 내가 테스트를 진행하는데 테스트의 검증 대상이 되는 객체가 사용하는 다른 객체를 임의(가짜)로 만들어서 테스트를 진행하게 하는게 Mock이다.
<br>

한가지 예를 들어보자 만약 학사관리 프로그램을 만드는데 테스트할 내용은 학생(Student 클래스)이 수강 신청을 하면 학생의 학년을 보고 수강 신청한 과목에 맞는 학년인지 검사하고 맞으면 DB에 추가하는 것이라고 해보자.
<br>
우선 학생 클래스와 수강신청을 관리하는 EnrollService라는 클래스가 있다고 해보자.

```java
@Component
public class Student {

    private int grade;

    public int getGrade(){
        return grade;
    }

    public void setGrade(int grade){
        this.grade = grade;
    }
}
```
<br>

```java
@Service
public class EnrollService {

    public boolean enroll(Student student){

        if(student.getGrade() == 3){
            return true;
        }else{
            return false;
        }
    }
}
```
이제 테스트를 할텐데 코드에 나와있는 것처럼 grade가 3일때 정상적으로 DB에 저장되었다는 true를 리턴한다. 테스트에서는 3을 줄때 아닐때 내가 의도한 대로 리턴하는지 확인을 하는데 이때 Student클래스를 임의로 주어야한다. 이때의 Student클래스가 Mock이 된다.<br><br>

## Mockito
이제 mock의 의미를 알았다. 그러면 우리가 일일이 이런 Mock을 인스턴스를 새로 생성해야 할까? 아니다 다행이도 이런 Mock을 만들어주는 Mockito라는 Test Tool이 있다.<br>

```xml

<dependency>
    <groupId>org.mockito</groupId>
    <artifactId>mockito-core</artifactId>
    <version>3.2.4</version>
    <scope>test</scope>
</dependency>
```
일단 메이븐에서는 위의 의존성을 추가하면 되고 스프링 부트는 spring-boot-starter-test를 추가하면 같이 들어온다.
<br>

사용하는 방법은 간단하다.

```java
   @Mock
   Student student;

   @Autowired
   EnrollService enrollService;


    @Test
    void contextLoads() throws Exception{
        given(student.getGrade()).willReturn(3);
        assertThat(enrollService.enroll(student)).isEqualTo(true);
    }
```
위의 코드를 보면 @Mock로 Student를 Mock으로 만든 것이다. 그다음 contestLoads를 보면 given이라는 메서드에 getGrade를 인자로 주고 willReturn으로 3을 정했다. 메스드명이 직관적이라 한번에 이해가 가지만 설명을 하자면 given은 인자로 받은 메서드를 임의의 결과를 가지게 정하는 것이고 willReturn은 결과값을 우리가 정하는 것이다.
<br>그다음 assertJ로 true를 리턴했는지 검사를 했다.
<br> 여기서 given과 willReturn은 when과 thenReturn으로도 대체가 가능하다. 두 개의 차이는 테스트를 BDD로 할지 TDD로 할지의 차이이다. 자세한 설명은 여기에 있다
<br>
[https://blog.aliencube.org/ko/2014/04/02/differences-between-bdd-and-tdd/](https://blog.aliencube.org/ko/2014/04/02/differences-between-bdd-and-tdd/)
<br><br>
방식이 매우 간단하고 코드또한 직관적이다.

### @MockBean
우리는 스프링을 공부중이니 스프링에서 사용할 수 있는 @MockBean을 알아보자
<br>
우선 @MockBean은 @Mock과 동일하지만 @MockBean은 스프링 Bean을 대상으로 Mock을 만들때 사용한다. 또한 @MockBean을 사용하면 대상 객체를 의존하고 있는 모든 Bean들에세 이 @MockBean으로 만든 Mock객체를 준다.

<br>위의 학사관리 예제를 좀 더 추가해서 예시를 보자

```java
@Repository
public class EnrollRepository {

    public boolean save(Student student){
        if(student.getGrade() == 3)
            return true;
        else
            return false;
    }
}
```
우선 DB에 직접 Access하는 Repository클래스이다.(예시이니 그냥 보자) 이제는 Service에서 직접 DB에 넣는 것이다 아니다.
<br><br>

```java
@Service
public class EnrollService {

    @Autowired
    EnrollRepository enrollRepository;

    public boolean enroll(Student student){
        return enrollRepository.save(student);
    }
}
```
이제 service는 repository에 의존해서 DB에 저장한다.<br>
보다싶이 repository는 스프링 Bean을 등록이 되어있다.<br>
이제 테스트를 해보면 Repository를 Mock으로 만들어서 3일때 false를 리턴하게 해보자
<br><br>

```java
@MockBean
   EnrollRepository enrollRepository;

   @Autowired
   EnrollService enrollService;


    @Test
    void contextLoads() throws Exception{
        Student student = null;
        given(enrollRepository.save(student)).willReturn(false);
        assertThat(enrollService.enroll(student)).isEqualTo(false);
    }
```
설명한대로 false를 리턴하게 해서 테스트가 성공했다. @Mock과 @MockBean의 차이점은 대상 객체가 스프링 Bean이 아니냐 맞는냐 이다.
<br>
추가적으로 위의 코드를 보면 student는 null이다 그런데도 정상적으로 작동한다. 즉, given, willReturn은 메서드의 호출에 대해서 내부의 동작 방식은 전혀 상관하지 않는다. 그저 호출됐으면 willReturn으로 정한 값은 주는 것이다.


## MockMvc
이번에는 Mock중에서 웹환경의 Controller에 대한 Mock을 만들어주는 MockMvc를 알아보자.
<br>
MockMvc는 컨트롤러는 Mocking해서 요청에 대한 응답을 테스트 하는대 사용한다. 예를 들어 우리가 로그인 페이지,기능을 만들고 테스트를 할려고 하는데 테스트 할 방법은 애플리케이션을 실행 시켜 아이디, 패스워드를 쳐보고 확인 할 수 있는데 이 방법은 좀 힘들다. 우선 애플리케이션 크기가 크다면 구동시간이 오래걸릴 것이고 여러가지 케이스를 테스트 할려면 브라우져를 닫았다 다시 여는 노가다 행위를 해야한다.
<br><br>
이런 힘든 과정을 하기전에 MockMvc를 사용하면 로그인 페이지에서 보낸 아이디, 패스워드를 보고 로그인 승인을 제대로 하는지에 대해 테스트를 할 수 있다.


```java

@RestController
public class MyController {


    @GetMapping("/hello")
    public String hello(){
        return "<h1>Hello World!</h1>";
    }
}
```
우선 예제로 만든 컨트롤러이다. 이 컨트롤러는 /hello라는 요청이 간단하게 Hello Wrold!라는 문자열을 준다. (컨트롤러가 RestController이다!)

![image](https://user-images.githubusercontent.com/47367509/75048546-fdd7c900-550b-11ea-8f15-f6e1a9318453.png)

<br>
그럼 이제 이 컨트롤러를 테스트 해보자

```java
@RunWith(SpringRunner.class)
@SpringBootTest
@AutoConfigureMockMvc    //MockMvc를 위해 추가
class DemoApplicationTests {

    @Autowired
   MockMvc mockMvc;


    @Test
    void contextLoads() throws Exception{
        mockMvc.perform(get("/hello"))
                .andExpect(status().isOk())
                .andExpect(content().string("<h1>Hello World!</h1>"))
                .andDo(print());
    }

}
```
이게 테스트 코드이다. 우선 봐야 할것은 @AutoConfigureMockMvc를 추가해서 MockMvc를 Autowired로 가녀올 수 있게 하야한다. 이렇게 하면 해당 애플리케이션의 url요청에 대해 기대값을 테스트 할 수 있다.
<br>
이제 mockMvc.perform()부터 설명하면 일단 perform은 테스트할 url을 넣는 곳이다. 그 다음 andExpect는 요청에 대한 응답이 어떤 응답일 것인지 기대값을 넣는다. 첫번째 andExpect의 인자인 status.().isOk()는 url에 대해 결과가 200으로 나오는지 확인한다.(여기서 200이란 http응답에 대한것, 404 Not Found같은거) 다음 andExpect의 인자인 content().string()은 리턴의 결과를 테스트한다. 컨트롤러에서 리턴한 값을 테스트했다(RestController니깐 가능하다) 그다음 andDo(print())는 응답 (http)메세지를 콘솔에 띄워준다

![image](https://user-images.githubusercontent.com/47367509/75051215-cddef480-5510-11ea-8660-affd7a22c8c1.png)