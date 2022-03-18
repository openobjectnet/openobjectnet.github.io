---
title:  "실전! 스프링 데이터 JPA"
search: false
categories: 
  - JPA
toc: true  
tags:
  - JPA
author: 이성희
---

# Intro 
인프런 실전! 스프링 데이터 JPA 강의를 듣고 정리한 내용입니다.

## 1. 스프링 데이터 JPA
기존 순수 JPA에서 번거롭게 작성하던 기본 CRUD같은 코드 및 간단한 기능들을 스프링 데이터 JPA가 제공하는 공통 인터페이스를 사용함으로서 편리하고 쉽게 생산성을 높일 수 있다.

- 순수 JPA

```java
@Repository
public class MemberJpaRepository {
    // 회원 조회
    public Member find(Long id) {
        return em.find(Member.class, id);
    }

    // 회원 저장
    public Member save(Member member) {
        em.persist(member);
        return member;
    }
}
```
```java
public void testMember() {
    Member member = new Member("memberA");
    Member savedMember = memberJpaRepository.save(member);
    Member findMember = memberJpaRepository.find(savedMember.getId());
    ...
}
```

- 스프링 데이터 JPA

스프링  데이터 JPA가 제공하는 공통 인터페이스 사용<br>
JpaRepository 인터페이스: 공통 CRUD 제공

```java
public interface MemberRepository extends JpaRepository<Member, Long> {
}
```

```java
public void testMember() {
    Member member = new Member("memberA");
    Member savedMember = memberRepository.save(member);
    Member findMember = memberRepository.findById(savedMember.getId()).get();
    ...
}
```

<br>주요 메서드
- save : 새로운 엔티티는 저장하고 이미 있는 엔티티는 병합한다.
- delete : 엔티티 하나를 삭제한다. 내부에서 EntityManager.remove() 호출
- findById : 엔티티 하나를 조회한다. 내부에서 EntityManager.find() 호출
- findAll : 모든 엔티티를 조회한다. 정렬( Sort )이나 페이징( Pageable ) 조건을 파라미터로 제공


## 2. 쿼리 메소드
도메인에 특화된 쿼리가 필요할 경우 사용

### 메소드 이름으로 쿼리 생성

메소드 이름을 분석해서 JPQL을 생성하고 실행
```java
public interface MemberRepository extends JpaRepository<Member, Long> {
 List<Member> findByUsernameAndAgeGreaterThan(String username, int age);
}
```

스프링 데이터 JPA가 제공하는 쿼리 메소드 기능
- 조회 : find...By
- COUNT : count...By
- EXISTS : exists...By
- 삭제 : delete...By
- DISTINCT : findDistinct
- LIMIT : findFirst3, findFirst, findTop, findTop3

...에는 식별하기 위한 내용이 들어감


### @Query, 리포지토리 메소드에 쿼리 정의
@Query 어노테이션을 사용해서 리파지토리 인터페이스 메소드에 쿼리 직접 정의<br>
메소드 이름으로 쿼리 생성 기능은 파라미터가 증가하면 메서드 이름이 지저분해지기 때문에 @Query 기능을 자주 사용

```java
@Query("select m from Member m where m.username= :username and m.age = :age")
List<Member> findUser(@Param("username") String username, @Param("age") int age);
```

DTO로 직접 조회
```java
@Query("select new study.datajpa.dto.MemberDto(m.id, m.username, t.name) " +
        "from Member m join m.team t")
List<MemberDto> findMemberDto();
```
<br>

## 3. 스프링 데이터 JPA 페이징과 정렬
- 페이징과 정렬 파라미터
    - org.springframework.data.domain.Sort : 정렬 기능
    - org.springframework.data.domain.Pageable : 페이징 기능 (내부에 Sort 포함)

- 특별한 반환 타입
    - org.springframework.data.domain.Page : 추가 count 쿼리 결과를 포함하는 페이징
    - org.springframework.data.domain.Slice : 추가 count 쿼리 없이 다음 페이지만 확인 가능 (내부적으로 limit + 1 조회)
    - List(자바 컬렉션) : 추가 count 쿼리 없이 결과만 반환

아래와 같은 방식으로 Repository 인터페이스 내에서 사용
```java
Page<Member> findByUsername(String name, Pageable pageable); //count 쿼리 사용
Slice<Member> findByUsername(String name, Pageable pageable); //count 쿼리 사용 안함
List<Member> findByUsername(String name, Pageable pageable); //count 쿼리 사용 안함
List<Member> findByUsername(String name, Sort sort); // 정렬만 적용
```

