---
title: "프로젝트 환경설정"
categories:
  - Project - Shop_spring
tags:
  - 스프링 부트
  - thymeleaf
  - jpa
  - h2
  - lombok
use_math: true
---

이번에는 스프링 부트와 JPA를 활용하여 Shop에 관련된 웹 애플리케이션 프로젝트를 만들어 보려고 한다. <br>
회원, 상품 등록, 주문 등의 기능을 구현할 것이다. <br>

# 프로젝트 환경설정
- 스프링 부트 버전 : 2.5.5 
- Dependencies : web, thymeleaf, jpa, h2, lombok, validation 
- group : jpabook
- artifact : jpashop

<br>

### build.grade (Gradle) 전체설정을 보면

```java
plugins {
	id 'org.springframework.boot' version '2.5.5'
	id 'io.spring.dependency-management' version '1.0.11.RELEASE'
	id 'java'
}

group = 'jpabook'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '11'

configurations {
	compileOnly {
		extendsFrom annotationProcessor
	}
}

repositories {
	mavenCentral()
}

// 프로젝트 생성할때 선택한 의존관계
dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
	implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
	implementation 'org.springframework.boot:spring-boot-starter-validation'
	implementation 'org.springframework.boot:spring-boot-starter-web'
	compileOnly 'org.projectlombok:lombok'
	runtimeOnly 'com.h2database:h2'
	annotationProcessor 'org.projectlombok:lombok'
	testImplementation 'org.springframework.boot:spring-boot-starter-test'

	//JUnit4 추가
	testImplementation("org.junit.vintage:junit-vintage-engine") {
		exclude group: "org.hamcrest", module: "hamcrest-core"
	}
}

test {
	useJUnitPlatform()
}

```

<br>
프로젝트가 잘 생성 되었는지 확인하기 위해 JpashopApplication 클래스를 실행 시켜 보았다. <br>

![png](/images/Shop_spring(1)_files/프로젝트생성확인.png)

아무 문제없이 잘 실행 되니 프로젝트가 잘 생성 되었음을 확인할 수 있다.<br>
또한 실행 결과 부분을 더 자세히 보면 <br>

![png](/images/Shop_spring(1)_files/톰캣스타트.png)

<br>
Tomcat started on prot(s) ;8080 (http) with context path ''가 보인다. <br>
즉 실행 후 웹브라우저 주소창에 localhost:8080 이라고 검색 또는 127.0.0.1:8080 검색하면 <br>
![png](/images/Shop_spring(1)_files/웹주소.png) <br>

위 결과처럼 Whitelabel Error Page가 뜬다면 프로젝트가 잘 생성 된 것이다. <br>
<br>

### 롬북 적용
1. Prefrences plugin lombok 검색 한다. (재시작)
2. Prefrences Annotation Processors 검색 Enable annotation processing 체크 (재시작).


## 라이브러리를 살펴보자.

### gradle 의존관계 보기
build.gradle에서 dependencies를 보면 여러 라이브러리가 존재한다.

```java
dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
	implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
	implementation 'org.springframework.boot:spring-boot-starter-validation'
	implementation 'org.springframework.boot:spring-boot-starter-web'
	compileOnly 'org.projectlombok:lombok'
	runtimeOnly 'com.h2database:h2'
	annotationProcessor 'org.projectlombok:lombok'
	testImplementation 'org.springframework.boot:spring-boot-starter-test'

	//JUnit4 추가
	testImplementation("org.junit.vintage:junit-vintage-engine") {
		exclude group: "org.hamcrest", module: "hamcrest-core"
	}
}

```

좀더 자세히 라이브러리를 확인하려면<br>
터미널 창에서 jpashop 폴더에서

```zsh
$./gradlew dependencies —configuration compileClasspath
```
을 입력하면
![png](/images/Shop_spring(1)_files/gradle의존관계.png) <br>

위 그림처럼 의존관계를 자세히 확인 가능하다. (자세하게 여러 라이브러리 확인 가능)

<br>

### 스프링 부트 라이브러리를 알아보면
- spring-boot-starter-web 
	- spring-boot-starter-tomcat : 톰캣 (웹서버) 	
	- spring-webmvc : 스프링 웹 MVC
- spring-boot-starter-thymeleaf : 타임리프 템플릿 엔진(View)
- spring-boot-starter-data-jpa
	- spring-boot-starter-aop
	- spring-boot-starter-jdbc
		- HikariCP 커넥션 풀 (부트 2.0 기본)
	- hibernate + JPA : 하이버네이트 + JPA
	- spring-data-jpa : 스프링 데이터 JPA
