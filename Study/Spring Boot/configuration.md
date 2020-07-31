

 19년도 8월부터 Spring Boot 1.5.x 지원이 중단된다 한다.
 (*JDK8 사용이 필수)

- Build Tool
 -- Gradle 4 이상 (회사에서 사용중임으로 이걸 이용)
 -- Maven 3.2 이상



# 환경 설정 (설정을 직접하면서 추가적으로 작성)
 - Tool : Visual Studio Code
 - Tomcat 9
 - Spring Tools 4
 - JDK 8
 
 순서가 뒤죽박죽이긴하나 일단 계속 진행...!
 
 
 
 
현재 회사에서도 gradle을 사용중임으로 gradle을 사용해보기로 함.

1. vscode marketplace 설치
 - JAVA Extension Pack
 - Spring Boot Extension Pack
 - Korean Language Pack for Visual Studio Code : 한글어 팩

1.1 VSCode 확장 패키지 설치 후 JDK 경로 설정
 1) [파일] - 기본설정 > 설정
 2) 검색에서 jdk 검색 > setting.json 편집
 3) 하단에 "java.home" : "경로값" 추가 (환경 변수에도 값 설정할 것)
 
 2. Gradle Spring 
  2.1 ctrl + shift + p
  2.2 Spring Initializr 검색
  2.3 Gradle Project or Maven Project
  2.4 Java : 언어 선택
  2.5  
   - Package 이름 : JAVA 소스의 root 폴더명,Group ID
   - root 폴더 하위명.articact ID
  2.6 Spring boot version
  2.7 의존성 옵션 선택하여 추가
  2.8 모두 선택후 Project 생성
  
  --> build.gradle 파일로 설정 내용 확인 가능
  
  3. .
