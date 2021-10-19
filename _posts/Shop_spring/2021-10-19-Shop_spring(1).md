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