- spring-boot-starter(공통): 스프링 부트 + 스프링 코어 + 로깅
	- spring-boot
		- spring-core
- spring-boot-starter-logging
	- logback, slf4j

### 테스트 라이브러리에 대해 알아보면
- spring-boot-starter-test
	- junit: 테스트 프레임워크
	- mockito: 목 라이브러리
	- assertj: 테스트 코드를 좀 더 편하게 작성하게 도와주는 라이브러리
	- spring-test: 스프링 통합 테스트 지원

- 핵심 라이브러리
	- 스프링 MVC
	- 스프링 ORM
	- JPA, 하이버네이트
	- 스프링 데이터 JPA
- 기타 라이브러리
- H2 데이터베이스 클라이언트
- 커넥션 풀: 부트 기본은 HikariCP 
- WEB(thymeleaf)
- 로깅 SLF4J & LogBack
- 테스트

### View 환경 설정

View 환경 설정을 위해 이 프로젝트에서는 thymeleaf 템플릿 엔진을 사용할 것이다. <br>
<br>

- 스프링 부트 thymeleaf viewName 매핑 :
	- resources : templates/ +{ViewName}+'.html' 

View 환경 설정을 한번 해보려고 하는데 따로 세팅 해야할게 없나?? <br>
-> 없다. 스트링 부트의 엄청난 장점
<br>
build.gradle에서 

```java
implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
```
이렇게 dependencies에 pring-boot-starter-thymeleaf 하나만 있으면 된다.<br>

```java
package jpabook.jpashop;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class HelloController {

    @GetMapping("hello")
    public String hello(Model model) {
        model.addAttribute("data", "hello!!!");

        return "hello"; // hello.html
		// 어떻게 "hello"만 반환하였는데 resources/templates/hello.html 위치의 파일을 찾지??
		// 스프링 부트 thymeleaf viewName 매핑 : resources : templates/ +{ViewName}+'.html' 이런식으로 한다고 설명 하였음
    }
}

```
<br>

thymeleaf 템플릿엔진 동작 확인 (hello.html)

``` html
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title>Hello</title>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
</head>
<body>
<p th:text="'안녕하세요. ' + ${data}" >안녕하세요. 손님</p>
</body>
</html>

```

위 html파일 위치는 <br>
resources/templates/hello.html 이다.<br>
<br>
이렇게 하고 스프링 부트 메인인 JpashopApplicatioin 클래스를 실행시키면 <br>
정상 작동을 하고 웹브라우저 주소창에 localhost:8080/hello 를 입력하면<br>
![png](/images/Shop_spring(1)_files/타임리프동작.png) 
<br>
잘 실행 됬음을 확인할 수 있다. <br><br>

다시한번 강조하지만 hello 메서드에서 "hello"만 반환하였는데 resources/templates/hello.html 위치의 파일을 찾은 이유는<br>
 스프링 부트 thymeleaf viewName 매핑 : resources : templates/ +{ViewName}+'.html' 이런식으로 매핑해주기 때문이다. <br>
 

이번에는 정적인 페이지를 보여주고 싶은 경우를 보자 <br>
index.html 파일을 하나 만들자 <br>
파일 위치는 : static/index.html <br>

```html
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">
<head>
        <title>Hello</title>
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
    </head>
    <body>
    Hello
    <a href="/hello">hello</a>
    </body>
</html>
```

그리고 다시 JpashopApplicatioin 클래스를 실행시키고 웹브라우저 주소창에 localhost:8080을 입력하면 <br>
![png](/images/Shop_spring(1)_files/정적페이지(1).png) 
결과가 나오고 <br>
링크 hello를 클릭하면 (href 태크로 걸어둠)<br>
![png](/images/Shop_spring(1)_files/정적페이지(2).png) 
결과가 나오고 url 주소가 localhost:8080/hello로 바뀐다. <br><br>

참고사항으로 지금까지 View 파일 변경하거나 추가시 서버를 다시 재시작 해줘야 변경된 사항이 반영이 되었다. <br>
너무 귀찮다. <br>
spring-boot-devtools 라이브러리를 추가하면 html파일을 컴파일만 해주는 서버 재시작 없이 View 파일 변경이 가능하다. <br>
build.gradle에 dependencies에
``` java
implementation 'org-springframework.boot:spring-boot-devtools'
```
이 코드를 추가해준다. <br>
<br>
또다른 내용
인텔리J 컴파일 방법: 메뉴 build Recompile 