예제

시작페이지는 0, size는 3, username 내림차순 정렬하여 조회
```java
PageRequest pageRequest = PageRequest.of(0, 3, Sort.by(Sort.Direction.DESC, "username"));
Page<Member> page = memberRepository.findByAge(10, pageRequest);
```

Member를 가져오는 쿼리, count를가져오는 쿼리 확인
```java
select
    member0_.member_id as member_i1_0_,
    member0_.age as age2_0_
from
    member member0_ 
where
    member0_.age=? 
order by
    member0_.username desc limit ?

select
    count(member0_.member_id) as col_0_0_ 
from
    member member0_ 
where
    member0_.age=?
```

페이지를 유지하면서 엔티티를 DTO로 변환하기
```java
Page<Member> page = memberRepository.findByAge(10, pageRequest);
Page<MemberDto> dtoPage = page.map(m -> new MemberDto());
```


## 4. 페치조인, @EntityGraph
연관된 엔티티들을 SQL 한번에 조회하는 방법

지연로딩으로 설정했는데 한꺼번에 조회해야할 경우 사용

![entityclass](/assets/images/jpa/entityclass.PNG)

### JPQL 페치 조인
```java
@Query("select m from Member m left join fetch m.team")
List<Member> findMemberFetchJoin();
```

### @EntityGraph
사실상 페치 조인의 간편 버전
LEFT OUTER JOIN 사용
```java
//공통 메서드 오버라이드
@Override
@EntityGraph(attributePaths = {"team"})
List<Member> findAll();

//JPQL + 엔티티 그래프
@EntityGraph(attributePaths = {"team"})
@Query("select m from Member m")
List<Member> findMemberEntityGraph();

//메서드 이름으로 쿼리에서 특히 편리하다.
@EntityGraph(attributePaths = {"team"})
List<Member> findByUsername(String username)
```

## 5. JPA Hint
SQL에서 Hint를 사용하듯이 JPA에서도 JPA구현체에 힌트를 전달

### ReadOnly(조회만 사용)
Hint를 사용하여 영속성 컨텍스트에 저장되는 것을 방지

```java
@QueryHints(value = @QueryHint(name = "org.hibernate.readOnly", value = "true"))
Member findReadOnlyByUsername(String username);
```
```java
Member member = memberRepository.findReadOnlyByUsername("member1");
member.setUsername("member2");
em.flush(); //Update Query 실행X
```


## 6. Auditing
엔티티의 변경 시점에 언제, 누가 변경했는지에 대한 정보를 기록하는 기능

@MappedSupperclass : 부모 클래스임을 명시하며 상속받는 클래스에서 멤버변수가 컬럼이 되도록 함

@EntityListeners(AuditingEntityListener.class) : 값이 변경되었을 때 자동으로 기록


```java
@EntityListeners(AuditingEntityListener.class)
@MappedSuperclass
public class BaseEntity {
    @CreatedDate
    @Column(updatable = false)
    private LocalDateTime createdDate;
    
    @LastModifiedDate
    private LocalDateTime lastModifiedDate;
    
    @CreatedBy
    @Column(updatable = false)
    private String createdBy;
    
    @LastModifiedBy
    private String lastModifiedBy;
}
```

```java
public class Member extends BaseEntity {}
```

## 7. 도메인 클래스 컨버터
- HTTP 파라미터로 넘어온 엔티티의 아이디로 엔티티 객체를 찾아서 바인딩
- findMember : id를 파라미터로 받아 직접 member를 찾아옴
- findMember2 : id를 파라미터로 받으면 도메인 클래스 컨버터가 중간에 동작해서 member를 반환

```java
@GetMapping("/members/{id}")
public String findMember(@PathVariable("id") Long id){
    Member member = memberRepository.findById(id).get();
    return member.getUserName();
}


@GetMapping("/members2/{id}")
public String findMember2(@PathVariable("id") Member member){
    //Member member = memberRepository.findById(id).get();
    return member.getUserName();
}
```
도메인 클래스 컨버터로 엔티티를 파라미터로 받으면 단순 조회용으로만 사용

트랜잭션이 없는 범위에서 엔티티를 조회했으므로, 엔티티를 변경해도 DB에 반영되지 않는다.