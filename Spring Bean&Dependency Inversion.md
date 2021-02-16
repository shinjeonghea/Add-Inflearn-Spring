# 스프링 빈과 의존관계

<br>

## 스프링 빈(Bean)

- Spring Ioc 컨테이너가 관리하는 자바 객체를 빈(Bean)이라고 한다.
- 애플리케이션이 실행될 때 스프링이 생성해서 유지하고 있어야 접근/사용이 가능하다.
- 스프링 빈( Bean)간의 참조가 있을 경우 의존관계를 설정해줘야 한다.
  - 스프링 빈 등록 방법은 2가지가 있다.

#### 스프링 빈(Bean) 등록 방법

**1. 컴포넌트 스캔 & 자동 의존관계 설정**

- 회원 컨트롤러 의존관계 추가

    ~~~java
   package hello.hellospring.controller;
   
   import hello.hellospring.service.MemberService;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.stereotype.Controller;
   
   @Controller
   public class MemberController {
       private final MemberService memberService;
       @Autowired
       public MemberController(MemberService memberService) {
           this.memberService = memberService;
       }
   }
   ~~~

 - 회원 서비스 스프링 빈 등록

    ~~~java
    @Service
    public class MemberService {
        private final MemberRepository memberRepository;
        @Autowired
        public MemberService(MemberRepository memberRepository) {
            this.memberRepository = memberRepository;
        }
    }
    ~~~

 - 회원 리포지토리 스프링 빈 등록
   
   ~~~java
   @Repository
   public class MemoryMemberRepository implements MemberRepository {}
   ~~~
   
    - 스프링이 실행될 때 `@Component` 애노테이션을 가진 모든 요소를 Scan해서 Bean으로 등록하는 과정을 Component Scan이라고 한다.
    - 위의 예시에서의 `@Controller` 또는 `@Service`, `@Repository` 모두 내부 적으로 `@Component`를 포함한다.
    - Bean 간에 참조하는 변수 위에 `@Autowired` 애노테이션으로 표시해서 의존성을 자동으로 주입하게 한다.
      - 스프링이 연관되 객체를 스프링 컨테이너에서 찾아서 넣어준다
      - 이렇게 객체 의존관계를 외부에서 넣어주는 것을 [DI(Dependency Injection)](./DI.md), 의존성 주입이라고 한다.
   
- 스프링 빈 등록 이미지

    <img src="https://user-images.githubusercontent.com/58902042/107868299-a2de7500-6ec6-11eb-800d-11d9417006f8.png" height=260> 

    - 위의 코드를 통해 memberService와 memberRepository가 스프링 컨테이너에 스프링 빈으로 등록되었다.
    - memberController는 memberService와 memberRepository를 사용할 수 있다.

**2. 자바 코드로 직접 스프링 빈 등록**

~~~java
package hello.hellospring;

import hello.hellospring.repository.MemberRepository;
import hello.hellospring.repository.MemoryMemberRepository;
import hello.hellospring.service.MemberService;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class SpringConfig {
    @Bean
    public MemberService memberService() {
        return new MemberService(memberRepository());
    }
    
    @Bean
    public MemberRepository memberRepository() {
        return new MemoryMemberRepository();
    }
}
~~~

- 위의 의존관계에서 넣었던 `@Controller` , `@Service`, `@Repository` 애노테이션을 제거하고 진행한다.
- `@Configuration`을 이용하는 클래스를 생성한다.
  - `@Configuration`역시 내부적으로 `@Component`를 포함한다.
- `@Bean` 애노테이션을 사용하여 빈을 직접 선언한다. 후에 해당 객체가 호출되면 스프링 컨테이너에서 찾아서 자동으로 이어준다.

- 이후, 상황에 따라 구현 클래스를 변경해야하는 경우

  ~~~java
  @Configuration
  public class SpringConfig {
   private final DataSource dataSource;
   public SpringConfig(DataSource dataSource) {
   this.dataSource = dataSource;
   }
   @Bean
   public MemberService memberService() {
   return new MemberService(memberRepository());
   }
   @Bean
   public MemberRepository memberRepository() {
  // return new MemoryMemberRepository();
  return new JdbcMemberRepository(dataSource);
   }
  }
  ~~~

  <img src="https://user-images.githubusercontent.com/58902042/108045333-b1738a80-7086-11eb-994e-77b2515d01b0.png" height=300> 

  <img src="https://user-images.githubusercontent.com/58902042/108046423-efbd7980-7087-11eb-8576-88dc27e53171.png" height=300> 

  - [개방-폐쇄의 원칙](./OCP.md)
  - 스프링의 [DI](./DI.md)를 사용하여 기존 코드를 손대지 않고, 설정만으로 구현 클래스를 변경할 수 있다.

#### 결론

- 주로 정형화된 컨트롤러, 서비스, 리포지토리 같은 코드는 컨포넌트 스캔방식을 사용한다.
- 정형화 되지 않거나, 상황에 따라 구현 클래스를 변경해야 하는 경우 설정을 통해 직접 스프링 빈을 등록한다.

****

**<참조>**

- [Springboot 입문(4) - 스프링 빈과 의존관계](https://velog.io/@neity16/Springboot-%EC%9E%85%EB%AC%B84-%EC%8A%A4%ED%94%84%EB%A7%81-%EB%B9%88%EA%B3%BC-%EC%9D%98%EC%A1%B4%EA%B4%80%EA%B3%84)
- [스프링 빈과 의존관계](https://velog.io/@dnstlr2933/%EC%8A%A4%ED%94%84%EB%A7%81-%EB%B9%88%EA%B3%BC-%EC%9D%98%EC%A1%B4%EA%B4%80%EA%B3%84)
- [Spring 입문 - 스프링 빈과 의존관계](https://backtony.github.io/spring/2021/01/26/spring-start-4/)

