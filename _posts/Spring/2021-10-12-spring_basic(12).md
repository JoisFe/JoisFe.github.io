---
title: "Spring 빈 조회"
categories:
  - Spring
tags:
  - 스프링 컨테이너
  - 스프링 빈
  - 스프링 빈 조회
use_math: true
---

이전에 컨테이너에 빈을 등록하였다. <br>
그렇다면 등록한 빈을 어떻게 조회할 수 있을까?<br>

## 컨테이너에 등록된 모든 빈, 애플리케이션 빈 출 조회

```java
package hello.spring_basic.beanfind;

import hello.spring_basic.AppConfig;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.config.BeanDefinition;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class ApplicationContextInfoTest {
    AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);

    @Test
    @DisplayName("모든 빈 출력하기")
    void findAllBean() {
        String[] beanDefinitionNames = ac.getBeanDefinitionNames(); // 스프링에 등록된 모든 빈 이름을 조회

        for (String beanDefinitionName : beanDefinitionNames) {
            Object bean = ac.getBean(beanDefinitionName); // 빈 이름이 beanDefinitionName으로  빈 객체(인스턴스)를 조회

            System.out.println("name = " + beanDefinitionName + "object = " + bean);
        }
    }

    @Test
    @DisplayName("애플리케이션 빈 출력하기")
    void findApplicationBean() {
        String[] beanDefinitionNames = ac.getBeanDefinitionNames();

        for (String beanDefinitionName : beanDefinitionNames) {
            BeanDefinition beanDefinition = ac.getBeanDefinition(beanDefinitionName); // 빈 하나하나에 대한 메타데이터 정보

            // getRole()로 스프링 내부에서 사용되는 빈인지 내가 등록한 빈인지 알 수 있음
            // ROLE_APPLICATION -> 일반적으로 사용자가 정의한 빈 혹은 외부 라이브러리
            if (beanDefinition.getRole() == BeanDefinition.ROLE_APPLICATION) {
                Object bean = ac.getBean(beanDefinitionName);

                System.out.println("name = " + beanDefinitionName + "object = " + bean);
            }
        }
    }
}

```

<br>
먼저 모든 빈을 출력하는 findAllBean() 코드를 보자 <br>
ac.getBeanDefinitionNames() : 스프링에 등록된 모든 빈 이름을 조회한다. <br>
ac.getBean() : 빈 이름으로 빈 객체(인스턴스)를 조회한다. <br>
<br>
findAllBean() 함수를 실행시켜보켜보면 <br>
![png](/images/Spring_basic(12)_files/모든 빈 조회.png)

<br>

컨테이너에 있는 모든 빈을 조회할 수 있음을 확인할 수 있다.<br>
우리가 등록한 appConfig 및 등록한 4가지 빈이 확인 가능하다. <br> 
나머지는 스프링이 내부적으로 스프링 자체를 확장하기 위한 기반의 빈들임
<br> <br>


애플리케이션 빈을 출력하는 findApplicationBean() 코드를 보자 <br>
스프링 내부에서 사용하는 빈은 제외하고 사용자가 등록한 빈만 출력해 볼 것이다. <br>

<br>
getBeanDefinition() : 빈 하나 하나에 대한 메타데이터 정보 <br>
<br>
스프링 내부에서 사용하는 빈은 getRole()로 구분 가능 <br>
ROLE_APPLICATION : 일반적으로 사용자가 정의한 빈 또는 외부 라이브러리 <br>
ROLE_INFRASTRUCTURE : 스프링 내부에서 사용하는 빈 <br><br>

findApplicationBean() 함수를 실행시켜 보면 <br>
![png](/images/Spring_basic(12)_files/애플리케이션 빈 조회.png)

<br>
등록한 appConfig 및 등록한 4가지 빈만이 확인 가능하다.<br>
스프링 내부에서 사용하는 빈은 확인할 수가 없다. <br>
<br>

그렇다면 ROLE_APPLICATION 을 ROLE_INFRASTRUCTRE로 변환하여 스프링 내부에서 사용하는 빈이 조회되는지 확인해 보자 <br>

```java
//if (beanDefinition.getRole() == BeanDefinition.ROLE_APPLICATION) 
if (beanDefinition.getRole() == BeanDefinition.ROLE_INFRASTRUCTURE) 
```
<br>
위 코드로 변경하고 findApplicationBean() 함수를 실행시켜 보면 <br>
![png](/images/Spring_basic(12)_files/내부 빈 조회.png)

<br>

