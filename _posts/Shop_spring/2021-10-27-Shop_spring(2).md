---
title: "도메인 분석 설계"
categories:
  - Project - Shop_spring
tags:
  - 도메인 모델 설계
  - 테이블 설계
  - 엔티티 분석
  - 엔티티 클래스 개발
use_math: true
---

이번에는 도메인을 분석 설계 해보려고 한다.

# 도메인 분석 설계

## 요구사항 분석
먼저 구현하고자 하는 실제 동작하는 화면은 아래와 같다. <br>
![png](/images/Shop_spring(2)_files/실제동작화면.png) 
<br>

구체적으로 구현해야할 기능들에 대해 알아보자 <br>

### 기능 목록
- 회원 기능
    - 회원 등록
    - 회원 조회
- 상품 기능
    - 상품 등록
    - 상품 수정
    - 상품 조회
- 주문 기능
    - 상품 주문
    - 주문 내역 조회
    - 주문 취소
- 기타 요구사항
    - 상품은 재고 관리가 필요하다.
    - 상품의 종류는 도서, 음반, 영화가 있다.
    - 상품을 카테고리로 구분할 수 있다.
    - 상품 주문시 배송 정보를 입력할 수 있다.

## 도메인 모델과 테이블 설계

먼저 도메인 모델부터 구상해보자 <br>
![png](/images/Shop_spring(2)_files/도메인 모델.png) 
<br>

회원, 주문, 상품의 관계 : <br>
회원은 여러 상품을 주문할 수 있고 한 번 주문할 때 여러 상품을 선택할 수 있기 때문에 주문과 상품이 다대다 관계이다. <br>
그러나 다대다 관계는 관계형 데이터베이스와 엔티티에서 거의 사용하지 않기 때문에 그림처럼 주문상품이라는 엔티티를 추가해서 다대다 관계를 일대다, 다대일 관계로 풀어내는 것이 좋다. <br>
<br>

상품 분류 : <br>
상품은 도서, 음반, 영화로 구분된다. <br>
상품이라는 공통 속성을 사용하기 때문에 상속 구조로 나타내었다. <br>
<br>

### 회원 엔티티 분석
![png](/images/Shop_spring(2)_files/회원엔티티분석.png) <br>

회원(Member) : <br>
이름과 임베디드 타입인 주소(Address), 주문(orders) 리스트를 가짐
<br> <br>

주문(Order) : <br>
한 번 주문시 여러 상품을 주문할 수 있기 때문에 주문과 주문상품(OrderItem)은 일대다 관계가 된다. <br>
주문은 상품을 주문한 회원, 배송 정보, 주문 날짜, 주문 상태(status)를 가지고 있다. <br>
주문 상태는 열거형을 사용했고 주문(ORDER)과 취소(CANCEL)을 표현 가능하다. <br>
<br>

주문상품(OrderItem) : <br>
주문한 상품 정보와 주문 금액(orderPrice), 주문 수량(count) 정보를 가지고 있다.<br>
(보통 OrderLine, LineItem으로 많이 표현한다고 한다.) <br>
<br>

상품(Item) : <br>
이름, 가격, 재고수량(stockQuantity)을 가지고 있다. <br>
상품을 주문하면 재고수량이 줄어든다. <br>
상품의 종류는 도서, 음반, 영화가 있고 각각은 사용하는 속성이 조금씩은 다르다. <br>
<br>

배송(Delivery) : <br>
주문시 하나의 배송 정보를 생성한다.<br>
주문과 배송은 일대일 관계이다. <br>
<br>

카테고리(Category) : <br>
상품과 다대다 관계를 맺는다. <br>
parent, child로 부모, 자식 카테고리를 연결한다. <br>
<br>

주소(Address) : <br>
값 타입(임베디드 타입) 이다. <br>
회원과 배송(Delivery)에서 사용한다. <br>
<br>

