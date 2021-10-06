---
title: "IoC, DI, 컨테이너"
categories:
  - Spring
tags:
  - IoC
  - DI
  - 컨테이너
use_math: true
---

## IoC(Inversion of Control) 제어의 역전
기존의 프로그램 경우 클라이언트 구현 객체가 스스로 필요한 서버 구현 객체를 생성, 연결, 실행 하였다.<br>
즉 구현 객체가 프로그램의 제어 흐름을 스스로 조종했다는 의미이다. <br>
개발자 입장에서 보자면 자연스러운 흐름으로 보인다. <br>
<br>

반면에 이전에 설계한 것을 보면 AppConfig를 이용한 후 부터 구현 객체는 자신의 로직을 실행하는 역할만 담당하지 다른 역할을 할 필요가 없어졌다. <br> 
프로그램의 제어 흐름을 AppConfig가 가져가게 된 것이다. <br>
예를들면 OrderServiceImpl은 필요한 인터페이스들을 호출하지만 어떤 구현 객체들이 실행될지 모른다. <br><br>

프로그램에 대한 제어 흐름에 대한 권한은 모두 AppConfig가 가지고 있다. <br>
심지어 OrderServiceImpl도 AppConfig가 생성 하였다. <br>
그리고 AppConfig는 OrderServiceImpl이 아니라 OrderService 인터페이스의 다른 구현 객체를 생성할 수도 실행할 수도 있다.<br><br>

-> 프로그램의 제어 흐름을 직접 제어하는 것이 아니라 외부에서 관리하는 것을 <b>제어의 역전(IoC)</b>이라고 한다.
<br><br>

IoC에 대해 알아 보았는데 여기서 지금까지의 목적인 "스프링 프레임워크를 왜 쓰게 되었는지?" 에 대한 의문이 살짝 나왔다.<br>
그 부분을 알기 위해 프레임 워크와 라이브러리 차이를 알아보고자 한다. <br>
### 프레임워크 vs 라이브러리
프레임워크 : 자신이 작성한 코드를 제어하고, 대신 실행 ex) JUnit
라이브러리 : 자신이 작성한 코드가 직접 제어의 흐름을 담당
<br><br>
AppConfig가 프로그램의 제어 흐름, 제어 흐름에 대한 권한을 가졌는데 이 부분이 결국 우리가 스프링 프레임워크를 쓰게된 이유와 크게 관련이 되어있지 않을까??? 라는 생각을 할 수있다.
<br><br>

## DI(Dependency Injection) 의존관계 주입
OrderServiceImpl은 DiscountPolicy 인터페이스에 의존한다.<br>
OrderserviceImpl은 DiscountPolicy에 실제로 어떤 구현 객체가 사용되었는지 알 수 없다.<br>
<b>의존관계</b>는 정적인 클래스 의존관계와 실행 시점에 결정되는 동적인 객체(인스턴스) 의존 관계 들을 분리해서 생각해야 한다. <br><br>

### 정적인 클래스 의존관계
클래스가 사용하는 import 코드만 보고 의존관계를 쉽게 판단할 수 있다. <br>
따라서 정적인 의존관계는 애플리케이션을 실행하지 않아도 분석할 수 있다.<br>

아래 클래스 다이어그램을 보자
### 클래스 다이어그램
![jpeg](/images/Spring_basic(9)_files/주문 도메인 클래스 다이어그램.jpeg)
<br>
OrderServiceImpl은 MemberRepository, DiscountPolicy에 의존한다는 것을 확인할 수 있다. <br>
그러나 이러한 클래스 의존관계 만으로는 실제 어떤 객체가 OrderServiceImpl에 주입될지 알 수 없다. <br>
즉 OrderServiceImpl는 자신이 의존하는 인퍼페이스인  MemberRepository에 구현 객체가 MemoryMemberRepository가 사용되었을지 DbMemberRepository가 사용되었을지 알 수 없다.<br>
마찬가지로 OrderServiceImpl은 자신이 의존하는 인터페이스인 DiscountPolicy에 구현 객체가 FixDiscountPolicy가 사용될지 RateDiscountPolicy가 사용될지 모른다. <br><br>

