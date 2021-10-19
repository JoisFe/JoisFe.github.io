---
title: "스프링 빈 설정 메타 정보 - BeanDefinition"
categories:
  - Spring
tags:
  - BeanDefinition
use_math: true
---

이전에 스프링이 자바코드, XML 등 다양한 설정 형식을 지원하는 것을 알았다. <br>
그렇다면 스프링이 굳이 왜 이렇게 다양한 설정 형식을 지원할까 ?? <br>
이 부분에 대해 알기 위해서는 <b>BeanDefinition</b> 이라는 추상화를 알아야 한다. <br>

# 스프링 빈 설정 메타 정보 - BeanDefinition

BeanDefinition 이라는 추상화가 무엇인가 ??<br>
쉽게 말하면 <b>역할과 구현을 개념적으로 나눈 것</b> 이다. <br>
XML 기반 설정인 경우 XML을 읽어서 BeanDefinition을 만들면 된다.<br>
자바 코드로 된 설정인 경우 자바 코드를 읽어서 BeanDefinition을 만들면 된다. <br>
<br>

-> 스프링 컨테이너는 설정 기반이 XML인지 자바 코드인지 몰라도 된다. <br>
오직 BeanDefinition만을 알면 된다. <br>

### BeanDefinition 
: 빈 설정 정보 메타 정보라고 한다. <br>
애노테이션 기반 자바 코드 설정 사용한 경우 @Bean 당 각각 하나씩 메타 정보 생성<br>
XML 기반 설정 경우 \<bean\>당 각각 하나씩 메타 정보 생성 <br>
<br>
스프링 컨테이너는 이 메타정보를 기반으로 스프링 빈을 생성한다.

![jpeg](/images/Spring_basic(15)_files/BeanDefinition.jpeg)

<br>
코드 레벨로 조금 더 깊이 있게 들여다 보면 아래 그림의 구조와 같다. <br>

![jpeg](/images/Spring_basic(15)_files/ApplicationContext.jpeg)
<br>

위 그림을 보면 <br>
AnnotationConfigApplicationContext 는 AnnotatedBeanDefinitionReader를 사용해서 AppConfig.class 를 읽고 BeanDefinition 을 생성하는 것을 알 수 있다. <br>
GenericXmlApplicationContext 는 XmlBeanDefinitionReader를 사용해서 appConfig.xml 설정 정보를 읽고 BeanDefinition 을 생성하는 것을 알 수 있다. <br>
새로운 형식의 설정 정보가 추가되면, XxxBeanDefinitionReader를 만들어서 BeanDefinition 을 생성하면 된다. <br>
<br>

그렇다면 BeanDefinition이 무엇인지 자세히 알아보자.

## BeanDefinition 살펴보기

# BeanDefinition 정보
- BeanClassName : 생성할 빈의 클래스 명 (자바 설정 처럼 팩토리 역할의 빈을 사용하면 없다)
- factoryBeanName : 팩토리 역할의 빈을 사용할 경우 이름. ex) appConfig
- factoryMethodName : 빈을 생성할 팩토리 메서드 지정. ex) memberService
- Scope: 싱글톤(기본값)
- lazyInit: 스프링 컨테이너를 생성할 때 빈을 생성하는 것이 아니라 실제 빈을 사용할 때 까지 최대한 생성을 지연처리 하는지 여부를 나타냄.
- InitMethodName : 빈을 생성하고 의존관계를 적용한 뒤에 호출되는 초기화 메서드 명
- DestroyMethodName : 빈의 생명주기가 끝나서 제거하기 직전에 호출되는 메서드 명
- Constructor arguments, Properties : 의존관계 주입에서 사용한다. (자바 설정 처럼 팩토리 역할의 빈을 사용하면 없다)
<br><br>

코드를 통해 구현해 보자 <br>

```java
package hello.spring_basic.beandefinition;

import hello.spring_basic.AppConfig;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.config.BeanDefinition;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.support.GenericXmlApplicationContext;

public class BeanDefinitionTest {

    AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext (AppConfig.class); // 어노테이션 기반 자바 코드로 된 AppConfig.java 설정 정보

    // GenericXmlApplicationContext ac = new GenericXmlApplicationContext("appConfig.xml"); // xml 기반의 appConfig.xml 스프링 설정 정보

    @Test
    @DisplayName("빈 설정 메타정보 확인")
    void findApplicationBean() {
        String[] beanDefinitionNames = ac.getBeanDefinitionNames();

        for (String beanDefinitionName : beanDefinitionNames) {
            BeanDefinition beanDefinition = ac.getBeanDefinition(beanDefinitionName);

            if (beanDefinition.getRole() == BeanDefinition.ROLE_APPLICATION) {
                System.out.println("beanDefinitionName = " + beanDefinitionName + " beanDefinition = " + beanDefinition);
            }
        }
    }
}


```

먼저 어노테이션 기반 자바 코드로 된 AppConfig.java 설정 정보 결과를 보기 위해 <br>
BeanDefinitionTest 클래스를 실행시켜보면 <br>

```java
AnnotationConfigApplicationContext (AppConfig.class); // 어노테이션 기반 자바 코드로 된 AppConfig.java 설정 정보
```

![png](/images/Spring_basic(15)_files/자바어노테이션.png)

위 결과를 보면 아무 문제 없이 잘 실행이 되고<br>
또한 위에서 설명한 BeanDefinition 정보들이 출력이 된 것을 확인할 수 있다. <br>

그리고 이전 자바 코드로 된 AppConfig.java 설정 정보를 가져오는 것을 주석처리하고 <br>
xml 기반 설정 정보를 이용하면

```java
GenericXmlApplicationContext ac = new GenericXmlApplicationContext("appConfig.xml"); // xml 기반의 appConfig.xml 스프링 설정 정보

```
<br>
로 바꾸고 실행을 해보자 <br>

![png](/images/Spring_basic(15)_files/xml결과.png)

<br>

이 결과를 보면 어노테이션 기반 자바 코드로된 설정 정보를 이용한 결과와 조금 다르다. <br>
xml 설정 정보를 이용한 경우는 Generic bean이라고 해서 빈에 대한 정보가 명확하게 등록되어 있음을 알 수 있다. <br><br>

정리해보자 <br>
- BeanDefinition을 직접 생성해서 스프링 컨테이너에 등록할 수도 있다. <br>
하지만 실무에서는 BeanDefinition을 직접 정의하거나 사용할 일은 거의 없다고 한다. <br>
- BenaDefinition에 대해 너무 깊게 이해하기 보단 스프링이 다양한 형태의 설정 정보를 BeanDefinition으로 추상화해서 사용하는 것 정도만 이해하자. <br>
- 스프링 코드나 스프링 관련 오픈 소스 코드 볼때 BeanDefinition이 나왔을때 이번에 배운것을 떠올릴 수 있으면 된다. <br>
<br>

### Reference :
김영한 강사님 스프링 핵심 원리 - 기본편  강의 중 
