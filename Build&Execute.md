# Window cmd에서 빌드하고 실행하기

<img src="https://user-images.githubusercontent.com/58902042/107856252-4a798a00-6e6a-11eb-9c76-e02e1bf81808.png" height=120> 

1. 만든 프로젝트 위치로 간다. (cd 명령어)
   - 필자의 경우 `C:\Users\jungh\Spring_Study\Sjh-spring` 에 프로젝트가 있다.
   - 프로젝트명까지 들어가야 한다.

<img src="https://user-images.githubusercontent.com/58902042/107856301-8a407180-6e6a-11eb-9e74-e0ae1a32bccd.png" height=370> 

2. dir명령어를 통해 gradlew.bat 존재 확인

<img src="https://user-images.githubusercontent.com/58902042/107856386-35e9c180-6e6b-11eb-8129-ab88c7b783d9.png" height=200 > 

3. gradlew.bat build라고 명령어 입력(BUILD SUCCESSFUL in ~ 가 뜨면 성공)

<img src="https://user-images.githubusercontent.com/58902042/107856430-70535e80-6e6b-11eb-869a-b895ff68f7d4.png" height=380> 

4. dir명령어를 다시 하면 build 파일 생성

<img src="https://user-images.githubusercontent.com/58902042/107856445-97119500-6e6b-11eb-8af6-dfc60a536b32.png" height=80> 

5. cd build/libs 파일 위치로 간다.

<img src="https://user-images.githubusercontent.com/58902042/107856454-a264c080-6e6b-11eb-9855-1ccf30e94f42.png" height=200> 

6. dir 하면 .jar파일이 만들어져 있다.
   - 캡쳐본에서는 `Sjh-spring-0.0.1-SNAPSHOT.jar` 파일

<img src="https://user-images.githubusercontent.com/58902042/107856506-f079c400-6e6b-11eb-94e2-40ceec9dce03.png" height=280> 

7. java -jar .jar파일명 하면 실행된다.
   - `java -jar Sjh-spring-0.0.1-SNAPSHOT.jar`
8. intellj나 eclipse에서 실행 서버가 실행되고 있는 경우 8080에러가 난다. 주의할 것.

<img src="https://user-images.githubusercontent.com/58902042/107856572-5403f180-6e6c-11eb-8a66-0fa2721ae4e8.png" height="80"> 

9. 다 사용하고 나서는 cd ..을 두번하여 다시 프로젝트 위치로 간다.
   - 사용하고 끄는 방법은 `ctrl+Z`하면된다.

<img src="https://user-images.githubusercontent.com/58902042/107856600-9299ac00-6e6c-11eb-9fb4-c72b16011ffb.png" height=100> 

10. gradlew.bat clean 명령어를 입력한다.
    - 명령어는 사라지고 BUILD SUCCEESSFUL이 뜬다.

<img src="https://user-images.githubusercontent.com/58902042/107856608-a47b4f00-6e6c-11eb-82f0-4d53f504350d.png" height=300> 

11. dir하면 build파일이 사라진 것을 확인할 수 있다.

