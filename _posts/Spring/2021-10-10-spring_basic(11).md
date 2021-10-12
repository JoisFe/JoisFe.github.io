---
title: "Spring 컨테이너 생성"
categories:
  - Spring
tags:
  - 스프링 컨테이너
  - 스프링 빈
use_math: true
---

# 스프링 컨테이너 생성
스프링 컨테이너가 생성되는 과정을 알아보면
<br>

```java
package hello.spring_basic;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

// 스프링 컨테이너 생성
@SpringBootApplication
public class SpringBasicApplication {

	public static void main(String[] args) {
		SpringApplication.run(SpringBasicApplication.class, args);
	}

}

```

<br>
위 코드를 보면 알 수 있듯 <br>
ApplicationContext를 스프링 컨테이너라고 하고 <br>
이 ApplicationContext는 인터페이스 이다. <br>
<br>
스프링 컨테이너는 XML 기반, 어노테이션 기반의 자바 설정 클래스 등으로 만들 수 있다. <br>
이전에 만든 AppConfig를 사용했던 방식이 바로 어노테이션 기반의 자바 설정 클래스로 스프링 컨테이너를 만든 것이다. <br>
<br>

자바 설정 클래스를 기반으로 스프링 컨테이너(ApplicationContext)를 만들어 보면 <br>
new AnnotationConfigApplicationContext(AppConfig.class);<br>
로 만들고 <br>
이 클래스는 ApplicationContext 인터페이스의 구현체 이다. <br><br>

나중에 더 자세히 알아보겠지만 정확히 스프링 컨테이너를 부를 때는 BeanFactory, ApplicationContext로 구분한다. <br>
BeanFactory를 사용하는 경우는 매우 드물기 떄문에 일반적으로 ApplicationContext를 스프링 컨테이너라고 부른다. <br><br>

## 스프링 컨테이너의 생성 과정
### 1. 스프링 컨테이너 생성
![jpeg](/images/Spring_basic(11)_files/스프링 컨테이너 생성.jpeg)

<br>

new AnnotationConfigApplicationContext(AppConfig.class);<br>
를 통해 스프링 컨테이너를 생성한다. <br><br>

스프링 컨테이너를 생성할 때는 구성 정보를 지정해준다. <br>
여기에서는 구성 정보가 AppConfig.class 이다. <br><br>

### 2. 스프링 빈 등록
![jpeg](/images/Spring_basic(11)_files/스프링 빈 등록.jpeg)

<br>

스프링 컨테이너는 파라미터로 넘어온 설정 클래스 정보를 사용하여 스프링 빈을 등록함. <br>
<br>

<b>빈 이름</b>
빈 이름은 메서드 이름을 사용한다. <br><br>
빈 이름을 직접 부여할 수도 있음 <br> 
ex) @Bean(name="memberService2")
<br><br>

여기서 주의해야할 점!!! <br>
빈 이름은 항상 다른 이름을 부여해야 한다. <br>
만약 같은 이름을 부여하면 다른 빈이 무시되거나 기존 빈을 덮어버리는 일이 발생하여 설정에 따라 오류가 발생할 수 있다. <br><br>

### 3. 스프링 빈 의존관계 설정 - 준비
![jpeg](/images/Spring_basic(11)_files/빈 의존관계 설정 준비.jpeg)

<br>

### 4. 스프링 빈 의존관계 설정 - 완료
![jpeg](/images/Spring_basic(11)_files/빈 의존관계 설정 완료.jpeg)

<br>
위 그림을 보면 알 수 있듯이 <br>
스프링 컨테이너는 설정 정보를 참고해서 의존관계 주입(DI) 한다. <br> <br>

코드를 보면 단순히 자바 코드를 호출하는 것 같음... <br>
하지만 순수한 자바 코드를 호출하는 것과는 차이가 존재한다. <br>
그 부분은 이후에 싱클톤 컨테이너에 대해 배울때 설명할 것이다. <br><br>


스프링은 빈을 생성하고 의존관계를 주입하는 단계가 나누어 져 있다. <br>
그런데 자바 코드로 스프링 빈을 등록하면 생성자를 호출하여 의존관계 주입(DI) 또한 한번에 처리된다. <br>
위에서는 이해를 위해 개념적으로 나누어 설명하였지만 자세한 부분은 이후에 의존관계 자동 주입을 배우면서 다시 알아볼 것이다. <br><br>

정리해보자면 스프링 컨테이너를 생성하고 설정(구성) 정보를 참고해서 스프링 빈도 등록하고 의존관계(DI)도 설정하였다. <br>
<br>

다음에는 스프링 컨테이너에서 데이터를 조회해볼 것이다.
<br>

### Reference :
김영한 강사님 스프링 핵심 원리 - 기본편  강의 중 