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
