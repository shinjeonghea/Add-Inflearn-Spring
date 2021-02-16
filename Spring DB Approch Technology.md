# 스프링 DB 접근 기술

- Spring db에 접근하는 방식으로 순수 JDBC, 스프링 JDBC Template, JPA, 스프링 데이터 JPA로 볼 수 있다.
- 이 글은 H2 데이터베이스를 사용하는 것으로 한다.

## 순수 JDBC

- gradle방식으로 하는 경우, build.gradle 파일에 jdbc, h2 데이터베이스 관련 라이브러리 추가

    ~~~groovy
    dependencies {
        implementation 'org.springframework.boot:spring-boot-starter-jdbc'
        runtimeOnly 'com.h2database:h2'
    }
    ~~~

- `resources/application.properties`에 스프링 부트 데이터베이스 연결 설정 추가
  
  - 참고로, spring boot 2.4버전 부터는 `spring.datasource.username=sa` 를 꼭 추가 해주어야 하며, 마지막에 공백이 들어가지 않게 주의해야한다.

    ~~~properties
    spring.datasource.url=jdbc:h2:tcp://localhost/~/test
    spring.datasource.driver-class-name=org.h2.Driver
    spring.datasource.username=sa
    ~~~

- 리포지토리 구현
  
  - 순수 JDBC 리포지토리 구현은 [강의자료](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%EC%9E%85%EB%AC%B8-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8#)에서 볼 수 있으며, 이런 방식이 있구나 정도만 알면 되므로 따로 넣지 않는다.
  
- 스프링 설정 변경

  ~~~java
  package hello.hellospring;
  
  import hello.hellospring.repository.JdbcMemberRepository;
  import hello.hellospring.repository.JdbcTemplateMemberRepository;
  import hello.hellospring.repository.MemberRepository;
  import hello.hellospring.repository.MemoryMemberRepository;
  import hello.hellospring.service.MemberService;
  import org.springframework.context.annotation.Bean;
  import org.springframework.context.annotation.Configuration;
  import javax.sql.DataSource;
  
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

  - DataSource는 데이터베이스 커넥션을 획득할 때 사용하는 객체이다.
    - Spring boot는 데이터베이스 커넥션 정보를 바탕으로 DataSource를 생성하고 스프링 빈을 만들어 둔다. 그래서 DI를 받을 수 있다.
  - 실습을 통해 자바 코드로 직접 스프링 빈을 등록하는 방식을 사용했다.
    - 따라서, 순수 JDBC를 사용한 JdbcMemberRepository를  빈으로 등록하여 MemoryMemberRepository를 대체했다.
    - 위의 스프링 빈 등록방식과 의존관계에 대해 설명이 추가로 궁금하다면, [여기](Spring%20Bean&Dependency%20Inversion.md)를 통해 정보를 얻을 수 있다.

<br>

## JDBC Template

- JDBC Template는 순수 JDBC에서 **반복적인 코드들을 데부분 제거**한다.

- 하지만 SQL은 직접 작성해야 한다.

- 순수 JDBC와 동일한 환경설정을 한다.

  ~~~groovy
  dependencies {
      implementation 'org.springframework.boot:spring-boot-starter-jdbc'
      runtimeOnly 'com.h2database:h2'
  }
  ~~~

  ~~~properties
  spring.datasource.url=jdbc:h2:tcp://localhost/~/test
  spring.datasource.driver-class-name=org.h2.Driver
  spring.datasource.username=sa
  ~~~

- 리포지토리 구현

  ~~~java
  package hello.hellospring.repository;
  
  import hello.hellospring.domain.Member;
  import org.springframework.jdbc.core.JdbcTemplate;
  import org.springframework.jdbc.core.RowMapper;
  import org.springframework.jdbc.core.namedparam.MapSqlParameterSource;
  import org.springframework.jdbc.core.simple.SimpleJdbcInsert;
  import javax.sql.DataSource;
  import java.sql.ResultSet;
  import java.sql.SQLException;
  import java.util.HashMap;
  import java.util.List;
  import java.util.Map;
  import java.util.Optional;
  
  public class JdbcTemplateMemberRepository implements MemberRepository {
  
      private final JdbcTemplate jdbcTemplate;
      public JdbcTemplateMemberRepository(DataSource dataSource) {
          jdbcTemplate = new JdbcTemplate(dataSource);
      }
      @Override
      public Member save(Member member) {
          
          SimpleJdbcInsert jdbcInsert = new SimpleJdbcInsert(jdbcTemplate);
          jdbcInsert.withTableName("member").usingGeneratedKeyColumns("id");
          
          Map<String, Object> parameters = new HashMap<>();
          parameters.put("name", member.getName());
            //----------이렇게 하면 query를 짤 필요없다.
          
          Number key = jdbcInsert.executeAndReturnKey(new MapSqlParameterSource(parameters));
          member.setId(key.longValue());
          return member;
      }
      
      @Override
      public Optional<Member> findById(Long id) {
          List<Member> result = jdbcTemplate.query("select * from member where id = ?", memberRowMapper(), id);
          return result.stream().findAny();
      }
      
      @Override
      public List<Member> findAll() {
          return jdbcTemplate.query("select * from member", memberRowMapper());
      }
      
      @Override
      public Optional<Member> findByName(String name) {
          List<Member> result = jdbcTemplate.query("select * from member where name = ?", memberRowMapper(), name);
          return result.stream().findAny();
      }
      
      private RowMapper<Member> memberRowMapper() {
          return (rs, rowNum) -> {
              Member member = new Member();
              member.setId(rs.getLong("id"));
              member.setName(rs.getString("name"));
              return member;
          };
      }
  }
  ~~~

- 스프링 설정 변경

  ~~~java
  package hello.hellospring;
  
  import hello.hellospring.repository.JdbcMemberRepository;
  import hello.hellospring.repository.JdbcTemplateMemberRepository;
  import hello.hellospring.repository.MemberRepository;
  import hello.hellospring.repository.MemoryMemberRepository;
  import hello.hellospring.service.MemberService;
  import org.springframework.context.annotation.Bean;
  import org.springframework.context.annotation.Configuration;
  import javax.sql.DataSource;
  
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
          // return new JdbcMemberRepository(dataSource);
          return new JdbcTemplateMemberRepository(dataSource);
      }
  }
  ~~~

