Spring MVC - 비동기  
[참고] https://brunch.co.kr/@springboot/401  
Servlet API 초기, container가 요청을 받아 처리, 응답 결과가 client에게 전달될 때까지 Thread가 block  

- Servlet : servlet program 개발시 반드시 구현되야하는 method를 선언하고 있는 interface  
- servlet container : 서블릿 컨테이너는 요청이 들어 올때마다 새로운 java Tread를 만듦  
		      (container는 servlet의 생성 ~ 소멸 일련의 과정(Life cycle) 관리)   
		      tomcat과 같은 WAS가 java file을 complie, class 생성, memory를 올려 Servlet 객체 생성  

동기 vs 비동기 : 처리 예정 작업들을 어떤식으로 '흐름'으로 처리할지  
블로킹 vs 논블로킹 : 어떠한 작업이 처리되야하는 전체적인 작업 '흐름'을 막거나 안 막거나  
(순차적으로 작업이 처리되야 한다면 '동기' 혹은 '블로킹'방식 사용)   
  
XmlHttpRequest : client에 비동기로 message를 전달. but, 여전히 요청과 응답주기로만 이루어진다는것 같음.  
                 client와 server간 통신 가능.  
                  - 단방향 통신으로 서버-전송-이벤트 방식을 사용하는 것.  
                  - 양방향 통신에는 웹소켓이 있음  

- spring-boot-starter-web : 일반 Web application bulid 시 의존성 추가
- spring-boot-starter-sebflux : 반응형 Application build시 추가

# 서블릿 실행 순서  
 : 서블릿의 실행 순서 -> 서블릿 컨테이너가 관리(개발자 관리X, 블릿에 의해 사용자가 정의한 서블릿 객체가 생성되고 호출되고 소멸 됨)  
 => 개발자가 아닌 프로그램에 의해 객체들이 관리되는것 = IOC(Inversion of Control)  
 - 서블릿 컨테이너는 클라이언트로부터 처음 요청이 들어오면 현재 실행할 서블릿이 최초의 요청인지 판단하고 없으면 해당 서블릿을 새로 생성(최초 1회 실행)  
 

# Controller와 TaskExecutor로 비동기 요청 처리
  : 요청이 들어오면 비동기로 처리되며 HTTP 요청 처리 Thread 차단 (Servlet container의 부하를 줄임)  
  - 비동기 프로그래밍 : main Thread에서 분리된 Thread에서 동작하며 처리 완료 후 성공 혹은 실패를 main Thread에서 알려주는 non-blocking  
  - Callcable : 보통 값을 바로 반환하는 것 대신에 controller는 java.util.concurrent.Callable를 먼저 반환 후 spring에서 관리하는 별도의 Thread에서 값 반환  
		Java 1.5부터 사용 가능하며 타임아웃 설정은 불가  
    - 특징
      1. controller는 Callable 객체를 바로 리턴
      2. Spring MVC는 내부 TaskExecutor에게 해당 작업 전달
      3. DispatherServlet 그리고 모든 필터들은 Servlet Container Thread에서 벗어나지만 response는 여전히 열린상태
      4. Callable은 결과를 생성하고 Spring MVC는 Servlet container에게 재 요청을 진행
      5. DispatherServlet은 Callable에게서 반환된 결과값을 보고 다시 작업 진행
	- DeferredResult : Callable 대신 사용 가능하며 Class 인스턴스 생성 후 비동기 처리 작업을 한다음 내부에서 setResult()를 이용해 DeferredResult 결과값 설정
  - CompletableFuture : Future + CompletionStage, java8부터 사용 가능하며, Executor 지정 가능
	- future : Java 5부터 미래시점에 결과를 얻을 수 있음  
	           시간이 걸리는 작업을 Future로 작성하면 호출자 Thread가 결과를 기다리는 동안 다른 작업 가능  
	           but..복잡한 로직에 있어서 구현이 어렵다 함  
             200815 >> 아...어렵다 어려워...
	- completionStage : 각 단계에서 발생한 에러 관리 및 전달
	- TaskExecutor : 원래 스레드 풀에 대한 추상화가 필요한 곳에 다른 스프링 컴포넌트를 제공하기 위해 만들어진 것
	- supplyAsync (void 일 경우 runAsync) : 직접 Thread를 생성하지 않고 작업을 asyn으로 처리 가능(Lambda로 인자 전달)
		- supplier
		- Executor : Thread Pool Task Executor 인스턴스를 정의
		- supplier와 Executor 동시 사용 : 비동기 처리 TaskExecutor 재사용 법
		- supplier만 사용 : JVM에서 가능한 기본 분기/결합 Pool에서 수행
 - 비동기 컨트롤러 테스트
    - @WebMvcText : Controller Test에 필요한 최소한의 Spring Boot Application을 bootstrapping  
		    Test에 자동 연결할 Spring MockMvc를 자동으로 구성
	  * bootstrapping : ??
	  - @RunWith : jUnit framework가 Test 실행시 Test 실행 방법을 확장할 때 사용
	  - 비동기와 일반 Web Test의 차이
      1. 비동기 dispatch로 시작했는지 검증
      2. asyncDispatch 적용
      3. 예상되는 응답 확인
    - 비동기 처리 구성 : WebMvcConfigurer의 configureAsyncSupport method 재정의  
	-> 이 메소드의 재정의는 AsynsSupportConfigurer에 접근 가능, 사용할 AsyncTaskExecutor 설정
        
        
