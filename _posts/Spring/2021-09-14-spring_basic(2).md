---
title: "순수 자바로만 작성하는 예제_회원 도메인 개발"
categories:
  - Spring
tags:
  - 순수 자바 코드로 개발
  - 회원 도메인
  - 실행과 테스트
use_math: true
---

Spring이 왜 생기게 되었는지 알기위해  순수한 자바코드로  예제를 작성해보며 Spring의 필요성을 느껴보고자 한다. <br>
<br>

사용할 IDE : IntelliJ <br>
<br>
프로젝트 생성 : start.spring.io에서<br>
Project : Gradle Project <br>
Spring Boot : 2.5.4<br>
Language : Java<br>
Packaging : Jar<br>
<b>Dependencies : 선택 X </b><br>
<br>
아니 순수 자바코드로만 작성한다 해놓고 왜 스프링 부트 스타터로 프로젝트 생성 ?? <br>
-> 단순히 프로젝트 환경설정을 편리하게 설정하기 위해서 임, Dependencies 아무것도 설정 안했으니 Spring boot가 core 쪽 library만을 가지고 project 만든다. <br><br>

예제는 특정 상황을 가정할 것이다. <br>
비즈니스 요구사항이 주어질 것이고 그 요구사항에 맞게 설계를 할 것이다.<br>
처음에는 순수 자바코드로 작성할 것임 !! <br>

# 요구 사항 :
### 회원 :
1. 회원가입을하고 해당 회원을 조회할 수 있음<br>
2. 회원은 2가지 등급이 존재  (1) 일반 등급, (2) VIP 등급 <br>
3. 회원 데이터는 자체 DB를 구축할 수도 있고 외부 시스템과 연동할 수도 있음 -> 미확정인 상태임 !!!

### 주문과 할인 정책 :
1. 회원가입을 한 회원은 상품을 주문할 수 있음<br>
2. 회원은 등급에 따라서 할인 정책을 적용받을 수 있음 <br>
3. 할인 정책은 모든 VIP등급의 회원들에게 1000원을 할인해 주는 고정 금액 할인을 적용해 달라는 요구가 있음 (나중에 변경될 수 있음) <br>
4. 할인 정책은 변경가능성이 높기 때문에 (아직 기본 할인 정책을 회사에서 정하지 못함), 오픈 직전까지 이 고민을 미루려고 함 <br>
 혹은 할인을 적용 안할수도 있음 <br><br>
 
 ### 요구 사항에서 약간 골치아픈 문제가 2개이다. <br>
1. 회원 데이터에 대한 부분이 아직 미확정인 상태 <br>
2. 할인정책이 변경가능성이 크고 적용 안할 가능성 또한 있음 <br>
<br>

이러한 문제점이 있다고 해서 정책이 확실히 결정되기 까지 해당 부분 개발을 하지않고 기다릴 수는 없다. <br.
그럼 어떻게 해결 해야할까??<br>
<br>

이전에 객체 지향 설계 방법의 장점을 기억할 것이다. <br>
역할과 구현을 나누면 (인터페이스와 구현 객체 생각!!) -> 유연하고 변경이 용이해짐 <br>
<b>즉 인터페이스를 만들고 구현체를 얼마든지 갈아끼울 수 있도록 설계하면 됨 </b> <br>
<br>

# 설계를 시작해보자

먼저 도메인을 설계 해보자.

## 회원 도메인 설계

### 회원 도메인 요구 사항
1. 회원가입 할 수 있고 회원가입한 회원을 조회할 수 있음 <br>
2. 회원은 일반과 VIP 두 가지 등급 존재 <br>
3. 회원 데이터는 자체 DB를 구축 할 수 잇고 외부 시스템과 연동할 수도 있다. (미확정인 상태) <br>

### 회원 도메인 협력 관계
![jpeg](/images/Spring_basic(2)_files/회원 도메인 협력관계.jpeg)
<br>
여기서 클라이언트, 회원 서비스, 회원 저장소 3개는 역할 <br>
회원 데이터를 어떻게 저장하지 아직 확실히 정하지 않았으니 <br>
일단 먼저 DB를 구축하기 전에 기본적인 메모리 회원 저장소를 구현하고 <br>
차 후에 DB 회원 저장소, 외부 시스템 연동 회원 저장소를 구현 하려 한다. <bn>
즉 회원 저장소 역할의 구현으로 메모리 회원 저장소, DB 회원 저장소, 외부 시스템 회원 저장소 3가지를 구현하고자 한다. <br><br>

### 회원 클래스 다이어그램
![jpeg](/images/Spring_basic(2)_files/회원 클래스 다이어그램.jpeg)
<br>
회원 도메인 협력 관게를 클래스로 나타내보면 회원 클래스 다이어그램과 같다. <br>
각 역할들을 interface로, 역할들에 대한 구체적인 구현을 인터페이스에 상속받은 클래스로 구현하였다. <br>
<br>
간단히 정리하자면 <br>
인터페이스 : 역할 <br>
상속받은 다른 클래스 : 구현 <br>
<br>
인터페이스는 메서드 선언만 하고 <br>
상속 받은 다른 클래스는 implements 상속으로 메서드 정의 (메서드 오버라이딩)<br><br>