<BR>

## JPA

- 자바의 ORM기술 표준으로, 

    - ORM(Object-Relational-Mapping)은 객체와 관계형 DB를 매핑하는 기술
    - ex_) 애너테이션으로는, `@Entity`, `@Id`, `@GeneratedValue`
    
- JPA는 인터페이스이며, 구현체는 Hibernate.. 등이 있다.

- JPA는 기존의 반복 코드는 물론이고, 기본적인 SQL도 JPA가 직접 만들어서 실행해준다

    - SQL과 데이터 중심의 설계에서 객체 중심의 설계로 패러다임을 전환을 할 수 있다.
    
- JPA 동작 과정

    ![img](https://media.vlpt.us/images/pjm4142/post/eccbae3b-f978-4781-9459-cf50ba6b249b/image.png)  

    - JPA는 애플리케이션과 JDBC사이에서 동작한다.
      - JDBC를 이용하여 SQL을 통해 DB와 통신한다.

- gradle방식으로 하는 경우, build.gradle 파일에 jpa, h2 데이터베이스 관련 라이브러리 추가

    ~~~groovy
    dependencies {
        //implementation 'org.springframework.boot:spring-boot-starter-jdbc'
        implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
        runtimeOnly 'com.h2database:h2'
    }
    ~~~
    
- `resources/application.properties`에 스프링 부트 JPA설정 추가
  
   ~~~properties
spring.datasource.url=jdbc:h2:tcp://localhost/~/test
   spring.datasource.driver-class-name=org.h2.Driver
   spring.datasource.username=sa
   spring.jpa.show-sql=true
   spring.jpa.hibernate.ddl-auto=none
   ~~~
   
   - `show-sql`: JPA가 생성하는 SQL 출력 여부
   - `ddl-auto`: JPA가 제공하는 테이블 자동 생성 기능 사용 여부
     - `create`를 사용하면 엔티티 정보를 바탕으로 테이블도 직접 생성
   
- JPA 엔티티 매핑

   ~~~java
   package hello.hellospring.domain;
   
   import javax.persistence.Entity;
   import javax.persistence.GeneratedValue;
   import javax.persistence.GenerationType;
   import javax.persistence.Id;
   
   @Entity
   public class Member {
       @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
       private Long id;
       //@Column(name="username")
       private String name;
       
       public Long getId() {
           return id;
       }
       
       public void setId(Long id) {
           this.id = id;
       }
    
       public String getName() {
           return name;
       }
       
       public void setName(String name) {
           this.name = name;
       }
   }
   ~~~
~~~
   
- `@Entity`
   
      - 클래스Member와 DB에 생성한 table이 매핑된다.
      - `@Id`
         - 해당 column이 식별키라는 것을 의미한다.
         - @GeneratedValue와 함께 식별키를 어떤 strategy로 생성하는지 명시한다.
         - strategy=GenerationType.IDENTITY: 생성 방식을 DB에게 넘긴다
   - 만약 BD의 컬럼명과 코드의 변수명이 다른 경우
      - ex_) 컬럼명이 username일 경우,
         - @Column(name="username")

- 리포지토리 구현

   ~~~java
   package hello.hellospring.repository;
   
   import hello.hellospring.domain.Member;
   import javax.persistence.EntityManager;
   import java.util.List;
   import java.util.Optional;
   
   public class JpaMemberRepository implements MemberRepository {
       private final EntityManager em;
       public JpaMemberRepository(EntityManager em) {
           this.em = em;
       }
   
       public Member save(Member member) {
           em.persist(member);
           return member;
       }
       
       public Optional<Member> findById(Long id) {
           Member member = em.find(Member.class, id);
           return Optional.ofNullable(member);
       }
       
       public List<Member> findAll() {
           return em.createQuery("select m from Member m", Member.class).getResultList();
       }
   
       public Optional<Member> findByName(String name) {
           List<Member> result = em.createQuery("select m from Member m where m.name = :name", Member.class)
               .setParameter("name", name)
               .getResultList();
           return result.stream().findAny();
       }
   }
