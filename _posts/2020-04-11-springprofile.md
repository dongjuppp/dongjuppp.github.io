---
layout: post
title: 로그
comments: true
category: Spring
---

# Logging

이번글에서는 로깅에 대해 알아보자. 글의 카테고리가 스프링인 이유는 로그의 개념과 사용방법을 스프링에서 알아볼 것이기 때문이다.<br>
우선 우리는 로그가 무엇인지, 정확히 어떤 역할을 하는지 알아야 한다.

### 로그란?

>시스템을 작동할 때 시스템의 작동상태의 기록 · 보존, 이용자의 습성조사 및 시스템 동작의 분석 등을 하기 위해 작동 중의 각종 정보를 기록하여 둘 필요가 있다. 이 기록을 만드는 것을 로깅이라 한다. 또 기록 자체를 로그(log)라고 한다. - 네이버 지식백과

로그의 개념은 간단하게 말하면 애플리케이션이 동작하는 과정, 사용자의 요청을 기록하는 것이다. 그렇다면 왜 이런 로그가 필요한가?
<br>
우리가 만든 애플리케이션은 작은 규모라 할지라도 생각보다 복잡한 로직이 있을 수 있고, 당연하게 큰 규모라면 모든 로직을 이해하고 외우기 힘들정도일 수 도 있다. 이런 애플리케이션이 어떻게 동작하는지 관리 감독을 할 수 있는 방법이 로깅이다. 또한 우리는 이런 로깅을 통해 애플리케이션에 나타난 에러를 빠르게 찾을 수 있다.

<br>

## Log Level
로그 레벨은 로그로 기록할 것들의 단계를 나타낸다. 기록에 애플리케이션의 모든 정보가 아닌 내가 단순한 정보만 나타내는것 부터해서 디버깅 하듯이 세세히 나타내는것 까지 있다.

1. TRACE : 추적 레벨은 Debug보다 좀더 상세한 정보를 나타냄
2. DEBUG : 프로그램을 디버깅하기 위한 정보 지정
3. INFO :  상태변경과 같은 정보성 메시지를 나타냄 
4. WARN :  처리 가능한 문제, 향후 시스템 에러의 원인이 될 수 있는 경고성 메시지를 나타냄 
5. ERROR :  요청을 처리하는 중 문제가 발생한 경우
6. FATAL :  아주 심각한 에러가 발생한 상태, 시스템적으로 심각한 문제가 발생해서 어플리케이션 작동이 불가능할 경우

## (System.out.println, printf)  Versus  Logging
여기까지만 보면 개발을 많이 하지않은 사람들은 자신이 연습할 때 System.out.println, printf같은 입출력 함수를 통해 기록을 남기는 행위와 비슷하다고 생각 할 수 있다. 맞다 이런 행위가 로그와 비슷한 것이다. 하지만 엄연히 로깅이 아니다. 로깅은 보다 많은 정보가 있다. 입출력함수는 우리가 적은 내용만을 출력한다. 로깅에 비해 정보가 매우 부족하다. 또한 출력함수는 영구저장을 할 수 없다. System.out.println,printf같은 경우 콘솔화면에 나타나고 애플리케이션을 종료하면 해당 콘솔은 없어진다. 그래서 문제가 생겨서 애플리케이션이 강제 종료되면 우리가 남긴 기록은 날아간다. 하지만 로깅은 파일에 따로 저장이 가능하기 때문에 애플리케이션이 종료된 후에도 계속해서 유지가 가능하다.<br>
다음 차이는 성능에 있다. 출력함수와 로깅은 애플리케이션의 성능에도 영향을 미친다. 특히 Java의 System.out.println은 성능에 많은 영향을 미친다. 즉, Java에서 System.out.println으로 로그를 남기는 행위는 절대 하지 말아야한다.<br> 왜 그런지는 println함수의 원형을 보면 알 수 있다

