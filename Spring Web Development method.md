# 스프링 웹 개발 방식

- 스프링 웹 개발 방식에는 정적 컨텐츠, 템플릿 엔진을 사용한 방식, API를 사용한 방식으로 구분할 수 있다.
- 스프링 웹 개발 방식에서 html 자체를 내려주는 정적 컨텐츠를 제외한, 동적 컨텐츠의 처리에서는 두 가지만 기억하면 된다.
  - HTML파일로 반환하냐, API 같은 방식을 사용하여 데이터를 바로 반환하냐.

## 정적 컨텐츠

<img src="https://user-images.githubusercontent.com/58902042/108034955-9f3f1f80-7079-11eb-8c03-b68385f808d1.png" height=300> 

- 컨테이너에서 해당 요청을 받은 컨트롤러를 확인한 후에 없을 경우, static으로 넘어가서 정적 컨텐츠를 찾는다.
- Spring Boot에서 정적 컨텐츠는 `resources/static/`아래에 위치한다.
- Spring Boot에서 제공하는 [Welcome Page](./Welcome%20Page.md) 또한 정적 컨텐츠이다.
- 정적 컨텐츠는 html 그대로 반환한다.
  - 즉, 안에 어떠한 프로그래밍을 할 수 없다는 뜻이다.

<br>

## MVC, 템플릿 엔진

- [MVC 패턴](https://github.com/fake-developers/1st/blob/main/SJH/MVC%20Pattern.md) :  애플리케이션을 Model, View, Controller 세 가지의 역할로 구분한 개발 방법론
- Controller
~~~java
@Controller
public class HelloController {
    @GetMapping("hello-mvc")
    public String helloMvc(@RequestParam("name") String name, Model model) {
        model.addAttribute("name", name);
        return "hello-template";
    }
}
~~~

- View

~~~html
<html xmlns:th="http://www.thymeleaf.org">
<body>
<p th:text="'hello ' + ${name}">hello! empty</p>
</body>
</html>
~~~

<img src="https://user-images.githubusercontent.com/58902042/108034986-a9f9b480-7079-11eb-8b91-a74f0a296562.png" height=300> 

- `https://localhost:8080/hello-mvc?name=spring`으로 수신을 요청할 경우, 요청 주소에 대해 HelloController에서 검색하고 model을 매핑한다. 

  - Controller에서는 `@Getmapping("hello-mvc")`를 통해서 주소에 대해 매핑하고,
  - `@RequestParam("name") String name`으로 querystring(즉, url에서 ?뒤에 있는 값)을 받아서 템플릿 엔진으로 값을 넘긴다.

- mdoel을 통해 viewResover로 전달하여 템플릿 엔진이 모델을 하고 html로 변환하여 웹 브라우저에 전송한다.

   :point_right:*템플릿 엔진 tymeleaf에 대해서 더 알고 싶다면 [여기](./Thymeleaf%20Template%20Engine.md)를 클릭.*

<br>

## API

- @ResponseBody를 이용한 **문자 반환**

~~~java
@Controller
public class HelloController {
    @GetMapping("hello-string")
    @ResponseBody 
    public String helloString(@RequestParam("name") String name){
        return "hello" + name; 
    }
}
~~~

- @ResponseBody를 이용한 **객체 반환**

~~~java
@GetMapping("hello-api")
    @ResponseBody
    public Hello helloApi(@RequestParam("name") String name){
        Hello hello = new Hello();
        hello.setName(name);
        return hello;
    }

    static class Hello{
        private String name;

        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }
    }
}
~~~

<img src="https://user-images.githubusercontent.com/58902042/108035010-b251ef80-7079-11eb-8090-cf1736be6cae.png" height=300> 

- `@ResponseBody` 애노테이션을 사용하면 http 응답 body부분에 데이터를 직접 넣어주겠다는 뜻이다.
  - 즉,  view가 없다 그냥 그대로 내려간다. 
  - template을 거치지 않고 그대로 처리한다.
    - viewReslover를 사용하지 않는다는 뜻이다.
- `@ResponseBody`의 사용
  - viewResolver 대신 HttpMessageConverter가 동작하며,
  - 기본 문자 처리에는 StringHttpMessageConverter
    - 문자는 기본적으로 String으로 처리
  - 기본 객체 처리에는 MappingJackson2HttpMessageConverter로 처리한다.
    - 객체는 기본적으로 JSON으로 처리
    - 위 Converter에서 객체를 JSON으로 변경해주는 라이브러리 Jackson

<br>

---

**<참조>**

- [SpringBoot 입문(2) - 빌드 / 템플릿엔진 / API](https://velog.io/@neity16/Spring-%EC%9E%85%EB%AC%B82-%EB%B9%8C%EB%93%9C-%ED%85%9C%ED%94%8C%EB%A6%BF%EC%97%94%EC%A7%84-API)

- [Spring 기본 정리(Spring Web MVC 구조)](https://velog.io/@hono2030/Spring-%EA%B8%B0%EB%B3%B8-%EC%A0%95%EB%A6%AC)

- [인프런-스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술의 강의자료](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%EC%9E%85%EB%AC%B8-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8#)