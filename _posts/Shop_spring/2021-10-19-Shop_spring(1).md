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
  - Gradle 설정
  - View 환경설정
  - thymeleaf 템플릿 엔진
  - application.yml
  - H2 데이터베이스
  - JPA
  - 쿼리 파라미터 로그
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
사용하는 OS에 맞는 버전을 설치 후 h2폴더를 원하는 위치에 두고 h2 폴더 내의 bin폴더로 들어간 후 h2.sh를 실행시킨다. <br>
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
JDBC URL : jdbc:h2:tcp://localhost/~/jpashop
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
주의해야 할 점 <br>
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

## Entity, Repository 동작 확인

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

No EntityManager with actual transaction ~~~ 에러를 내뱉었다. <br>
EntityManager를 통한 모든 데이터의 변경은 항상 transaction 안에서 일어나야하는데 transaction이 없기 때문에 에러가 일어난 것이다. <br>
따라서 Test 케이션에 @Transactional 어노테이션을 걸어주자<br>
Transaction 어노테이션은 스프링 프레임워크가 제공해주는 것과 자바 표준이 제공해주는 것 2가지가 존재하는데 <br>
스프링 프레임워크가 제공해주는 것을 사용할 것이다. <br>

``` java
package jpabook.jpashop;

import org.assertj.core.api.Assertions;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;
import org.springframework.transaction.annotation.Transactional;

import static org.junit.Assert.*;

@RunWith(SpringRunner.class) // 스프링과 관련된 것을 테스트를 할 것임을 Junit에게 알림
@SpringBootTest
public class MemberRepositoryTest {

    @Autowired MemberRepository memberRepository;

    @Test
    @Transactional
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
<br>

다시 테스트를 실행시켜보면 <br>
![png](/images/Shop_spring(1)_files/테스트에러해결.png) <br> 
<br>
문제 없이 잘 실행되었음을 확인할 수 있다.
<br><br>

그럼 DB가 잘 생성되었는지 확인해 보자. <br>
먼저 실행창을 보면 <br>
![png](/images/Shop_spring(1)_files/drop_db.png) <br>

member라는 table이 존재시 drop (테이블을 날림)
<br>
![png](/images/Shop_spring(1)_files/db생성확인.png) <br> 

application.yml에서 hibernate에 ddl-auto: create로 했기 때문에 member table이 생성되었음을 확인할 수 있다.
<br>
![png](/images/Shop_spring(1)_files/db테이블생성.png) <br> 
테이블이 생성되었음을 확인할 수 있다. <br>
그런데 왜 data는 존재하지 않을까 ???<br>
지금 test케이스를 통해 실행 해보았다. <br> 
@Transactional 어노테이션이 테스트케이스에 있다면 테스트 후에 전부 Rolled back을 해버린다. <br>
테스트가 아닌 곳에 있으면 data가 저장될 것이다. <br>
당연히 테스트에서는 data를 그대로 남기면 반복적인 테스트가 불가하기 때문이다 <br><br>
![png](/images/Shop_spring(1)_files/롤백.png) <br> 

<br>
테스트 에서도 데이터가 롤백 없이 남은 모습을 보고 싶을때는
@Rollback(false) 어노테이션을 통해 롤백을 하지않게 한다. <br>

```java
package jpabook.jpashop;

import org.assertj.core.api.Assertions;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.annotation.Rollback;
import org.springframework.test.context.junit4.SpringRunner;
import org.springframework.transaction.annotation.Transactional;

import static org.junit.Assert.*;

@RunWith(SpringRunner.class) // 스프링과 관련된 것을 테스트를 할 것임을 Junit에게 알림
@SpringBootTest
public class MemberRepositoryTest {

    @Autowired MemberRepository memberRepository;

    @Test
    @Transactional
    @Rollback(false)
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
        Assertions.assertThat(findMember).isEqualTo(member);
        System.out.println("findMemebr == member : " + (findMember == member));
    }
}
```java

```
테스트를 실행시켜 보자 <br>
![png](/images/Shop_spring(1)_files/테스트추가.png) <br> 

