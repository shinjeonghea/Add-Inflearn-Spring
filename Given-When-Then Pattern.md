# Given-When-Then 패턴

<br>

- Test Code  스타일을 표현하는 방식으로, Test를 세 부분으로 나누어 구조화된 접근법을 제공한다.
  
  - **Given** : **테스트 수행 이전의 상태**를 설명하며, Test를 위한 사전 조건으로 생각할 수 있다.
  - **When** : **사용자가 지정하는 동작**을 설명한다.
- **Then** : 지정된 동작으로 인해 **예상되는 변경 사항**
  - 즉, 준비- 실행- 검증의 부분으로 나눈다는 뜻이다.
  
- ex_) 기능 사용자 주식 트레이드

  - 시나리오 : 트레이드가 마감되기 전에 사용자가 판매를 요청

  - "Given" : 나는 MSFT 주식을 100가지고 있다. 

  ​			    나는 APPL 주식을 150가지고 있다. 

  ​		        시간은 트레이드가 종료되기 전이다. 

  - "When" :  나는 MSFT 주식 20을 팔도록 요청했다. 

  - "Then"   : 나는 MSFT 주식 80 가지고 있어야 한다.

  ​	                    나는 APPL 주식 150을 가지고 있어야 한다.

  ​	                    MSFT 주식 20이 판매 요청이 실행되었어야 한다.

------

**<참조>**

- [Given When Then](https://velog.io/@koseungbin/Given-When-Then)
- [Given-When-Then 패턴](https://kchanguk.tistory.com/40)