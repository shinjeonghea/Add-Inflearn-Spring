# DI(Dependency Injection)

<br>

## DI란?

- Dependency Injection의 약자로 의존성 주입이라는 뜻이다.

- 개체간의 의존성을 스프링 컨테이너가 자동으로 연결해주는 것으로 **개발자가 빈(Bean) 설정파일에 의존관계가 필요한 정보를 추가하면 스프링 컨테이너가 자동으로 연결**해준다.

- 즉, 개발자가 객체를 직접 생성하는 방식이 아니라 **외부에서 생성해서 주입 시켜주는 방식**이다.

- IoC원칙 하에 객체 간의 결합을 약하게 해주고 유지보수가 좋은 코드를 만들어 준다.

  - IoC (제어의 역전) : 객체 관리의 주체가 개발자가 아닌 컨테이너

    <br>

  🚨*Spring IoC 컨테이너에 의한 주입은 Bean끼리만 가능하다.*

<BR>

## DI의 장점

- Dependency Reduction : 객체 상호 간의 의존성 관계를 줄여준다.
- Reusable Structure : 코드의 재사용과 조합이 용이하다.
- Readability : 코드의 분리로 가독성이 좋아진다.
- Loose Coupling & Easy to change : 구조의 변화에 민감하지 않다. 
- 테스트가 용이하고 다양한 패턴을 적용하는데 유연하다.

<br>

## DI 방식

- `@Autowired` 애노테이션을 이용하여 의존성 주입 방법을 제공하다.
- 스프링 에서의 DI 방식은 3가지 종류가 있다.
  - **필드 주입(filed Injection)**
  - **세터 주입(setter Injection)**
  - **생성자 주입(Constructor Injection)**

- Spring 3.x 버전까지만 해도 Setter Injection을 권장하였으나, 최근은 순환 참조, 객체간의 결합 등의 문제로 Spring 4.3 이후는 Constructor Injection을 권장한다.

#### 1. 필드 주입

~~~java
@Component
public class SampleController {
    @Autowired
    private SampleRepository sampleRepository;
}
~~~

- 변수의 선언부에 `@Autowired` 애노테이션을 붙인다.

#### 2. 세터 주입

~~~java
@Component
public class SampleController {
    private SampleRepository sampleRepository;
 
    @Autowired
    public void setSampleRepository(SampleRepository sampleRepository) {
        this.sampleRepository = sampleRepository;
    }
}
~~~

- setter 메소드에 `@Autowired` 애노테이션을 붙인다.

#### 3. 생성자 주입

~~~java
@Component
public class SampleController {
    private SampleRepository sampleRepository;
 
    @Autowired
    public SampleController(SampleRepository sampleRepository) {
        this.sampleRepository = sampleRepository;
    }
}
~~~

- 생성자에 `@Autowired` 애노테이션을 붙인다.
- Spring 4.3부터는 클래스의 생성자가 하나이고, 그 생성자로 주입받을 객체가 빈으로 등록되어있다면 생성자 주입에서 `@Autowired` 생략이 가능하다.
- 생성자 주입이 권장되는 이유
  - **필수적으로 사용해야 하는 레퍼런스(객체, 의존성) 없이는 인스턴스를 만들지 못하도록 강제하는 역할**을 할 수 있기 때문이다.

<br>

---

**<참조>**

- [[Spring] 의존성 주입(DI, Dependency Injection)의 세가지 방법](https://atoz-develop.tistory.com/entry/Spring-%EC%9D%98%EC%A1%B4%EC%84%B1-%EC%A3%BC%EC%9E%85DI-Dependency-Injection%EC%9D%98-%EC%84%B8%EA%B0%80%EC%A7%80-%EB%B0%A9%EB%B2%95)

- [[Java]Spring- 의존성주입(Dependency Injection,DI) 개념](https://blog.naver.com/PostView.nhn?blogId=dktmrorl&logNo=222116844258&categoryNo=0&parentCategoryNo=0&viewDate=&currentPage=1&postListTopCurrentPage=1&from=postView)

- [[Spring] Spring DI(의존성 주입, Dependency Injection)](https://chanhuiseok.github.io/posts/spring-5/)