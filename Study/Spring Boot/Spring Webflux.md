# Spring Webflux  
 : 완전한 Non-blocking으로, Reactive Stream back pressure(받는 컴포넌트에서 보내는 컴포넌트에게 얼마큼의 데이터를 소화할 수 있는지 알려줌.속도가 다른 컴포넌트 통신에서 유리)를 지원  
   Spring Web MVC와 Spring Webflux 각각은 Spring Framework 안에서 대칭적으로 존재  
   각 모듈은 선택적이며, 어플리케이션은 하나, 또는 다른 모듈, 경우에 따라 둘 모두 사용 가능  
   Web Application에서 Reactive program을 제공하는 Framework  
   Mono와 Flux로 데이터를 응답    
  
	?? Webflux : 웹의 스트리밍 처리 담당  
	  
	?? Reactive : 이 용어는 변경에 대한 반응에 중점을 두어 만들어진 프로그래밍 모델을 가르킴  
				  네트워크 컴포넌트는 I/O 이벤트에 반응하며, UI 컨트롤러는 이벤트에 반응  
				  Non-bloking Reactive => 동작 중단 대신 명령의 완료 또는 데이터의 제공 등의 알람에 반응하는 방식을 취함  
  
	?? reactive streams에서 중요한 용어  
		- 스트리밍 처리  
		- 논블로킹(asynchronous)  
		- 백프레셔(back pressure) : 다이나믹 풀 방식의 데이터 요청을 통해 구독자가 수용할 수 있는 만큼 데이터를 요청  
				  
- org.springframework.http.server.reactive 패키지에서 ServerHttpRequest와 ServerHttpResponse를 모두 가져옴  
  인터페이스로 실행에 사용되는 컨테이너에 다라 인터페이스 인스턴스가 생성  
- HttpHandler : 요청과 응답을 처리하는 싱글 메서드를 가진 단순한 계약.의도적으로 작게 설계되었고 목적은 오로지 각기 다른 HTTP 서버 API에 대응하는 최소형 추상화  
- 서블릿 3.1 컨테이너에서 실행될 때 serveletHttpHandlerAdapter는 일반 서블릿 세계에서 반응형 세계로 적용하기 위해 사용  
- Netty 같은 기본 반응형 엔진에서 실행될 때는 ReactorHttpHandlerAdapter가 사용  
	-> Netty ? protocol server, client 등 java network application 개발을 위한 비차단 입출력 client-server framework  
- 웹 요청이 스프링 웹플럭스 애플리케이션으로 들어오면, HandlerAdapter가 먼저 요청 받음  
  그 후 요청을 처리하는 데 필요한 스프링의 애플리케이션 컨텍스트에 설정된 각 구성 요소를 구성  
- Spring Webflux에서 Controller class를 정의시 @Controller or @RestController 필요  
- Controller Class가 적절한 Handler Method를 선택하면 요청과 함께 핸들러 메소드의 로직을 호출  
  핸들러 메소드의 로직은 진행 중인 흐름의 일부를 형성할 많은 입력 인수에 정보를 추가하거나 제거  
  유연한 접근 방식을 제공하기 위해 핸들러 메소드의 반환값은 뷰의 구현체가 아닌 확장자가 빠진 논리적인 뷰의 핸들러 메소드의 반환 값은 String이나 void임  
  컨트롤러 클래스가 뷰를 수신하면 뷰 리졸버를 통해 논리적인 뷰 이름을 특정한 뷰 구현체로 해석  
  뷰 리졸버는 ViewResolver 인터페이스를 구현한 웹 애플리케이션 컨텍스트에 구성된 빈  
  논리적인 뷰 이름에 대한 특정뷰 구현체를 반환
- 반응형 요청은 웹플러스 필요 : spring-boot-starter-webflux에 의존성을 추가
  <pre>
	  <dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-webflux</artifactId>
	  </dependency>
	  //spring-webflux와 프로젝트 Reactor 의존성 같은 필요한 의존성을 가져오며 반응형 실행의 기본 모듈인 네티가 포함됨
	  //spring boot는 반응형 실행을 감지하고 server.*properties을 사용해 구성함
  </pre>
- spring webflux controller 생성
	: 애노테이션 기반 컨트롤러 클래스는 특정 인터페이스를 구현하지 않거나 특정 기본 클래스를 확장하는 임의의 클래스가 될 수 있음  
	  핸들러 메소드는 다양한 인수를 허용할 만큼 충분히 유연
- @GetMapping : 메소드를 컨트롤러의 HTTP GET Handler Method로 쓸 때 사용되는 기본 HTTP GET 메소드가 선언되지 않으면   
                ServletException이 발생하므로 컨트롤러는 반드시 최소 하나의 URL 경로와 하나의 메소드를 갖고 있어야 함.

- 반응형 컨트롤러의 단위 테스트
  - @WebFluxTest : 웹 인프라가 포함된 최소한의 애플리케이션 컨텍스트를 시작하고, MockMvc를 사용해 컨트롤러 테스트
  - 일반적인 단위 테스트와 완전한 통합테스트 사이라고 보면 됨
  - 컨트롤러를 인스턴스화하고 간단히 테스트할 메소드를 호출
  - 반응형 테스트 모듈 StepVerifier를 사용.메소드가 호출되고 결과는 StepVerifier를 사용해 검증

