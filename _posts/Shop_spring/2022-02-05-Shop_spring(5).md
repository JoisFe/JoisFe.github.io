---
title: "회원 도메인 개발"
categories:
  - Project - Shop_spring
tags:
  - 회원 도메인 개발
  - 회원 리포지토리 개발
use_math: true
---

# 회원 도메인 개발

- 구현 기능
    - 회원 등록
    - 회원 목록 조회
<br>

- 순서
    - 회원 엔티티 코드 다시 보기
    - 회원 리포지토리 개발
    - 회원 서비스 개발
    - 회원 기능 테스트
<br>

엔티티 코드는 이전에 작성하였으니 회원 리포지토리 코드를 작성해 볼 것이다.
<br>

## 회원 리포지토리 개발

### 회원 리포지토리 코드

```java
package jpabook.jpashop.repository;

import jpabook.jpashop.domain.Member;
import org.springframework.stereotype.Repository;

import javax.persistence.EntityManager;
import javax.persistence.PersistenceContext;
import java.util.List;

@Repository // Spring에서 제공하는 어노테이션으로 컴포넌트 스캔(Component Scan) 의 대상이 되어 자동으로 스프링 빈으로 관리되게 됨
public class MemberRepository {

    @PersistenceContext // JPA가 제공하는 표준 어노테이션 이 어노테이션이 있으면 스프링이 생성한 JPA entity manager를 주입해줌
    // 만약 이 어노테이션 사용하지 않고 순수하게 JPA를 사용하면
    // EntityManagerFactory에서 직접 EntityManager를 꺼내서 써야함
    private EntityManager em; // 스프링이 Entity manager를 만들어 여기에 주입해줌

    public void save(Member member) {
        em.persist(member); // member를 저장
        // em.persist 하면 일단 영속성 컨텍스트에 member 엔티티를 넣고
        // 그리고 나중에 transaction이 commit 되는 시점에 DB에 insert 쿼리가 날라가 DB에 반영됨
    }

    public Member findOne(Long id) {
        return em.find(Member.class, id); // member 조회 (id에 해당하는 member 1개 조회)
    }
    // em.find는 단건 조회임 파라미터는 첫번째는 타입 두번째는 PK를 넣어주면 됨

    public List<Member> findAll() {
        // member 모두 조회 하고 싶은 경우 JPQL을 작성해야 (SQL과 유사하지만 차이가 존재)
        // SQL은 테이블을 대상으로 쿼리를 하지만
        // JPQL은 엔티티 객체에 대상으로 쿼리를 한다.
        // 결국 JPQL이 SQL로 번역되어야함
        return em.createQuery("select m from Member m", Member.class)
                .getResultList();

        // from의 대상이 테이블이 아니라 entity 여기선 Member.class
    }

    public List<Member> findByName(String name) {
        // name 으로 member를 조회하는 경우
        return em.createQuery("select m from Member m where m.name = :name", Member.class)
                .setParameter("name", name)
                .getResultList();
    }
}

```

<br>

회원 서비스 코드를 작성해보자 <br>

## 회원 서비스 개발

### 회원 서비스 코드

```java
package jpabook.jpashop.service;

import jpabook.jpashop.domain.Member;
import jpabook.jpashop.repository.MemberRepository;
import lombok.RequiredArgsConstructor;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;


import java.util.List;

@Service // 이 어노테이션 또한 컴포넌트 스캔의 대상이 되어 자동으로 스프링 빈으로 등록이 됨
@Transactional(readOnly = true) // 트랜잭션, 영속성 컨텍스트로 JPA의 어떤 모든 데이터 변경이나 로직들은 트랜잭션 안에서 실행되어야 하기 때문에
// readOnly = true 경우 데이터의 변경이 없는 읽기 전용 메서드에 사용하는 것으로
// 영속성 컨텍스트를 flush 하지 않기 때문에 약간의 성능 향상이 일어남 (읽기 전용엔 다 적용)
// DB 드라이버가 지원하면 DB에서 성능 향상
@RequiredArgsConstructor // final 있는 필드만 가지고 생성자 만들어 줌
public class MemberService {
    private final MemberRepository memberRepository; // 생성자 주입 방법

    /*
    @Autowired // 스프링이 스프링 빈에 등록되어 있는 MemberRepository를 Injection 해줌 -> Field Injection
    private MemberRepository memberRepository;
     */
    // 사실 @Autowired를 이용한 Injection 방법 (Field Injection)에는 단점이 많다
    // 변경이 어렵다는 문제 등
    // 따라서 생성자 Injection 많이 사용, 생성자가 하나면 생략 가능하다

    /*
    private final MemberRepository memberRepository;
    // final 해두면 생성자 내에서 값 세팅 안하면 에러 터트림 -> 컴파일 시점에 memberRepository를 설정하지 않는 오류 체크 가능
    // (보통 기본 생성자를 추가할 때 발견)

    @Autowired // 요즘은 @Autowired 생략가능 <- 생성자가 하나만 있는 경우
    public MemberService(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }
     */

    /*
    // 롬북을 이용하면 생성자 작성 안해도 되는 장점

    @RequiredArgsConstructor // final 있는 필드만 가지고 생성자 만들어 줌
    public class memberService {
        private final MemberRepository memberRepository;

        // 생성자 작성 생략 가능
     */
    
    // 회원 가입
    @Transactional // 읽기전용이 아닌 경우 readOnly = true 넣으면 DB의 데이터 변경이 일어나지 않음
    // class 전체에 @Transaction 걸면 기본적으로 클래스 내의 public 메서드에 적용됨
    // 메서드에 따로 @Transaction 걸면 해당 메서드는 따로 건 어노테이션을 우선으로 적용됨
    public Long join(Member member) {
        validateDuplicateMember(member); // 중복 회원 검증
        memberRepository.save(member);

        return member.getId();
    }

    public void validateDuplicateMember(Member member) {
        List<Member> findMembers = memberRepository.findByName(member.getName());
        // 만약 동시에 DB에 같은 회원명으로 회원가입하여 (멀티 쓰레드 등의 상황)
        // validateDuplicateMember 통과하게 되면 동시에 insert가 되어 문제가 될 수 있음
        // 따라서 DB에서 Member의 name을 unique로 제약 조건을 걸어야 한다

        if (!findMembers.isEmpty()) {
            throw new IllegalStateException("이미 존재하는 회원입니다.");
        }
    }

    // 회원 전체 조회
    public List<Member> findMember() {
        return memberRepository.findAll();
    }

    // 회원 단건 조회
    public Member findOne(Long memberId) {
        return memberRepository.findOne(memberId);
    }
}

```