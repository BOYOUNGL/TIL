# Spring Boot
 : 최소한의 스프링 설정으로 빠르고 간편하게 개발 할 수 있도록 함
 
 - 특징
  1. 단독으로 실행 가능한 스프링 애플리케이션 생성
  2. 서버 구축이 간단(Tomcat이 내장되어 있음)
  3. 많은 라이브러리를 쉽게 가져와 사용 가능
  4. 가능한 자동으로 설정
  5. 설정 정보를 담는 XML 작성이 필요없어 가벼움 -> 개발 환경 구축을 쉽게

 
# Java Configure : XML 대신 java로 Bean 생성
 1. @SpringBootApplication (3가지 어노테이션)
 - @Configuration : 클래스에 이 어노테이션이 붙어 있으면 스프링은 해당 클래스를 JAVA Config로 간주
 - **@ComponentScan** : 이 어노테이션은 basePackage를 지정없이도 main class가 위치한 패키지를 Root Package로 보고 그 이하의 모든 컴포넌트 클래스들을 Bean으로 등록
   	                FirstApplication.class는 메인 클래스로 examples.boot.first 패키지에 위치하며 컴포넌트 스캔의 루트 패키지이다.(하위 모든 컴포넌트 검색)
			@Component 어노테이션이 있는 클래스 검색
 - @EnableAutoConfiguration : 어플리케이션에 추가된 설정을 자동으로 추가(환경설정에 필요한 설정값들을 등록)
 
	여기서 @ComponentScan와 @EnableAutoConfiguration가 중요하다 함!
	Bean이 이 두 단계로 나눠서 읽힌다고...
	근데 Spring META-INF 파일을 읽는거라 EnableAutoConfiguration를 사용하지 않을수도 있다고 함.
 


# Application.java 파일 내용
 - @SpringBootApplication : @EnableAutoConfiguraion, @ComponentScan, @Configuration을 하나로 묶어둔것
 
 - 스프링부트 기동 : SpringApplicaiotn.run(FirstApplication.class, args);
 
	 ex) 사무실에서 사용한 코드를 재구성, filename : TestApplication.java
	 <pre>
	 <code>
		@SpringBootApplication
		@ComponentScan({"com.test"})
		public class FletaIbrmApplication {
		  public static void main(String[] args) {
		    SpringApplication.run(TestApplication.class, args);
		  }
		}
	</code>
	</pre>
 
  **이때, 이 어노테이션이 설정된 클래스를 package 최상위라 생각하고 ComponentScan을 실행함. 
   때문에 이 어노테이션이 있는 클래스 파일 위치가 중요함.**
   
   run()에는 JAVA Config 클래스가 들어간다(@Configuration이 있어야 JAVA Config)
 - Application Context가 Java Configure을 읽으며 run()안에서 확인 가능하다함.
 
200728 >> 이 부분은 Spring boot를 실행할때 필요한 듯?
 	  일을 하다가 이 파일이 두개가 있어서 생긴 버그를 봤었던 기억이 있음.
          정확한 오류 내용은 기억나지 않으나 중복될 수 없다는 메시지였던거 같음.한 개를 지워주니 잘 해결 되었다.
 	  중복이 일어나면 안 되는 이유가 위에서 말한 스프링의 객체 관리가 Singleton 패턴으로 이루어지기 때문이었구나 싶음.
 
   
- spring-boot-dependencies : spring version 관리

# @Bean
 : 자동으로 컴포넌트 감지
   팩토리 함수 사용 -> 빈의 생성자에 의해 추가 제어가 필요할때 유용
   	 	   -> @Bean 어노테이션이 붙은 함수
		      Bean을 애플리케이션 컨텍스트에 등록할 때 사용
 - Bean명 = 함수명
 - 함수는 매개변수를 가질 수 있으며 애플리케이션 컨텍스트의 다른 Bean을 연결

# @profile
 : 특정한 프로파일에서만 특정 Bean 등록 혹은 특정 프로파일에서 Bean 설정을 다르게 하고 싶을때
   @Profile("first")
   
  - application.properties에서 사용할 프로파일 설정 가능
     -> spring.profiles.active = 설정값
     -> my.name = [프로파일명]junseo : 프로파일용 프로퍼티(프로퍼티 파일의 key값으로 잡는듯
        ex) my.name = [first]junseo
	    my.name = [second]junseo
     -> spring.profiles.include  = 추가할 프로파일 (활성화하라는것)
  - 프로파일마다 해당 프로파일 프로퍼티 설정이 가능한것으로 보임
    application.properties보다 우선순위 높음

# @test
 - junit : java에서 독립된 단위 테스트 지원(프레임워크)
 - spring-boot-starter-test
   1. spring-boot-test : 핵심 기능
   2. spring-boot-test-configuration : 테스트를 위한 auto configuration
 
# @MockBean
   
   
   
--------------------------------------------------------------------------------------------------------------------------------------------------------

[공부 일기]
200728 >> 이미 완성된 Spring Boot를 사용한 프로젝트에 참여하고 있으나, 정작 Spring Boot에 대해 정확히 아는 바가 없다.
          물론 Spring을 명확하고 정확하게 이해하고 있는것도 아니라 이해할 수 있는 선에서 짚어가면서 같이 정리해보려한다.
          뒤죽박죽 정리되는 내용은 추후 이해도가 높아졌을때 다시 정리하는것을 목표로 공부한대로 작성하고 읽고 수정하고를 반복하고자 한다.
	  
200731 >> 공부하는 책 구성에 맞게 Bean 구성을 찾아봐야함.

200802 >> 예제가 생각대로 풀리지 않아 이론 먼저....
