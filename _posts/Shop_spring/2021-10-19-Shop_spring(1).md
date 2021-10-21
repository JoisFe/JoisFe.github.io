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
![png](/images/Shop_spring(1)_files/정적페이지(1).png) <br>
결과가 나오고 <br>
링크 hello를 클릭하면 (href 태크로 걸어둠)<br>
![png](/images/Shop_spring(1)_files/정적페이지(2).png) <br>
결과가 나오고 url 주소가 localhost:8080/hello로 바뀐다. <br><br>

참고사항으로 지금까지 View 파일 변경하거나 추가시 서버를 다시 재시작 해줘야 변경된 사항이 반영이 되었다. <br>
너무 귀찮다. <br>
spring-boot-devtools 라이브러리를 추가하면 html파일을 컴파일만 해주는 서버 재시작 없이 View 파일 변경이 가능하다. <br>
build.gradle에 dependencies에
``` java
implementation 'org-springframework.boot:spring-boot-devtools'
```
이 코드를 추가해준다. <br>
그러고 다시 Recompile 하면 됨 <br>
인텔리J 컴파일 방법: 메뉴 build Recompile <br>


## H2 데이터베이스 설치
H2 DB는 개발이나 테스트 용도에 쓰이는 가볍고 편리한 DB이다. <br>
웹 화면을 제공해준다 <br>
<br>

- H2 DB 버전 : 1.4.200
<br>
사용하는 OS에 맞는 버전을 설치 후 h2폴더를 원하는 위치에 두고 h2 폴더 내의 bin폴더로 들어간 후 bin.sh를 실행시킨다. <br>
![png](/images/Shop_spring(1)_files/h2다운.png) <br>

permission denied가 뜨면 <br>
chmod 755 h2.sh를 통해 권한을 풀어준다. <br>
<br>
그러면 아래와 같은 웹 화면이 뜬다. <br>
![png](/images/Shop_spring(1)_files/h2웹페이지.png) <br>

만약 화면이 안뜨면 아래와 같이 localhost로 url 주소를 설정한다. <br>
![png](/images/Shop_spring(1)_files/h2로컬웹페이지.png) <br>

주의할 점은 url주소에 해당하는 key값은 유지 시켜줘야한다. <br>
<br>
그리고 데이터베이스 파일 생성 방법으로는 <br>

- 저장한 설정 : Generic H2(Server)
- JDBC URL : jdbc:h2:~/jpashop (최소 한번) <br>
입력하여 DB 파일을 생성할 경로를 지정한 후 연결을 누른다. <br>
그러면 파일 모드로 실행이 된다. <br>

그려면 아래와 같이 나타난다.
![png](/images/Shop_spring(1)_files/h2DB파일생성.png) <br>
~/jpashop.mv.db 파일 생성된 것을 확인할 수 있다. <br>

이후 왼쪽 하단에 연결 끊기를 누르면 DB 연결이 끊긴다. <br>
하지만 ~/jpashop.mv.dv 파일이 생성 되었기 때문에 이후부터는
- JDBC URL : jdbc:h2:tcp://localhost/~/jpashop
로 파일 모드가 아닌 네트워크 모드로 접근할 수 있다. <br>
![png](/images/Shop_spring(1)_files/h2DB네트워크모드.png) <br>

이렇게 하면 h2 DB 설치가 완료 되었다. <br>
<br>
이전에 실행해둔 h2.sh를 끄면 DB가 아무것도 동작하지 않게되므로 DB를 사용하려면 h2.sh를 실행시켜둬야한다.

## JPA와 DB 설정, 동작 확인
설정 파일에 해당하는 기존의
jpashop/src/main/resources/application.properties 경로에 있던 application.properties를 삭제하고 yml 파일을 사용하기 위해 해당 위치에 application.yml을 만든다. <br>
<br>
설정 파일은 application.properties를 사용해도 되고 application.yml을 사용해도 된다. <br>

``` yml
spring:
  datasource:
    url: jdbc:h2:tcp://localhost/~/jpashop; # MVCC=TRUE # h2 DB 접근하는 곳 경로
    # MVCC=TRUE 넣어주는것 권장, 이것 넣어주면 여러 db에 한번에 접근시 더빠름
    # MVCC 옵션은 1.4.198버전부터 제거가 되었으니 1.4.200 버전에서 사용시 오류남
    username: sa
    password:
    driver-class-name: org.h2.Driver

  jpa:
    hibernate:
      ddl-auto: create # 애플리케이션 실행시점에 내가 가지고 있는 entity 다 지우고 다시 생성
    properties:
      hibernate:
#        show_sql: true # System.out에 하이버네이트 실행 SQL 남김
        format_sql: true

logging:
  level:
    org.hibernate.SQL: debug # logger를 통해 하이버네이트 실행 SQL을 남김
    # 하이버네이트가 남기는 모든 로그가 debug모드로

```
- 주의해야 할 점
 application.yml 같은 yml 파일은 띄어쓰기(스페이스) 2칸으로 계층을 만든다 <br>
 따라서 띄어쓰기 2칸을 필수로 적어줘야 한다. <br>
ex)datasource 는 spring: 하위에 있고 앞에 띄어쓰기 2칸이 있으므로 spring.datasource가 되는 것이다. 


이제 어느정도 기본적인 설정파일의 설정이 끝이났다. <br>
이제부터는 회원 엔티티를 하나 만들어 잘 동작하는지 확인해 보자. <br>

## 실제 동작하는지 확인

### 회원 엔티티

```java
package jpabook.jpashop;

import org.springframework.stereotype.Repository;

import javax.persistence.EntityManager;
import javax.persistence.PersistenceContext;

@Repository
public class MemberRepository {

    @PersistenceContext // 해당 어노테이션이 있으면 스프링 부트 EntityManager를 주입 해줌
    private EntityManager em;

    public long save(Member member) { // 저장
        em.persist(member);

        return member.getId();
    }

    public Member find(Long id) { // 조회
        return em.find(Member.class, id);
    }
}
```

<br>

### 회원 레포지토리
``` java
package jpabook.jpashop;

import org.springframework.stereotype.Repository;

import javax.persistence.EntityManager;
import javax.persistence.PersistenceContext;

@Repository
public class MemberRepository {

    @PersistenceContext // 해당 어노테이션이 있으면 스프링 부트 EntityManager를 주입 해줌
    private EntityManager em;

    public long save(Member member) { // 저장
        em.persist(member);

        return member.getId();
    }

    public Member find(Long id) { // 조회
        return em.find(Member.class, id);
    }
}

```

<br>

### 테스트
```java
package jpabook.jpashop;

import org.assertj.core.api.Assertions;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;

import static org.junit.Assert.*;

@RunWith(SpringRunner.class) // 스프링과 관련된 것을 테스트를 할 것임을 Junit에게 알림
@SpringBootTest
public class MemberRepositoryTest {

    @Autowired MemberRepository memberRepository;

    @Test
    public void testMember() throws Exception {
        //given
        Member member = new Member();
        member.setUsername("memberA");

        //when
        Long savedId = memberRepository.save(member);
        Member findMember = memberRepository.find(savedId);

        //then
        Assertions.assertThat(findMember.getId()).isEqualTo(member.getId());
        Assertions.assertThat(findMember.getUsername()).isEqualTo(member.getUsername());
    }
}
```
아 앞으로 h2 DB 실행 시켜나야함 <br>

이렇게 하고 테스트 코드의 testMember() 메서드를 실행시켜 보면 <br>
아래와 같은 에러가 난다. <br>
![png](/images/Shop_spring(1)_files/테스트오류.png) <br>