회원이 주문을 하기 때문에 회원이 주문리스트를 가지는 것이 잘 설계한 것 같지만 객체 세상은 실제 세상과 다르다는 것을 알아야 한다!!! <br>
실무에선 회원이 주문을 참조하지 않고 주문이 회원을 참조하는 것으로 충분하다 <br>
정리하면 회원이 주문을 하니까 회원에 주문의 리스트를 두고 하면 될 것 같지만 이것은 사람의 생각일 뿐이고 시스템은 회원과 주문은 동급으로 놓고 고민해야 한다. <br>
회원을 통해서 항상 주문이 일어나는 것이 아니라 주문을 생성할때 회원이 필요하다고 보는 것이 객체 세상에서는 더 맞다.<br>
<br>

여기서 일대다, 다대일의 양방향 연관관계를 설명하기 위해 추가한 것이다. <br>
<br>

### 회원 테이블 분석
![png](/images/Shop_spring(2)_files/회원테이블분석.png) <br>

MEMBER :
회원 엔티티의 Address 임베디드 타입 정보가 회원 테이블에 그대로 들어갔다. <br>
DELIVERY 테이블도 마찬가지
<br><br>

ITEM :
앨범, 도시, 영화 타입을 통합해서 하나의 테이블로 만듬 <br>
DTYPE 칼럼으로 타입을 구분했다. <br>
<br>

CATEGORY와 ITEM도 다대다 관계인데 이전에 설명 했듯 관계형 데이터베이스와 엔티티에서 거의 사용하지 않기 때문에 그림처럼 CATEGORY_ITEM 테이블을 추가하여 다대다 관계를 일대다, 다대일 관계로 풀어내는 것이 좋다.<br><br>

!! 테이블명이 ORDER이 아니라 ORDERS인 것은 데이터베이스가 order by 때문에 예약어로 잡고 있는 경우가 많기 때문에 관례상 ORDERS를 많이 사용한다고 한다. 참고하자!!!
<br> <br>

실제 코드에서는 DB에 소문자 + _(언더스코어) 스타일을 사용할 것이다. <br>
DB 테이블 명, 칼럼 명에 대한 관례는 사람, 회사마다 다르지만 보통은 대문자 + _나 소문자 + _ 방식 중 하나를 지정하여 일관성 있게 사용한다. <br>
이번 프로젝트에서는 객체와 차이를 나타내기 위해 DB 테이블, 칼럼명은 대문자로 설명하지만 실제 코드에서는 소문자 + _ 스타일을 사용할 것이다. <br><br>


### 연관관계 매핑 분석

- 회원과 주문 : <br>
일대다, 다대일의 양방향 관계이다.<br>
따라서 연관관계의 주인을 정해야 하는데 외래 키가 있는 주문을 연관관계의 주인으로 정하는 것이 좋다. <br>
따라서 Order.member를 ORDERS.MEMBER_ID 외래 키와 매핑을 한다. <br>
<br>

여기서 잠깐 왜 외래 키가 있는 곳을 연관관계의 주인으로 정하는 거지 ???? <br>
연관관계의 주인은 단순히 외래 키를 누가 관리하냐의 문제이지 비즈니스상 우위에 있다고 주인으로 정하면 안된다. <br>
예를 들어서 자동차와 바퀴가 있으면 일대다 관계에서 항상 다쪽에 외래 키가 있으므로 외래 키가 있는 바퀴를 연관관계의 주인으로 정하면 된다. <br>
물론 자동차를 연관관계의 주인으로 정하는 것이 불가능 한 것은 아니지만 자동차를 연관관계의 주인으로 정하면 자동차가 관리하지 않는 바퀴 테이블의 외래 키 값이 업데이트 되므로 관리와 유지보수가 어렵고 추가적으로 별도의 업데이트 쿼리가 발생하는 성능 문제도 있기 때문이다. <br>
<br>

- 주문상품과 주문 : <br>
다대일 양방향 관계이다. <br>
외래 키가 주문상품에 있으므로 주문상품이 연관관계의 주인이 된다. <br>
그러므로 OrderItem.order를 ORDER_ITEM.ORDER_ID 외래 키과 매핑한다. <br>
<br>