등록한 빈이 아닌 스프링 내부에서 사용하는 빈만이 조회됨을 확인할 수 있다.<br>

## 스프링 빈 조회 - 기본
스프링 컨테이너에서 스프링 빈을 찾는 가장 기본적인 조회 방법을 알아보자.<br>
### ac.getBean(빈 이름, 타입)
: 빈 이름과 타입으로 빈을 조회하는 방법 <br>

### ac.getBean(타입)
: 빈 이름을 생략하고 타입으로만 빈을 조회하는 방법 <br>
<br>

### 조회 대상 스프링 빈이 없으면 예외가 발생한다. <br>
NoSuchBeanDefinitionException: No bean named 'xxxxx' available <br>
<br>

테스트 코드를 통해 알아보자

```java
package hello.spring_basic.beanfind;

import hello.spring_basic.AppConfig;
import hello.spring_basic.member.MemberService;
import hello.spring_basic.member.MemberServiceImpl;
import org.assertj.core.api.Assertions;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.NoSuchBeanDefinitionException;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

import static org.junit.jupiter.api.Assertions.*;

class ApplicationContextBasicFindTest {
    AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);

    @Test
    @DisplayName("빈 이름으로 조회")
    void findBeanByName() {
        MemberService memberService = ac.getBean("memberService", MemberService.class); // 빈 이름, 타입으로 조회

        Assertions.assertThat(memberService).isInstanceOf(MemberServiceImpl.class);
    }

    @Test
    @DisplayName("이름 없이 타입으로만 조회")
    void findBeanByType() {
        MemberService memberService = ac.getBean(MemberService.class); // 타입으로만 조회 (타입이 인터페이스 명임)

        Assertions.assertThat(memberService).isInstanceOf(MemberServiceImpl.class);
    }

    // 위에는 인터페이스로 타입으로 조회한 것임 (-> 인터페이스의 구현체가 대상이되어 조회됨)

    @Test
    @DisplayName("구체 타입으로 조회") // 인터페이스가 아닌 구체 타입으로 조회
    void findBeanByName2() {
        MemberService memberService = ac.getBean("memberService", MemberService.class); // 타입이 인터페이스 명이 아닌 구체 타입의 명임

        Assertions.assertThat(memberService).isInstanceOf(MemberServiceImpl.class);
    }

    // 타입이 꼭 인터페이스 일 필요는 없음
    // AppConfig 에서 보면
    // @Bean
    // public MemberService memberService() { //MemberService를 Appconfig에서 만듬
    //        return new MemberServiceImpl(memberRepository());
    // 반환타입이 MemebrService로 인터페이스 여야할 것 같지만
    // return에서 보이는 것과 같이 Bean에 등록된 인스턴스 타입을 통해 결정되기 때문에 꼭 인터페이스 일 필요는 없음

    // 물론 구체 타입으로 조회하는 것은 좋지 않은 방법임 (가능 하지만 )
    // 왜냐하면 역할과 구현을 구분하고 역할에 의존하다록 코드를 작성하는 것이 좋다.

    // 실패 테스트도 만들어주기
    @Test
    @DisplayName("빈 이름으로 조회X")
    void findBeanByNameX() {
        // ac.getBean("xxxxx", MemberService.class);
        // 존재하지 않는 빈 이름 "xxxxx"로 조회
        assertThrows(NoSuchBeanDefinitionException.class, () -> ac.getBean("xxxxx", MemberService.class));
        // () -> 이후에 있는 로직을 실행하면 왼쪽에 있는 예외가 터져야한다. (예외가 던저저야 성공)

    }
}

```

위 테스트를 실행시켜 보면 <br>
![png](/images/Spring_basic(12)_files/기본조회 결과.png)

<br>

그런데 타입으로만 빈을 조회하면 타입이 겹치면 어떻게 조회 해야하지 ?? <br>
그 부분에 대해 알아보자 <br><br>

## 스프링 빈 조회 - 동일한 타입이 둘 이상
타입으로 조회시 같은 타입의 스프링 빈이 둘 이상이면 오류가 발생한다. <br>
이 경우는 빈 이름을 지정한다. <br>
<br>

### ac.getBeanOfType()
해당 타입의 모든 빈을 조회.

<br><br>

코드로 확인해 보자.
<br>

