# Gradle 라이브러리 의존성 설정

<BR>

:bulb: *Gradle은 의존관계가 있는 라이브러리를 함께 다운로드 한다.*

<BR>

## :page_with_curl:강의자료  

#### 스프링 부트 라이브러리 

- spring-boot-starter-web 
  - spring-boot-starter-tomcat: 톰캣 (웹서버) 
  - spring-webmvc: 스프링 웹 MVC 

- spring-boot-starter-thymeleaf: 타임리프 템플릿 엔진(View) 

- spring-boot-starter(공통): 스프링 부트 + 스프링 코어 + 로깅 

  - spring-boot

    -  spring-core 
  - spring-boot-starter-logging 
    - logback, slf4j 
#### 테스트 라이브러리
- spring-boot-starter-test 
  - junit: 테스트 프레임워크 
  - mockito: 목 라이브러리 
  - assertj: 테스트 코드를 좀 더 편하게 작성하게 도와주는 라이브러리 
  - spring-test: 스프링 통합 테스트 지원

--------------------------

<br>

#### 라이브러리 의존성 설정

- 라이브러리 의존성 설정은 **build.gradle** 스크립트 파일에서 할 수 있다.

- 스크립트는 Groovy와 Kotlin언어로 작성될 수 있다.
  - 프로젝트를 생성하면 기본적으로 Groovy로 작성된다.

    ```groovy
    plugins {
       id 'org.springframework.boot' version '2.4.2'
       id 'io.spring.dependency-management' version '1.0.11.RELEASE'
       id 'java'
    }

    group = 'SJH'
    version = '0.0.1-SNAPSHOT'
    sourceCompatibility = '11'

    repositories {
       mavenCentral()
    }

    dependencies {
       implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
       implementation 'org.springframework.boot:spring-boot-starter-web'
       implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
       runtimeOnly 'com.h2database:h2'
       testImplementation('org.springframework.boot:spring-boot-starter-test') {
          exclude group: 'org.junit.vintage', module: 'junit-vintage-engine'
       }
    
    }
    
    test {
   useJUnitPlatform()
    }
    ```


- build.gradle 파일 내부의 코드는 다음과 같이 플러그인, 저장소, 의존성을 설정하는 스크립트가 작성되어 있다.

~~~
repositories {
   mavenCentral()
}
~~~

- 위의 코드는 Gradle이 의존성 모듈을 가져올 저장소를 선언하는 부분

- Maven Central 저장소에서 의존성을 가져오도록 설정되어있다.

  <img src="https://user-images.githubusercontent.com/58902042/107844664-d4990280-6e18-11eb-9f53-f7845ec97b2d.png" height="300"> 

  - 만약 Bintray JCenter, Google Android 저장소에서 가져오도록 설정하고 싶다면 jcenter(), google() 노테이션을 활용하여 스크립트를 수정하면 된다.

<br>

#### 의존성 구성(**Dependency Configuration**)

- Gradle 프로젝트에서 선언된 모든 의존성은 사용되는 **특정 범위** 를 가진다.
  - **Implementation** : 구현할 때에만 사용
  - **compileOnly**: 컴파일할 때에만 사용되고 런타임 때에는 사용되지 않음
  - **runtimeOnly**: 런타임 때에만 사용
  - **testImplementation**: 테스트할 때에만 사용

<br>

#### 의존성 선언

~~~groovy
dependencies {
   implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
   implementation 'org.springframework.boot:spring-boot-starter-web'
   implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
   runtimeOnly 'com.h2database:h2'
   testImplementation('org.springframework.boot:spring-boot-starter-test') {
      exclude group: 'org.junit.vintage', module: 'junit-vintage-engine'
   }
~~~

- build.gradle스크립트의 dependencies 부분에서 사용할 의존성을 configuration과 함께 선언
- Spring Boot에서는 starter을 통해 버전 관리를 쉽게 할 수 있도록 도와준다.
- 따라서, starter를 사용할 때에는 버전을 따로 명시하지 않아도 된다.



---

**<참조>**

- [[Spring Boot] Gradle이란? Gradle 의존성 설정하기](https://limdevbasic.tistory.com/12)

- [인프런-스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술의 강의자료](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%EC%9E%85%EB%AC%B8-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8#)

