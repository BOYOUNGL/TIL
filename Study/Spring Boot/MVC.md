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
  ex) <a th:href="@{test.html}" href="#">
 - spring-boot-starter-thymeleaf
  : 위의 의존성 추가로 Thymeleaf 라이브러리와 Thymeleaf Spring Dialect를 가져 올 수 있으며,
    스프링 부트는 자동으로 ThymeleafViewResolver를 구성
	ThymeleafViewResolve는 뷰를 해석, 렌더링하기 위해 ThymeleafTemplateEngine이 필요.
 - Model(Model Class = 요청 처리 메소드)
   모든 Method는 org.springframework.ui.Model의 메소드 인자로 가지고 있으며 모델에 데이터를 넣을 수 있음
 - return시 렌더링 할 View명을 반환하여 ThymeleafViewResolver로 전달되어 classpath:/templates/study/test.html의 경로가 됨.
   **왜나하면 Thymeleaf file 위치는 반드시 src/main/resource/templates여야 함**
 - @RequestParam : 요청 인자가 메소드 인자로 사용
	ex) @RequestParam("data") String text
