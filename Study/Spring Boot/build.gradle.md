# 간단히 공부
1. Gradle vs Maven
   >> 사실 Maven과 Gradle의 차이를 정확히는 알지 못했다.
      Gradle이 Maven보다 더 늦게 나왔고 때문에 더 좋다?라고만 어렴풋이 기억하고 있었던거 같다.
      현재 업무에서 Gradle을 사용하고 있고 이때다 싶어 Gradle에 익숙해져보려고 했을뿐 이론적으로 이해를 하고 있거나 하지 못했다.
      해서 왜 Gradle이 Maven을 앞설거라 하는지 알아보기로 했다.
   - Maven
     - Apache 이름으로 출시
     - Ant를 사용하던 개발자들의 불편함 해소 및 부가기능 추가
     - 특징
        - 빌드를 쉽게 하도록 해줌.새로운 기능에 대한 설치 및 업데이트가 편함
        - pom.xml을 사용
   - Gradle
     - Groovy라는 언어 기반의 빌드 도구.(Groovy는 Java 가상 머신에서 실행되는 스크립트 언어)
         스크립트 언어로 소스 코드 그대로 실행.JAVA와 호환되며 Java Class 파일 그대로 Groovy 클래스로 사용 가능(Java와 매우 유사)
     - 빌드 스크립트가 일률적이지 않다.
     - 다수의 빌드 지원 파일 구성
     - 특징
        - Ant 처럼 유연함
        - Maven 사용 가능.maven과 lvy 레파지토리 지원
        - 멀티 프로젝트에 효율적(대형 프로젝트)
        - 원격 저장소나 pom, ivy 없이 연결되는 의존성 관리 지원
   - **Gradle을 사용하려는 이유**
     - Maven은 가독성이 떨어짐.그에비해 Gradle은 설정이 간편
     - Groovy라는 언어(Java와 유사)는 xml에 비해 확장성이 뛰어남
     - 대규모 프로젝트에서 유리
     - Maven은 멀트 프로젝트 구성을 상속 구조로 사용 but Gradle은 설정을 주입
     - 빌드 속도가 Maven보다 빠름
     
    ?? Groovy는 무엇인가.
     - JVM에서 실행되며 JAVA와 유사함.때문에 호환 가능
     - 문법
       - 메소드 호출시 소괄호 생략
         test('ya!')
         test 'ya!'
       - 형 생략 : def로 모든 타입 번수 선언 가능
         int cnt = 0
         def cnt = 0

2. 설정 내용
 - repositories
   : 필요한 라이브러리를 자동으로 다운로드 및 통합하는데, 그 각종 프로그램들이 저장되는 위치
  - mavenCentral() : Maven 중앙 저장소를 이용함.
- dependencies : 저장소에서 필요할 라이브러리 정의
 - complie : 컴파일시 사용
 - classpath : 지정된 라이브러리를 클래스 경로에 추가
- 라이브러리 지정
 - '그룹 : 이름 : 버전'
   group:'그룹', name:'이름', version:'버전'

--------------------------------------------------------------------------------------------------------------------------------------------------------

plugins {
    id 'org.springframework.boot' version '2.1.5.RELEASE'
    id 'java'
    id 'war'
    id 'idea'
}

apply plugin: 'io.spring.dependency-management'

group = 'com.fleta'
version = '1.1.0'
sourceCompatibility = '1.8'

repositories {
    mavenCentral()
}
dependencies {
}