```java
package hello.spring_basic.beanfind;

import hello.spring_basic.AppConfig;
import hello.spring_basic.member.MemberRepository;
import hello.spring_basic.member.MemoryMemberRepository;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import java.beans.BeanProperty;

public class ApplicationContextSameBeanFindTest {
    // 이전처럼 AppConfig.class 가 아닌 따로만든 SameBeanConfig.class 이용
    AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(SameBeanConfig.class);

    @Test
    @DisplayName("타입으로 조회시 같은 타입이 둘 이상 있으면 중복 오류가 발생한다")
    void findBeanByTypeDuplicate() {
        MemberRepository bean = ac.getBean(MemberRepository.class); // 타입만 지정하여 빈 조회
    }

    // config 따로 만듬 (테스트 코드이니 여기에서만 사용할 것임)
    @Configuration
    static class SameBeanConfig {

        @Bean
        public MemberRepository memberRepository1() {
            return new MemoryMemberRepository();
        }

        @Bean
        public MemberRepository memberRepository2() {
            return new MemoryMemberRepository();
        }

        // SameBeanConfig 클래스에는 스프링 컨테이너가 스프링 빈 2개만 등록
        // 2개의 빈의 타입이 같음 (MemoryMemberRepository)
    }
}

```

<br>
위의 ApplicationContextSameBeanFindTest 클래스를 실행시켜 보면 <br>
![png](/images/Spring_basic(12)_files/타입겹쳐에러.png)

<br>
위 결과를 보면 expected single matching bean but found 2: memberRepository1,memberRepository2 <br>
즉 하나의 타입만이 매칭되어야 하는데 2개 memberRepository1, memberRepository2가 같은 타입을 가지기 때문에 에러가 발생하였다. <br><br>

이 에러를 처리하기 위해 assertThrows 코드로 바꾸자 <br>
또한 타입으로 조회시 같은 타입이 둘 이상 있는경우 빈 이름을 지정하는 코드를 추가해보자 <br>
또한 특정 타입을 모두 조회하는 코드 또한 추가해 보자 <br>
<br>

```java
package hello.spring_basic.beanfind;

import hello.spring_basic.AppConfig;
import hello.spring_basic.member.MemberRepository;
import hello.spring_basic.member.MemoryMemberRepository;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.NoUniqueBeanDefinitionException;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import java.beans.BeanProperty;
import java.util.Map;

import static org.assertj.core.api.Assertions.assertThat;
import static org.junit.jupiter.api.Assertions.assertThrows;

public class ApplicationContextSameBeanFindTest {
    // 이전처럼 AppConfig.class 가 아닌 따로만든 SameBeanConfig.class 이용
    AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(SameBeanConfig.class);

    @Test
    @DisplayName("타입으로 조회시 같은 타입이 둘 이상 있으면 중복 오류가 발생한다")
    void findBeanByTypeDuplicate() {
        // MemberRepository bean = ac.getBean(MemberRepository.class); // 타입만 지정하여 빈 조회
        assertThrows(NoUniqueBeanDefinitionException.class, () -> ac.getBean(MemberRepository.class));
        // () -> 이후에 있는 로직을 실행하면 왼쪽에 있는 예외가 터져야한다. (예외가 던저저야 성공)
    }

    @Test
    @DisplayName("타입으로 조회시 같은 타입이 둘 이상 있으면 빈 이름을 지정하면 된다")
    void findBeanByName() {
        MemberRepository memberRepository = ac.getBean("memberRepository1", MemberRepository.class);
        // 빈 이름과 타입 두가지 모두 지정하여 빈 조회

        assertThat(memberRepository).isInstanceOf(MemberRepository.class);
    }

    @Test
    @DisplayName("특정 타입을 모두 조회하기")
    void findAllBeanByType() {
        Map<String, MemberRepository> beansOfType = ac.getBeansOfType(MemberRepository.class);

        for (String key : beansOfType.keySet()) {
            System.out.println("key = " + key + " value = " + beansOfType.get(key));
        }

        System.out.println("beansOfType = " + beansOfType);

        assertThat(beansOfType.size()).isEqualTo(2); // 검증을 beansOfType의 크기가 2개인지 확인해보면 된다.
    }

    // config 따로 만듬 (테스트 코드이니 여기에서만 사용할 것임)
    @Configuration
    static class SameBeanConfig {

        @Bean
        public MemberRepository memberRepository1() {
            return new MemoryMemberRepository();
        }

        @Bean
        public MemberRepository memberRepository2() {
            return new MemoryMemberRepository();
        }

        // SameBeanConfig 클래스에는 스프링 컨테이너가 스프링 빈 2개만 등록
        // 2개의 빈의 타입이 같음 (MemoryMemberRepository)
    }
}


```

