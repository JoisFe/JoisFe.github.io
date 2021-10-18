---
title: "BeanFactory와 ApplicatioinContext"
categories:
  - Spring
tags:
  - BeanFactory
  - ApplicationContext
use_math: true
---
# BeanFactory와 ApplicationContext

![jpeg](/images/Spring_basic(13)_files/BeanFactory.jpeg)
<br>

## BeanFactory
스프링 컨테이너의 최상위 인터페이스 <br>
스프링 빈을 관리하고 조회하는 역할을 한다. <br>
getBean()을 제공한다. <br>
이전에 구현한 대부분 기능은 BeanFactory가 제공하는 기능들 이다.
<br>

## ApplicationContext
BeanFactory 기능을 모두 상속받아서 제공 <br>
<br>

근데 빈을 관리하고 검색하는 기능을 BeanFactory가 제공해주는데 굳이 왜 ApplicationContext가 존재하지 ?? <br>
-> 애플리케이션을 개발할 때는 빈은 관리하고 조회하는 기능 뿐만 아니라 여러 부가기능이 필요하기 때문이다. <br>
<br>
그럼 ApplicationContext가 제공하는 부가기능에 대해 간단히 알아보자

## ApplicationContext가 제공하는 부가기능

![jpeg](/images/Spring_basic(13)_files/ApplicationContext.jpeg)

<br>

### MessageSource : 메시지 소스를 활용한 국제화 기능
예를 들어 한국에서 들어오면 한국어로, 영어권에서 들어오면 영어로 출력을 한다. <br>
### EnvironmentCapable : 환경변수
로컬, 개발, 운영 등을 구분해서 처리한다. <br>
### 애플리케이션 이벤트
이벤트를 발생하고 구독하는 모델을 편리하게 지원한다. <br>
### 편리한 리소스 조회
파일, 클래스패스, 외부 등에서 리소스를 편리하게 조회한다. <br>
<br>

정리해보면 <br>
ApplicationContext는 BeanFactory의 기능을 상속받으며, 빈 관리기능 + 편리한 부가 기능을 제공한다. <br>
따라서 BeanFactory를 직접 사용할 일은 거의 없고 부가기능까지 포함디ㅗㄴ ApplicationContext를 사용한다. <br>
BeanFactory나 ApplicationContext를 <b>스프링 컨테이너</b>라고 부른다.
<br>

### Reference :
김영한 강사님 스프링 핵심 원리 - 기본편  강의 중 