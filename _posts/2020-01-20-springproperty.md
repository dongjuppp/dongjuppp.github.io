---
layout: post
title: Data Binding
comments: true
category: Spring
---

# 바인딩
스프링에서는 사용자의 데이터를 바인딩해주는 바인딩 기술이 있다. 이것을 사용하는 경우는 아주 빈번한데 예를들어 사용자의 입력이 대부분 문자열이나 숫자로 이루어 진다. 그런데 이 입력을 받는 것이 내가 만든 POJO라면 이것을 변환해 주어야 한다.
이러한 데이터를 변환하는 과정을 스프링에서 직접 설정해서 바꿀 수 있다.

<br>
우선 변경하는 방법으로 Converter와 Formatter가 있다.

## Converter
먼저 converter를 사용하는 방법이다.

```java
@Component
    public class StringToEventConverter implements Converter<String,Event> {

        public Event convert(String source){
            Event event = new Event();
            event.setId(Integer.parseInt(source));
            return event;
        }

    }
```
여기서 Event가 POJO이다. 그리고 이 코드는 사용자의 입력이 문자 String인데 이것을 event의 id에 넣으려고 하는것이다.
지금 보면 클래스가 Converter인터페이스를 상속받고 있는데 여기세 제너릭으로 String,Event가 주어진다. Converter의 원형의 제너릭은 Converter<S,T>인데 S를 T로 바꾸는 것을 의미한다. 즉, converter를 사용하려면 클래스에서 S의 위치에 바꿀것을 넣고 T의 위치에 반환값을 넣으면 된다.

## Formatter
다음으로는 Formatter인데 converter와 사용하는데에는 큰 차이점이 없다.

```java
public class EventFormatter implements Formatter<Event>{
    
    public Event parse(String text,Locale locale) throws ParseExcepotion{
        int id = Integer.parseInt(text);
        event.setId(id);
        return event;
    }

    public String print(Event obj,Locale locale)P
    return obj.getId().toString();
}
```
이번에는 Formatter를 인터페이스를 상속받았는데 converter와는 다르게 제너릭의  인자가 한개다. 코드를 보면 그냥 이해가 가지만 설명하면 parse는 String을 받아서 Event로 바꾸고 pring는 Event를 String으로 바꾼다. 그런데 여기서 converter와는 차이점인 제너릭인자의 차이가 나온다. converter에서는 분면 바꿀대상과, 그결과를 제너릭으로 정했는데 이번에는 제너릭에는 한개의 타입을 주고 바꿀대상으로 자동으로 String이 정해졌다. 이게 converter와 formatter의 차이다.
<br>
formatter는 String과 Object간의 변환만 가능하다. 그리고 converter는 Object와 Object간의 변환이 가능하다. 
<br>
<br>
그렇다면 이 바인딩을 사용하는 방법은 어떻게 하는 것인지 보겠다.

우선 사용자의 입력을 받는 controller를 보자

```java
@RestController
public class EventController {

    @InitBinder
    public void init(WebDataBinder webDataBinder){
        webDataBinder.registerCustomEditor(Event.class,new EventFormatter());
    }

    @GetMapping("/event/{event}")
    public String getEvent(@PathVariable Event event){
        System.out.println(event);
        return event.getTitle();
    }
}
```
이 컨트롤러에서는 url에 사용자의 입력이 주어진다. 그런데 이 입력이 숫자로 오면 이것을 Event로 바꿔야 하는데 이때 바인딩 기술이 들어간다 위의 @InitBinder가 붙은 메서드에서 WebDataBinder에 Event를 결과로 등록하고 변환 기술을 적용한 클래스를 EventFormatter를 등록했다. 이렇게 하면 위에서 만든 변환 클래스가 자동으로 바꿔준다.
