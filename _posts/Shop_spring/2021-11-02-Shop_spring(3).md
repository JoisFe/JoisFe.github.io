---
title: "엔티티 설계시 주의점"
categories:
  - Project - Shop_spring
tags:
  - 엔티티 설계 주의점
use_math: true
---

# 엔티티 설계시 주의점

### 엔티티에는 가급적 Setter를 사용하지 말도록 한다.
Setter가 열려있다면 변경 포인트가 너무 많아서 유지 보수가 어렵기 때문에 현재 코드는 Setter를 모두 열어놓았지만 나중에 리팩토링으로 Setter를 제거할 것이다. <br>

### 모든 연관관게는 지연로딩으로 설정한다
즉시로딩(EAGER)는 예측이 어렵고 어떤 SQL이 실행될지 추적하기가 매우 어렵다. <br>
특히 JPQL을 실행하는 경우에 N + 1 문제가 자주 발생하게 된다. <br>
실무에서 모든 연관관계는 지연로딩(LAZY)로 설정하는 것이 좋다 <br>
연관된 엔티티를 함께 DB에서 조회해야하는 경우 fetch join 또는 엔티티 그래프 기능을 사용하다록 하자 <br>
@XToOne(OneToOne, ManyToOne) 관계의 경우에는 기본(default)이 즉시로딩이므로 직접 지연로딩으로 설정해야함을 주의하자 !! <br>

### 컬렉션은 필드에서 초기화 한다
컬렉션은 필드에서 바로 초기화 하는 것이 안전하다 <br>
이게 무슨말이냐 ?? <br>
예를 들어보자 <br>
```java
// 필드에서 바로 초기화 하는 경우
private List<Order> orders = new ArrayList<>();

// 생성자를 이용한 초기화
private List<Order> orders;

public Member() {
    orders = new ArrayList<>();
}
```

필드에서 바로 초기화 하는 경우는 초기화에 대해 고민할 필요가 없다 <br>
따라서 null 문제에서 안전하다. <br>
그리고 다른 이유도 존재하는데 <br>
``` java
Member member = new Member(); // 초기화 하지 않고 맴버 객체 생성
  System.out.println(member.getOrders().getClass()); // 맴버 객체 출력

  em.persist(team); // 영속화
  // JPA 입장에서 DB에 저장하겠다는 선언

  System.out.println(member.getOrders().getClass()); // 영속화 후 맴버 객체 출력 

//출력 결과
class java.util.ArrayList // 영속화 전 맴버 객체 출력 결과

class org.hibernate.collection.internal.PersistentBag // 영속화 후 맴버 객체 출력 결과
```

또한 하이버네이트는 엔티티를 영속화 할 때 컬렉션을 감싸서 하이버네이트가 제공하는 내장 컬렉션으로 변경하는데 (하이버네이트가 해당 컬렉션이 변경된 것을 추적할 수 있어야 하므로 본인의 내장 컬렉션으로 바꾸어야함) 만약 getOrders() 처럼 임의의 메서드에서 컬렉션을 잘못 생성하게 되면 하이버네이트 내부 메커니즘에 문제가 발생할 수 있다. <br>
따라서 필드레벨에서 생성하는 것이 가장 안전할 뿐만 아니라 코드 또한 간결해지게 된다. <br>
초기화 하지 않고 생성한 컬렉션을 가급적이면 밖으로 꺼내지도 말고 변경하지 않는 것이 좋다.<br>

### 테이블, 컬럼명 생성 전략
스프링 부트에서 하이버네이트 기본 매핑 전략을 변경하기 때문에 실제 테이블 필드명은 다르다.
<br>
<br>
하이버네이트 기존 구현 : 엔티티의 필드명을 그대로 테이블의 컬럼명으로 사용한다 (SpringPhysicalNamingStrategy) <br>
<br>

스프링 부트 신규 설정(엔티티(필드) -> 테이블(컬럼)) <br>
1. 카멜 케이스 -> 언더스코어(memberPoint -> member_point) <br>
2. .(점) -> _(언더스코어) <br>
3. 대문자 -> 소문자  <br>
<br>

<b>적용 2단계</b>
1. 논리명 생성: <br>
명시적으로 컬럼, 테이블명을 직접 적지 않으면 ImplicitNamingStrategy 사용한다. <br>
<b>spring.jpa.hibernate.naming.implicit-strategy</b> : 테이블이나, 컬럼명을 명시하지 않을 때 논리명 적용한다. <br>

2. 물리명 적용: <br>
<b>spring.jpa.hibernate.naming.physical-strategy</b> : 모든 논리명에 적용된다.<br>
실제 테이블에 적용 (username usernm 등으로 회사 룰로 바꿀 수 있다) <br>

### Reference :
김영한 강사님 실전! 스프링 부트와 JPA 활용1 - 웹 애플리케이션 개발 강의 중 


