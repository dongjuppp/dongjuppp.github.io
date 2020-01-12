---
layout: post
title: ResourceLoader
comments: true
category: Spring
---

# ResourceLoader

스프링에서는 외부 파일을 읽어오기 위한 ResourceLoadder가 ApplicationContext에 구현되어 있다. 여기서 외부 파일이란 txt, json들을 의미한다. 이걸 왜 따로 구현 했는가 하면 스프링은 실행 시키면 설정에 따라 jar, war파일로 변환이 된다. 이때 java파일에서 리소스를 가져올때 경로를 classpath기준으로 가져오는 기능이 없었기 때문이다. <br>
일단 내가 알고있는 File을 가져오는 방법이 File경로 기준, ClassPath기준이 있는데 File 기준은 내 로컬 컴퓨터에서 절대 위치를 의미하고 ClassPath기준은 jar,war파일 안에서의 위치를 의미한다.<br>

그럼 한번 2가지의 경로를 가지고 파일을 가져오는 것을 코드로 구현해보겠다.<br>

구현 전에 test로 만든 friend.txt파일을 C드라이브와 스프링 resource 2군데에 위치 시켰다.

![image](https://user-images.githubusercontent.com/47367509/72219062-3b4c4c80-3585-11ea-8efa-e692707b8326.png)


![image](https://user-images.githubusercontent.com/47367509/72219053-296aa980-3585-11ea-9648-06253b18669f.png)

```java
@Component
public class AppRunner implements ApplicationRunner {

    @Autowired
    ResourceLoader resourceLoader;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        Resource resource = resourceLoader.getResource("classpath:friend.txt");
        Resource fresource = resourceLoader.getResource("file://C://friend.txt");
        
        //두 경로로 가져와서 존재 하는지 검사
        System.out.println(resource.exists());
        System.out.println(fresource.exists());

        // 파일을 가져온다
        File file = resource.getFile();
        File file2 = fresource.getFile();

        // 두개의 경로를 찍어 본다
        System.out.println(file.getPath());
        System.out.println(file2.getPath());

        //파일을 읽는다
        try{
            FileReader filereader = new FileReader(file);
            FileReader fileReader2 = new FileReader(file2);
            int singleCh = 0;
            System.out.println("classpath 기준");
            while((singleCh = filereader.read()) != -1){
                System.out.print((char)singleCh);
            }
            System.out.println("\n\nfile 기준");
            while((singleCh = fileReader2.read()) != -1){
                System.out.print((char)singleCh);
            }
            filereader.close();
        }catch (FileNotFoundException e) {
            // TODO: handle exception
        }catch(IOException e){
            System.out.println(e);
        }
    }
}
```

우선 ResourceLoader를 @Autowired로 가져왔다. <br>

이때 중요한게 있는데 이 ResourceLoader는 ApplicationContext의 타입에 따라 종류가 달라진다
<br>

* UrlResource
* ClassPathResource
* FileSystemResource
* ServletContextResource

이렇게 4종류가 있는데 각자 파일이름을 명시해도 가져오는 경로의 기준이 다르다. 그래서 이걸 강제하기 위해서 위의 코드 처럼 "classpath:', "file:" 이런식으로 perfix를 써서 명시하는게 좋다<br>
또한 이렇게 명시하면 나중에 ResourceLoader만 보고도 어디서 가져오는지 명확하게 알 수 있는 장점이 있다.
<br>

그다음으로
```java
System.out.println(file.getPath());
System.out.println(file2.getPath());
```
이부분의 결과는 아래와 같다
![image](https://user-images.githubusercontent.com/47367509/72219663-f4158a00-358b-11ea-86f5-c5be168951ba.png)

첫번째는 classpath로 가져온것을 java File의 getPath로 가져온 것이기 때문에 길게 나왔자만 파일의 위치는 resource폴더다. 그리고 file은 c드라이브에 있다.

<br><br>
마지막으로 다시 정리하면 ResourceLoader는 spring에서 다른 resource를 가져오기 위해 만들어진 interface이고 가져오는 resource의 경로를 명시 해줘야 정확하고 나중에 알아 보기도 쉽다.