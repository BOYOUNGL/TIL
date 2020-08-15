Spring MVC - 비동기  

Servlet API 초기, container가 요청을 받아 처리, 응답 결과가 client에게 전달될 때까지 Thread가 block  

- Servlet : servlet program 개발시 반드시 구현되야하는 method를 선언하고 있는 interface  
- servlet container : 서블릿 컨테이너는 요청이 들어 올때마다 새로운 java Tread를 만듦(container는 servlet의 생성 ~ 소멸 일련의 과정(Life cycle) 관리)  
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
 : 서블릿의 실행 순서 -> 서블릿 컨테이너가 관리(개발자 관리X)  
   즉, 서블릿에 의해 사용자가 정의한 서블릿 객체가 생성되고 호출되고 사라짐.  
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
 
=======================================================================================================================================
 [참고]
 https://12bme.tistory.com/555
 https://12bme.tistory.com/565