MemoryMemberRepository 클래스 경우 일단 간단한 메모리 저장하는 역할을 하는 클래스를 정의 하였고 <br>
DbMembeerRepository 클래스 경우 자체 DB 구축 에 대해서 정의할 클래스이다. (DB 부분은 차후에 정의할 것) <br>
<br>

이렇게 역활과 구현을 분리해 두면 유연하고 변경이 용이해 지기 때문에 이전에 비즈니스 요구사항 중 회원 데이터 부분이 아직 미확정 이라고 하였는데 나중에 변경이 일어나도 유연하게 대처가 가능해진다. <br><br>

### 회원 객체 다이어 그램
![jpeg](/images/Spring_basic(2)_files/회원 객체 다이어그램.jpeg)
<br>

클래스 다이어그램에서 정의한 클래스를 이용해 객체를 만들어 객체간의 참조 구조를 작성해보면 위와 같다.<br>
<br>

회원 서비스 경우에는 new MemberServiceImpl(); 을 통해  <br>
메모리 회원 저장소경우에는 new MemoryMemberRepository();를 통해  <br>
객체를 생성하고자 한다. <br> <br>

# 회원 도메인 구조를 기준으로 코드로 작성해보도록 하자. <br>

코드 작성에 앞서 패키지 이름은 spring_basic이다. <br>
코드 윗부분에 패키지, 클래스 명을 보면 파일 구조를 파악할 수 있을 것이다. <br>

마지막 부분에 전체적인 파일 구조를 정리하도록 하겠다. <br>

### Gradle 전체설정
build.gradle
```java
plugins {
	id 'org.springframework.boot' version '2.5.4'
	id 'io.spring.dependency-management' version '1.0.11.RELEASE'
	id 'java'
}

group = 'hello'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '11'

repositories {
	mavenCentral()
}

dependencies {
	implementation 'org.springframework.boot:spring-boot-starter'
	testImplementation 'org.springframework.boot:spring-boot-starter-test'
}

test {
	useJUnitPlatform()
}
```

## 회원 엔티티에 대해 작성해 보자 <br>

### 회원 등급

```java
package hello.spring_basic.member;

public enum Grade { //Enum으로  회원의 등급 설정
    Basic,
    VIP
}

//Enum class는 열거형이라 불리며 서로 연관된 상수들의 집합을 의미
//(기존 상수를 정의하던 final static string과 같이 문자열이나 숫자들을 나타낸는 기본자료형의 값을 enum 이용해서 나타낼 수 있음)
```

### 회원 엔티티

```java
package hello.spring_basic.member;

public class Member { //회원 entity에 대한 클래스를 만듬
    private Long id;
    private String name;
    private Grade grade;

    public Member(Long id, String name, Grade grade) {
        this.id = id;
        this.name = name;
        this.grade = grade;
    }

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Grade getGrade() {
        return grade;
    }

    public void setGrade(Grade grade) {
        this.grade = grade;
    }
}

```

## 회윈 저장소에 대해 작성해 보자

### 회원 저장소 인터페이스
```java
package hello.spring_basic.member;

public interface MemberRepository {  //인터페이스
    void save (Member member); // 회원을 저장하는 메서드

    Member findById(Long memberId); //회원의 아이디로 회원을 찾는 메서드
}
```

### 메모리 회원 저장소 구현체
```java
package hello.spring_basic.member;

import java.util.HashMap;
import java.util.Map;

public class MemoryMemberRepository implements MemberRepository {
    //implements : 부모 객체는 선언만 하며 정의(내용)은 자식에서 오버라이딩 해서 사

    private static Map<Long, Member> store = new HashMap<>(); // 데이터 저장하기 위해 해시맵에 데이터 저장

    // HashMap은 사실 동시성 문제가 발생할 수 있으므로 그런 경우에는 ConcurrentHashMap 사용하면 된다. 

    @Override
    public void save(Member member) {
        store.put(member.getId(), member);
    } //회원 저장하는 메서드 정의

    @Override
    public Member findById(Long memberId) {
        return store.get(memberId);
    } //회원 ID로 회원 찾는 메서드 정의
    
    //인터페이스에서 선언한 메서드를 구체적으로 메서드 정의 -> 구현
}

```
<br>
DB가 아직 확정이 되지 않았음. 하지만 그렇다고 개발을 안할수는 없음. <br>
-> 개발은 진행하되 단순한 메모리 회원 저장소(단순히 해시맵에 저장하여 구현)를 구현해서 개발 진행할 것
<br><br>

## 회원 서비스에 대해 작성해 보자.

### 회원 서비스 인터페이스

