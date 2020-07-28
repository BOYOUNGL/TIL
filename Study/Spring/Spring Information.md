# Spring 
	: 보통 '자바 엔터프라이즈 개발을 편하게 해주는 오픈소스 경량급 애플리케이션 프레임워크'라고 정의한다.
	  
200728 >> 내가 이해할만한 내용으로 바꿀 것
  - 장점	
    - 중복 코드를 줄여줌
    - 로직 간단히
    - 오픈소스 활용하기 좋음	
  - 특징 
    - POJO (Plain Old Java Object)
       : Getter / Setter
    - DI (Dependency Injection) - 의존성 주입
       : 구성 요소의 의존 관계가 외부 설정 파일로 정의됨 (필요할때마다 유연하게 사용)
    - oC (Inversion of Control) - 제어 반전
       : 객체 생명주기 관리를 스프링이 해줌
    - OP (Aspect Oriented Programming) - 관점 지향 프로그래밍
       : 기능을 분리하여 관리, 재사용성 큼
    -PSA (Potable Service Abstraction)
	 
	
# Spring 객체 관리 : @Component 어노테이션을 포함한 클래스들의 인스턴스를 자동으로 생성/삭제
  - @Component 종류(레이어 구분)
   - @Controller - @RestController
   - @Service
   - @Repository
  - **Singleton 패턴의 객체 생성**


# Spring Framwork : 자바 기반 엔터프라이즈 애플리케이션 개발을 위한 인프라 플랫폼
 - 오픈 소스 기반
 - 유지보수 환경 제공
 - 엔터프라이즈 애플리케이션 개발을 쉽게 해줌.
  - 엔터프라이즈 애플리케이션 
  	: 기업형 프로그램으로 자바에서는 javaEE(Dava Enterprise Edition)을 통해 개발 됨
      스프링은 JavaEE(Java Standard Edition:서버측 개발을 위한 기능이 더해진 자바 버전)를 대체하기 위해 개발되었음.
      때문에 엔테프라이즈 애플리케이션 개발을 위한 플랫폼이라 소개되는것임.


# Spring Bean Container 역할 : 스프링 객체를 스스로 관리
 - @ComponentScan : @Component가 붙은 클래스들을 읽어서 객체로 생성
 - @Bean : 개발자가 생성하는 코드 작성
 - 인스턴스 생명주기를 관리.
 - ApplicationContext(BeanFactory의 향상 ver으로 BeanFactory 상속을 받음)
   : ApplicationContext는 IoC를 적용, 모든 오브젝트에 대한 생성, 관계설정 담당.즉 구체적인 팩토리 클래스를 알 필요가 없음
 	   무엇을 하는가?
 	   오브젝트 생성, 관계 설정, create 방식, 자동 생성, 후처리 등

----------------------------------------------------------------------------------

[공부 일기]
200728 >>
	어렴풋이 아는 용어지만 정확히 모르는게 문제...
	틈날때마다 정리해야지.공부할것은 많고...일단 여기까지!
  내용이 너무 길어지면 기능과 따로 나눠서 써야할지 고민해봐야겠다.