아무 문제없이 잘 실행된다.
findMember와 member가 같은지 확인해보는 것은 <br>
저장한 것과 조회한 것이 같은지 확인하는 것이다 <br>
당연히 같다. <br>
같은 transaction 안에서 저장하고 조회하면 영속성 컨텍스트가 똑같을 것이다. <br>
같은 영속성 컨텍스트 안에서 ID값이 같으면 같은 entity로 식별한다. <br>
따라서 findMember == member 를 True를 반환한다. <br>
<br>
만약에 테스트 실행 중 아래와 같은 오류가 발생할 수 있다. <br>
 No tests found for given includes: [jpabook.jpashop.MemberRepositoryTest] 
(filter.includeTestsMatching) <br>

이 경우 해결방법으로는
스프링 부트 2.1.x 버전을 사용하지 않고, 2.2.x 이상 버전을 사용하면 Junit5가 설치된다 <br>
이때는 build.gradle 마지막에 다음 내용을 추가하면 테스트를 인식할 수 있다.<br>
Junit5 부터는 build.gradle 에 아래 코드 내용을 추가해야 테스트가 인식된다.<br>

```java
test {
  useJUnitPlatform()
}
```
## jar를 빌드해서 동작 확인
아래와 같이 jar를 빌드해서 동작을 확인해보자 <br>
![png](/images/Shop_spring(1)_files/jar빌드오류.png) <br> 

하 그런데 왜 이러한 오류가 날까??? <br>
에러 메시지를 자세히 보면 <br>
Could not find org-springframework.boot:spring-boot-devtools: <br>
즉 build.gralde에서 dependencies에 입력한 org-springframework.boot:spring-boot-devtools를 찾지 못하는 것이다. <br>
<br>

이 부분을 다시해결하기 위해 <br>
intellij에서 build.gradle 파일을 연 후 <br>
'org-springframework.boot:spring-boot-devtools' 항목을 <br>
'org.springframework.boot:spring-boot-devtools' 로 바꾸고 저장하면 <br>
파일 우측 상단에 코끼리 로고가 뜨는데 이것을 한번 클릭하고 작업이 완료될때까지 기다린 후 <br>
(파일 우측 상단에 코끼리 로고를 누르는 것은 dependencies를 refresh 해주는 것이다.) <br>
다시 ./gradlew clean build 를 통해 build를 하면 <br>
![png](/images/Shop_spring(1)_files/빌드완성.png) <br> 

빌드가 잘 되었음을 확인할 수 있다. <br>
빌드가 다 된후 build 파일의 lib 파일안에 jpashop-0.0.1-SNAPSHOT.jar 파일이 생성됨을 확인할 수 있다. <br>

![png](/images/Shop_spring(1)_files/jar파일.png) <br> 

이 파일이 생성된 후에 "java -jar 생성된 파일"을 입력하면 해당 부분이 실행 된다. <br>
![png](/images/Shop_spring(1)_files/jar실행.png) <br> 
즉 intelliJ가 아닌 터미널에서 실행한 것이다. <br>
<br>
그 결과로 <br>
![png](/images/Shop_spring(1)_files/settingfinish.png) <br>
웹 페이지가 정상적으로 뜨는것을 확인할 수 있다. <br>
<br>

스프링 부트를 통해 복잡한 설정이 다 자동화 되어있기 때문에 스프링 부트를 통한 추가 설정은 스프링 부트 메뉴얼을 참고하면 된다. <br><br>

### 쿼리 파라미터 로그 남기기
jpa를 쓸때 sql 나가는 것과 DB 커넥션 가져오는 것들이 어느 타이밍에 일어나는지 궁금한 경우가 많은데 <br>
쿼리 파라미터를 찍으면 알 수 있는데 그 부분이 되게 답답하다. <br>
이전에 테스트 케이스 실행하였을때 아래와 같은 결과를 확인하였다 <br>
![png](/images/Shop_spring(1)_files/쿼리파라미터.png) <br>
(?, ?) 로 쿼리 파라미터가 남아있지 않아 굉장히 답답하다. <br>
이 문제를 해결해주기 위해 <br>
application.yml의 로그(logging)에 다음을 추가하여 org.hibernate.type : trace로 두면 <br>
SQL 실행 파라미터를 로그로 남긴다. <br>

