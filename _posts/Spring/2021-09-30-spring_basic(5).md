---
title: "관심사의 분리"
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
use_math: true
---

이전에 설계한 것을 DIP를 지키도록 하기 위해 <br>
private DiscountPolicy discountPolicy; 로 변경함으로써<br>
인터페이스에만 의존하도록 바꾸었다. <br><br>

하지만 구체적인 것이 있어야 뭔가 돌아가지 인터페이스로만 돌아가지 않았다. <br>
그것을 해결할 방법으로 누군가가 클라이언트인 OrderServiceImpl에 DiscountPolicy의 구현 객체를 대신 생성하고 주입해주면 된다고 하였다. <br> <br>
그리고 나의 의문점은 <br>
음 ?? 그럼 누군가가 도대체 누구고?? <br>
누군가가 있다면 어떻게 대신 구현 객체를 생성하고 주입해주는지 모르겠다는 점이였다. <br><br>

이것은 <b>관심사의 분리</b>로 가능하다.
<br>

# 관심사의 분리
도대체 관심사의 분리가 무엇인지 이해가 되지 않는다. <br>
따라서 이전에 언급하였던 드라마로 예를 들어 보겠다. <br>
드라마 "나의 아저씨"에서 실제 배역에 맞는 배우를 선택해야한다고 하자<br>
누가 배우를 선택해야 할까??<br><br>

나의 아저씨 드라마에서 "이지안"역을 누가할지 정하는 것은 배우가 정하는게 아니다!!! <br>
그런데 이전에 구현한 코드를 보면 배우가 다른 배우를 정하는 것과 비슷하다.<br>
마치 남자 주인공 "박동훈"역할(인터페이스)으로 선정된 이선균 배우(구현체)가 여자 주인공 "이지안"역할(인터페이스)으로 특정 배우(구현체)를 선택하는 것과 같은상황이다. <br> <br>

이렇게 되면 이선균 배우(구현체)가 연기도 해야하고, 담당 배우 또한 섭외해야하는 <b>많은 책임</b>을 가지게 된다.<br><br>

이전 코드 OrderServiceImpl 클래스 일부를 다시보면 <br>
```java
public class OrderServiceImpl implements OrderService {

    //OrderService는 2개가 필요
    private final MemberRepository memberRepository = new MemoryMemberRepository(); // memberRepository에서 회원 찾아야 하므로
    private final DiscountPolicy discountPolicy = new FixDiscountPolicy(); // discountPolicy에서 할인 정책되로 적용 해야하므로
    
```
위 코드르 보면 OrderServiceImpl 에서<br>
private final DiscountPolicy discountPolicy = new FixDiscountPolicy(); 코드를 확인할 수 있다.<br>
<br>
OrderServiceImpl은 order service에 관련된 기능만 해야하는데 <br>
dsicountPolicy가 FixDiscountPolicy()가 되야한다라고 선택하고 있다.<br>
즉 할인 정책까지 OrderServiceImpl에서 선택하는 많은 책임을 가지고 있다.<br><br>

다시 정리하면 OrderServiceImpl이 order service에 관련된 기능 뿐만 아니라 "할인 정책에 대한 것이 정액 할인 정책이어야 한다." 라는 구체적인 역할까지 직접 할인 정책에 대한 객체를 생성하고 구현체 까지 선택하고 있다.<br><br>

즉 이선균 배우가 자신의 "박동훈" 역할에 대한 연기 뿐만 아니라 다른 배역을 섭외하는 역할까지 가지고 있는 상황과 유사한 것을 알 수 있다.<br><br>

### 따라서 관심사를 분리해야 한다. <br>
배우는 본인의 역할인 해당 배역에 대한 연기만 잘하도록 집중해야한다.<br>
이선균 배우는 "이지안"역할에 어떤 배우가 오든 똑같이 연기를 할 수 있어야 한다.<br><br>

그리고 배우를 섭외하는 역할은 별도의 드라마 기획 담당자들이 해야한다. <br>
-> 즉 드라마 기획자가 나올 시점!!! <br>
<br>
<b>드라마 기획자를 따로 만들어 배우를 뽑는 일을 시켜 배우와 드라마 기획자의 책임을 확실하게 분리해야 한다.</b>
<br><br>

이전에 누군가가 클라이언트인 OrderServiceImpl에 DiscountPolicy의 구현 객체를 대신 생성하고 주입해주면 된다라고 하였는데 <br>
위의 예시에서 드라마 기획자가 누군가가 되는 것이다 <br><br>