# 응답 작성
 - ResponseBodyEmitter : 비동기 Stream 형태로 server에서 client로 data 전달  
		         하나의 결과 대신 다수의 객체를 Client에게 반환하고자 할대 유용.  
		         객체를 전달 시 HttpMessageConverter를 사용하여 결과를 반환  
 	- .send() : 결과를 하나씩 반환
	- .complete() : 응답을 전달할 Thread에서 요청을 완료 후 처리를 위해 반환
	- completeWithError : 예외 발생이 일어나면 그 내용을 전달하기 위해 사용
 - sseEmitter : ResponseBodyEmitter와 비슷하며 Spirng5에서 지원하는 HTTP 통신 기반임.  
		요청 처리 메소드에서 이벤트 전달 시 SseEmitter 인스턴스 생성 후 반환
	- 서버-전달-이벤트 : server -> client 전달되는 메시지, text/event-stream인 Content-Type header를 가짐
	- text/event-stream : Stream Event임을 나타내며, 이때 Stream은 열린상태가 유지되어 Event alarm을 수신받음.
	- event() 팩토리-메소드 : 인스턴스 생성 후 id와 event 항목을 추가 가능.
	- HttpMessageConverter : 작성된 객체 JSON 타입으로 변환하며 data 태그에 작성됨
	- SseEvemtBuilder : Event에 더 많은 정보를 추가.

# 웹 소켓
 - WebSocket : **client마다 원하는 topic의 구독 신청을 해두고 어떠한 사용자가 해당 topic에 메시지를 보내면 그 topic을 구독하는 모든 사용자에게 보냄**
	1. 특정 Port를 통한 실시간 양방향 통신
	2. web socket은 HTTP가 해결 할 수 없었던, Client 요청이 없음에도 Server로부터 응답받는 상황을 해결하기 위한 해결책
	3. 실시간 서비스 또는 짧은 시간에 많은 양의 정보를 보낼때 적합
	4. 익스플로러 구버전 지원 안됨 - 미지원 브라우저에 대한 문제점 해결 : Socket.io, SockJS
 - HTTP(HyperText Transfer Protocol) : 하이퍼미디어 문서를 전송하기 위한 프로토콜
	1. 단방향 통신
	2. Port80을 사용, Request와 Response로 구성됨
	3. Client의 요청이 있을 경우만 응답하여 정보 전송 후 바로 연결 종료
 - spring-boot-starter-websocekt : 웹 소켓 지원을 자동으로 구성
 - @EnableWebSocekt : 웹 소켓 활성화, 웹 소켓에 대해 대부분 자동 설정  
		      @springBootApplication or @Configuration이 붙은 Class에서 사용  
 - WebSocketHandler : web socket message와 생애주기 Event 처리를 위해 WebSocketHandler를 생성하고 endpoint URL을 등록
	- webSocketHandler method
		1. afterConnectionEstablished : 웹 소켓 접속이 열리고 사용 준비시 호출 (웹 소켓 연결 = front에서 web socket이 정확한 경로를 잡아 생성되는 것)
		2. handleMassage : 웹 소켓 메시지가 핸들러에 도착했을 때 호출 (session에서 메시지 수신됫을때 실행)  
						   메시지 타입에 따라 handleTextMessage(), handleBinaryMessage()가 실행 됨.
		3. handleTransportError : 오류 발생 시 호출
		4. afterConnectionClosed : 웹 소켓 접속 종료 후 호출 (session 종료)
		5. supportsPartiaMessages : 핸들러가 부분 메시지 지원시 true일 경우 웹 소켓 메시지가 여러 호출 통해 도착
 - Echo Server : 소켓이 처음 나올때 출력되는 server라는 이름을 썼음.echo는 관례적으로 붙임
 - TextWebSocketHandler를 확장해 EchoHandler를 만들며 afterConnectionEstablished나 handle TextMessage method를 구현함.  
 - TextMessage : 전송 받은 메시지 정보
 - 메시지 전달 과정
	1. 접속 구성이 완료되면 TextMessage가 client에게 접속 구성 완료 메시지 전송.
	2. 수신되면 페이로드(실제 메시지)는 추출되어 RECEIVED를 접두어로 추가한 후 client에 전달
	3. URL이 포함된 핸들러 등록
	4. WebSocketConfigurer를 구현한 @Configuration Class 생성후 registerWebSocketHandlers method에 등록
	5. 해당 인터페이스를 EchoApplication class에 추가
 - registerWebSocketHandlers : 
 - WebSocketSession : spring에서 WebSocket connection이 맺어진 session
 - @ClientEndPoint : endpoint는 데이터를 보내고 받음.sendMessage()를 통해 메시지 보냄
	1. @OnOpen : 접속이 열릴 때(handshake가 완료, connection이 맺어짐)
	2. @OnClose : 접속이 닫힐 떄
	3. @OnMessage : 메시지가 수신될 때 (socket에서 정보 수신 시 실행되며 evt.data로 정보가 들어옴)
 - handshake : HTTP에서 Web Socket으로 프로토콜 전환

