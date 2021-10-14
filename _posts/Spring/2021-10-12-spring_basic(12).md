---
title: "Spring 빈 조회"
categories:
  - Spring
tags:
  - 스프링 컨테이너
  - 스프링 빈
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