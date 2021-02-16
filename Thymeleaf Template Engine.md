# Thymleaf 템플릿 엔진

<br>

## 템플릿 엔진이란?

- 템템플릿 양식과 특정 데이터 모델에 따른 입력 자료를 합성하여 결과 문서를 출력하는 소프트웨어(또는 소프트웨어 컴포넌트)를 말한다.

- 그 중 웹 웹 템플릿 엔진(web template engine)이란 웹 문서가 출력되는 템플릿 엔진을 말한다.

  - 즉, 웹 템플릿 엔진은 웹 템플릿들(web templates)과 웹 컨텐츠 정보(content information)를 처리하기 위해 설계된 소프트웨어이다.
  - 웹 템플릿 엔진은 **view code(html)와 data logic code(DB connection)를 분리해주는 기능**을 한다.

- #### 템플릿 엔진의 종류

  1. **서버 사이드 템플릿 엔진**
     - 서버에서 DB 혹은 API에서 가져온 데이터를 미리 정의된 Template에 넣어 html을 그려서 클라이언트에 전달해주는 역할을 한다.
     - ex_) javascript_template_engine(EJS(Embedded JavaScript Templates), Jade(Pug), Handlebars(Handlebars.js) 등)
     - ex_) java_template_engine(Freemarker, **Thymeleaf**, Groovy, Velocity, jade4j, Handlebars(Handlebars.java), Mustache, JSP 등)

  2. **클라이언트 사이드 템플릿 엔진**
     - html 형태로 코드를 작성할 수 있으며, 동적으로 [DOM](https://github.com/fake-developers/1st/blob/main/SJH/BOM%26DOM.md)을 그리게 해주는 역할을 한다.
     - ex_)Mustache, Squirrelly, Handlebars(Handlebars.js)

  <br>

  :point_right: *이 글은 thymeleaf에 초점을 두었으므로, 템플릿 엔진의 종류에 대한 것은 [여기(아직 하지 않음)]()에서 추가적으로 설명하겠다.*

  <br>

------

스프링 부트가 자동 설정을 지원하는 템플릿 엔진은 4가지가 있다.

1. FreeMarker
2. Groovy
3. Thymeleaf
4. Mustache

지금은 Thymeleaf에 대해서 설명한다.

---------

<br>

## Thymeleaf

- 서버 사이드 템플릿 엔진 중 하나
- 서버에서 DB 혹은 API에서 가져온 데이터를 미리 정의된 Template에 넣어 html을 그려서 클라이언트에 전달해주는 역할을 한다.
  - 즉, HTML 코드에서 고정적으로 사용되는 부분은 템플릿으로 만들어두고 동적으로 생성되는 부분만 템플릿 특정 장소에 끼워넣는 방식으로 동작하게 한다.

- Thymeleaf는 비교적 최근에 만들어진 템플릿 엔진이다.

<img src="https://user-images.githubusercontent.com/58902042/107883391-15386f00-6f32-11eb-9fa4-4ba4e3b7bc81.png">

- intellij에서 Gradle을 통해 Thymeleaf를 사용하기 위해서는 build.gradle 설정파일에서 위와같은 의존성을 추가해주어야 한다.

- 기본적으로 자동 설정이 적용되고 나서, 모든 동적으로 생성되는 view들은 `src/main/resources/templates` 에서 찾게된다.

- ex_)

  - HelloController

  ~~~java
  @Controller
  public class HelloController {
      @GetMapping("hello")
      public String hello(Model model) {
          model.addAttribute("data", "hello!!");
          return "hello";
      }
  }
  ~~~

  - resources/templates/hello.html

  ~~~java
  <!DOCTYPE HTML>
  <html xmlns:th="http://www.thymeleaf.org">
  <head>
   <title>Hello</title>
   <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
  </head>
  <body>
  <p th:text="'안녕하세요. ' + ${data}" >안녕하세요. 손님</p>
  </body>
  </html>
  ~~~

  - 동작 환경 그림

  <img src="https://user-images.githubusercontent.com/58902042/107883754-1d91a980-6f34-11eb-9431-7a78df70022d.png" height=280> 

  - 컨트롤러에서 리턴 값으로 문자를 반환하면 뷰 리졸러(viewResolver)가 화면을 찾아서 처리한다.
    - 스프링 부트 템플릿 엔진 viewNmae 매핑은 `resources:templates/ +{ViewName}+ .html` 로 처리된다.

----

**<참조>**

- [[Template Engine] 템플릿 엔진(Template Engine)이란](https://gmlwjd9405.github.io/2018/12/21/template-engine.html)

- [Template Engine](https://qjadud22.tistory.com/68)
- [[Template Engine] 타임리프(Thymeleaf)](https://happiestmemories.tistory.com/42)
- [[Spring Boot] 템플릿 엔진 - Thymeleaf](https://velog.io/@max9106/Spring-Boot-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%97%94%EC%A7%84-Thymeleaf)

- [인프런-스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술의 강의자료](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%EC%9E%85%EB%AC%B8-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8#)

\+ 앞으로 추가적으로 공부할 것 jsp vs thymeleaf, 템플릿 엔진의 종류