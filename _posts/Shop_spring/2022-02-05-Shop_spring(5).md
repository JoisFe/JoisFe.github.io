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

@Repository // Spring에서 제공하는 어노테이션으로 컴포넌트 스캔(Component Scan) 내에 자동으로 스프링 빈으로 관리되게 됨
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