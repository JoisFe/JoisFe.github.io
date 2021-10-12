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





