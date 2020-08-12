# MVC : 하나의 어플리케이션을 구성할 때 그 역할을 세가지로 나눈 패턴
  1. Model : 어플리케이션의 정보, 데이터, 데이터베이스, 초기화값, 변수.  
             정보들의 가공을 책임지는 컴포넌트.  
             재사용 가능해야함.  
             **'무엇'을 할것인가?**  
   - 사용자가 편집하길 위하는 모든 데이터를 가지고 있어야함.  
     (화면의 위치정보, 크기, 내용 등)  
   - 뷰나 컨트롤러에 대해 어떤 정보도 알 수 없으며 변경 내용에 대한 처리 구현  
  2. View : 사용자 인터페이스(데이터, 객체 입력/출력).  
            재사용이 가능해야함.  
            **무엇을 보여줄것인가?**  
   - 모델이 가지고 있는 정보를 따로 저장하면 안 됨  
   - 모델이나 컨트롤러와 같이 다른 구성요소를 몰라야 하며 변경 내용에 대한 처리 구현  
  3. Controller : 데이터와 사용자인터페이스를 잇는 역할.  
                  **'어떻게' 할 것인가?**  
   - 모델이나 뷰를 알아야 하며 지속적인 확인  
   
**사용해야하는 이유는?  
  => 각 역할을 명확히 나눠 사용하면 유연성과 유지보수, 확장성이 증가하며 중복코딩을 피할 수 있음**  
  
Spring MVC에 필요한 컴포넌트를 사용하기 위해 자동 구성을 진행하는데,  
활성화를 위해 Spring Boot가 classpath에서 spring MVC class를 감지해야 함.  
이때 의존성에 **spring-boot-starter-web**이 필요함.  
  
Spring Boot Starter Web은 Web Application의 패키지한것을 가져오기 때문에 개발을 빠르게 시작 가능.  
  
**Spring Boot의 핵심  
Spring Boot는 classpath상에 사용 가능한 프레임워크와 이미 있는 환경설정을 바라본다.  
이것을 기반으로 스프링 부트는 Application을 이 framework들과 함께 구성하는 데 필요한 기본 환경 설정을 제공 = Auto Configuration**  
  
  
# Application 실행시 작업 순서
  1. 내장형 톰캣이 8080으로 시작됨  
  2. 기본 서블릿 필터 등록 후 활성화  
  3. 정적 리소스 구성을  
  4. WebJars로 통합 활성화를  
  5. 기본 오류 처리 기능 구성  
  6. 필요한 Component로 DispatcherServlet 사전 구성을  
 200808 >> 어느 블로그에서 본건데 설명이 잘되있었는데...어딘지 기억이..찾으면 url을...  
  
  
# @RestController  
 : @RestController가 있는 클래스가 @Controller이며 Spring Boot로부터 감지될 대상임을 알려줌  
   때문에 @RequestMapping에 들어오는 요청을 감지하여 처리하는 메소드를 작성 할 수 있음  
 - @RequestMapping : URL을 컨트롤러의 메서드와 매핑할 때 사용  
200808 >> 그냥 @Controller와의 차이는?  
          Spring3부터 @ResponseBody를 이용하여 Rest 방식으로 처리를 하였으나, Spring4가 나오면서 @RestController가 나옴.  
            
          기본의 MVC @Controller는 View를 사용(@ResponseBody를 선언하여 객체를 리턴할 수 도 있음)   
          @RestController는 객체를 반환할때 객체 데이터는 바로 JSON/XML 타입의 HTTP 응답을 직접 리턴.  
          즉, @RestController는 View가 아닌 REST 방식의 Data 자체를 넘겨줌.  
          때문에 return에 JSP명이 써있다하더라도 View를 불러오진 않는다고 함.  
  - REST : Representational State Transfer.하나의 고유한 리소스를 대표하도록 설계  
  
  
# @ResponseBody  
 : 모든 요청 처리 메소드에 추가돼 Client에 결과를 전송한다는 것을 알려줌(JSON 형태로 반환)   
    
    
# @GetMapping
 : 메소드를 URL에 도착하는 모든 Get 요청에 매핑  
  1. @GetMapping("/")  
  = @RequestMapping(value="/", method=RequestMethod.GET)  
  2. @PostMapping("/")  
  = @RequestMapping(value="/", method=RequestMethod.POST)  
    
    