```java

//println의 원형
public void println(String x) {
        synchronized (this) {
            print(x);
            newLine();
        }
    }

// newLine의 원형
private void newLine() {
        try {
            synchronized (this) {
                ensureOpen();
                textOut.newLine();
                textOut.flushBuffer();
                charOut.flushBuffer();
                if (autoFlush)
                    out.flush();
            }
        }
        catch (InterruptedIOException x) {
            Thread.currentThread().interrupt();
        }
        catch (IOException x) {
            trouble = true;
        }
    }
```
위의 코드는 println의 원형이다. 여기서 주목해야 하는 것은 synchronized이다. synchronized는 사용된 곳의 접근이 여러개 일때 애플리케이션의 모든 스레드들이 이 함수가 실행되는 동안 멈추게(block) 만든다. 그래서 짧은 순간이지만 다른 작업들에 영향을 미치게 된다. 또한 println안의 newLine함수에도 synchronized가 호출되는 것을 볼 수 있다.
<br> 혹시나 이해가 안된다면 예를 한번 들어보자. 우리가 만든 애플리케이션에 로그인 기능이 있다고 가정하다. 

```java
public synchronized void login(String id, String pw){
    /*
    로그인 기능
    */
}
```
이 로그인 함수에 한명이 접근해서 blcok상태라면 다른 사용자의 로그인이 block이 unblock될때 까지 멈추게 된다.
<br>
이게 System.out.println의 제일 큰 단점이다.
다음으로 볼 차이점 Logging에 있는 유용한 기능이다.
<br>
우리가 System.out.println을 사용하면 이 함수는 어떠한 상황에도 출력이 된다. 하지만 로깅은 우리가 필요한 경우에만 특정 정보를 볼 수 있다. 예를 들어 간단한 정보만을 알고 싶다면 info레벨, 문제를 자세히 알고 싶을땐 Trace레벨로 설정하면 필요한 정보를 때에 따라 다르게 볼 수 있다.
 그럼 마지막으로 System.out.println과 Logging의 차이를 정리하자

1. System.out.println은 영구저장을 할 수 없다
2. System.out.println은 제공하는 정보가 적다, Logging은 많은 정보를 제공한다
3. System.out.println은 시스템에 부담을 준다
4. 로깅은 여러 Level로 구성되어 선택지가 여러개 있다.

<br>

## Spring에서 Log 사용하는 방법

이제까지 Log가 무엇인지 알았으니 Spring에서 사용하는 방법에 대해 알아보자

```java

public class Test{
    private Logger logger = LoggerFactory.getLogger(Test.class);


    public void Foo(){
        logger.info("Foo 함수 호출");
    }
}

```
위 코드는 로그를 사용하는 간단한 방법이다. 우선 LoggerFactory를 통해 해당 클래스의 Logger를 가져온다. 이렇게 가져온 로거를 통해 필요한 위치에서 logger.info를 통해 로그를 만든다. 여기서 보면 info는 로그 레벨에 있던것이다. 당연히 다른 레벨도 존재하고 사용하는 방법은 logger.debug, logger.trace로 남기면 된다.

<br>

### 로그 위치
다음으로는 이런 로그를 저장하는 위치를 정하는 방법인데 이 설정을 따로 하지않으면 애플리케이션 최상단에 log.log파일로 생성이 된다.
설정하는 방법은 스프링의 application.properties에 logging.file.path = 위치 를 적으면 해당 위치로 저장이된다. 예를 들어 logging.file.path = logs 라고 하면 최상단에 logs라는 폴더가 생성되고 그안에 로그파일이 들어간다.

### 로그 레벨 설정
다음으로는 로그 레벨을 설정하는 방법이다.
이 방법도 application.properties에 설정을 하는데 logging.level.패키지=레벨 이런식으로 지정하면 된다. 만약 패키지를 애플리케이션 전체로 하고 싶으면 loggin.level.root로 하면 된다.

### 로그 그룹 설정
다음으로는 로그로 남길 패키지들의 그룹을 지정하는 방법이다. 만약 debug나 trace같이 많은 로그가 남는 레벨이 필요 없는 패키지를 dubug,trace로 설정하면 로그 기록의 분량이 필요없이 늘어나게 된다. 그래서 이런 패키지들을 따로 묶어서 레벨을 지정할 수 있다.

```java
logging.group.그룹명=패키지1, 패키지2 ...
logging.level.그룹명=레벨
```