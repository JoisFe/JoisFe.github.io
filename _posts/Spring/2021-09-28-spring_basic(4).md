---
title: "순수 자바로만 작성하는 예제_새로운 할인 정책 개발"
categories:
  - Spring
tags:
  - 순수 자바 코드로 개발
  - 주문 할인 도메인
  - 실행과 테스트
use_math: true
---

자 이제는 기획자가 할인 정책을 이전에 구현했던 방식인 정액 할인이 아닌 정률 할인으로 변경을 원한는 상황이라고 가정하자 <br>
### 회원이 VIP인 경우 10% 할인을 해주자.
<br>
<br>
갑자기 할인 정책이 바뀌어서 난감하지만 역할과 구현을 분리를 잘 하여 개발하였다면 변경에 용이할 것이다. <br>
개발을 하면서 지금까지 정말 객체지향 설계 원칙을 잘 준수했다면 큰 문제가 없을 것인데 과연 잘 준수하였는지 확인해보자 <br><br>

자 정률 할인을 구현하기 위해   정률 할인에 대해 구현해보자

### 정률 할인 정책 RateDiscountPolicy 클래스 다이어그램
RateDiscountPolicy 클래스 다이어그램을 추가해보자. 아래와 같다.
<br>
![jpeg](/images/Spring_basic(4)_files/정률할인정책 클래스다이어그램.jpeg)
<br>

### RateDiscountPolicy(정률 할인 정책) 관련 코드

```java
package hello.spring_basic.discount;

import hello.spring_basic.member.Grade;
import hello.spring_basic.member.Member;

public class RateDiscountPolicy implements DiscountPolicy { // 이전에 작성한 인터페이스인 DiscountPolicy 상속

    private int discountPercent = 10; // 10 % 할인 할 것이니

    @Override
    public int discount(Member member, int price) {
        if (member.getGrade() == Grade.VIP) {
            return price * discountPercent / 100; // 해당 회원이 VIP라면 10% 할인

            // return price * (discountPercnet / 100) 하면 discountPercnet가 100이 아닌 이상 전부 결과가 0이 됨
            // discountPercnt / 100 결과가 0.~~ 인 경우 int 형으로 0이 되기 때문 !! 조심
        }

        else {
            return 0; // 회원이 VIP 아닐 시 할인 적용 X
        }
    }
}
```
<br>
지금 작성한 메서드인 discount가 잘 구현되었는지 걱정이 된다...<br>
이 부분이 잘 구현되었는지 테스트 해보자. <br>
<br>

### RateDiscountPolicy 잘 작성하였는지 테스트

```java
package hello.spring_basic.discount;

import hello.spring_basic.member.Grade;
import hello.spring_basic.member.Member;
import org.assertj.core.api.Assertions;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;

import static org.assertj.core.api.Assertions.*;
import static org.junit.jupiter.api.Assertions.*;

class RateDiscountPolicyTest { // VIP 회원이 10% 할인이 잘 되는지 테스트 해보자

    RateDiscountPolicy discountPolicy = new RateDiscountPolicy();

    @Test
    @DisplayName("VIP는 10% 할인이 적용되어야 한다.")
    void vip_o() {
        //given
        Member member = new Member(1L, "memberVIP", Grade.VIP); // 회원이 VIP임

        //when
        int discount = discountPolicy.discount(member, 10000); //가격이 10000원일때 할인되는 가격

        //then
        assertThat(discount).isEqualTo(1000); // 10000원의 10% 할인가격은 1000원 되어야하므로
                                              // discount가 1000원이 되는지 확인해봐야한다.
    }

    // 테스트에서 중요한 점은 성공테스트뿐만 아니라 실패테스트도 만들어 봐야한다.
    @Test
    @DisplayName("VIP가 아니면 할인이 적용되지 않아야 한다.")
    void vip_x() {
        //given
        Member member = new Member(2L, "memberBASIC", Grade.BASIC); // 회원이 VIP가 아닌 일반 회원임

        //when
        int discount = discountPolicy.discount(member, 10000); // 가격이 10000원일때 할인되는 가격

        //then
        assertThat(discount).isEqualTo(1000); // 여기서 같지 않아야한다. 회원이 VIP가 아니기 때문에 할인된 가격이 0원이 되야하므로
    }
}
```

<br>
먼저 성공하는 경우 즉 할인이 제대로 되어야 하는 상황에 테스트를 해본 결과를 확인해보자. <br>
즉 회원이 VIP인 경우 할인이 잘 적용되었는지 확인해본다. <br>
vip_o() 메서드에 해당<br>

![png](/images/Spring_basic(4)_files/정률할인정책 성공 테스트.png)
<br>
vip_o() 실행 결과 제대로 할인이 잘 된 것을 알 수 있다. <br>

그리고 실패하는 경우 즉 할인이 제대로 되지 않는 상황에 테스트를 해본 결과를 확인해 보자. <br>
즉 회원이 VIP가 아닌 BASIC인 경우 할인이 적용이 되었는지 확인해 본다. <br>
vip_x() 메서드에 해당 <br>

![png](/images/Spring_basic(4)_files/정률할인정책 실패 테스트.png)
<br>
vip_x() 실행 결과 제대로 할인이 되지 않은 것을 알 수 있다.<br>
회원이 VIP가 아니면 할인이 되면 안되니 예상한 결과대로 잘 나온것을 확인할 수 있다. <br><br>

