---
title: "새로운 구조에 변경된 할인 정책 적용"
categories:
  - Spring
tags:
  - 사용 영역
  - 구성 영역
  - 할인 정책 변경
use_math: true
---

자 이제 AppConfig를 통해 사용과 구성을 분리하였으니 기존의 정액 할인 정책을 정률 할인으로 바꿔보자 <br>

# 새로운 구조와 할인 정책 적용
AppConfig로 인해 애플리케이션이 크게 사용 영역과, 객체를 생성하고 구성하는 영역으로 분리가 되었다.<br><br>

어떤 부분만 변경하면 될지 그림으로 알아보자 <br>
<br>
기존 정액 할인 정책인 경우<br>
![jpeg](/images/Spring_basic(7)_files/정액할인.jpeg)
<br>

정률 할인 정책으로 바뀐 경우<br>
![jpeg](/images/Spring_basic(7)_files/정률할인으로변경.jpeg)
<br>

기존 정률 할인 정책에서 정액 할인 정책으로 바뀌었으니<br>
FixDiscountPolicy 에서 RateDiscountPolicy로 변경하였따. <br>
그림을 보면 알 수 있듯 할인 정책을 변경하여도 구성 영역(AppConfig)만 변경되고 사용 영역은 전혀 변하지 않았다. <br><br>

즉 구성 영역만 영향을 받고 사용 영역은 전혀 영향을 받지 않음을 알 수 있다.<br><br>

이제 정률 할인 정책으로 변경을 위해 코드를 변경해보자 <br>

## 할인 정책 변경 구성 코드
이전에 설명하였듯 할인 정책을 변경하는데 있어 사용 영역 부분 코드는 건드릴 필요가 없이 구성 영역 코드만 변경하면 된다. <br>
즉 AppConfig 부분 코드만 변경하면 된다. <br>

```java
package hello.spring_basic;

import hello.spring_basic.discount.DiscountPolicy;
import hello.spring_basic.discount.FixDiscountPolicy;
import hello.spring_basic.discount.RateDiscountPolicy;
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

        // return new FixDiscountPolicy(); // -> 기존 정액 할인 정책에서 정률 할인정책으로 변경 위해 코드 지움

        return new RateDiscountPolicy(); // 정률 할인 정책으로 변경
    }

    // 위 처럼 구현하면 코드를 보기만 해도 메서드 명을 보는 순간 역할이 다 보임
    // 특정 역할 변경이 일어날때 해당 역할 부분 메서드만을 변경하면 된다는 장점이 있음
}
```
<br>

AppConfig 에서 DiscountPolicy() 클래스에서<br>
return new FixDiscountPolicy(); 에서 <br>
return new RateDiscountPolicy();로 변경하여<br>
정액 할인 정책에서 정률 할인 정책으로 바꾸면 된다. <br><br>

잘 실행하는지 테스트를 해보면
![png](/images/Spring_basic(7)_files/테스트전체실행.png)
<br>
문제 없이 잘 실행 되는 것을 확인할 수 있고 <br><br>

OrderApp 클래스의 메인 메서드를 실행시켜 보면
![png](/images/Spring_basic(7)_files/OrderApp메인메서드실행.png)
<br>
주문 가격 10000원에서 VIP 경우 정률 할인 정책 10%할인 가격 1000원이 잘 출력되는 것을 확인할 수 있다. <br><br>

주문 가격 10000원 이면 정액할인이든 정률할인이든 1000원이 할인되니 주문 가격을 20000원으로 변경해보자 <br>

```java
package hello.spring_basic;

import hello.spring_basic.member.Grade;
import hello.spring_basic.member.Member;
import hello.spring_basic.member.MemberService;
import hello.spring_basic.member.MemberServiceImpl;
import hello.spring_basic.order.Order;
import hello.spring_basic.order.OrderService;
import hello.spring_basic.order.OrderServiceImpl;

public class OrderApp {
    public static void main(String[] args) {

        AppConfig appConfig = new AppConfig();

        MemberService memberService = appConfig.memberService(); // memberService 필요시 appConfig에서 인터페이스 만듬
        // memberService에는 MemberServiceImpl 객체인데 생성자로 MemoryMemberRepository()를 사용하는 것을 주입 (AppConfig에 있음)

        OrderService orderService = appConfig.orderService(); // orderService 필요시 appConfig에서 인터페이스 만듬
        // orderService에는 OrderServiceImpl 객체인데 생성자로 MemoryMemberRepository()와 FixDiscountPolicy()를 사용하는 것을 주입 (AppConfig에 있음)

        // 기존에 main 메서드에서 직접 MemberServiceImpl, OrderServiceImpl을 생성함
        // -> DIP 어기기 떄문거

        Long memberId = 1l; //멤버 아이디 생성
        Member member = new Member(memberId, "memberA", Grade.VIP); // Member 객체 생성 (vip 회원 만듬)
        memberService.join(member); //memberService를.join 통해 메모리 객체에 넣어둠 -> 그래야 주문에서 찾아 쓸 수 있으니

        Order order = orderService.createOrder(memberId, "iteA", 20000); //orderService.createOrder를 통해 order 생성

        System.out.println("order =" + order); //order.toString()으로 정의한 내용들이 출력 (order클래스를 보면 toString()함수 정의해놨음)
        //order라는 객체 자체를 출력했으므로 order내의 toString()함수가 호출 됨
    }
}

```
<br>
다시 OrderApp 클래스의 메인 메서드를 실행시켜보면<br>
![png](/images/Spring_basic(7)_files/20000원으로변경.png)
<br>
주문 가격 20000원에서 10% 정률할인하면 할인가격이 2000원이 되어야하는데<br>
제대로 된 결과가 나왔음을 알 수 있다<br>
<br>

AppConfig에서 할인 정책 역할을 담당하는 구현을 FixDiscountPolicy에서 RateDiscountPolicy 객체로 변경하였다.<br>
이제 할인 정책을 변경하더라도 애플리케이션 구성 역할을 담당하는 AppConfig만을 변경하면 되게끔 설계과 된 것을 알 수 있다.<br>
클라이언트 코드인 OrderSerivceImpl을 포함해서 사용 영역의 어떤 코드도 변경할 필요가 없다는 의미이다. <br>
구성 영역은 당연히 변경되고, 구셩 역할을 담당하는 AppConfig를 이전에 예로 들었던 것을 이용하면 애플리케이션이라는 드라마 기획자로 볼 수 있고, 드라마 기획자는 당연히 드라마 참여 배우인 구현 객체들을 모두 알아야 한다. <br><br>

이번에 새로운 구조에 대해 기존의 정액 할인 정책에서 정률 할인 정책으로 변경해 보았다. <br>
다음에는 지금까지 진행한 흐름을 정리해보도록 할 것이다.<br>

### Reference :
김영한 강사님 스프링 핵심 원리 - 기본편  강의 중