- 주문상품과 상품 : <br>
다대일 단방향 관계이다. <br>
OrderItem.item을 ORDER_ITEM.ITEM_ID 외래 키와 매핑한다. <br>
<br>

- 주문과 배송 : <br>
일대일 양방향 관계이다. <br>
Order.delivery를 ORDERS_DELIVERY_ID 외래 키와 매핑한다. <br>
<br>

- 카테고리와 상품 : <br>
@ManyToMany를 사용해서 매핑한다. <br>
!!! 실무에서는 @ManyToMany는 사용하지 말자 <br>
여기서는 다대다 관계를 예제로 보여주기 위해서 추가했을 뿐인 것을 명심하자 <br>
<br>

### 엔티티 클래스 개발

이번 프로젝트에서는 엔티티 클래스에 Getter, Setter를 모두 열고 최대한 단순하게 설계를 할 것이다. <br>
하지만 실무에서는 가급적 Getter는 열어두지만 Setter는 꼭 필요한 경우에만 사용하는 것이 좋다고 한다. <br>
<br>

자 이제는 위에서 만든 엔틴티 클래스를 코드로 작성을 해보자
<br><br>
이전에 작성한 Member와 MemberRepository 클래스를 삭제하자 <br>

### 회원 엔티티

코드 작성 중에 다시 중요한 부분을 얘기하자면 <br>
Member는 orders를 List로 가지고 있고 <br>
Order 또한 member를 Member로 가지고 있다 <br> 
즉 양방향 참조를 가지고 있다. <br>
여기서 데이터베이스의 Foreign Key(FK)는 ORDERS에 있는 MEMBER_ID 하나 뿐이다. <br>
문제는 MEMBER와 ORDERS의 관계를 뭔가 바꾸고 싶다면 FK에 있는 값을 변경해야 한다. <br>
Member에도 ORDERS와 관련된 연관된 필드인 orders를 가지고 있고 <br>
반면에 Order에도 MEMBER와 관련된 연관된 필드인 member를 가지고 있다. <br>
그러면 JPA에서는 둘다 값을 확인해서 바꾸어야하는지 혼란이 온다. 도대체 어디에 값이 변경 되었을때 FK 값을 바꾸어야 하는지 고민이 된다. <br>
예를 들어 member에는 값을 세팅을 하였는데 orders에는 값을 세팅 안한 경우라던지 반대인 경우에 JPA는 둘 중 뭘 믿고 FK를 누가 업데이트를 해야할지 혼란이 온다. <br>
<br>
이런 문제를 해결하기 위해 FK를 업데이트 하는 것은 JPA에서는 둘 중에 하나만 선택을 하게 약속을 하였다. <br>
왜냐하면 객체는 변경 포인트가 두 군데 이지만 테이블은 FK 하나만 변경하면 되기 때문이다. <br>
따라서 둘 중 하나를 연관관계 주인을 정하면 된다.<br>
여기서는 orders와 member 중 어떤 값이 변경 되었을떄 FK값을 바꿀꺼야 라고 지정하면 되는데 그 지정된 것이 연관관계 주인이다. <br> <br>

그럼 연관관계 주인을 정하는 기준은 ??? <br>
FK가 가까운 곳으로 정하면 된다.<br>
일대다 관계에서 다에 FK가 들어간다 <br>
Order를 보면 ORDERS 테이블에 MEMBER_ID인 FK가 있다.<br>
이것을 연관관계 주인이 있는 곳으로 매핑하면 된다.<br>
Member에 있는 orders와 Order에 있는 member 중 Order에 있는 member가 FK에 가까이 있으므로 member를 연관관계 주인으로 정하면 된다 <br>
연관관계 주인을 정하지 않으면 Member에 있는 뭔가를 바꾸면 ORDERS 테이블에 무엇인가 업데이트 되면서 자신은 Member에 있는 어떤것을 바꾸엇는데 왜 Order에 있는 무엇인가 바뀌었는지 혼란이 온다 <br>
즉 Order에 있는 member를 바꾸면 자신의 테이블에 대해 바꾸었으니 자신의 테이블에 있는 column이 바뀌는 구나 라고 자연스럽게 생각할 수 있다. <br><br>