```yml
logging:
  level:
    org.hibernate.SQL: debug # logger를 통해 하이버네이트 실행 SQL을 남김
    # 하이버네이트가 남기는 모든 로그가 debug모드로
    org.hibernate.type: trace #SQL 실행 파라미터를 로그로 남긴다

```
<br>
이렇게 수정후 테스트 케이스를 다시 실행시켜 주면 <br>
![png](/images/Shop_spring(1)_files/쿼리파라미터결과.png) <br>

(?, ?)는 그대로 남지만 아래에
1번 parameter는 VARCHAR이고 memberA<br>
2번 parameter는 BIGINT이고 1 이라는 로그를 남겨준다 <br><br>

하지만 조금 더 SQL 실행 파라미터 로그를 제대로 남기기 위해 <br>
외부 라이브러리를 사용한다 <br>
외부 라이브러리 : https://github.com/gavlyukovskiy/spring-boot-data-source-decorator <br>

스프링 부트를 사용하는 경우에는 해당 라이브러리만 추가해주면 된다. <br>
라이브러리를 추가하는 방법은 build.gradle에서 dependencies에 <br>
implementation 'com.github.gavlyukovskiy:p6spy-spring-boot-starter:1.5.6' 를 추가해준다. <br>

```java
dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
	implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
	implementation 'org.springframework.boot:spring-boot-starter-validation'
	implementation 'org.springframework.boot:spring-boot-starter-web'
	implementation 'org.springframework.boot:spring-boot-devtools'
	implementation 'com.github.gavlyukovskiy:p6spy-spring-boot-starter:1.5.6' // SQL 실행 파라미터를 로그로 남기기 위한 외부 라이브러리

    ~~~
```
<br>

dependencies에 추가해 주었기 때문에 intelliJ경우 우측 상단 코끼리 모양을 클릭하여 dependencies를 refresh 해줘야 한다. <br>
이전에도 refesh를 해주지 않아 오류가 났던적이 있으니 항상 dependencies 수정 및 새로 등록시 refresh 하는것을 잊지말자 <br>
<br>
자 다시 이제 테스트 코드를 실행시켜 보면 <br>
![png](/images/Shop_spring(1)_files/라이브러리추가.png) <br>
p6spy로 나오는 것을 볼 수 있다. <br>
(?, ?) 로 첫번쨰는 원본이 출력되고 <br>
두번쨰는 parameter 값들이 출력된다. <br>

로그를 어떤 형식으로 출력하고 싶은지는 이전에 언급한 외부 라이브러리에 대한 URL 주소를 통해 옵션을 어떻게 변경하는지 확인한 후 수정하면 된다. <br>
<br>

여기서 뜬금 없지만 dependencies를 보면 어떤 것은 뒤에 version이 적혀있다. <br>
implementation 'com.github.gavlyukovskiy:p6spy-spring-boot-starter:1.5.6'  과 같이 <br>
하지만 implementation 'org.springframework.boot:spring-boot-starter-data-jpa'와 같이 어떤 것들은 version이 없는데 왜 그럴까?? <br>
-> 그 이유는 스프링 부트가 왠만한 라이브러리들은 자신의 버전과 맞는 라이브러리를 버전을 세팅 해놓았다. <br>
하지만 스프링 부트가 세팅을 해놓지 않은 라이브러리들은 뒤에 따로 버전을 적어야 한다.<br><br>

참고로 쿼리 파라미터를 로그로 남기는 외부 라이브러리는 시스템 자원을 사용하기 때문에 개발 단계에서는 편하 게 사용해도 되지만 운영시스템에 적용하려면 꼭 성능테스트를 하고 사용하는 것이 좋다고 한다. <br> <br>

지금까지 프로젝트를 진행하기 위한 setting이 완료 되었다. <br>
앞으로는 프로젝트 목표인 Shop에 관련된 웹 애플리케이션 프로젝트를 만들어 보자 <br>

### Reference :
김영한 강사님 실전! 스프링 부트와 JPA 활용1 - 웹 애플리케이션 개발 강의 중 