# Dao, Service는 인터페이스와 클래스 파일이 한 세트로 구성되며 필요한 서비스들의 목록을 나열 할 수 있는 인터페이스를 만듦.
인터페이스는 내부에 메소드 본체를 가질 수 없으며 메소드 헤더만 정의하고 이를 구현하는 클래스에서 본체를 구현 할 수 있음  
인터페이스로 구성시 장점은 확장성과 유연성을 꼽을 수 있음.  
대부분 '구현함'의 의미로 Impl을 많이 씀.  
오버라이드를 이용해 메소드 본체를 작성하며, 해당 클래스가 Service라는 것을 알리기 위해 @Service를 붙여줌  
@Controller에서 시작해 @Service -> @Repository까지 이어지는 또 그러한 과정에서 @Autowired로 이루어지는것을 확실히 알아야함  
    
    
# 테스트
 - spring-boot-starter-web에 의존성을 가지며 Jackson 라이브러리가 기본으로 포함되어있음  
 - JSON 마샬링  
	- 마샬링   
		1. 객체지향 객체의 직렬화는 객체와 관계된 메소드를 포함하지 않음  
		2. 객체의 메모리 표현을 저장 공간 또는 전송에 적합하며, 데이터 포맷으로 변환하는 과정임.  
 - @Service : Spring Boot에서 이 Class를 감지, 객체 생성..???  
 - @RunWith : Class내의 Test method를 지정.  
 	      Junit framework 확장판.  
	      스스로 고유한 기능을 만들어 추가가 가능  
 - @WebMvcTest : Mock MVC 기반의 테스트 생성  
   Controller를 수행을 돕는 최소한의 Spring Boot Application 생성.  
 - 모키토 : 단위 테스트를 위한 Java Mocking Framework  
 - HTTPie가 파라미터를 JSON 요청 변환하면 Jackson 라이브러리가 읽을 수 있게 변환  
   기본적으로 Jackson은 JSON 메시지를 객체에 매핑시 getter/setter 이용  
	 - HTTPie : python기반의 콘솔용 http client로 http 개발 및 디버깅 용도로 사용 가능하며 JSON 탑재  
	 - cURL : 다양한 프로토콜을 지원하는 데이터 전송용 command Line Tool  
   
   
# Thymeleaf
 - spring boot 권장 사용  
 - HTMl Tag를 그대로 사용하며 Model의 data th:text="${ }"와 [[${ }]]로 이용   
	  <pre>
		th:href="@{test.html}" href="#" //a 태그에 사용시
	  </pre>
 - MessageSource에서 메시지 반영 : #{}  
 - spring-boot-starter-thymeleaf  
  : 위의 의존성 추가로 Thymeleaf 라이브러리와 Thymeleaf Spring Dialect를 가져 올 수 있으며,  
    스프링 부트는 자동으로 ThymeleafViewResolver를 구성  
    ThymeleafViewResolve는 뷰를 해석, 렌더링하기 위해 ThymeleafTemplateEngine이 필요.  
 - Model(Model Class = 요청 처리 메소드)  
   모든 Method는 org.springframework.ui.Model의 메소드 인자로 가지고 있으며 모델에 데이터를 넣을 수 있음  
 - return시 렌더링 할 View명을 반환하여 ThymeleafViewResolver로 전달되어 classpath:/templates/study/test.html의 경로가 됨.  
   **왜냐하면 Thymeleaf file 위치는 반드시 src/main/resource/templates여야 하기 **  
 - @RequestParam : 요청 인자가 메소드 인자로 사용  
  		   지정한 키가 빈값으로 들어온다면(null) Bad Request로  400번대 에러 발생.  
		   Default Value를 지정하여 해당 버그를 방지해야함.  
		   또한 Map으로도 받아 올 수 있음  
   ex) @RequestParam("data") String text  
       @RequsetPara HashMap<String, String> TestMap  
  
  
# 예외 처리
 : Spring boot에서 default가 활성화  
   Server.error.whitelabel.enable를 false로 놓으면 전체 비활성화 가능.  
    -> 예외 처리는 spring, spring boot에서 제공되는 일반적 예외 처리 메커니즘 대신 서블릿 컨테이너에서 처리됨  
   일반 오류 페이지 구성 시 사용하는 속성  
     1. 주로 모델에 어떤 항목을 포함할지  
     2. 선택적으로 화면에 표시  
 - 기본 일반 오류 페이지 사용자 정의  
 - src/main/resources/templates에서 error.html 추가시 오류 페이지 변경 가능  
 - server.error.include-exception=true && server.error.include-stacktrace=always  
 	=> 맞춤형 오류 페이지에 예외 클래스명, strack trace가 포함됨  
 ?? stack trace : 예외가 던져지는 중간에 애플리케이션이 있던 메소드 호출 목록  
