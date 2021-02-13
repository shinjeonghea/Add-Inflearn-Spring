# AOP(Aspect Oriented Programming)

<br>

## AOP란?

- Aspect Oriented Programming의 약자

- 관점 지향 프로그래밍

  - 어떤 로직을 기준으로 핵심적인 관점, 부가적인 관점으로 나누어서 보고 그 관점을 기준으로 각각 모듈화하겠다는 것

<br>

***공통의 관심사를 Aspect로 모듈화하고 핵심적인 비즈니스로직에서 분리하여 재사용하겠다는 것이 AOP의 목적***

<br>

## AOP의 용어 개념

- **Aspect** 
  - aspect = advice + pointcut
  - 여러 객체에 공통적으로 적용되는 관심 사항
- **Target **
  -  Aspect를 적용하는 곳 (클래스, 메서드 .. )
  -  핵심기능이 담긴 클래스이거나 추가적인 부가기능을 제공할 프록시 오브젝트일 수 있다.
- **Advice** 
  - 실질적으로 어떤 일을 해야할 지에 대한 것, 실질적인 부가기능을 담은 구현체
  - 타겟에게 제공할 부가기능
  - Advice를 정의 하는 태그
    - Around : Joinpoint 앞과 뒤에서 실행되는 Advice
    - Before : Jointpoint 앞에서 실행되는 Advice
    - After Returning : Jointpoint 메서드 호출이 정상적으로 종료된 뒤에 실행되는 Advice
    - After Thorwing : 예외가 던져 질때 실행되는 Advice
- **Advisor** 
  - advisor = advice + pointcut
  - 아주 단순한 aspect라고 볼 수 있다.
  - advisor는 Spring AOP에서만 사용되는 특별한 용어이다.
- **JointPoint** 

  - Advice가 적용될 위치, 끼어들 수 있는 지점. 메서드 진입 지점, 생성자 호출 시점, 필드에서 값을 꺼내올 때 등과 같은 특수한 지점

  - JoinPoint 메서드

    - getArgs()  : 메서드 arguments를 반환한다.                  
    - getThis()  : proxy 객체를 반환한다.     
    - getTarget() : 대상 객체를 반환한다.                          
    - getSignature() : advisor 또는 메서드의 설명(description)을 반환한다. 
    - toString() : advisor 되는 메서드의 설명을 출력한다.              
- **PointCut** 
  - JointPoint의 상세한 스펙을 정의한 것
  - 'A란 메서드의 진입 시점에 호출할 것'과 같이 더욱 구체적으로 Advice가 실행될 지점을 정할 수 있다.
  - 즉, Joinpoint가 Pointcut에 일치할 때마다 해당 Pointcut에 관련된 Advice가 실행된다.
- **Weaving**

  - weaving은 pointcut에 의해서 결정된 target의 joinpoint에 부가기능(advice)를 삽입하는 과정
    
  - weaving은 AOP가 핵심기능(target)의 코드에 영향을 주지 않으면서 필요한 부가기능(advice)를 추가할 수 있도록 해주는 핵심적인 처리과정이다.

<br>

## AOP 특징

**1. Spring은 proxy 기반 AOP를 지원한다.**

- Spring은 target 객체에 대한 proxy를 만들어 제공한다.
- 타겟을 감싸는 proxy는 실행시간(Runtime)에 생성된다.
- proxy는 advice를 tartget 객체에 적용하면서 생성되는 객체이다.

**2. Proxy가 호출을 가로챈다(Intercept)**

- proxy는 target 객체에 대한 호출을 가로챈 다음 advice의 부가기능 로직을 수행하고 난 후에 target의 핵심기능 로직을 호출한다.(전처리 어드바이스)
- 또는 target의 핵심기능 로직 메서드를 호출한 후에 advice을 수행하는 경우도 있다.(후처리 어드바이스)

**3. Spring AOP는 메서드 조인 포인트만 지원한다.**

- Spring은 동적 proxy를 기반으로 AOP를 구현하므로 메서드 joinpoint만 지원한다.
- target의 메서드가 호출되는 런타임 시점에만 advice을 적용할 수 있다.