또한 일대일 관계에서 FK를 어디에 두냐는 문제가 존재한다.<br>
일대일 관계의 문제점은 FK를 Order에 두어도 되고 Delivery에 두어도 된다 <br>
단 어디에 두느냐에 따라 장단점이 존재한다 <br>
주로 access를 많이 하는 곳에 FK를 두는 방법이 좋다. <br>
이번 프로젝트에서 Delivery를 직접 조회하는 경우 보다 Order를 보면서 Delivery에 접근하는 경우가 많다 <br>
따라서 FK를 ORDERS에 DELIVERY_ID를 FK로 두었다. <br>
그러면 연관관계의 주인을 FK에 가까이 있는 Order에 있는 delivery를 선택하면 된다.
<br><br>

### 회원 엔티티

```java
package jpabook.jpashop.domain;

import lombok.Getter;
import lombok.Setter;

import javax.persistence.*;
import java.util.ArrayList;
import java.util.List;

@Entity
@Getter @Setter
public class Member {

    @Id @GeneratedValue
    @Column(name = "member_id") // 매핑을 위해 primary key는 "MEMBER_ID"
    private Long id;

    private String name;

    @Embedded
    private Address address;

    @OneToMany(mappedBy = "member") // Member와 Order의 관계는 일대다 관계
    // Order 테이블에 있는 member 필드에 의해서 매핑 된것이므로 (즉 연관관계 주인이 Order 테이블에 있는 member)
    // 이것을 mappedBy를 통해 적어줌
    // 여기에 값을 넣는다고 해서 foreign key (Member의 member_id) 값이 바뀌지 않음

    private List<Order> orders = new ArrayList<>();


}

```
<br>

여기서 보면 엔티티 식별자는 id를 사용하고 PK column명은 member_id를 사용했음을 확인할 수 있다. <br>
엔티티는 타입(여기서 Member)이 있으므로 id 필드만으로도 쉽게 구문할 수 있다. <br>
하지만 테이블은 타입이 없기 때문에 구분이 어렵다. 그리고 테이블은 관례상 "테이블 명 + id"를 많이 사용한다. <br>
따라서 객체에서 id 대신에 memberId를 사용해도 된다. <br>
중요한 것은 일관성 있게 작성 !!!! <br>
<br>

### 주문 엔티티
``` java
package jpabook.jpashop.domain;

import lombok.Getter;
import lombok.Setter;

import javax.persistence.*;
import java.time.LocalDateTime;
import java.util.ArrayList;
import java.util.Date;
import java.util.List;

@Entity
@Table(name = "orders")
@Getter @Setter
public class Order {

    @Id @GeneratedValue
    @Column(name = "order_id") // primary key가 ORDER_ID
    private Long id;

    @ManyToOne // Order와 Member는 다대일 관계
    @JoinColumn(name = "member_id") // 매핑을 "member_id"로 할 것 (foreign key는 "MEMBER_ID")
    // Order와 Member 사이에서 Order 테이블에 있는 member가 연관관계 주인 -> 그냥 두면 됨
    // 여기에 값을 세팅하면 member_id인 foreign key 값 다른 멤버로 바뀌게 됨
    private Member member;

    @OneToMany(mappedBy = "order") // Order와 OrderItem은 일대다 관계
    // OrderItem에 order로 매핑이 됨
    private List<OrderItem> orderItems = new ArrayList<>();

    @OneToOne // Order과 Delivery 간의 관계는 일대일 관계
    // 일대일 관계의 문제점은 FK를 Order에 두어도 되고 Delivery에 두어도
    // 단 어디에 두느냐에 따라 장단점이 존재
    // 주로 access를 많이 하는 곳에 FK를 두는 방법이 좋다.
    // 이번 프로젝트에서 Delivery를 직접 조회하는 경우 보다 Order를 보면서 Delivery에 접근하는 경우가 많아서
    // 따라서 FK를 ORDERS에 DELIVERY_ID를 FK로 두었다.
    // 그러면 연관관계의 주인을 FK에 가까이 있는 Order에 있는 delivery를 선택하면 된다.
    @JoinColumn(name = "delivery_id") // 매핑을 "delivery_id"로 할 것 (foreign key는 "DELIVERY_ID")
    private Delivery delivery;

    private LocalDateTime orderDate; // 주문시간

    @Enumerated(EnumType.STRING)
    // EnumType이 ORDINAL이면 column이 숫자로 들어감
    // 그런데 ORDINAL로 하면 중간에 다른 상태가 생기면 번호가 바뀌면서 문제가 생김
    // 따라서 EnumType을 꼭 STRING으로 써서 중간에 다른것이 들어와도 순서에 의해서 문제가 생기지 않음
    private OrderStatus status; // 주문상태 [ORDER, CANCEL]
}


```
<br>