# STOMP와 웹 소켓
 - STOMP (Streaming Text Oriented Message Protocol) 
  : Streaming Text 기반의 간단한 Stream Message Protocol.  
  단순하며 읽기 쉽다는 장점(TCP 기반)이 있으나 서버측에서 구현할 것이 많음
  HTTP와 동일하게 Fram 기반의 프로토콜인데,
  이때, Frame이란? 주소와 명령, 명령 수행을 위한 Data가 모두 포함된 데이터로 메시지를 수신할 대상 집합을 관리
	- 텍스트 기반의 매우 간단한 프로토콜인데
	- 메시지 브로커
	- 양방향 네트워크 프로토콜 사용 가능
 - Websocket 과 STOMP의 차이
  : **Websocket은 Server와 Client의 1:1 통신(연결이 끊어지면 message가 사라질 수 있다고 함)**  
  **STOMP는 메시지 브로커로 1:N의 관계가 될수도 있으며 메시지를 서버에 저장해두면 클라이언트가 주기적으로 가져갈 수 있음.**  
 - @MessageMapping : Message를 수신 받기 위해 해당 어노테이션을 추가하며 수신할 목적지가 어디인지 알려줌.  
	**1. @sendTo -> 1:n으로 메시지를 보낼때 사용하는 구조로 default 경로는 /topic**  
	**2. @SendToUser -> 1:1로 메시지를 보낼때 사용하며 default 경로는 /queue**  
 - @EnableWebSocketMessageBroker : 웹 소켓을 통해 메시징을 사용 가능하도록 함.
	1. /topic : 브로커에게 전달
	2. /app : 메시지 핸들러(@MessageMapping이 붙은 메소드와 같은)에 전달
	- configureMessageBroker : 한 client에서 다른 client로 메시지 라우팅 할때 사용하는 브로커 구성
 - MessageConverter
   : @RequestBody or @ResponseBody와 같은 어노테이션을 작성하면 spring이 메시지 컨버터를 이용해 HTTP 요청/응답을 메시지로 변환한다고 함.
	<pre>
		@ResonseBody
		@RequestMapping(value="/test", method=RequestMethod.POST)
		public void Test(**@RequestBody String data**) { 
		}
	</pre>
   1. @RequestBody : 파라미터 타입에 맞는 메시지 컨버터 선택, HTTP 요청 본문을 통째로 메시지로 변환하여 파라미터에 바인딩
   2. @ResponseBody : return 타입에 맞는 메시지 컨버터 선택 후 리턴값을 통째로 메시지로 변환후 리턴
   * GET 방식은 HTTP 요청 본문이 없어 @RequestBody를 사용할 수 없으며 @RequestParam or @ModelAttribute를 사용해야 함  
   [참고] https://joont92.github.io/spring/MessageConverter/
 - @LocalServerPort : 해당 어노테이션이 있으면 정수 변수가 실제 port를 가져 올 수 있음
	<pre>
		@LocalServerPort
		private int port;
	</pre>
 - 소켓 연결 disconnect
	<pre>
		//책에서 브로커에 접속된 세션 정리를 위한 코트 예시
		@After
		public void cleanUp() {
			this.sessions.forEach(StompSession::disconnect);
			this.sessions.clear();
		}
	</pre>
 - completableFuture : 람다 기반 비동기식 병렬 프로그래밍 기법  
   
	[참고] https://brunch.co.kr/@springboot/267  
	       https://12bme.tistory.com/546
 --------------------------------------------------------------------------------------------------------------------------
 [참고]
 https://12bme.tistory.com/555  
 https://12bme.tistory.com/565

200816 >> 와...이거 대체 먼 내용이냐...ㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋ  
200818 >> 내가 정리했지만...참 읽기 힘들다 