<br>

## AOP 동작

#### 1. AOP가 필요한 상황

- 모든 메서드의 호출 시간을 측정하고 싶다면?

   <img src="https://user-images.githubusercontent.com/58902042/107842679-6e0be880-6e08-11eb-9d7b-1281ceaf07b3.png" height=300> 

   ~~~java
   package hello.hellospring.service;
   
   @Transactional
   public class MemberService {
        /**
        * 회원가입
        */
        public Long join(Member member) {
            
            long start = System.currentTimeMillis();
            
            try {
                validateDuplicateMember(member); //중복 회원 검증
                memberRepository.save(member);
                return member.getId();
            } finally {
                long finish = System.currentTimeMillis();
                long timeMs = finish - start;
                System.out.println("join " + timeMs + "ms");
           	 }
            }
       
            /**
            * 전체 회원 조회
            */
        public List<Member> findMembers() {
            
            long start = System.currentTimeMillis();  
            
            try {
                return memberRepository.findAll();
            } finally {
                long finish = System.currentTimeMillis();
                long timeMs = finish - start;
                System.out.println("findMembers " + timeMs + "ms");
            }
        }
   }
   ~~~
   
   - 회원가입, 회원 조회에 시간을 측정하는 기능은 핵심 관심 사항이 아니다.
   -  시간을 측정하는 로직은 공통 관심 사항이다. 
   - 시간을 측정하는 로직과 핵심 비즈니스의 로직이 섞여서 유지보수가 어렵다.
   -  시간을 측정하는 로직을 별도의 공통 로직으로 만들기 매우 어렵다.
   -  시간을 측정하는 로직을 변경할 때 모든 로직을 찾아가면서 변경해야 한다.

#### 2. AOP 적용

- 공통 관심 사항, 핵심 관심 사항 분리

   <img src="https://user-images.githubusercontent.com/58902042/107843600-dad6b100-6e0f-11eb-9da0-ea015bbdea86.png" height=300> 
   
   ~~~java
   @Component
   @Aspect
   public class TimeTraceAop {
       @Around("execution(* hello.hellospring..*(..))")
       //@Around("Pointcut")
       public Object execute(ProceedingJoinPoint joinPoint) throws Throwable {
           long sart = System.currentTimeMillis();
    		System.out.println("START: " + joinPoint.toString());
           //joinPoint 메서드 사용
    		try {
   			 return joinPoint.proceed();
    		} finally {
    			long finish = System.currentTimeMillis();
    			long timeMs = finish - start;
   			 System.out.println("END: " + joinPoint.toString()+ " " + timeMs +"ms");
   		 }
    }
   }
   ~~~
   
   - 회원가입, 회원 조회등 핵심 관심사항과 시간을 측정하는 공통 관심 사항을 분리한다.
   - 시간을 측정하는 로직을 별도의 공통 로직으로 만든다. 
   - 핵심 관심 사항을 깔끔하게 유지할 수 있다. 
   - 변경이 필요하면 이 로직만 변경하면 된다. 
   - 원하는 적용 대상을 선택할 수 있다.

#### AOP 적용 전 의존관계

<img src="https://user-images.githubusercontent.com/58902042/107843749-e8406b00-6e10-11eb-97b3-4511a22f7782.png" height=280>

#### AOP 적용 후 의존관계

<img src="https://user-images.githubusercontent.com/58902042/107843755-f42c2d00-6e10-11eb-846b-d9cc4c15ebbf.png" height=280>




------

**<참조>**

- [[Spring] 스프링 AOP (Spring AOP) 총정리 : 개념, 프록시 기반 AOP, @AOP](https://engkimbs.tistory.com/746)
- [Spring AOP와 Aspectj 비교하기](https://logical-code.tistory.com/118)
- [Spring AOP, Aspect 개념 특징, AOP 용어 정리](https://shlee0882.tistory.com/206)
- [인프런-스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술의 강의자료](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%EC%9E%85%EB%AC%B8-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8#)