~~~

- 서비스 계층에 트랜잭션 추가

   ~~~java
   import org.springframework.transaction.annotation.Transactional
   
   @Transactional
   public class MemberService {}
   ~~~

   - 스프링은 해당 클래스의 메서드를 실행할 때 트랜잭션을 시작하고, 메서드가 정상 종료되면 트랜잭션을 커밋한다. 만약 런타임 예외가 발생하면 롤백한다.
   - **스프링은 해당 클래스의 메서드를 실행할 때 트랜잭션을 시작하고, 메서드가 정상 종료되면 트랜잭션을 커밋한다. 만약 런타임 예외가 발생하면 롤백한다.**

- 스프링 설정 변경

  ~~~java
  @Configuration
  public class SpringConfig {
      private final EntityManager em;
      
      public SpringConfig(EntityManager em) {
          this.em = em;
      }
      
      @Bean
      public MemberService memberService() {
          return new MemberService(memberRepository());
      }
      
      @Bean
      public MemberRepository memberRepository() {
         // return new MemoryMemberRepository();
         // return new JdbcMemberRepository(dataSource);
         // return new JdbcTemplateMemberRepository(dataSource);
         return new JpaMemberRepository(em);
     }
  }
  ~~~

  - Entity manager: 엔티티 객체들(Member)을 관리하는 역할

<br>

## 스프링 데이터 JPA

- JPA를 편리하게 사용하도록 도와주는 기술
- 리포지토리에 구현 클래스 없이 인터페이스 만으로 개발을 완료할 수 있다.
- 또한 반복 개발해온 기본 CRUD 기능도 모두 제공한다.
  
- 개발자는 핵심 비즈니스 로직을 개발하는데, 집중할 수 있다.
  
- JPA와 동일한 환경설정을 사용한다.

  ~~~groovy
    dependencies {
        implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
        runtimeOnly 'com.h2database:h2'
    }
  ~~~
  
  ~~~properties
  spring.datasource.url=jdbc:h2:tcp://localhost/~/test
  spring.datasource.driver-class-name=org.h2.Driver
  spring.datasource.username=sa
  spring.jpa.show-sql=true
  spring.jpa.hibernate.ddl-auto=none
  ~~~
  

- 리포지토리 구현

  ~~~java
  package hello.hellospring.repository;
  
  import hello.hellospring.domain.Member;
  import org.springframework.data.jpa.repository.JpaRepository;
  import java.util.Optional;
  
  public interface SpringDataJpaMemberRepository extends JpaRepository<Member,
  Long>, MemberRepository {
       //JPQL, select m from Member m where m.name = ?
      //NAME 같은 경우 회사마다 공통적인 이름으로 쓸 수 없기 때문에 옵션에 맞춰 만들어 준다.
      Optional<Member> findByName(String name);
  }
  ~~~

  - Interface가 extends로 spring data가 제공하는 JpaRepository를 받으면, 구현체를 자동으로 만들어주고 스프링 빈에 자동으로 등록한다.
  - 스프링 데이터 JPA가 SpringDataJpaMemberRepository 를 스프링 빈으로 자동 등록해준다.

- 스프링 설정 변경

  ~~~java
  package hello.hellospring;
  
  import hello.hellospring.repository.*;
  import hello.hellospring.service.MemberService;
  import org.springframework.context.annotation.Bean;
  import org.springframework.context.annotation.Configuration;
  
  @Configuration
  public class SpringConfig {
      
      private final MemberRepository memberRepository;
      
      public SpringConfig(MemberRepository memberRepository) {
          this.memberRepository = memberRepository;
      }
      
      @Bean
      public MemberService memberService() {
          return new MemberService(memberRepository);
      }
  }
  ~~~

  - 여기서 따로 리포지토리를 설정할 필요가 없다.

- 스프링 데이터 JPA 제공 클래스

  <img src="https://user-images.githubusercontent.com/58902042/108055006-bd654980-7092-11eb-9ff9-2b0cdeb2a2a7.png" height=500 > 

  - 인터페이스를 통한 기본적인 CRUD
  -  findByName() , findByEmail() 처럼 메서드 이름 만으로 조회 기능 제공 
  - 페이징 기능 자동 제공

<br>

---------

**<참조>**

- [Spring 입문 - step6. 스프링 DB 접근 기술](https://velog.io/@pjm4142/Spring-%EC%9E%85%EB%AC%B8-step6.-%EC%8A%A4%ED%94%84%EB%A7%81-DB-%EC%A0%91%EA%B7%BC-%EA%B8%B0%EC%88%A0)

- [인프런-스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술의 강의자료](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%EC%9E%85%EB%AC%B8-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8#)