### 드라마 예시처럼 애플리케이션 또한 실제 실행되는 객체들은 본인의 역할만 수행해야 하고 <b>어떤 구현체들이 인터페이스에 할당될지는 드라마 기획자 처럼 누군가가 해줘야 한다.</b> <br>

그 누군가의 역할을 -> <b>AppConfig</b>가 할 것이다. <br>
<br>

## AppConfig 등장
애플리케이션의 전체 동작 방식을 구성(config)하기 위해<br>
<b>구현 객체를 생성</b>하고, <b>연결</b> 하는 책임을 가진 별도의 설정 클래스를 만들 것이다. <br>
그 설정 클래스가 AppConfig 이다.

### AppConfig에 대한 코드를 보면

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

### AppConfig는 애플리케이션의 실제 동작에 필요한 구현 객체를 생성한다.
1. MemberServiceImpl<br>
2. MemoryMemberRepository<br>
3. OrderServiceImpl<br>
4. FixDiscountPolicy<br>
<br>

### AppConfig는 생성한 객체 인스턴스의 참조(레퍼런스)를 생성자를 통해서 주입(연결) 해준다. -> 생성자 주입
1. MemberServiceImpl -> MemoryMemberRepository<br>
2. OrderServiceImpl -> MemoryMemberRepository, FixDiscountPolicy <br>
<br>

이제 MemberServiceImpl 클래스와 OrderServiceImpl 클래스 코드를 변경해 호자, <br>

### MemberServiceImpl - 생성자 주입
```java
package hello.spring_basic.member;

public class MemberServiceImpl implements MemberService {

    private final MemberRepository memberRepository;
    // new MemoryMemberRepository() 지움
    // memberRepository의 구현체 선택을 여기서 바로 하지 말자 -> DIP 꺠트리기 때문
    // 즉 인터페이스에만 의존하도록 변경 (구체 클래스에 의존 하지 않음)

    // MemberServiceImpl의 생성자를 만듬 -> 생성자를 통해 memberRepository의 구현체가 무엇이 들어갈지 선택
    public MemberServiceImpl(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }

    // 자 이렇게 코드를 고치고 나니 MemberServiceImpl에 MemoryMemberRepository에 대한 부분이 없다.
    // 오로지 MemberRepository라는 인터페이스만 존재한다.
    // 즉 추상화만 의존한다. -> 드디어 DIP를 지켰다.
    // 구체적인것에 대한것은 MemberServiceImpl은 전혀 모른다.
    // 누군가가 memberRepository에 MemoryMemberRepository를 넣어줄지, DBMemberRepository를 넣어줄지 모른다.

    // 구체적인 것에 대한것은 생성자를 통해 밖에서(AppConfig) 생성해서 넣어줌
    // 즉 생성자를 통해 객체가 (new MemoryMemberRepository())가 생성되어 들어감
    // -> "생성자 주입" 이라고 함.

    @Override
    public void join(Member member) {
        memberRepository.save(member);
    }
    /*
    save메서드 호출시 다형성에 의해 인터페이스인 MemberRepository클래스가 아닌 MemoryMemberRepository 클래스에 있는 save함수를 호출한다.
    */

    @Override
    public Member findMember(Long memberId) {
        return memberRepository.findById(memberId);
    } // 회원 조회 메서드 정의

    // 인터페이스에서 선언한 메서드를 구체적으로 메서드 정의 -> 구현
}

```
설계의 변경으로 인해 MemberServiceImpl은 더이상 MemoryMemberRepository를 의존하지 않음 <br><br>
단지 MemberRepository 인터페이스에만 의존 <br><br>

MemberServiceImpl 입장에서 생성자를 통해 어떤 구현 객체가 들어올지(주입될지)는 알 수 없음.<br>
MemberServiceImpl의 생성자를 통해서 어떤 구현 객체를 주입할지는 오직 외부(AppConfig)에서 결정된다. <br><br>

MemberServiceImpl은 앞으로 <b>의존관계</b>에 대한 고민들은 외부(AppConfig)에 맡기고 오직 <b>실행</b>에 대한 부분에만 집중하면 된다. <br><br>

자 설계를 변경하였으니 변강한 대로 클래스 다이어그램으로 다시 나타내보자.

### 변경 후 클래스 다이어그램
![jpeg](/images/Spring_basic(5)_files/변경 후 클래스 다이어그램.jpeg) 
<br>

객체의 생성과 연결은 AppConfig가 담당하고 있음을 알 수 있다. <br>
<b>DIP</b> 확실하게 지키게됨 : MemberServiceImpl은 MemberRepository인 추상에만 의존하면 된다. <br>
이제부턴 구체 클래스를 몰라도 된다.!!! <br><br>

