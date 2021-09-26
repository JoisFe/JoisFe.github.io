---
title: "순수 자바로만 작성하는 예제_주문 할인 도메인 개발"
categories:
  - Spring
tags:
  - 순수 자바 코드로 개발
  - 주문 할인 도메인
  - 실행과 테스트
use_math: true
---

이전에는 회원 도메인 설계를 했다면 이제는 주문 할인 도메인 설계를 해보려 한다.

## 주문 할인 도메인 설계
### 주문 할인 정책
1. 회원은 상품을 주문할 수 있음 <br>
2. 회원의 등급에 따라 할인 정책을 적용할 수 있음 <br>
3. 할인 정책은 모든 VIP 등급의 경우에는 1000원을 할인해주는 고정 금액 할인을 적용하려 한다. (하지만 변경 될 수도 있다.) <br>
4. 할인 정책은 변경 가능성이 높다. 왜냐하면 회사에서 기본 할인 정책을 하직 정하지 못했기 때문이다. 오픈 직전까지 고민을 미루려고 하고, 최악의 경우에는 할인을 적용하지 않을 수도 있다. <br>
<br>

### 주문 도메인 협력, 역할, 책임 관계
![jpeg](/images/Spring_basic(3)_files/주문 도메인 협력 역할 책임 관계.jpeg)
<br>

1. 주문 생성 : 클라이언트가 주문 서비스에 주문 생성을 요청 <br>
2. 회원 조회 : 할인을 위해서 회원 등급이 필요하기 때문에 주문 서비슨는 회원 저장소에서 회원을 조회하여 해당 회원의 등급을 알아야 한다. <br>
3. 할인 적용 : 주문 서비스는 해당 회원의 등급을 확인한 후 할인 여부를 할인 정책에 위임한다. <br>
4. 주문 결과 반환 : 주문 서비스는 할인 결과를 포함한 전체 주문에 대한 결과를 클라이언트에게 반환한다. <br> <br>

참고로 실제 주문 데이터는 DB에 저장할 것이다. 하지만 여기 예제에서는 생략하고 단순하게 주문 결과를 반환하는 정도로 구현할 것이다. <br>
<br>

### 주문 도메인 전체
주문 할인 도메인 뿐만 아니라 이전에 구현했던 회원 도메인까지 다 합친 주문 도메인 전체에 대해 나타내면 <br>

![jpeg](/images/Spring_basic(3)_files/주문 도메인 전체.jpeg)
<br>
<br>

### 위 처럼 설계를 하면 역할과 구현을 분리했기 때문에 자유롭게 구현 객체를 조립할 수 있다.
### 따라서 아직 정확히 결정되지 않은 회원 저장소, 할인 정책 또한 유연하게 변경할 수 있다. 

<br><br>
 
### 주문 도메인 클래스 다이어그램
주문 도메인 클래스 다이어그램을 나타내보면 <br>
![jpeg](/images/Spring_basic(3)_files/주문 도메인 클래스 다이어그램.jpeg)
<br>
<br>

2 가지 객체 다이어그램을 생각해 볼 것이다. <br>
### 주문 도메인 객체 다이어그램1
먼저 첫 번째는 회원 정보를 메모리 회원 저장소에 저장하고, 할인 정책을 정액 할인 정책을 이용하는 경우이다 <br>

![jpeg](/images/Spring_basic(3)_files/주문 도메인 객체 다이어그램1.jpeg) 
<br>
주문 서비스 구현체는 new OrderServiceImpl();을 통해<br>
메모리 회원 저장소는 new MemoryMemerRepository();를 통해<br>
정액 할인 정책은 new FixDiscountPolicy();를 통해<br>
객체를 생성하고자 한다.
<br>
<br>

회원 정보를 메모리 회원 저장소에서 조회하고 정액 할인 정책을 지원해도 주문 서비스를 변경하지 않아도 된다. <br>
역할들의 협력 관계를 그대로 재사용할 수 있다. <br>
만약 메모리를 DB로 바꾼다해도, 정액 할인을 정률 할인으로 바꾼다고 해도 주문 서비스 구현체에도 영향이 없다 <br>
-> 역할과 구현을 분리했기에 가능한 것이다. <br><br>