- 반응형 컨트롤러의 통합 테스트
  - @SpringBootTest를 사용하여 테스트를 진행하며 다른 모든 빈을 포함한 전체 애플리케이션 테스트가 가능해야함
  - webEnvironment로 원하는 환경을 만들 수 있음
    - RANDOM_PORT, MOCK(default), DEFINE_PORT, NONE
  - webEnvironment로 MOCK(기본값)을 사용할 때, 테스트를 위해 WebTestClient를 가져오려면 @AutoConfigureWebTestClient 어노테이션 추가
	
	
# 반응형 REST 서비스의 배포와 사용
- 반응형은 응답 값 Mono or Flux로 래핑
	- Mono : 단일 데이터, 0~1개의 데이터 전달
	- Flux : 리스트 데이터, 0~N개의 데이터 전달
- 스트리밍 : HTTP 응답 스트리밍 시에는 연결이 끊어진 클라이언트를 가능한 빠르게 감지하기 위해 데이터를 주기적으로 전송하는 것이 중요
	- content-Type
		1. application/json
		2. application/stream+json : 결과 스트리밍 하려면
			@GetMApping(produces = MediaType.APPLICATION_STREAM_JSON_VALUE) : 일부 결과가 준비됐을 때 스프링에 결과를 스트리밍하도록 지시
		3. text/event-stream
			@GetMApping(produces = MediaType.TEXT_EVENT_STREAM_VALUE) : 결과가 더 없을때까지 부드럽게 스트리밍 됨

- 통합 테스트
	- @DirtiesContext : spring의 텍스트 컨텍스트 프레임워크에게 해당 클래스의 테스트에서 애플리케이션 컨텍스트의 상태를 변경한다는것을 알려줌.
	<pre>
		@DirtiesContext(classMode = DirtiesContext.ClasMode.AFTER_EACH_TEST_METHOD)
	</pre>
	- @AutoConfigureWebTestClient는 Mock 환경에서 WebTestClient를 가져오기 위해 필요
	  WebTestClient를 사용하면 요청을 쉽게 작성해 서버로 보낼 수 있으며 응답을 보낸 후에 결과에 대한 기댓값도 확인 가능

# 템플릿 엔진으로 타임리프 사용
- spring-boot-starter-webflux 및 spring-boot-starterthymeleaf 의존성 추가 시, 스프링 부트가 웹플럭스 애플리케이션에서 사용하는 타임리프를 자동으로 구성
	<pre>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-webflux</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-thymeleaf</artifactId>
		</dependency>
	</pre>
	
	- 의존성을 추가하면 Thymeleaf 라이브러리뿐만 아니라 Thymeleaf Spring Dialect도 추가되어 통합됨
	   ?? Dialect : 
	ThymeleafReactiveViewResolver를 자동으로 구성된
	  => ThymeleafReactiveViewResolver : 뷰를 분석하고 렌더링하려면 ThymeleafISpirngWebFluxTemplateEngine(SpringDialect로 사전 구성돼 타임리프 페이지에서 SpEL을 사용)이 필요
	
	
	- 애플리케이션을 실행하고 페이지를 열 때, 페이지가 렌더링되기 전까지 잠시 기다려야 함  
	  페이지를 렌더링하고 모델에 Flux가 있으면 Flux가 전체 소비될 때까지 대기  
	  그 후 페이지 렌더링 시작  
	  Flux 대신 Collection을 검색하는 것처럼 기본적으로 동작  
	  페이지를 빠르게 렌더링하고 페이지 스트리밍을 시작하려면 Flux를 ReactiveDataDriverContextVariable에 래핑  	
	  ?? ReactiveDataDriverContextVariable : 
	
# 웹플러스와 웹 소켓
- javax.websocket-api 추가하여 핸들러를 구현하는 반응형 WEbSocketHandler 인터페이스로 반응형 application에서 웹 소켓을 사용 할 수 있음
 <pre>
	<dependency>
		<groupId>javax.websocket</groupId>
		<artifactId>javax.websocket-api</artifactId>
		<version>1.1</version> // spring webflux는 websocket 1.1 버전을 사용해야한다
	</dependency>
 </pre>
- 핸들러는 SimpleUrlHandlerMapping을 사용해 URL에 매핑  
   WebSocketHandlerAdapter가 들어오는 반응형 런타임 요청을 이해하게 해야함  
   네티를 사용하기 떄문에 ReactorNettyRequestUpgradeStrategy를 사용해 WEbSocketService를 구성해야함  
- 통합 테스트
 1. 서버 시작
 2. 웹 소켓 연결은 서버에 연결
 3. 수동으로 메시지를 보내고 응답 확인할
 
- @LocalServerPort : 실제 포트값을 가져오며 연결할 URL를 구성
 <pre>
	@LocalServerPort
	private int port;
 </pre>
