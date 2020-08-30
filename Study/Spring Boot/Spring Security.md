- spring-security : 애플리케이션의 보안(인증, 권한, 인가 등)을 담당
- spring-boot-starter-security : 스프링 보안 라이브러리
	1) 추가되는 의존성
		- spring-security-core
		- spring-security-config
		- springsecurity-web
	2) 모든 엔드포인트를 자동으로 보호
	3) 시작 시 생성된 암호로 로그인	
  <pre>	
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-security</artifactId>
    </dependency>
  </pre>
- 스프링 부트는 스프링 보안을 기본 인증과 폼 로그인으로 인증, 보안을 위한 HTTP 헤더 활용, 서블릿 API 통합, 익명 로그인, 리소스 캐싱 방지 등을 설정

 1. 테스트
	- @WebMvcTest를 이용하면 보안 인프라가 자동으로 적용
	<dependency>
		<groupId>org.springframework.security</groupId>
		<artifactId>spring-security-test</artifactId>
	</dependency>
	- 보안 테스트 안하려면
	<pre>
		@WebMvcTest(value=TestController.class, secure=false)
	</pre>
	- @WithMockUser : 인증된 사용자로 실행 (@WithUserDetails로 대처 가능)  
		-> 인증된 사용자로 실행하며 기본 사용자(이름:User, 암호 : password)  
		-> with(csrf()) : CSRF 토큰을 요청에 추가하는 작업 수행  
	- 스프링 보안이 기본적으로 CSFP(Cross Site Request Forgery)보호가 가능해서 헤더 또는 매개변수를 요청에 추가하여 사용  
	  ?? CSFP : 사용자가 의도치 않은 처리가 실행되는 공격

 2. 웹 어플리케이션 로그인
 : 기본 웹 페이지를 제공하여 폼 기반 로그인 지원  
   사용자 정의 웹페이지를 로그인 페이지로 제공  
   HTTP 요청 헤더에 제시된 기본 인증 자격 증명을 처리해 HTTP 기본 인증  
   	-> HTTP는 필요에 따라 고쳐 쓸 수 있는 제어 헤더로 다른 인증 프로토콜에 맞춰 확장 할 수 있는 프레임워크 제공  
	-> 기본 인증과 다이제스트 인증이 있음  
	[참고] http://iloveulhj.github.io/posts/http/http-basic-auth.html  
   	  HTTP 기본 인증은 원격 프로토콜 및 웹 서비스로 이뤄진 요청을 인증하는 데에도 사용자   
	- 일부는 익명 접근을 허용  
	  스프링 보안 정책을 정의할 때 일반 사용자처럼 익명 사용자를 처리 할 수 있도록 보안 주체와 권한을 익명 사용자에게 할당할 수 있는 익명 로그인 서비스 제공하여  
	  여러 브라우저 세션에서 사용자 신원을 기억할 수 있는 Remember-Me 로그인 지원(하단에 다시 작성)  
	  사용자가 처음 로그인한 후 다시 로그인할 필요 없음  
	- WebSecurityConfigurerAdapter(스프링 시큐리티의 기본 설정 사용)가 없을 때 기본 보안 설정  
	  HTTP 자동 설정 -> 로그인 서비스가 자동으로 등록
	- 최소 예외 처리와 보안 컨텍스트 통합 구성  
		1) HTTP 기본 인증
		  - httpBasic()
		2) 폼 기반 : 사용자가 로그인 폼이 포함된 웹 페이지를 렌더링해 로그인 세부 정보를 입력하고 로그인 폼 처리 (커스텀 가능)
		  - CSRF 보호가 설정돼 있어 CSRf 토큰을 폼에 숨겨진 필드로 추가해야함  
		  ?? CSRF 토큰 : CSRF 공격에 대응하기 위한 방어기법 중 하나  
		  >>  'CSRF를 비활성화한 경우 숨은 필드 제거' 무슨말인가....  
	- 스프링 보안이 생성한 기본 로그인 페이지를 사용하면, 로그인이 실패했을 때 오류 메시지와 함께 로그인 페이지를 다시 렌더링   
	  사용자 정의 -> 인증 실패 URL을 구성해 로그인 오류 시 리디렉션할 URL 지정  
		<pre>
      //예외를 저장하는 세션 범위 속성인 SPRING_SECURITY_LAST_EXCEPTION을 사용해 오류 메시지를 표시  
		   span th:text="${session.SPRING_SECURITY_LAST_EXCEPTION}"></span>
		</pre>
	- 기본적으로 URL/logout에 매핑되며 POST 요청에만 반응하며 logoutSuccessURL 구성 메소드를 사용해 다른 URL로 이동 하게 할 수 있음
	- 로그아웃 후 브라우저 뒤로가기를 하면 로그아웃을 했더라도 이전 페이지를 볼 수 있음.이것은 브라우저가 페이지를 캐시해서라고 함
	- headers() : 보안 헤더를 활성화하면 브라우저가 페이지를 캐시하지 않도록 함
	- no-cache 헤더 옆에서 콘텐츠 스니핑이 비활성화되고 x-frame 보호가 활성화
	- 로그인 페이지로 리디렉션
	- 익명 로그인 : 자바 구성의 anonymous()로 구성하며 익명 사용자의 사용자 이름과 권한을 사용자 정의
		 1) anonymousUser
		 2) ROLE_ANONYMOUS
	- **Remember-Me**
	  : 기본적으로 사용자 이름, 암호, remember-me 만료 시간 및 개인 키를 토큰으로 인코딩하고 쿠키로 사용자의 브라우저에 저장  
	    다음에 사용자가 동일한 웹 애플리케이션에 엑세스하면 이 토큰이 감지돼 사용자가 자동으로 로그인  
	    Remember-Me 토큰은 해커가 캡처할 수 있기 때문에 보안 문제가 있음  
 
 3. 인증 사용자
	: 체인으로 연결된 하나 이상의 AuthenticationProvider에 의해 수행  
	  어느 인증 제공자라도 사용자가 비활성화됐거나 잠겼거나 자격 증명이 잘못됐다고 기록되거나 인증 제공자가 사용자를 인증할 수 없는 경우에는 사용자 애플리케이션에 로그인할 수 없음
	- 사용자 세부 정보를 저장하는 사용자 리포지토리로 사용자 인증
		- 항상 암호화된 비밀번호 저장
		- 단방향 해시 함수를 사용해 암호를 인코딩
		- 스프링 보안은 암호를 암호화하는 여러가지 알고리즘을 지원하며 이러한 알고리즘을 위한 기본 암호 인코더를 제공
		- 암호의 암호화 : PASSWORD 필드에 BCrypt 해시를 저장하려면 필드 길이가 최소 68자
	- 인메모리 기반 사용자 인증 순서
		1) 스프링 보안 구성 파일에서 사용자 세부 사항을 정의해 애플리케이션 메모리에 로드
		2) jdbcAuthentication() 사용 
		3) DataSource를 전달

 4. 접근 제어 설정
	- 접근제어 : 사용자가 애플리케이션에서 리소스에 접근할 수 있는지를 결정하는 것
	- 사용자 인증 상태
	- 리소스의 특성 및 접근 속성을 기반
	- SpEL(Spring Expression language) : 강력한 접근 제어 규칙
	- access  
		  - permitAll : 모든 접근자를 항상 승인  
		  - denyAll : 모든 사용자의 접근을 거부  
		  - anonymous : 사용자가 익명 사용자인지 확인  
		  - authenticated : 인증된 사용자인지 확인  
		  - rememberMe : 사용자가 remember me를 사용해 인증했는지 확인  
		  - fullyAuthenticated : 사용자가 모든 크리덴셜을 갖춘 상태에서 인증했는지 확인  
	- hasRole을 사용할 때 역할에 전달된 것이 ROLE_(기본 역할 접두사)로 시작하면 그대로의 값으로 확인  
	  그렇지 않은 경우에는 권한을 확인하기 전에 추가로 ROLE_로 시작하는 값을 먼저 확인  
	  hasRole('ADMIN')은 현재 사용자에게 ROLE_ADMIN 권한이 있는지 먼저 확인  
	  hasAuthority를 사용할 때에는 전달된 값을 그대로 확인  
		>> 예전에 스프링 시큐리티 설정을 하다가 이런식으로 ROLE을 정해서 들어올 수 있게 하는걸 봤었음  
		<pre>
		      @Secured("ROLE_ADMIN") // ROLE_ADMIN만
		      @RequestMapping("/admin/home.do")
		      public String adminHome(HttpServletRequest req, HttpServletResponse res, ModelMap model) throws Exception {    
			try {
			    System.out.println("admin home");
			} catch (Exception e) {
			  // TODO: handle exception
			  e.printStackTrace();
			}
			return "home";
		      }
		</pre>
	- 애노테이션과 표현식을 사용한 보안 메소드
	 1) **preAuthorize** : 요청을 받은후 함수 실행전 권한 검사  
			   #을 통해 파라미터에 접근 가능 ex) #test
	 2) **PostAuthorize** : 함수 실행 후 client에게 응답하기 직전 권한 검사  
	- 애노테이션 처리를 사용하려면 @EnableGlobalMethodSecurity 애노테이션을 보안 구성에 추가하고 prePostEnabled 속성을 true 설정
		<pre>
			@EnableGlobalMethodSecurity(prePostEnabled=true)
		</pre>

 5. 웹플럭스 애플리케이션에 보안 추가하기
	- @EnableWebFluxSecurity : spring security 5에서 Reactive 지원을 하며 생긴 기능으로 보임
	- URL 접근 보안 : 접근 규칙 -> SecurityWebFilterChain
	- WebFluxSecurityConfiguration 클래스는 보안 구성을 포함하는 SecurityFilterChain의 인스턴스 감지  
	  WebFilter로 감싸져서 웹플러스가 들어오는 요청에 정상적인 서블릿 필터처럼 동작을 추가하는데 사용
	- 웹플럭스 애플리케이션에 로그인  
	  인증 관리자가 자동으로 감지되는 데, ReactiveAuthenticationManager or UserDetailsRepository 유형의 빈을 등록하면 됨  
	- 기본 구현 방법은 Websession에 컨텍스트를 저장하는 WebserssionServerSecurityContextRepository  
	  NoOpServerSecurityContextRepository가 있는데 Stateless 애플리케이션에서 사용  
	  ?? Stateless : http와 같이 client의 이전 상태를 기록하지 않는 접속.즉, 웹서버가 사용자 작업을 기억하지 않음
	  ?? Stateful : 사용자의 상태를 서버가 기억하고 활용  
	- 사용자 인증 : 스프링 웹플럭스 기반 애플리케이션에서 사용자 인증은 ReactiveAuthenticationManager를 통해 수행.단일 인증 메소드
	- 접근 제어 설정 : 어떤 시점에서 사용자가 가지고 있는 권한이나 역할에 따라 접근 권한을 부여

 6. 요약
	- 보안 영역에서는 인증, 권한 부여, 접근 제어 개념이 반드시 사용
	- 중요한 URL은 무단으로 액세스하는 것을 방지하며 선언적인 방식으로 보호
	  간단한 자바 기반으로 서블릿 필터를 적용해 보안 처리
	- 사용자가 폼 기반 로그인이나 HTTP 기본 인증 등 웹 애플리케이션에 로그인하는 여러가지 방법을 지원
	  익명 사용자를 처리할 수 있는 익명 로그인 서비스 제공
	  Remember-Me 지원을 통해 애플리케이션은 여러 브라우저 세션에서 사용자의 신원을 기억
	- 인메모리 정의, 관계형 데이터베이스, LDAP 저장소 -> 사용자 인증 지원
	  일반 텍스트 암호는 해커 공격에 취약하기 때문에 항상 암호화된 암호를 사용자 저장소에 저장
	  원격 질의 수행의 오버헤드를 줄이기 위해 사용자 세부 정보를 로컬에 캐싱하는 기능도 지원
	- 리소스에 접근할 수 있는지 여부는 접근 관리자가 결정
	- @PreAuthorize, @PostAuthorize : 선언적 방법으로 메소드 호출 보호
	  스프링 웹플럭스 기반 앱플리케이션의 보안을 지원
    
-------------------------------------------------------------------------------------------------------------------------------------

200829 >> 전에 한번 일주인가 이주를 spring security 설정한다고 헤맸던 일이 있어서 그런지 이번건 그리 어렵진 않았음.