200808 >> 예외처리를 제대로 공부하지 않으면 시간낭비를 많이하겠구나 싶었다.디버깅하는 연습도 꾸준히 해야겠구나.  
 - 특정 HTTP 상태 코드별 오류 페이지 추가  
   : src/main/resources/templates/error에 <http-status>.html을 추가 후 적용 가능.  
 - @ErrorAttributes : 오류가 발생시 응답 모델을 생성  
		      bean 등록시 BasicErrorController는 해당 ErrorAttributes를 사용  
		      기본적으로 사용, 구성되는것은 DefaultErrorAttributes  
 - LibraryApplication :   
  	
  
# Application 국제화 (다국어 지원)
 : 메시지 설정파일을 모아놓고 로컬라이징하여 접속하는 세션(Accept-Language 헤더 )의 지역에 따라 자동으로 로딩  
   ApplicationContext 컨테이너가 초기화 될 때 MessageSource를 구현한 bean을 찾아서 내부 프로퍼티 인 MessageSource에 등록하는데,  
   이때 id가 messageSource인 것을 찾아 즉시 캐스팅 시도를 하기 때문에 구현체가 아니라면 컨테이너 초기화에 실패한다함.  
 - @MessageSource : 메시지를 다국화함.  
 		    페이지 템플릿에 특수한 Tag를 사용 or 메시지 find  
 - properties 파일명 형식 :  basname_언어코드_국가코드.properties  
   
   
# 사용자 언어 결정
 - LocaleResolver : DispatcherServelet이 LocalResolver를 통해 사용자 식별  
 		   언어 변경이 가능하게 하려면 LocaleResolver를 사용할 것  
		   즉,접속한 사용자에 맞는 언어를 보여주기 위해 브라우저의 useragent, 쿠키, 세션을 보고 locale 값을 처리  
 	1. LocaleChangeInterceptor : 요청을 가로채서 언어를 감지.Request의 Locale 정보에 의해 Locale을 변경(사용자가 url를 통해 언어를 변경할 경우 사용)  
				     현재 HTTP 요청에 특정 인자(paramName, 기본값 local)를 확인하여 감지.  
				     LocaleChangeInterceptor를 빈으로 추가 후 interceptor로 등록하여 사용  
				     여기서 Interceptor 등록은 WebMvcConfigurer에서 addInterceptors 메소드 이용  
	2. HandlerInterceptor : 세션/쿠키 검증  
 - spring.mvc.locale.resolver  
 	1. ACCEPT(기본값) : AcceptHeaderLocaleResolver(Spring boot에서 기본으로 등록된 언어 Resolver) 생성  
			   AcceptHeaderLocaleResolver : HTTP 요청의 Accept-Language 헤더를 감지하여 언어 결정(브라우저 언어 설정에 의한 값)  
			   200809 >> 앞서 공부한 Application 국제화랑 비슷해보임.  
			   	     책에 보면 사용자 운영체제에 설정된 언어를 수정할 수 없어 사용자 언어 변경이 불가능하다 되어있음.  
				     그럼 아래의 FixedLocalResolver와 뭐가 다른거지..??  
				     >> 헤더에서 Locale 정보를 가져온다는게 다른점이었네.언어 변경이 불가능한게 맞음!  
	2. FIXED : FixedLocaleResolver -> 항상 동일하게 고정된 언어를 반환하지만(기본값은 JVM 기본 언어 반환.웹 요청관는 상관 없음) defaultLocale 속성 설정 시 다른 언어로 반환이 가능  
		    			  setLocale() 메서드를 지원 안함.  
 - SessionLocalResolver : 사용자 세션에 사전 정의된 속성 감지후 언어 결정  
 			  세션이 없다면? -> Accept-Language HTTP 헤더로부터 기본 언어 설정  
			  		  defaultLocal속성을 설정할수 있고, 언어가 저장된 세션 속성을 변경해 사용자 언어도 변경이 가능  
 - CookieLocaleResolver : 사용자 브라우저 쿠키를 감지하여 언어 결정.  
 			  쿠키가 없다면? -> Accept-Language HTTP 헤더로부터 기본 언어 결정  
			  		   defaultLocale 속성을   
	- cookieName  
	- cookieMaxAge : cookie 유효 시간  
			 저장된 값이 -1이라면? -> 브라우저 종료시 쿠키 비활성화  
 - 사용자 언어 변경 밥법 : LoclaResolver.setLocale() 호출 or LocaleChangeInterceptor 매핑  
   
  **결국 다국화 처리를 위해선 이런 조합이 필요함!!!  
  LocaleChangeInterceptor + messageSource**  
  
  
  # 내장된 서버 선택 및 구성
   :  spring boot는 톰캣, 제티, 언더토우가 classpath에 있는지 확인후 이에 맞게 컨테이너를 구성함.  
    - spinrg-boot-starter-web 의존성을 갖게되면 하위의 아티펙트 의존성을 갖게됨(다른 서블릿으로 변경하려면 아래와 같음)  
    	1. spring-boot-starter-tomcat  
	2. spring-boot-starter-jetty  
	3. spring-boot-starter-undertow  
		  <pre>
			<dependency>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-starter-web</artifactId>
				<exclusions>
					<exclusion> // 메이븐 의존성 제외
						<groupId>org.spirngframework.boot</groupId>
						<artifactId>spring-boot-starter-tomcat</articactId>
					</exclusion>
				</exclusions>
			</dependency>
			<dependency> //제티 컨테이너 추가
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-starter-jetty</artifactId>
			</dependency>
		  </pre>
   - 내장된 컨테이너 모두 서블릿 명세를 지원하며 JSP 페이지를 지원.(default : 활성화)  
   - spring MVC 사용시 속성을 저장할 때 HTTP 세션을 사용.  
     일반 서블릿 구성도 HTTP 세션과 저장된 방법에 대한 구성이 가능  
   -  server.servlet.context-path or server.servlet.path  
  	1. contextPath : /JSP : 프로젝트명  
	2. serveltPath : /study/test.do : servlet 주소  
    
    
