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
XML 기반 설정 경우 <bean>당 각각 하나씩 메타 정보 생성 <br>
<br>
스프링 컨테이너는 이 메타정보를 기반으로 스프링 빈을 생성한다.

![jpeg](/images/Spring_basic(15)_files/BeanDefinition.jpeg)

<br>
코드 레벨로 조금 더 깊이 있게 들여다 보면 아래 그림의 구조와 같다. <br>

![jpeg](/images/Spring_basic(15)_files/ApplicationContext.jpeg)