<br>

위의 ApplicationContextSameBeanFindTest 클래스를 실행시켜 보면 <br>

![png](/images/Spring_basic(12)_files/타입겹쳐에러해결.png)

<br>
문제없이 잘 실행됨을 확인할 수 있다.
<br><br>

이제는 상속 관계에 있는 스프링 빈을 조회해볼 것이다.
<br>

## 스프링 빈 조회 - 상속 관계
부모 타입으로 조회하면 자식 타입도 함께 조회된다. <br>
따라서 모든 자바 객체의 가장 root 부모인 Object 타입으로 조회시 모든 스프링 빈을 조회하게 된다. <br>

![jpeg](/images/Spring_basic(12)_files/빈 상속관계 조회.jpeg)

<br>
코드를 통해 알아보자 <br>

```java
package hello.spring_basic.beanfind;

import hello.spring_basic.discount.DiscountPolicy;
import hello.spring_basic.discount.FixDiscountPolicy;
import hello.spring_basic.discount.RateDiscountPolicy;
import org.assertj.core.api.Assertions;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.NoUniqueBeanDefinitionException;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import java.util.Map;

import static org.assertj.core.api.Assertions.assertThat;
import static org.junit.jupiter.api.Assertions.assertThrows;

public class ApplicationContextExtendsFindTest {
    AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(TestConfig.class);

    @Test
    @DisplayName("부모 타입으로 조회시, 자식이 둘 이상 있으면 중복 오류가 발생한다.")
    void findBeanByParentTypeDuplicate() {
        DiscountPolicy bean = ac.getBean(DiscountPolicy.class); // 타입으로 빈 조회
        // 해당 DiscountPolicy 는 부모 클래스 타입임
        // 아래 TestConfig에서 스프링 컨테이너에 담은 스프링 빈은 타입이 RateDiscountPolicy, FixDiscountPolicy로
        // DiscountPolicy의 자식 클래스들 이다.
        // 따라서 두 빈 모두 조회 된다. -> assertThrows로 처리해야

        assertThrows(NoUniqueBeanDefinitionException.class, () -> ac.getBean(DiscountPolicy.class));
    }

    // 부모 클래스 타입으로 조회했더니 여러 자식 클래스 까지 같이 조회되며 하나의 빈이 아닌 여러 빈이 조회되어 에러가 나게 된다.
    // 이러한 경우는 저번에 배웠듯 빈 이름까지 지정하여 조회하면 된다.
    @Test
    @DisplayName("부모 타입으로 조회시 자식이 둘 이상 있으면 빈 이름을 지정하면 된다.")
    void findBeanByParentTypeBeanName() {
        DiscountPolicy rateDiscountPolicy = ac.getBean("rateDiscountPolicy", DiscountPolicy.class);
        // 빈 이름 rateDiscountPolicy, 타입 DiscountPolicy
        // 타입은 DiscountPolicy 이지만 실제 구현 객체인 rateDiscountPolicy가 조회 될 것임

        assertThat(rateDiscountPolicy).isInstanceOf(RateDiscountPolicy.class);
    }

    // 또 다른 방법으로 특정 하위 타입으로 조회하면 됨
    // 물론 이 방법은 좋지 않은 방법임 !! (역할과 구현을 구분하고 역할에 의존하다록 코드를 작성해야 좋으므로)
    @Test
    @DisplayName("특정 하위 타입으로 조회")
    void findBeanBySubType() {
        RateDiscountPolicy bean = ac.getBean(RateDiscountPolicy.class);
        // 특정 하위 타입 으로 빈 조회함.

        assertThat(bean).isInstanceOf(RateDiscountPolicy.class);
    }

    // 이제는 부모 타입으로 부모와 그 자식에 해당하는 모든 빈을 조회해볼 것이다.
    @Test
    @DisplayName("부모 타입으로 모두 조회하기")
    void findAllBeanParentType() {
        Map<String, DiscountPolicy> beansOfType = ac.getBeansOfType(DiscountPolicy.class);

        assertThat(beansOfType.size()).isEqualTo(2); //DiscountPolicy 클래스에 스프링 컨테이너에 등록한 스프링 빈은 2개일테니

        for (String key : beansOfType.keySet()) {
            System.out.println("key = " + key + "value = " + beansOfType.get(key));
        }

        // 참고로 위에 출력하는 것은 공부하기 위한 용도임
        // 실제 테스트케이스 작성할때는 출력하는 코드를 작성하는 방법은 좋지 않음
        // 테스트를 시스템이 통과 실패를 결정하게 작성하는게 중요함
    }

    // 모든 객체의 가장 root 부모인 Object를 이용해 모든 타입을 조회해보자
    @Test
    @DisplayName("부모 타입으로 모두 조회 - Object")
    void findAllBeanObjectType() {
        Map<String, Object> beansOfType = ac.getBeansOfType(Object.class);
        // Object 객체 타입으로 빈 조회

        for (String key : beansOfType.keySet()) {
            System.out.println("key = " + key + "value = " + beansOfType.get(key));
        }
    }

    @Configuration
    static class TestConfig { // 이번 테스트에서 사용할 config 관련 클래스 만듬
        @Bean
        public DiscountPolicy rateDiscountPolicy() {
            return new RateDiscountPolicy();
        }

        // 궁금한점 어차피 반환을 구체 클래스인 RateDiscountPolicy()를 반환할 것인데
        // 왜 rateDiscountPolicy 함수의 반환형을 RateDiscountPolicy가 아닌
        // 인터페이스인 DiscountPolicy로 지정하지??
        // -> 물론 반환형을 실제 반환할 구체 클래스형으로 지정해도 됨..
        // 하지만 개발 및 설계를 할때 역할과 구현을 쪼갠것을 확실히 알기위해
        // 실제 함수의 반환값이 구현에 해당해도 메서드의 반환형 선언을 역할로 두는 것이 이해하기 쉽기 때문
        // ex) 의존관계 주입할때 메서드의 반환형 선언을 보고 역할을 알 수 있다.

        @Bean
        public DiscountPolicy fixDiscountPolicy() {
            return new FixDiscountPolicy();
        }
    }
}

```

