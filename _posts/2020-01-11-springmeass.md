---
layout: post
title: MessageSource
comments: true
category: Spring
---

# MessageSource

스프링은 MessageSource라는 interface를 통해 국제화(i18n) 기능을 처리할 수 있다. 여기서 국제화란 
**출판물이나 소프트웨어를 다른 언어환경에서 사용할 수 있게 하는것**이다. <br>
즉, 내가 만든 Application을 영어권 국가나 기타 다른 언어를 사용하는 국가에서 사용할 때 그 국가의 언어에 맞게 메세지를 변환 하는 것이다. 물론 자동으로 언어를 번역하는 것이 아니다. 그럼 사용하는 방법을 알아 보자.

<br><br>
MessageSource는 ApplicationContext에 구현 되어 있으니 사용하는 곳에서 @Autowired로 가져올 수 있다.

```java
@Component
public class AppRunner implements ApplicationRunner {

    @Autowired
    MessageSource messageSource;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        messageSource.getMessage("greeting",new String[]{"dongju"}, Locale.KOREA)

    }
}
```
<br>
위의 코드를 보면 MessageSource를 가져왔다. ApplicationContext에 구현되어 있기 때문에 @Autowired로 가져올 수 ApplicationContext를 바로 가져와도 되지만 이렇게 MessageSource로 가져온 이유는 그 사용 용도를 명확히 하기 위해서 이다.<br><br>
그다음 messageSource.getMessage 메서드로 Message를 가져온다<br>
이때 getMessage의 반환은 String이다. 그리고 Message를 위해 미리 준비 해야할 것은 resource 디렉토리 밑에 각 언어에 맞는 메세지 번들을 준비해야 한다.

![image](https://user-images.githubusercontent.com/47367509/72204418-0e336780-34bb-11ea-9bc6-0a0239da258f.png)
<br>
Resource Bundle 'messages'라는 디렉토리는 따로 만든게 아니라 messages~~~.properties를 만들면 자동으로 Intellij가 인식하고 만들어 준다. <br>
<br>
각 언어에 맞게 한국어는 messages_ko_Kr, 일본어는 messages_ja, 영어는 messages_en이런식으로 properties파일을 만든다.<br>
그 다음 properties안에 Message를 넣는다. Message작성은 
Message이름 = Message내용 {숫자} 이런식으로 적는데 {숫자는} 이 메세지에 넣을 매개변수가 들어간다.
![image](https://user-images.githubusercontent.com/47367509/72204465-99146200-34bb-11ea-9ae2-ff30be9b1ba3.png)

<br>위에서 getMessage메서드의 첫번째 인자로 "greeting"을 주었기 때문에 properties에 greeting이라는 Message이름을 정해 주었다.

<br><Br>
이렇게 하면 결과는 안녕, dongju가 나온다.<br>