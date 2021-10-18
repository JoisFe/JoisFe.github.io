---
title: "BeanFactory와 ApplicatioinContext"
categories:
  - Spring
tags:
  - BeanFactory
  - ApplicationContext
use_math: true
---

# 다양한 설정 형식 지원 -자바 코드, XML

스프링 컨테이너는 다양한 형식의 설정 정보를 받아들일 수 있게 유연하게 설계되어 있다. <br>
대표적으로 자바 코드, XML, Groovy 등등 여러가지 형식으로 설정 정보 저장할 수 있다. <br>

<br>
![jpeg](/images/Spring_basic(14)_files/다양한 형식.jpeg)

<br>

## 애노테이션 기반 자바 코드 설정 사용
이전까지 구현한 방법이 애노테이션 기반 자바 코드 설정을 사용한 것이다. <br>

```java
new AnnotationConfigApplicationContext(AppConfig.class)
```
를 통해 AnnotationConfigApplicationContext 클래스를 사용하면서 자바 코드로 된 설정 정보를 넘기면 된다. <br>

## XML 설정 사용

XML 기반 설정은 최근에 스프링 부트를 많이 사용하게 됨으로써 잘 사용하지 않게 되었다. <br>
하지만 아직까지 XML로 이루어진 많은 레거시 프로젝트 들이 존재하기도 하고 <br>
XML을 사용하면 컴파일 없이 빈 설정 정보를 변경할 수 있기 때문에 어노테이션 기반 자바 코드 설정 보다 좋은 장점 또한 존재한다.<br>
<br>

```java
GenericXmlApplicationContext("appConfig.xml");
```
를 사용하여 xml설정 파일을 넘기면 된다.

### XmlAppConfig 사용 자바 코드

```java
package hello.spring_basic.xml;

import hello.spring_basic.member.MemberService;
import org.assertj.core.api.Assertions;
import org.junit.jupiter.api.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.GenericXmlApplicationContext;

import static org.assertj.core.api.Assertions.assertThat;

public class XmlAppContext {

    @Test
    void xmlAppContext() {
        ApplicationContext ac = new GenericXmlApplicationContext("appConfig.xml");
        MemberService memberService = ac.getBean("memberService", MemberService.class);
        assertThat(memberService).isInstanceOf(MemberService.class);
    }
}

```

### xml 기반의 스프링 빈 설정 정보
-> src/main/resources/appConfig.xml 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id = "memberService" class = "hello.spring_basic.member.MemberServiceImpl">
        <constructor-arg name = "memberRepository" ref = "memberRepository" />
    </bean>

    <bean id = "memberRepository"
          class = "hello.spring_basic.member.MemoryMemberRepository" />

    <bean id = "orderService" class = "hello.spring_basic.order.OrderServiceImpl">
        <constructor-arg name = "memberRepository" ref = "memberRepository" />
        <constructor-arg name = "discountPolicy" ref = "discountPolicy" />
    </bean>

    <bean id="discountPolicy" class="hello.spring_basic.discount.RateDiscountPolicy" />
</beans>
```
<br>

위 XmlAppContext 클래스를 실행시켜보면 <br>
![png](/images/Spring_basic(14)_files/xml_결과.png)

<br>
아무 문제없이 잘 실행되는 것으로 보아 <br>
xml 기반의 appConfig.xml 스프링 설정 정보와 자바 코드로 된 AppConfig.java 설정 정보를 비교해보면 거의 비슷하다는 것을 알 수 있다. <br>
다만 xml 기반으로 설정하는 것은 최근에 잘 사용하지 않는다. <br><br>

### Reference :
김영한 강사님 스프링 핵심 원리 - 기본편  강의 중 