### 주문 상태
``` java
package jpabook.jpashop.domain;

import jpabook.jpashop.domain.item.Item;
import lombok.Getter;
import lombok.Setter;

import javax.persistence.*;

@Entity
@Getter @Setter
public class OrderItem {

    @Id @GeneratedValue
    @Column(name = "order_item_id") // 매핑을 위해 primary key는 "ORDER_ITEM_ID"
    private Long id;

    @ManyToOne // OrderItem과 Item은 다대일 관계
    @JoinColumn(name = "item_id") // 매핑을 ITEM_ID가 연관관계 주인
    private Item item;

    @ManyToOne // OrderItem과 Order는 다대일 관계
    @JoinColumn(name = "order_id") // 매핑을 ORDER_ID가 연관관계 주인
    // Order에도 orderItems가 있고 OrderItem에도 order가 있다.
    // 양방향 연관관계
    // 테이블을 보면 ORDER_ITEM에 ORDER_ID가 FK
    private Order order;

    private int orderPrice; // 주문 가격

    private int count; // 주문 수량
}

```
<br>

### 상품 엔티티

``` java
package jpabook.jpashop.domain.item;

import jpabook.jpashop.domain.Category;
import lombok.Getter;
import lombok.Setter;

import javax.persistence.*;
import java.util.ArrayList;
import java.util.List;

@Entity
@Inheritance(strategy = InheritanceType.SINGLE_TABLE)
// 상속관계 매핑에서는 상속관계 전략을 부모 클래스에 집어 넣어줘야 한다.
// 여기서는 SINGLE_TABLE 전략을 사용할 것이다.
@DiscriminatorColumn(name = "dtype") // 부모클래스에서 선언하는 하위 클래스를 구분하는 용도의 column
@Getter @Setter
public abstract class Item { // 추상클래스로 만든 이유는 구현체를 이용할 것이기 떄문

    @Id
    @GeneratedValue
    @Column(name = "item_id") // primary key가 ITEM_ID
    private Long id;

    private String name;
    private int price;
    private int stockQuantity;

    @ManyToMany(mappedBy = "items") // Category도 리스트로 Item을 가지고 Item도 리스트로 Category를 가지므로 다대다 관계
    // Category에 items로 매핑이 됨
    private List<Category> categories = new ArrayList<>();
}


```
<br>

### 상품 - 도서 엔티티

``` java
package jpabook.jpashop.domain.item;

import lombok.Getter;
import lombok.Setter;

import javax.persistence.DiscriminatorValue;
import javax.persistence.Entity;

@Entity
@DiscriminatorValue("B") // 하위클래스에서 선언하는 entity를 저장할 때 슈퍼타입의 구분 column에 저장할 값을 지정
// 어노테이션 선언 않을 경우 기본값으로 클래스 이름이 들어감
@Getter @Setter
public class Book extends Item { // Item 클래스에 상속

    private String author;
    private String isbn;
}

```
<br>