### 주문 도메인 객체 다이어그램2
주문 도메인 객체 다이어그램1에서 메모리를 DB로, 정액 할인을 정률 할인으로 바꾼 경우이다.
<br>
![jpeg](/images/Spring_basic(3)_files/주문 도메인 객체 다이어그램2.jpeg) 
<br>
이 부분은 아직 정확히 구현은 하지 않고 나중으로 미루겠다.
<br><br>

회원을 메모리가 아닌 실제 DB에서 조회하고 정률 할인 정책을 지원해도 주문 서비스를 변경하지 않아도 된다는 것을 이 객체 다이어그램을 통해 알 수 있다.<br>
즉 협력 관계를 그대로 사용할 수 있다.<br>
<br> 이전에 설명했지만 역할과 구현을 분리했기에 가능한 것이다. <br><br>

# 주문 할인 구조를 기준으로 코드로 작성해보도록 하자. <br>

## 주문과 할인 도메인 개발

### 할인 정책 인터페이스
```java
package hello.spring_basic.discount;

import hello.spring_basic.member.Member;

public interface DiscountPolicy {

    /**
     * @return 할인 대상 금액
     */
    int discount(Member member, int price); //할인 기능 함수 선언

}
```

### 정액 할인 정책 구현책

```java
package hello.spring_basic.discount;

import hello.spring_basic.member.Grade;
import hello.spring_basic.member.Member;

public class FixDiscountPolicy implements DiscountPolicy{

    private int discountFixAmount = 1000; // 1000원 할인

    @Override
    public int discount(Member member, int price) {
        if (member.getGrade() == Grade.VIP) { // 현재 회원이 VIP 등급이면
            return discountFixAmount; // discountFixAmount 리턴 (즉 1000원 리턴)
        }

        else { // 현재 회원이 VIP가 아니라면
            return 0; // 0을 리턴해라
        }
    }
}

```
<br><br>

### 주문 엔티티

```java
package hello.spring_basic.order;

public class Order {
    private Long memberId; // 회원 아이디 
    private String itemName; // 물건 이름
    private int itemPrice; // 물건 가격
    private int discountPrice; // 할인 가격

    public Order(Long memberId, String itemName, int itemPrice, int discountPrice) {
        this.memberId = memberId;
        this.itemName = itemName;
        this.itemPrice = itemPrice;
        this.discountPrice = discountPrice; //Order 위해 필요한 정보들
    }

    public int calculatePrice() {
        return itemPrice - discountPrice; //정액 할인 정책에 맞게끔 할인 적용
    }

    public Long getMemberId() {
        return memberId;
    }

    public void setMemberId(Long memberId) {
        this.memberId = memberId;
    }

    public String getItemName() {
        return itemName;
    }

    public void setItemName(String itemName) {
        this.itemName = itemName;
    }

    public int getItemPrice() {
        return itemPrice;
    }

    public void setItemPrice(int itemPrice) {
        this.itemPrice = itemPrice;
    }

    public int getDiscountPrice() {
        return discountPrice;
    }

    public void setDiscountPrice(int discountPrice) {
        this.discountPrice = discountPrice;
    }

    @Override
    public String toString() {
        return "Order{" +
                "memberId=" + memberId +
                ", itemName='" + itemName + '\'' +
                ", itemPrice=" + itemPrice +
                ", discountPrice=" + discountPrice +
                '}';
    }
    /*
    toString함수 특징은 객체 자체를 출력하면 toString()이 호출이 됨.

    ex)
    order라는 객체가 있다면
    System.out.println(order); -> order 내의 toString()함수 호출
     */
}

```
<br><br>


### 주문 서비스 인터페이스
```java
package hello.spring_basic.order;

public interface OrderService {
    Order createOrder(Long memberId, String itemName, int itemPrice); // createOrder 선언
}
```
<br><br>

