---

mindmap-plugin: basic

---

# Spring

## AOP
- 관점 지향 프로그래밍 (Aspect-oriented Programming)
- Aspect
   - 흩어진 관심사(Crosscutting Concerns)를 묶어서 모듈화 한 것
   - Advice
      - 어떤 일을 해야할 지에 대한 것
      - 해야할 일들에 대한 정보를 가지고 있다
   - Point Cut
- 용어
   - Join point
      - 메서드 실행 시점
      - Advice가 적용될 위치, 끼어들 수 있는 지점.
         ex 생성자 호출 직전, 생성자 호출 시, 필드에 접근하기 전, 필드에서 값을 가져갔을 때 등
   - Point Cut
      - 어디에 적용해야 하는지에 대한 정보
- AOP 구현체
   - AspectJ
   - 스프링 AOP
      - 프록시 기반의 AOP 구현체
      - 스프링 빈에만 AOP를 적용할 수 있다
      - 중복코드, 프록시 클래스 작성의 번거로움, 객체 간 관계 복잡도 증가 등
         를 해결하기 위한 솔루션을 제공하는 것이 목적

## Spring vs Spring Boot

## Bean, Component

## Spring Security

## Spring JPA

## Spring Web MVC
- 서블릿을 low level로 개발하지 않아도 된다

## 스프링 삼각형
- Sub title