---
layout: post
title: Autowired
comments: true
category: Spring
---

## Autowired

Autowired는 필요한 의존 객체의 타입에 해당하는 Spring Bean을 찾아서 주입해준다<br>

예를 들어서 BookService는 BookRepository를 필요로한다.
```java
public class BookService{

    BookRepository bookRepository;
}
```

여기에 BookRepository를 자동으로 등록을 해주는게 Autowired이다 <br>

```java
public class BookService{
    @Autowired
    BookRepository bookRepository;
}
```

Autowired를 사용하는 방법에는 3가지 방법이 있다.
* 생성자
* Setter
* 필드

### 생성자
생성자는 필요한 의존 객체를 생성자의 매개변수로 등록해서 받는 방법이다.
```java
public class BookService{
    private BookRepository bookRepository;

    @Autowired
    public BookService(BookRepository bookRepository){
        this.bookRepository = bookRepository;
    }
}
```
이 방법은 Spring 4.3 부터는 Autowired를 생략해도 자동으로 등록을 해준다. 그리고 Spring에서 이 방법을 추천한다.<br>

### Setter
```java
public class BookService{
    private BookRepository bookRepository;

    @Autowired
    public setBookService(BookRepository bookRepository){
        this.bookRepository = bookRepository;
    }
}
```
이 방법은 생성자와 유사해 보이지만 생성자가 아닌 Setter 메서드를 이용한다<br>

### 필드
```java
public class BookService{

    @Autowired
    private BookRepository bookRepository;
}
```
이 방법은 필드에 바로 Autowired를 적용하는 방식이다.<br>
<br>
<br>
<br>

여기서 중요한것이있다. 바로 생성자와 다른 주입 방식의 자바로서의 차이점이다.<br>
생성자 같은 경우 객체를 생성하기 위해서 반드시 호출해야 한다.
하지만 다른 방식은 자바 객체를 생성하기 위해 반드시 호출해야 하는것이 아니다.<br>
만약 주입해야 하는 객체가 Spring Bean으로 등록이  안되어있다면 당연히 Spring에서 에러를 일으킨다. 이럴경우 대처방법에서 우리는 3가지  주입 방식의 차이점을 좀 더 확실히 알 수 있다.<br>
<br>
일단 처리 방법은 @Autowired(required = falsse)를 이용하면 된다. 이방법은 해당 객체를 만약 찾지 못했다면 주입 안해도 에러를 일으키지 않는 방법이다.
예를 들어보면
```java
public class BookService{
    private BookRepository bookRepository;

    @Autowired(required = false)
    public setBookService(BookRepository bookRepository){
        this.bookRepository = bookRepository;
    }
}
```

이렇게 하면 된다.
<br>
<br>
하지만 이 방식은 Setter와 필드에만 허용이 된다. 이유는 간단하다. 생성자 방식에서는 일단 객체를 생성할때 반드시 매개변수를 주어야 하기 때문이다. 즉, 찾지못해서 매개변수를 주지 못하면 객체 생성 자체를 할 수 없다.
<br>
<br>
<br>

그 다음으로 알아볼 내용은 주입할 객체가 여러개의 같은  타입일때이다.

```java
public interface BookRepository{

}
```

```java
@Repository
public class MyBookRepository implements BookRepository{

}
```

```java
@Repository
public class DongjuBookRepository implements BookRepository{

}
```

이렇게 하나의 인터페이스를 구현한 객체 2개가 있고 BookRepository를 의존 주입 받는 BookService가 있으면

```java
@Service
public class BookService{
    @Autowired
    BookRepository bookRepository;
}
```
Spring은 어느것을 주입 받을지 모른다.<br>
이런 경우 해결책은 4가지가 있다
* @Primary
* @Qualifier
* 모두 주입 받기
* 이름으로 구분

### @Primary
이 방법은 가장 중요한 구현체에 @Primary Annotation을 붙이는 방법이다.

```java
@Repository
@Primary
public class MyBookRepository implements BookRepository{

}
```
이렇게 하면 MyBookRepository가 주입된다.<br>

### @Qualifier
이 방식은 주입 받은 Spring Bean의 이름을 적는다
```java
@Service
public class BookService{
    @Autowired
    @Qualifier("myBookRepository")
    BookRepository bookRepository;

}
```
이 방식은 주입 받은 Spring Bean의 이름의 첫 번째 문자를 소문자로 해서 주입을 받는 방법이다.<br>

### 모두 주입 받기
이 방법은 인터페이스를 구현한 모든 Spring Bean을 List로 주입 받는 방법이다.
```java
@Service
public class BookService{
    @Autowired
    List<BookRepository> bookServices;
}
```
이렇게 하면 리스트에 모두 들어간다.<br>

### 이름으로 구분
이방법은 Qualifier와 비슷하지만 가급적 사용하지 말자.
```java
@Service
public class BookService{
    @Autowired
    BookRepository myBookRepository; //이름을 구분
}
```
이렇게 필드의 이름을 주입받을 Spring Bean의 이름으로 정해주면 알아서 주입해준다.<br>
