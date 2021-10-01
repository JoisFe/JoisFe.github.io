---
title: "AppConfig 리팩터링"
categories:
  - Spring
tags:
  - DIP 위반
  - 관심사의 분리
  - AppConfig
  - 생성자 주입
  - DI
  - 의존관계 주입
  - 의존성 주입
  - BeforeEach
use_math: true
---

자 저번 글에서 이전에 구성한 AppConfig가 약간의 문제가 있다고 언급하였다.<br>
어떤 문제가 있었을까?<br><br>

-> 현재 AppConfig를 보면 중복이 되어있고, 역할에 따른 구현이 잘 보이지 않는다. <br><br>

# AppConfig 리팩터링

이전에 주문 도메인 전체 그림이 기억날 것이다. <br>
다시 보이면 <br>
![jpeg](/images/Spring_basic(6)_files/기대하는 도메인.jpeg) 
<br>

이 구조가 우리가 역할과 구현이 잘 분리되어 있어 한눈에 잘 보인다. <br>
<br>
하지만 이전에 작성한 AppConfig에서는 이런식으로 되어 있지 않다. <br>
AppConfig는 설정정보 인데 역할과 구현이 확실히 잘 구분되어 있고 한눈에 잘 보여야 함에도 <br>
중복되 되어있고, 역할과 구현이 잘 보이지 않는다. <br><br>

이러한 문제점을 고치기 위해 AppConfig를 리팩터링 해야한다. <br>

자 다시 이전에 작성한 AppConfig 코드를 보면
### 리팩터링 전
```java
package hello.spring_basic;

import hello.spring_basic.discount.FixDiscountPolicy;
import hello.spring_basic.member.MemberService;
import hello.spring_basic.member.MemberServiceImpl;
import hello.spring_basic.member.MemoryMemberRepository;
import hello.spring_basic.order.OrderService;
import hello.spring_basic.order.OrderServiceImpl;

// 애플리케이션에 전체 동장 방식을 구성하는 것을 AppConfig에서 하자
public class AppConfig {

    public MemberService memberService() { //MemberService를 Appconfig에서 만듬
        return new MemberServiceImpl(new MemoryMemberRepository());

        // 누군가 AppConfig 통해 memberService() 불러다 쓸떄 MemberServiceImpl인 구현체의 객체가 생성되어 반환되는데
        // 그떄 거기에 new MemoryMemberRepository() 들어감
        // 즉 MemberServiceImpl의 생성자의 매개변수로 MemoryMemberRepository의 객체가 들어감
    }

    public OrderService orderService() { // 위와 마찬가지
        return new OrderServiceImpl(new MemoryMemberRepository(), new FixDiscountPolicy());
        // 누군가 AppConfig를 통해 orderService()를 조회하면 OrderServiceImpl 구현체의 객체가 생성되어 반환하는데
        // OrderServiceImpl 클래스를 보면
        // OrderServiceImpl의 생성자에 두 매개변수가 필요한
        // MemberRepository 객체와, DiscountPolicy 객체 2개 모두 필요하므로
        // OrderServiceImpl의 생성자의 매개변수로 MemoryMemberRepository의 객체와, FixDiscountPolicy 객체 2개 모두 들어감
    }
}
```
<br>
이 코드에서 중복을 제거하고 역할에 따른 구현이 보이도록 리팩터링 해보자.
<br>

### 리팩터링 후

```java
package hello.spring_basic;

import hello.spring_basic.discount.DiscountPolicy;
import hello.spring_basic.discount.FixDiscountPolicy;
import hello.spring_basic.member.MemberRepository;
import hello.spring_basic.member.MemberService;
import hello.spring_basic.member.MemberServiceImpl;
import hello.spring_basic.member.MemoryMemberRepository;
import hello.spring_basic.order.OrderService;
import hello.spring_basic.order.OrderServiceImpl;

// 애플리케이션에 전체 동장 방식을 구성하는 것을 AppConfig에서 하자
public class AppConfig {

    // memberService 역할
    public MemberService memberService() { //MemberService를 Appconfig에서 만듬
        return new MemberServiceImpl(memberRepository());
        // 리턴타입이 기존에는 구체 클래스인 new MemoryMemberRepository()에서 인터페이스 반환하는 memberRepository() 메서드로 바꿔주었다.
        // memberRepository() 메서드는 아래에 구현되어있음

        // 누군가 AppConfig 통해 memberSe rvice() 불러다 쓸떄 MemberServiceImpl인 구현체의 객체가 생성되어 반환되는데
        // 그떄 거기에 new MemoryMemberRepository() 들어감
        // 즉 MemberServiceImpl의 생성자의 매개변수로 MemoryMemberRepository의 객체가 들어감
    }

    // memberRepository 역할
   public MemberRepository memberRepository() {
        return new MemoryMemberRepository();
        //
    }

    // orderService 역할
    public OrderService orderService() { // 위와 마찬가지
        return new OrderServiceImpl(memberRepository(), discountPolicy());
        // 리턴 타입이 기존에는 구체 클래스인 new MemoryMemberRepository()에서 인터페이스 반환하는 memberRepository() 메서드로
        // 구체 클래스인 new FixDiscountPolicy()에서 인터페이스 반환하는 discountPolicy() 메서드로 변경
        // memberRepository() 메서드는 위에서, discountPolicy() 메서드는 아래에서 구현되어있음

        // 누군가 AppConfig를 통해 orderService()를 조회하면 OrderServiceImpl 구현체의 객체가 생성되어 반환하는데
        // OrderServiceImpl 클래스를 보면
        // OrderServiceImpl의 생성자에 두 매개변수가 필요한
        // MemberRepository 객체와, DiscountPolicy 객체 2개 모두 필요하므로
        // OrderServiceImpl의 생성자의 매개변수로 MemoryMemberRepository의 객체와, FixDiscountPolicy 객체 2개 모두 들어감
    }

    // discountPolicy 역할
    public DiscountPolicy discountPolicy() {
        return new FixDiscountPolicy();
    }

    // 위 처럼 구현하면 코드를 보기만 해도 메서드 명을 보는 순간 역할이 다 보임
    // 특정 역할 변경이 일어날때 해당 역할 부분 메서드만을 변경하면 된다는 장점이 있음
}
```
<br>

위 처럼 리팩터링 하고 나면 여러 장점이 있다.<br>
먼저 메서드 명을 보는 순간 역할이 한눈에 보인다는 것이다 <br>
또한 특정 역할이 변경이 일어났을때 해당 특정 역할 부분에 대한 메서드만 변경하면 된다는 것이다. <br><br>

정리 해보면 <br>
AppConfig를 보면 역할과 구현 클래스가 한눈에 들어오게 되었다 <br>
따라서 애플리케이션 전체 구성이 어떻게 되어있는지 빠르게 파악할 수 있다.<br><br>
또한 new MemoryMemberRepository() 이 부분이 중복 제거되었다. <br>
이제 MemoryMemberRepository를 다른 구현체로 변경할 때 한 부분만 변경하면 된다. <br>
<br>

이번에는 AppConfig를 리팩터링 해보았다. <br>
앞으로 AppConfig 설계를 이런 식으로 하는 것이 좋다. <br><br>

지금까지 정액 할인 정책 (FixDiscountPolicy)만 적용해 보았는데 <br>
다음에는 정률 할인 정책 (RateDiscountPolicy)로 바꾸어 볼 것이다 <br>
<br>

### Reference :
김영한 강사님 스프링 핵심 원리 - 기본편  강의 중