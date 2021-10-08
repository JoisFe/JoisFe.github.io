---
title: "Spring으로 전환"
categories:
  - Spring
tags:
  - 스프링 컨테이너
  - 스프링 빈
use_math: true
---

지금 까지 순수한 자바 코드로만 작성해온 것을 우리의 목표인 스프링을 사용해 볼 것이다. <br>

### AppConfig를 스프링 기반으로 변경

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
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

// 애플리케이션에 전체 동장 방식을 구성하는 것을 AppConfig에서 하자

@Configuration // @Configuration 어노테이션 아래에는 애플리케이션의 설정 정보(구성 정보) 적어줌
public class AppConfig {
    @Bean // @Bean 어노테이션 아래에 있는 것은 Spring Container에 등록이 됨
    // memberService 역할
    public MemberService memberService() { //MemberService를 Appconfig에서 만듬
        return new MemberServiceImpl(memberRepository());
        // 리턴타입이 기존에는 구체 클래스인 new MemoryMemberRepository()에서 인터페이스 반환하는 memberRepository() 메서드로 바꿔주었다.
        // memberRepository() 메서드는 아래에 구현되어있음

        // 누군가 AppConfig 통해 memberSe rvice() 불러다 쓸떄 MemberServiceImpl인 구현체의 객체가 생성되어 반환되는데
        // 그떄 거기에 new MemoryMemberRepository() 들어감
        // 즉 MemberServiceImpl의 생성자의 매개변수로 MemoryMemberRepository의 객체가 들어감
    }

    @Bean
    // memberRepository 역할
   public MemberRepository memberRepository() {
        return new MemoryMemberRepository();
        //
    }

    @Bean
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