# SSL 구성
 : 인증을 받아 키 저장소에 적재 후 server.ssl 네임스페이스 설정을 이용해 키 저장소 구성.  
     HTTPS만을 통해 접속이 가능하도록 구성(default값이 HTTPS)  
 - server.ssl.key-store : 내장 컨테이너 구성을 위해 키 저장소 정보를 입력  
    			    SSL이 필요함(application 보안 인증서 필요)  
     - 자가 서명 인증서  
        1. keytool을 통해 인증서 생성  
	<pre>
				(알고리즘)	  (별칭 = 파일명)    (key 저장소)
		keytool -genkey -keyalg RSA -alias study-test -keystore study-test.pfx
		-storepass password -validity 3600 -keysize 4096 -storetype pkcs12
				    (3600일 동안 유효)
	</pre>  
	?? keystore = 비밀키, 관련된 인증서나 인증서 체인을 가지고 있음  
	2. 인증된 기관에서 발급받지 않앟기 때문에 안전하지 않다는 경고 발생  
     - 키 장소를 위한 spring boot 구성  
	     <pre>
		server.ssl.key-store=classpath:study-test.pfx
		server.ssl.key-store-type=pkcs12 //저장소 유형
		server.ssl.key-store-password=password
		server.ssl.key-password=password
	     </pre>
 - HTTP and HTTPS  
     >> 차이 : HTTP는 정보를 Text로 주고 받음.HTTPS는 여기에 S(Secure Socket)을 추가.  
     	      즉, 기본적인 것은 동일하지만 '보안' 요소가 추가됨   
	      default값은 HTTPS이며  
	      	HTTP port : 8080  
		HTTPS port : 8443  
    1. TomcatServletWebServerFactory : 내장된 톰캣 접속자 추가 설정(bean에 추가 필요), @configuration 어노테이션이 추가된 class내에 있어야 함.  
    	- 일반적 spring boot는 컨테이너 감지 후 사용할 WebServerFactory 선택.  
	- HTTP와 HTTPS를 동시에 사용하려면 수동으로 추가가 필요  
	  Spring 보안을 사용하여 HTTP 대신 HTTPS 강제 가능(리다이렉션 내용 같음)  
 - HTTP를 HTTPS로 리다이렉션  
  : HTTP, HTTPS 모두 사용 가능하게 만들었더라도 모든 요청은 HTTPS로 가는게 좋다고 함.  
     	- 모든 URL은 SecurityConstraint로 보호해야함.  
	- Spring Boot에서 특수한 TomcatContextCustomizer를 사용해 톰캣 시작전 컨텍스트의 사전 처리를 가능하게 함.  
	
  -----------------------------------------------------------------------------------------------------------------------------------------------------------
  200809 >>
	    참고하면 좋을것 같은 블로그를 공부하기 위해 적어둔다.
	    https://unabated.tistory.com/entry/%EB%8B%A4%EA%B5%AD%EC%96%B4-%EC%B2%98%EB%A6%AC-localeResolver-messageSource
