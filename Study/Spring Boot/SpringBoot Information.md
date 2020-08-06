# Spring Boot
 : 최소한의 스프링 설정으로 빠르고 간편하게 개발 할 수 있도록 함
 
 - 특징
  1. 단독으로 실행 가능한 스프링 애플리케이션 생성
  2. 서버 구축이 간단(Tomcat이 내장되어 있음)
  3. 많은 라이브러리를 쉽게 가져와 사용 가능
  4. 가능한 자동으로 설정
  5. 설정 정보를 담는 XML 작성이 필요없어 가벼움 -> 개발 환경 구축을 쉽게
 - 내장 웹 서버
   : 자동 설정에 웹 서버(톰캣, 서블릿 등) 기본 설정이 포함되며, 톰캣이 내장됨.때문에 Run할 경우 Spring Boot App을 선택하여 돌리되, 실행중인 톰캣은 중지할 것
   - 웹서버 사용 안할때
     spring.main.web-application-type=none
   - port 변경 : application.properties 파일에 설정
     server.port=8080 (기본 값.0일 경우는 랜덤으로 사용하는듯)
 - JAR
   - jar 하나의 파일로 묶어 이 파일만 실행하면 됨.
     spring-boot-maven-plugin(자체 메이븐 플러그인)으로 메이븐 프로젝트를 .jar 파일로 만들어준다함.
     200803 >> 여기서 궁금한건 gradle도 마찬가지인가?
   - 독립적으로 실행
  
 
# Java Configure : XML 대신 java로 Bean 생성
 1. @SpringBootApplication (3가지 어노테이션 포함 때문에 이 어노테이션으로 Spring Boot 실행이 가능)
 - @Configuration : 클래스에 이 어노테이션이 붙어 있으면 스프링은 해당 클래스를 JAVA Config로 간주
 - **@ComponentScan** : 이 어노테이션은 basePackage를 지정없이도 main class가 위치한 패키지를 Root Package로 보고 그 이하의 모든 컴포넌트 클래스들을 Bean으로 등록
   	                FirstApplication.class는 메인 클래스로 examples.boot.first 패키지에 위치하며 컴포넌트 스캔의 루트 패키지이다.(하위 모든 컴포넌트 검색)
			@Component 어노테이션이 있는 클래스 검색
 - @EnableAutoConfiguration : 어플리케이션에 추가된 설정을 자동으로 추가(환경설정에 필요한 설정 값들을 등록)
 
  + 여기서 @ComponentScan와 @EnableAutoConfiguration가 중요하다 함!
  Bean이 이 두 단계로 나눠서 읽힌다고...
  근데 Spring META-INF 파일을 읽는거라 EnableAutoConfiguration를 사용하지 않을수도 있다고 함.
 

# Application.java 파일 내용
 - @SpringBootApplication 
   : @EnableAutoConfiguraion, @ComponentScan, @Configuration을 하나로 묶어둔 것
     최상위 패키지가 되는듯
 
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
 
200728 >> 이 부분은 Spring boot를 실행할때 무조건 필요한 듯.
 	  일을 하다가 이 파일이 두개가 있어서 생긴 버그를 봤었던 기억이 있음.
          정확한 오류 내용은 기억나지 않으나 중복될 수 없다는 메시지였던걸로 기억함.두개 중 사용하지 않는 파일을 지워주니 잘 해결 되었다.
 	  중복이 일어나면 안 되는 이유가 위에서 말한 스프링의 객체 관리가 Singleton 패턴으로 이루어지기 때문이었구나 싶음.
   
- spring-boot-dependencies : spring version 관리