### 주문 서비스 구현체
```java
package hello.spring_basic.order;

import hello.spring_basic.discount.DiscountPolicy;
import hello.spring_basic.discount.FixDiscountPolicy;
import hello.spring_basic.member.Member;
import hello.spring_basic.member.MemberRepository;
import hello.spring_basic.member.MemoryMemberRepository;

public class OrderServiceImpl implements OrderService {

    //OrderService는 2개가 필요
    private final MemberRepository memberRepository = new MemoryMemberRepository(); // memberRepository에서 회원 찾아야 하므로
    private final DiscountPolicy discountPolicy = new FixDiscountPolicy(); // discountPolicy에서 할인 정책되로 적용 해야하므로

    @Override
    public Order createOrder(Long memberId, String itemName, int itemPrice) {

        Member member = memberRepository.findById(memberId); // member를 일단 찾자..

        int discountPrice = discountPolicy.discount(member, itemPrice);
        //OrderService 입장에선 할인에 대해서느 잘 모르겠으니 discountPolicy 에게 그 일을 맡기고 결과만 받는 다고 생각
        //-> 설계가 잘 된 (SRP (단일책임원칙) 잘 지킨 것)
        //만약 할인에 대해 변경할 사항이 있으면 할인에 대한 부분만 고치면 되고 주문에 관련된 부분은 고칠 필요가 없기 때문에

        return new Order(memberId, itemName, itemPrice, discountPrice); //Order를 만들어 반환.

        /*
        정리하면 주문 생성요청이 오면 회원정보를 먼저 조회를 하고 할인 정책에 회원을 넘기고
        결과들을 이용해 주문을 만들어 반환한다.

        MemoryMemberRepository()와 FixDiscountPolicy()을 구현체로 생성함
         */
    }
}
```
<br>
<br>

자 이제 주문 할인 도메인 개발을 해 보았으니 제대로 작동하는지 테스트를 해보자. <br>
<br>

# 주문 할인 도메인 실행과 테스트를 해보자.

## 주문과 할인 도메인 생성과 테스트
<br>
<br>
회원 도메인 테스트 할때 순서처럼 main 함수를 만들어 해당 멤버가 vip일때 할인 정책을 적용하고 실제로 잘 적용 받는지 확인해 보자 <br>

### 주문과 할인 정책 실행
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
        MemberService memberService = new MemberServiceImpl(); //memberservice 만들고
        OrderService orderService = new OrderServiceImpl(); //orderservice 만듬

        Long memberId = 1l; //멤버 아이디 생성
        Member member = new Member(memberId, "memberA", Grade.VIP); // Member 객체 생성 (vip 회원 만듬)
        memberService.join(member); //memberService를.join 통해 메모리 객체에 넣어둠 -> 그래야 주문에서 찾아 쓸 수 있으니

        Order order = orderService.createOrder(memberId, "iteA", 10000); //orderService.createOrder를 통해 order 생성

        System.out.println("order =" + order); //order.toString()으로 정의한 내용들이 출력 (order클래스를 보면 toString()함수 정의해놨음)
        //order라는 객체 자체를 출력했으므로 order내의 toString()함수가 호출 됨
    }
}

```
main 함수를 실행시켜보면 <br>

![png](/images/Spring_basic(3)_files/주문 할인 main 실행.png)
<br>
위 그림과 같이 문제없이 잘 실행이 된다.<br>
<br>

이전에 이야기 했듯이 이러한 방법으로 테스트 하는 것은 좋지 않은 방법이다. <br>
(애플리케이션 로직으로 위의 방식으로 테스트 하는 방법)<br>
<br>

### -> JUnit 테스트를 이용한다
<br>

### 주문과 할인 정책 테스트

```java
package hello.spring_basic.order;

import hello.spring_basic.member.Grade;
import hello.spring_basic.member.Member;
import hello.spring_basic.member.MemberService;
import hello.spring_basic.member.MemberServiceImpl;
import org.assertj.core.api.Assertions;
import org.junit.jupiter.api.Test;

public class OrderServiceTest {
    MemberService memberService = new MemberServiceImpl();
    OrderService orderService = new OrderServiceImpl();

    @Test
    void createOrder() {
        Long memberId = 1L;
        Member member = new Member(memberId, "memberA", Grade.VIP);
        memberService.join(member);

        Order order = orderService.createOrder(memberId, "itemA", 10000);
        Assertions.assertThat(order.getDiscountPrice()).isEqualTo(1000); //VIP경우 1000원 할인해주기로 했으니 그게 되는지 확인해보자.
    }
}

```
<br><br>
OrderServiceTest 클래스를 실행시켜보면<br>
![png](/images/Spring_basic(3)_files/주문 할인 main test.png)
<br>
위 그림에서 아무 문제없다는 결과를 내 놓는다. <br>
<br>
<br>

지금까지 "주문 도메인 전체"에 대해 만들었다. <br>
이후에는 할인정책을 바꾸었을때 객체지향적으로 잘 개발됬을지 (변경 용이, 클라이언트에 영향 안받을지) 확인해보자 <br>
<br>

### Reference :
김영한 강사님 스프링 핵심 원리 - 기본편  강의 중