<b>관심사의 분리</b> : 객체를 생성하고 연결하는 역할과 실행하는 역할이 명확히 분리 되었음을 알 수 있다. <br><br>

이제는 변경 후 회원 객체 인스턴스 다이어그램을 나타내 보자
### 변경 후 회원 객체 인스턴스 다이어그램
![jpeg](/images/Spring_basic(5)_files/변경 후 객체다이어그램.jpeg) 
<br>

appConfig 객체는 memoryMemberRepository 객체를 생성하고 그 참조값을 memberServiceImpl을 생성하면서 생성자로 전달한다.<br>
<br>

클라이언트인 memberServiceImpl 입장에서 보면 의존관계를 마치 외부에서 주입해주는 것 같다고 하여 <b>DI(Dependency Injection)</b> 이라고 하며 <b>의존관계 주입</b> 또는 <b>의존성 주입</b> 이라고 한다. 
<br><br>

이제는 OrderServiceImpl에 대해 설계를 변경해보자. <br>

### OrderServiceImpl - 생성자 주입
```java
package hello.spring_basic.order;

import hello.spring_basic.discount.DiscountPolicy;
import hello.spring_basic.discount.FixDiscountPolicy;
import hello.spring_basic.discount.RateDiscountPolicy;
import hello.spring_basic.member.Member;
import hello.spring_basic.member.MemberRepository;
import hello.spring_basic.member.MemoryMemberRepository;

public class OrderServiceImpl implements OrderService {

    //OrderService는 2개가 필요
    private final MemberRepository memberRepository;
    // new MemoryMemberRepository(); 지움
    // memberRepository의 구현체 선택을 여기서 바로 하지 말자 -> DIP 꺠트리기 때문
    // 즉 인터페이스에만 의존하도록 변경 (구체 클래스에 의존 하지 않음)

    // private final DiscountPolicy discountPolicy = new FixDiscountPolicy(); // discountPolicy에서 할인 정책되로 적용 해야하므로
    // private final DiscountPolicy discountPolicy = new RateDiscountPolicy(); // 정액 할인에서 정률 할인으로 바꿈
    private DiscountPolicy discountPolicy;
    // new FixDiscountPolicy(); 지움
    // discountPolicy의 구현체 선택을 여기서 바로 하지 말자 -> DIP 꺠트리기 때문
    // 즉 인터페이스에만 의존하도록 변경 (구체 클래스에 의존 하지 않음)

    // 생성자를 만들자
    public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
        this.memberRepository = memberRepository;
        this.discountPolicy = discountPolicy;

        // 자 이렇게 코드를 고치고 나니 OrderServiceImpl에 MemoryMemberRepository와 FixDiscontPolicy에 대한 부분이 없다.
        // 오로지 MemberRepository, DiscountPolicy라는 인터페이스만 존재한다.
        // 즉 추상화만 의존한다. -> 드디어 DIP를 지켰다.
        // 구체적인것에 대한것은 OrderServiceImpl은 전혀 모른다.

        // 구체적인 것에 대한 것은 생성자를 통해 밖에서(AppConfig) 생성해서 넣어줌
        // 즉 생성자를 통해 객체가 (new MemoryMemberRepository(), new FixDiscountPolicy())가 생성되어 들어감
        // -> "생성자 주입" 이라고 함.
    }

    // OrderServiceImpl 클래스는 인터페이스에만 의존하고 있으므로
    // 구체적인 클래스에 대해서 전혀 모름
    // 누군가가 memberRepository에 MemoryMemberRepository를 넣어줄지, DBMemberRepository를 넣어줄지 모른다.
    // discountPolicy에 FixDiscountPolicy를 넣어줄지 RateDiscountPolicy를 넣어줄지 모른다.
    // 철저하게 DIP를 지키고 있음을 알 수 있다.

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
         */
    }
}

```

<br>
설계 변경으로 OrderServiceImpl은 FixDiscountPolicy를 의존하지 않게되었다. <br>
단지 DiscountPolicy 인터페이스에만 의존한다.<br><br>

OrderServiceImpl 입장에서 생성자를 통해 어떤 구현 객체가 들어올지(주입될지) 알 수 없다. <br>
OrderServiceImpl 생성자를 통해서 어떤 구현 객체를 주입할지는 오직 외부(AppConfig)에서 결정한다. <br><br>

OrderServiceImpl은 앞으로 <b>의존관계</b>에 대한 고민들은 외부에 맡기고 <br>
<b>실행</b>에만 집중하면 됨 <br><br>

코드를 보면 <br>
OrderServiceImpl에는 MemoryMemberRepository와 FixDiscountPolicy 객체의 의존관계가 주입되었음을 확인할 수 있다. <br>