### 상품 - 음반 엔티티

```java
package jpabook.jpashop.domain.item;

import lombok.Getter;
import lombok.Setter;

import javax.persistence.DiscriminatorValue;
import javax.persistence.Entity;

@Entity
@DiscriminatorValue("A") // 하위클래스에서 선언하는 entity를 저장할 때 슈퍼타입의 구분 column에 저장할 값을 지정
// 어노테이션 선언 않을 경우 기본값으로 클래스 이름이 들어감
@Getter @Setter
public class Album extends Item { // Item 클래스에 상속

    private String artist;
    private String etc;
}

```
<br>

### 상품 - 영화 엔티티

```java
package jpabook.jpashop.domain.item;

import lombok.Getter;
import lombok.Setter;

import javax.persistence.DiscriminatorValue;
import javax.persistence.Entity;

@Entity
@DiscriminatorValue("M") // 하위클래스에서 선언하는 entity를 저장할 때 슈퍼타입의 구분 column에 저장할 값을 지정
// 어노테이션 선언 않을 경우 기본값으로 클래스 이름이 들어감
@Getter @Setter
public class Movie extends Item { // Item 클래스에 상속

    private String director;
    private String actor;
}

```

<br>

### 배송 엔티티

```java
package jpabook.jpashop.domain;

import lombok.Getter;
import lombok.Setter;

import javax.persistence.*;

@Entity
@Getter @Setter
public class Delivery {

    @Id @GeneratedValue
    @Column(name = "delivery_id") // 매핑을 위해 primary key는 "DELIVERY_ID"
    private Long id;

    @OneToOne(mappedBy = "delivery") // Delivery와 Order 간은 일대일 관계
    // Order에 delivery로 매핑이 됨
    private Order order;

    @Embedded
    private Address address;

    @Enumerated(EnumType.STRING)
    // EnumType이 ORDINAL이면 column이 숫자로 들어감
    // 그런데 ORDINAL로 하면 중간에 다른 상태가 생기면 번호가 바뀌면서 문제가 생김
    // 따라서 EnumType을 꼭 STRING으로 써서 중간에 다른것이 들어와도 순서에 의해서 문제가 생기지 않음
    private DeliveryStatus status; // READY(배송 준비), COMP(배송 완료)
}

```

<br>

### 배송 상태

```java
package jpabook.jpashop.domain;

public enum DeliveryStatus {
    READY, COMP
}


```

<br>

### 카테고리 엔티티

```java
package jpabook.jpashop.domain;

import jpabook.jpashop.domain.item.Item;
import lombok.Getter;
import lombok.Setter;

import javax.persistence.*;
import java.util.ArrayList;
import java.util.List;

@Entity
@Getter @Setter
public class Category {

    @Id @GeneratedValue
    @Column(name = "category_id") // 매핑을 위해 primary key는 "CATEGORY_ID"
    private Long id;

    private String name;

    @ManyToMany // Category도 리스트로 Item을 가지고 Item도 리스트로 Category를 가지므로 다대다 관계
    @JoinTable(name = "category_item", // 중간 테이블 CATEGORY_ITEM으로 매핑
        joinColumns = @JoinColumn(name = "category_id"), // 중간 테이블에서 Category 쪽으로 들어가는 FK는 CATEGORY_ID
            inverseJoinColumns = @JoinColumn(name = "item_id")) // 중간 테이블에서 Item 쪽으로 들어가는 FK는 ITEM_ID
    // 객체는 collection이 존재하여 collection 끼리 매핑하여 다대다관계가 가능하지만
    // 관계형DB경우 collection관계가 양쪽에서 가질 수 없기 때문에
    // 일대다, 다대일 관계 풀어내는 중간 테이블이 존재해야 한다!!
    private List<Item> items = new ArrayList<>();

    @ManyToOne // 해당 클래스 부모이니 Category와 부모 다대일 관계
    @JoinColumn(name = "parent_id") // 매핑을 위한 부모는 primary key가 "PARENT_ID"
    private Category parent; // Category구조는 계층구조 인데 부모에 대한

    @OneToMany(mappedBy = "parent") // 해당 클래스는 자식이니 Category와 자식은 일대다 관계
    // Category에 parent로 매핑이 됨 (자식은 부모와 매핑시킴)
    // 셀프로 양방향 연관관계를 것 것으로 볼 수 있음
    // 이름만 내 것이지 다른 Entity처럼 매핑하는 것으로 연관관계를 지어주면 됨
    private List<Category> child = new ArrayList<>();
}

```