```java
package hello.spring_basic.member;

public interface MemberService {

    void join (Member member); //회원 가입 메서드 선언

    Member findMember(Long memberId); // 회원 조회 메서드 선언
}

```

### 회원 서비스 구현체

```java
package hello.spring_basic.member;

public class MemberServiceImpl implements MemberService {

    private final MemberRepository memberRepository = new MemoryMemberRepository(); // 회원 가입 메서드 정의

    @Override
    public void join(Member member) {
        memberRepository.save(member);
        /*
        save메서드 호출시 다형성에 의해 인터페이스인 MemberRepository클래스가 아닌 MemoryMemberRepository 클래스에 있는 save함수를 호출한다.
        */
    }

    @Override
    public Member findMember(Long memberId) {
        return memberRepository.findById(memberId);
    } // 회원 조회 메서드 정의
    
    // 인터페이스에서 선언한 메서드를 구체적으로 메서드 정의 -> 구현
}

```
<br><br>

자 이제 회원 도메인 개발을 해 보았으니 제대로 작동하는지 테스트를 해보자. <br>
<br>

# 회원 도메인 실행과 테스트를 해보자.

### 회원 도메인 - 회원 가입 main
main 함수를 만들어 직접 회원을 만들어 회원가입 시키고 회원가입이 되었는지 확인해보자. <br>

```java
package hello.spring_basic;

import hello.spring_basic.member.Grade;
import hello.spring_basic.member.Member;
import hello.spring_basic.member.MemberService;
import hello.spring_basic.member.MemberServiceImpl;

public class MemberApp {

    public static void main(String[] args) {
        MemberService memberService = new MemberServiceImpl(); // MemberService의 객체 memberservice 생성 (MemberServiceImpl 구현 가지는)
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

main 함수를 실행시켜보면 <br>

![png](/images/Spring_basic(2)_files/회원가입 main 실행.png)
<br>
위 그림과 같이 문제없이 잘 실행이 된다.<br>
<br>

하지만 이러한 방법으로 테스트 하는 것은 좋지 않은 방법이다. <br>
(애플리케이션 로직으로 위의 방식으로 테스트 하는 방법)<br>
<br>

### -> JUnit 테스트를 이용한다
JUnit은 자바 프로그래밍 언어용 유닛 테스트 프레임워크로 <br>
@Test 메서드가 호출되면 독립적인 테스트가 가능하다. -> 어노테이션으로 편리하게 테스트 가능

<br><br>

### 회원 도메인 - 회원 가입 테스트

```java
package hello.spring_basic.member;

import org.assertj.core.api.Assertions;
import org.junit.jupiter.api.Test;

public class MemberServiceTest {

    MemberService memberService = new MemberServiceImpl();

    @Test
    void join() {
        //given : 이런 이런 환경 주어졌을때
        Member member = new Member(1L, "memberA", Grade.VIP);

        //when : 이렇게 했을때
        memberService.join(member);
        Member findMember = memberService.findMember(1L);

        //then : 이렇게 된다. -> 검증
        Assertions.assertThat(member).isEqualTo(findMember);

        //정리하면 새로운 member가 주어졌을때
        // 그 새로운 멤버를 회원가입(등록) 했을때
        // member와 findMember가 같아야 한다.
    }
}

```
test 코드를 작성하는 팁으로 대부분 위에서 적어둔 given, when, then 방식이 잘 맞아 떨어진다고 한다. (모두 그런것은 아님) <br>
<br>
<br>
MemberServiceTest 클래스를 실행시켜보면<br>
![png](/images/Spring_basic(2)_files/회원가입 main test.png)
<br>
위 그림에서 아무 문제없다는 결과를 내 놓는다. <br>
<br>

지금까지 회원 도메인을 설계하여 실행해보고 테스트 또한 해보았는데 아무 문제없이 실행되었던 것 같다. <br>
실행이 잘 되었다고 설계에 있어서 문제점은 없었을까?
<br>

### 회원 도메인 설계의 문제점 :
MemberServiceImpl 클래스를 보면 <br>

```java
public class MemberServiceImpl implements MemberService {

    private final MemberRepository memberRepository = new MemoryMemberRepository(); 

    ~~~
```
위 코드를 보면 <br>
memberRepository는 interface인 MemebrRepository에 의존하고 있다. <br>
그런데 memberRepository = new MemoryMemberRepository(); 이 코드를 보면 <br>
실제 할당하는 부분이 구현체에 의존하고 있음을 알 수 있다. (MemoryMemberRepository는 구현체) <br>
<br>
따라서 MemberServiceImpl 클래스는 추상화와 구체화 모두에 의존하고 있다. <br>
<b>-> DIP를 위반하고 있다는 의미이다. 즉 OCP 원칙을 지키지 못한 상황이다.</b> <br>
-> 이 문제를 잘 기억하고 있다가 나중에 어떻게 해결하는지 알아보도록 하자. <br>
<br>

### Reference :
김영한 강사님 ㅅ프링 핵심 원리 - 기본편  강의 중