    @Bean
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

AppConfig에 설정을 구성한다는 뜻의 <b>@Configuration</b>을 붙여준다. <br>
그리고 각 메서드에 <b>@Bean</b>을 붙여준다. 이렇게 하여 스프링 컨테이너에 스프링 빈으로 등록할 수 있다. <br><br>

### MemberApp에 스프링 컨테이너 적용
```java
package hello.spring_basic;

import hello.spring_basic.member.Grade;
import hello.spring_basic.member.Member;
import hello.spring_basic.member.MemberService;
import hello.spring_basic.member.MemberServiceImpl;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class MemberApp {

    public static void main(String[] args) {
        // AppConfig appConfig = new AppConfig();
        // MemberService memberService = appConfig.memberService(); // memberService 필요시 appConfig에서 인터페이스 만듬
        // memberService에는 MemberServiceImpl 객체인데 MemoryMemberRepository()를 사용하는 것을 주입 (AppConfig에 있음)

        // 기존에 main 메서드에서 MemberServiceImpl을 직접 생성했었다. -> DIP 어김 따라서 제거

        ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);
        // 환경설정 정보를 가지고 Spring이 AppConfig 클래스 안에 있는 (@Bean 아래 있는 것들은 Spring Container 안에 등록)

        MemberService memberService = applicationContext.getBean("memberService", MemberService.class); // 매개변수 (name, 타입)
        // 스프링 컨테이너에 등록한 AppConfig에 있는 memberService 가져오고 싶을때
        // 기본적으로 name은 메서드 이름

        Member member = new Member(1L, "memberA", Grade.VIP);// 새로운 Member의 객체 member 생성
        //이름은 memberA, 등급은 VIP

        memberService.join(member); // 새로운 member를 등록 (회원가입)

        Member findMember = memberService.findMember(1L);//위 사람이 제대로 등록(회원 가입) 되었는지 확인해보자.

        System.out.println("new member = " + member.getName());
        System.out.println("find Member = " + findMember.getName()); //회원가입이 잘 되었다면 member.getName()과 findMember.getName()이 같은 출력을 내놓아야야

        //똑같은 "memberA"를 출력함
    }
}

```
<br>

MemberApp 클래스를 실행시켜보면 <br>

![png](/images/Spring_basic(10)_files/MemberApp실행.png)
<br>
아무 문제없이 잘 실행된다. <br>
여기서 이전 실행과 다른점이 보인다 <br>
~~singleton bean '~'가 보인다. <br>
'~' 해당부분이 스프링 컨테이너에 등록이 된 것을 확인할 수 있다. <br><br>

### OrderApp에 스프링 컨테이너 적용
```java
package hello.spring_basic;

import hello.spring_basic.member.Grade;
import hello.spring_basic.member.Member;
import hello.spring_basic.member.MemberService;
import hello.spring_basic.member.MemberServiceImpl;
import hello.spring_basic.order.Order;
import hello.spring_basic.order.OrderService;
import hello.spring_basic.order.OrderServiceImpl;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class OrderApp {
    public static void main(String[] args) {

        // AppConfig appConfig = new AppConfig();

        // MemberService memberService = appConfig.memberService(); // memberService 필요시 appConfig에서 인터페이스 만듬
        // memberService에는 MemberServiceImpl 객체인데 생성자로 MemoryMemberRepository()를 사용하는 것을 주입 (AppConfig에 있음)

        // OrderService orderService = appConfig.orderService(); // orderService 필요시 appConfig에서 인터페이스 만듬
        // orderService에는 OrderServiceImpl 객체인데 생성자로 MemoryMemberRepository()와 FixDiscountPolicy()를 사용하는 것을 주입 (AppConfig에 있음)

        // 기존에 main 메서드에서 직접 MemberServiceImpl, OrderServiceImpl을 생성함
        // -> DIP 어기기 떄문거

        ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);

        MemberService memberService = applicationContext.getBean("memberService", MemberService.class);
        OrderService orderService = applicationContext.getBean("orderService", OrderService.class);
        // 스프링 컨테이너에 등록한 AppConfig에 있는 memberService, orderService 가져오고 싶을때

        Long memberId = 1l; //멤버 아이디 생성
        Member member = new Member(memberId, "memberA", Grade.VIP); // Member 객체 생성 (vip 회원 만듬)
        memberService.join(member); //memberService를.join 통해 메모리 객체에 넣어둠 -> 그래야 주문에서 찾아 쓸 수 있으니

        Order order = orderService.createOrder(memberId, "iteA", 20000); //orderService.createOrder를 통해 order 생성

        System.out.println("order =" + order); //order.toString()으로 정의한 내용들이 출력 (order클래스를 보면 toString()함수 정의해놨음)
        //order라는 객체 자체를 출력했으므로 order내의 toString()함수가 호출 됨
    }
}

```
<bn>

OrderApp 클래스 실행시켜보면 <br>
1
![png](/images/Spring_basic(10)_files/OrderApp실행.png) 

<br>
아무문제 없이 잘 실행되는 것을 확인할 수 있다.<br><br>

# 스프링 컨테이너 :
<b>ApplicatioinContext</b>를 <b>스프링 컨테이너</b>라고 한다. <br>
<br>
지금까지 직접 AppConfig를 사용해 직접 객체를 생성하고 DI를 했다. <br>
하지만 Spring을 사용한 후로 부터는 스프링 컨테이너를 통해서 사용할 것이다. <br><br>

스프링 컨테이너는 <b>@Configuration</b>이 붙은 AppConfig를 설정(구성) 정보로 사용하였다.<br>
앞으로는 <b>@Bean</b>이라 적힌 메서드를 모두 호출하여 반환된 객체를 스프링 컨테이너에 등록할 것이다.<br>
이처럼 스프링 컨테이너에 등록된 객체를 <b>스프링 빈</b>이라고 한다. <br><br>

스프링 빈은 @Bean이 붙은 메서드의 명을 스프링 빈의 이름으로 사용한다. <br> 
이전 코드에서 봤듯 'memberService', 'orderService'를 보면 알 수 있다. <br>
예전에는 개발자가 필요한 객체를 AppConfig를 사용해서 직접 조회했지만 스프링 프레임워크를 사용한 후 부터는 스프링 컨테이너를 통해 필요한 스프링 빈(객체)를 찾으면 된다. <br>
스프링 빈은 <b>applicationiContext.getBean()</b> 메서드를 사용해서 찾을 수 있다. <br><br>

### 기존 개발자가 직접 순수 자바코드로 모든 것을 했다면 앞으로는 스프링 컨테이너에 객체를 스프링 빈으로 등록하고, 스프링 컨테이너에서 스프링 빈을 찾아서 사용하도록 변경 되었다.
<br><br>

그런데 스프링 기반으로 AppConfig를 변경하고, 스프링 컨테이너를 MemberApp, OrderApp 클래스에서 적용하면서 느낀점은 코드가 더 복잡해진 것 같다. <br> 
굳이 스프링 컨테이너를 사용해야만하는 장점이 있는건지.. ?? <br>
-> 엄청난 큰 장점이 존재한다. <br>
이 장점에 관련된 것은 점점 알아갈 것이다. <br><br>

### Reference :
김영한 강사님 스프링 핵심 원리 - 기본편  강의 중
