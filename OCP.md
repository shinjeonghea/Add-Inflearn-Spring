# OCP(Open/Closed principle)

- 개방-폐쇄 원칙

- SOLID 원칙 중 하나로 "소프트웨어 개체는 **확장에 대해 열려 있어야 하고, 수정에 대해서는 닫혀 있어야 한다**"는 프로그래밍 원칙이다.

  - 확장에 대해 열려 있다.
    - 모듈의 동작을 확장할 수 있다는 것을 의미
    - 애플리케이션에의 요구 사항이 변경될 때, 변경에 맞게 새로운 동작을 추가해 모듈을 확장할 수 있다.
  - 수정에 대해 닫혀 있다.
    - 모듈의 소스 코드나 바이너리 코드를 수정하지 않아도 모듈의 기능을 확장하거나 변경할 수 있다.

- #### 예시

  <img src="https://user-images.githubusercontent.com/58902042/107848709-f2765f80-6e38-11eb-8e76-08d81e6b8a9d.png" height=270>

  - 현재의 상황에서 도서관 대여 명부와 같은 새로운 매체를 생성해 학생 대여 기록을 출력하는 경우 어떻게 처리해야 할까?

    - OCP를 위반한 경우

      - 도서관 대여 명부 클래스를 만들어 SomeClient 클래스가 이 기능을 이용하도록한다.
      - 새로운 기능을 추가하기 위해 SomeClient클래스를 수정해야 한다.

    - OCP를 위반하지 않은 경우

      <img src="https://user-images.githubusercontent.com/58902042/107848763-69135d00-6e39-11eb-9c57-112ae23405b8.png" height=270> 

      - 추상화를 통해 인터페이스를 만든다.
      - 인터페이스에서 구체적인 출력 매체를 캡슐화해 처리한다.
      - 변경이 있더라도 기존의 클래스가 영향을 받지 않는다.

------

#### :page_with_curl: 강의 자료 

<img src="https://user-images.githubusercontent.com/58902042/107848851-2d2cc780-6e3a-11eb-85b5-80421fb2b512.png" height=300>

<img src="https://user-images.githubusercontent.com/58902042/107848876-6a915500-6e3a-11eb-88f4-1b2bff17c16d.png" height=280>

- 개방-폐쇄의 원칙을 이용해 Service가 의존하고 있는 Repository를 Service를 변경하지 않고 의존 설정만 변경하여 바꿀 수 있다.
- 여기서 스프링을 사용하는 큰 이유가 하나 나오는데, 객체지향적 설계가 좋기때문이다.
  - 즉, 객체지향적 설계가 좋으므로, 인터페이스를 두고 구현체를 바꿔끼기 가능하다는 것이다.

--------

####  SOLID (객체 지향 설계) :bulb:

| 두문자 | 약어 |                             개념                             |
| :----: | :--: | :----------------------------------------------------------: |
|   S    | SRP  | 단일 책임 원칙 (Single responsibility principle)<br>"한 클래스는 하나의 책임만 가져야 한다." |
|   O    | OCP  | 개방-폐쇄 원칙 (Open/closed principle)<br>"소프트웨어 요소는 확장에는 열려 있으나 변경에는 닫혀 있어야 한다." |
|   L    | LSP  | 리스코프 치환 원칙 (Liskov substitution principle)<br>"프로그램의 객체는 프로그램의 정확성을 깨뜨리지 않으면서 하위 타입의 인스턴스로 바꿀 수 있어야 한다." |
|   I    | ISP  | 인터페이스 분리 원칙 (Interface segregation principle)<br>"특정 클라이언트를 위한 인터페이스 여러 개가 범용 인터페이스 하나보다 낫다." |
|   D    | DIP  | 의존관계 역전 원칙 (Dependency inversion principle)<br>프로그래머는 "추상화에 의존해야지, 구체화에 의존하면 안된다." <br>의존성 주입은 이 원칙을 따르는 방법 중 하나다. |

----

**<참조>**

- [위키-SOLID (객체 지향 설계)](https://ko.wikipedia.org/wiki/SOLID_(%EA%B0%9D%EC%B2%B4_%EC%A7%80%ED%96%A5_%EC%84%A4%EA%B3%84))
- [위키-개방-폐쇄원칙](https://ko.wikipedia.org/wiki/%EA%B0%9C%EB%B0%A9-%ED%8F%90%EC%87%84_%EC%9B%90%EC%B9%99)

-  [[소프트웨어 설계] 개방-폐쇄 원칙(OCP : Open-Closed Principle)](https://velog.io/@y_dragonrise/%EC%86%8C%ED%94%84%ED%8A%B8%EC%9B%A8%EC%96%B4-%EC%84%A4%EA%B3%84-%EA%B0%9C%EB%B0%A9-%ED%8F%90%EC%87%84-%EC%9B%90%EC%B9%99OCP-Open-Closed-Principle)

- [인프런-스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술의 강의자료](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%EC%9E%85%EB%AC%B8-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8#)