<br>

### 주소 값 타입

```java
package jpabook.jpashop.domain;

import lombok.Getter;

import javax.persistence.Embeddable;

@Embeddable
@Getter
public class Address {

    private String city;
    private String street;
    private String zipcode;

    // jpa 생성시 여러 기술들을 사용하는데 (프록시 등)
    // 기본 생성자 없으면 이런 기술들을 사용할 수 없기 때문에 기본 생성자를 만들어 줘야한다
    protected Address() { // 접근지정자를 jpa 스펙상 protected까지 허용 (public으로 많은 호출 못하도록)
    }

    // 값 타입은 기본적으로 immutable 하게 설계되어야 한다.
    // 따라서 Setter를 제공하지 않고
    // 생성할 때 값이 세팅이 되고 그 뒤로 변경이 불가능 해야 한다.
    public Address(String city, String street, String zipcod) {
        this.city = city;
        this.street = street;
        this.zipcode = zipcode;
    }
}


```
<br>
참고로 값 타입은 변경 불가능하게 설계해야 한다!!! <br>
따라서 @Setter를 사용하지 않고 생성자에서 값을 모두 초기화해서 변경 불가능한 클래스로 만들어야 한다<br>
JPA 스펙상 엔티티나 임베디드 타입( @Embeddable )은 자바 기본 생성자(default constructor)를 public 또는 protected 로 설정해야 한다.<br>
public 으로 두는 것 보다는 protected 로 설정하는 것이 그나마 더 안전 하다. <br>
JPA가 이런 제약을 두는 이유는 JPA 구현 라이브러리가 객체를 생성할 때 리플랙션 같은 기술을 사용할 수
있도록 지원해야 하기 때문이다.
<br>
<br>

자 이제 엔티티 클래스 관련 코드를 다 작성하였으니 <br>
JpaShopApplication 클래스의 메인 메서드를 실행시켜 보자 <br>
![png](/images/Shop_spring(2)_files/메인메서드실행.png) <br>

만약 작성한 코드가 문제가 없다면 DB에 작성했던 결과대로 테이블이 생성되었을 것이다.<br>

![png](/images/Shop_spring(2)_files/회원테이블분석.png) <br>
위 그림 처럼 테이블이 생성되어있어야 한다. <br>

![png](/images/Shop_spring(2)_files/MEMBER.png) <br>
이처럼 MEMBER 테이블이 잘 생성 되었음을 확인할 수 있다. <br>

![png](/images/Shop_spring(2)_files/ORDERS.png) <br>
또한 ORDERS 테이블이 잘 생성 되었음을 확인할 수 있다. <br>

![png](/images/Shop_spring(2)_files/모든테이블(1).png) <br>
![png](/images/Shop_spring(2)_files/모든테이블(2).png) <br>

나머지 테이블 또한 모두 잘 생성 되었다. <br>
<br>

자 지금까지 도메인 분석 및 설계를 해 보았다. <br>
이후에는 지금까지 엔티티 클래스를 개발하였는데 엔티티 설계시 주의해야할 점에 대해 알아볼 것이다 <br>
<br>

### Reference :
김영한 강사님 실전! 스프링 부트와 JPA 활용1 - 웹 애플리케이션 개발 강의 중 