위 코드에서 알아볼 것들이 있다. <br>
Config관련 클래스를 보면 (이 코드에선 TestConfig.class) <br>
메서드 내용을 보면 return에서 구체 클래스의 타입으로 반환하는데 메서드 반환형을 보면 인터페이스 클래스 타입이다. <br>
위 코드에선  왜 rateDiscountPolicy 메서드의 반환형을 RateDiscountPolicy가 아닌 인터페이스인 DiscountPolicy로 지정하지?? <br>
<br>
-> 물론 메서드의 반환형을 실제 반환할 구체 클래스 타입으로 지정해도 된다. <br>
다만 개발 및 설계 중에 역할과 구현을 나눈것을 확실히 알아 보기 위해 메서드 반환형을 인터페이스 타입으로 둔다. <br>
실제 메서드의 반환값이 구현에 해당해도 메서드의 반환형 선언을 역할로 두는 것이 이해하기 쉽기 때문이다. <br>
ex ) 의존관계 주입할때 메서드의 반환형 선언을 보고 역할을 알 수 있다.
<br><br>

부모 클래스 타입으로 조회했더니 여러 자식 클래스 까지 같이 조회되며 하나의 빈이 아닌 여러 빈이 조회되어 에러가 나게 된다.<br>
 이러한 경우는 여러가지 방법이 존재한다. <br>

1. 저번에 배웠듯 빈 이름까지 지정하여 조회하면 된다. <br>

2. 특정 하위 타입으로 조회하면 됨<br>
 물론 이 방법은 좋지 않은 방법임 !! (역할과 구현을 구분하고 역할에 의존하다록 코드를 작성해야 좋으므로)
 <br>

위 ApplicationContextExtendsFindTest 클래스를 실행시켜 보면 <br>

![png](/images/Spring_basic(12)_files/테스트 결과 조회.png)

<br>

전체 아무 문제없이 잘 실행이 됨을 알 수 있다. <br>
<br>

이 중에서 부모타입인 DiscountPolicy로 모든 빈을 조회 해보면
<br>

![png](/images/Spring_basic(12)_files/부모타입 조회.png)

<br>
부모 타입인 DiscountPolicy의 자식 타입들인 등록한 2가지 빈이 조회가 됨을 확인할 수 있다. <br>

<br>

또한 모든 객체의 root 부모인 Object로 모든 빈을 조회 한 경우에는 <br>

![png](/images/Spring_basic(12)_files/Object 조회.png)

<br>
등록 한 객체 뿐만 아니라 Spring 컨테이너 내부 모든 빈이 조회 된 것을 알 수있다. <br><br>

지금까지 Spring 빈을 조회해 보았다. <br>

### Reference :
김영한 강사님 스프링 핵심 원리 - 기본편  강의 중 