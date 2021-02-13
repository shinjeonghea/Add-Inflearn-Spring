# Welcome Page

- 사용자가 주소창에 도메인을 입력하고 들어오면 가장 먼저 보게되는 페이지

- 스프링 부트는 resources/static/index.html 을 올려두면 Welcome page 기능을 제공한다

  ```html
  <!-- resources > static > index.html -->
  <!DOCTYPE HTML>
  <html>
  <head>
      <title>스프링 입문</title>
      <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" /> </head>
  <body>
      Hello! This is my Welcome Page!
  </body> 
  ```
  - 실행결과
  <img src="https://user-images.githubusercontent.com/58902042/107846887-a7ede680-6e2a-11eb-9aea-069b4507c17b.png" height= 100> 

<br>

*기본적으로 도메인(localhost:8080)에 대한 정의가 없을 경우에는 위와 같이 static의 index.html로 처리 되지만 Controller를 통해  정의할 경우 Controller에 있는 html로 페이지를 찾는다.*

<br>

## Controller를 통한 Welcome Page
<img src="https://user-images.githubusercontent.com/58902042/107847447-01581480-6e2f-11eb-8e78-837c5a027f8a.png" height=300>

- **컨트롤러는 정적 파일보다 우선순위가 높다.**

- 스프링은 스프링 컨테이너에서 먼저 컨트롤러에 매핑이 되어 있는지 확인하고 없는 경우, static으로 넘어가서 index.html의 welcome page를 찾는다.

- ex_)
   <img src="https://user-images.githubusercontent.com/58902042/107847405-93135200-6e2e-11eb-9539-417da6a84afe.png" height=200> 

  - 컨트롤러를 통해 GetMapping애노테이션을 설정하여 @GetMapping("/")을 할 경우, localhost:8080의 url이 적용된다.
  - retrun "home"을 통해서 그에 맞는 home.html을 찾아간다.

  <img src="https://user-images.githubusercontent.com/58902042/107847542-d7ebb880-6e2f-11eb-9dd0-5b9235806bc4.png" height=200> 

  - 위의 그림을 통해 resources/templates/home.html을 찾을 수 있다.
    -  resources/static/index.html가 있음에도 home.html이 먼저 실행된다.

  <img src="https://user-images.githubusercontent.com/58902042/107847552-e9cd5b80-6e2f-11eb-9716-aed8ae99ba10.png" height=200>         <img src="https://user-images.githubusercontent.com/58902042/107847639-99a2c900-6e30-11eb-9db2-3560e962dee7.png" height= 180>

  - localhost:8080로 들어갔을 때, home.html이 나오는 것을 볼 수 있다.



---

**<참조>**

- [인프런-스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술의 강의자료](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%EC%9E%85%EB%AC%B8-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8#)