# @Bean
 : 자동으로 컴포넌트 감지.
 
 개발자가 컨트롤이 불가능한 외부 라이브러리들을 Bean으로 등록하고 싶은 경우 (개발자가 생성한 Class에 @Bean 선언 할 수 없음)
 (참고 : https://jojoldu.tistory.com/27)
 
  200806 >> 외부의 객체를 주입시킬때 사용?(다시 확인해볼것)
   https://endorphin0710.tistory.com/93
   이 블로그에서 스프링 레퍼런스 메뉴얼 번역을 조금 가져왔다.

   스프링 IoC(Inversion of Control) 컨테이너에 의해서 관리되고 애플리케이션의 핵심을 이루는 객체들을 스프링에서는 Beans라고 부른다.
   빈은 스프링 Ioc 컨테이너에 의해서 인스턴스화되어 조립되거나 관리되는 객체를 말합니다.
   즉 Spring Bean과 Java 일반 객체의 차이는 없음.스프링 컨테이너에서 만들어지면 스프링 빈일뿐이라는거다.

   Beans는 설정 메타 데이터(XML)에 의해 생성됨 (Component Scanning 혹은 Bean 설정 파일을 통해)

   Spring은 모든 bean을 기본적으로 singleton으로 관리
   여기서 singleton은 spring 컨테이너에서 한번만 생성되며 컨테이너가 사라지면 함께 없어짐
   
 
   팩토리 함수 사용 -> 빈의 생성자에 의해 추가 제어가 필요할때 유용
   	 	   -> @Bean 어노테이션이 붙은 함수
		      Bean을 애플리케이션 컨텍스트에 등록할 때 사용
 - Bean명 = 함수명
 - 함수는 매개변수를 가질 수 있으며 애플리케이션 컨텍스트의 다른 Bean을 연결
 
 - Prototype : 모든 요청에서 새로운 객체를 생성

**Bean은 Bean 생성 Autowired는 Bean 주입!!**

# @Component
 : @Autowired(객체의 타입이 일치 확인 후 자동으로 주입) 할때 초기화 메소드가 실행되며 다른곳에서 호출없이도 실행 됨.
 - 사용자가 빈객체를 만들어 필요한 내용을 직접 만들어 스캔 할 수 있도록 해당 어노테이션을 추가
   (참고 : https://articles09.tistory.com/28)
 	ex) xml 파일에 아래와 같이 등록만 하면 다른 클래스에서 똑같이 Bean만 등록하면 됨
     	    <baen id="testMapper" class="study.test.TestMapper"></bean>
     	    
	    클래스에 의존하려면?
	    1. 
	    <pre><code>
	    	@Autowired
	    	public test(TestMapper testMapper) {
	    		this.testMapper = testMapper
	    	}
	     </code></pre>
	    2.
	    <pre><code>
	    	public class test {
	    		@Autowired
			private TestMapper testMapper;
	    	}
	     </code></pre>
	    


# @PostConstrut
 : 생성자가 호출됫을때 bean이 초기회 되지 않은 상태인데, 이 어노테이션을 사용하면 bean이 초기화와 동시에 의존성 확인 가능


# @Override
 : Spring boot 2.1부터는 Bean 오버라이딩 불가능.
 <pre>
 <code>
 	//이런식으로 사용이 안되는듯
	@overrid
	@Bean
<code>
 </pre>

# 속성 외부화 
 - application.properties
   - 외부 : 사용자가 만든 properties (우선 순위 더 높음)
   - 내부 : 내장된 propertiesㅎ
 - 폴더 상위에 있을 수록 적용 순위 높은 듯

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


# PropertySource
 - application.properties가 아닌 다른 파일을 사용하고자 할때 classpath를 이용해 추가 파일을 사용할 수 있음
  @PropertySource("classpath:your-external.propertyies")


# @test
 : (선언된 메소드, 클래스)해당 테스트가 진행
 - junit : java에서 독립된 단위 테스트 지원(프레임워크)
 - spring-boot-starter-test
   1. spring-boot-test : 핵심 기능
   2. spring-boot-test-configuration : 테스트를 위한 auto configuration
 
 
# @MockBean
 - 특정 Bean을 대체하는 어노테이션 같음.(Spring Boot에 내장되있다함)
 - 테스트 필요시
 - 같은 유형의 빈이 다수 존재할때 name에 빈의 이름 지정후 원하는것으로 대체하게 함.
   @MockBean(name="first")
 
 200802 >> MockBean과 Mock의 차이점에 대해 인터넷에 연관지어 나오는걸 봤음.
           Mock에서 파생된게 MockBean이라는거 같은데.이건 추가적으로 확인해봐야할 것 같음
	   -> Mock : 모의 객체라는 뜻
   	   -> Bean이 Contrainer에 있을땐 MockBean 아니면 Mock인거 같다는 내용을 봄
 
 
 # 로깅 구성
  : application.properties에 로깅 수준, 패턴, 선택적 로그 파일 위치등을 추가하여 사용
 - loggin.level.org.springframework.web=DEBUG
   -> root 수준으로 하려면 : loggin.level.root=수준
 - 파일 로깅 : logging.file or loggin.path
 	      logging.file=test.log (로그 파일 명)
	      loggin.path=/log/test (로그 파일 위치)
 - 파일 갯수 정의 : logging.file.max-history
 - 파일 크기 정의 : logging.file.max-size
 
 
 # 기존 설정 재사용
  : @Import or @ImportResource를 통해 @Configuration or @SpringBootApplication이 있는 클래스에 추가
 - 파일 시스템 위치시
   @ImportResource("file:/study/test/application-context.xml")
 - @Import : 명시된 클래스를 설정에 추가.컴포넌트 탐색이 안되거나 @Configuration의 자동감지를 비활성화했을때 
	      
  
--------------------------------------------------------------------------------------------------------------------------------------------------------

[공부 일기]
200728 >> 이미 완성된 Spring Boot를 사용한 프로젝트에 참여하고 있으나, 정작 Spring Boot에 대해 정확히 아는 바가 없다.
          물론 Spring을 명확하고 정확하게 이해하고 있는것도 아니라 이해할 수 있는 선에서 짚어가면서 같이 정리해보려한다.
          뒤죽박죽 정리되는 내용은 추후 이해도가 높아졌을때 다시 정리하는것을 목표로 공부한대로 작성하고 읽고 수정하고를 반복하고자 한다.
	  
200731 >> 공부하는 책 구성에 맞게 Bean 구성을 찾아봐야함.

200802 >> 예제가 생각대로 풀리지 않아 이론 먼저....

200803 >> 연결되서 이해를 해야하는데 그건 안되는거 같음.뒤돌아서면 잊어버리니 계속 읽어보는게 답이겠거니 함

200805 >> 이것저것 찾아서 읽다보니 사용하는 라이브러리에 대해 공부를 제대로 하라고 함.
	  버전이나 라이브러리를 잘못 넣어 문제가 생기는 경우가 있다고...
	  쓰고자하는 기능에 대해 제대로된 이해가 없으면 버그가 나도 아무것도 할 수 없다는데 뼈맞았다...
	  
200806 >> 스터디 모이는 날.확실히 공부한 내용을 이리저리 말하며 찾아보니 각자가 이해한 눈높이에서 설명이 오고가서 이해가 잘 갔다.
