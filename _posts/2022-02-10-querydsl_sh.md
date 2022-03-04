---
title:  "실전! Querydsl"
search: false
categories: 
  - Querydsl
tags:
  - JPA
  - Querydsl
toc: true
author: 김승희
---


# Intro
안녕하세요. 김승희입니다.<br>
인프런 '실전! Querydsl' 강의를 듣고 정리한 내용을 공유합니다.


# 1. Querydsl이란?
 - 쿼리를 자바코드로 작성할 수 있게 도와주는 기술이다.


# 2. Q클래스 파일
 - Querydsl은 컴파일 단계에서 엔티티를 기반으로 Q클래스 파일들을 생성시킨다.
 - Q클래스 파일을 기준으로 쿼리를 작성한다.

 * Member.java
    
    ```java
    @Entity
    @Getter @Setter
    public class Member {
        
        @Id @GeneratedValue
        @Column(name = "member_id")
        private Long id;
        private String userName;
        private int age;
        
        @ManyToOne(fetch = FetchType.LAZY)
        @JoinColumn(name = "team_id")
        private Team team;
    }
    ```

 * QMember.java

    ```java
    @Generated("com.querydsl.codegen.DefaultEntitySerializer")
    public class QMember extends EntityPathBase<Member> {
        private static final long serialVersionUID = -769675599L;
        private static final PathInits INITS = PathInits.DIRECT2;
        public static final QMember member = new QMember("member1");
        public final NumberPath<Integer> age = createNumber("age", Integer.class);
        public final NumberPath<Long> id = createNumber("id", Long.class);
        public final QTeam team;
        public final StringPath userName = createString("userName");
        
        public QMember(String variable) {
            this(Member.class, forVariable(variable), INITS);
        }
        public QMember(Path<? extends Member> path) {
            this(path.getType(), path.getMetadata(), PathInits.getFor(path.getMetadata(), INITS));
        }
        public QMember(PathMetadata metadata) {
            this(metadata, PathInits.getFor(metadata, INITS));
        }
        public QMember(PathMetadata metadata, PathInits inits) {
            this(Member.class, metadata, inits);
        }
        public QMember(Class<? extends Member> type, PathMetadata metadata, PathInits inits) {
            super(type, metadata, inits);
            this.team = inits.isInitialized("team") ? new QTeam(forProperty("team")) : null;
        }
    }
    ```


# 3. JPQL - Querydsl 비교

* JPQL

    ```java
    @Test
    public void startJPQL() {
        String qlString =
            "select m from Member m " +
            "where m.username = :username";
        Member findMember = em.createQuery(qlString, Member.class)
                              .setParameter("username", "member1")
                              .getSingleResult();
    }
    ```

* Querydsl

    ```java
    @Test
    public void startQuerydsl() {
        JPAQueryFactory queryFactory = new JPAQueryFactory(em);
        // QMember qMember = new QMember("m"); 별칭 직접 지정
        QMember qMember = QMember.member; //기본 인스턴스 사용
        Member findMember = queryFactory
                                .select(m)
                                .from(m)
                                .where(m.username.eq("member1")) //파라미터 바인딩 처리
                                .fetchOne();
    }
    ```

* Querydsl의 장점
 - Querydsl은 *컴파일* 시점에 문법오류를 잡을 수 있다.
 - 파라미터 바인딩을 자동으로 처리한다.
 - 메서드 체이닝으로 이루어져 *동적쿼리* 작성에 유리하다.


# 4. 검색조건쿼리

    ```java
    @Test
    public void search() {
        Member findMember = queryFactory
                                .selectFrom(member)
                                .where(member.username.eq("member1")
                                        , member.age.eq(10))
                                //.and(member.age.eq(10)))
                                .fetchOne();
    }
    ```


# 5. 결과조회

 - fetch() : 리스트 조회, 데이터 없으면 빈 리스트 반환
 - fetchOne() : 단 건 조회
    - 결과가 없으면 : null
    - 결과가 둘 이상이면 : com.querydsl.core.NonUniqueResultException
 - fetchFirst() : 처음 한 건 조회, limit(1).fetchOne()
 - fetchResults() : 페이징 정보 포함, total count 쿼리 추가 실행
 - fetchCount() : count 쿼리로 변경해서 count 수 조회


# 6. 조인

## 6-1. 기본조인

 ```java
 @Test
 public void join() throws Exception {
    QMember member = QMember.member;
    QTeam team = QTeam.team;
    List<Member> result = queryFactory
                            .selectFrom(member)
                            .join(member.team, team)
                            .where(team.name.eq("teamA"))
                            .fetch();
 }
 ```

## 6-2. 외부조인

 ```java
 @Test
 public void join_on_filtering() throws Exception {
    List<Tuple> result = queryFactory
                            .select(member, team)
                            .from(member)
                            .leftJoin(member.team, team)
                            .on(team.name.eq("teamA"))
                            .fetch();
 }
 ```

## 6-3. 페치조인

 ```java
 @Test
 public void fetchJoinUse() throws Exception {
    Member findMember = queryFactory
                            .selectFrom(member)
                            .join(member.team, team).fetchJoin()
                            .where(member.username.eq("member1"))
                            .fetchOne();
 }
 ```


## 7. 서브쿼리
JPAExpressions 클래스를 이용해 서브쿼리를 만들 수 있다.

* where절 서브쿼리

    ```java
    @Test
    public void subQuery() throws Exception {
        QMember memberSub = new QMember("memberSub");
        List<Member> result = queryFactory
                                .selectFrom(member)
                                .where(member.age.eq(
                                        JPAExpressions
                                            .select(memberSub.age.max())
                                            .from(memberSub)
                                ))
                                .fetch();
    }
    ```

* select절 서브쿼리
   
    ```java
    List<Tuple> fetch = queryFactory
                            .select(member.username,
                                JPAExpressions
                                    .select(memberSub.age.avg())
                                    .from(memberSub))
                            .from(member)
                            .fetch();
    ```

> JPQL 서브쿼리의 한계점으로 from절에서의 서브쿼리는 지원하지 않는다.
> 필요한 경우 조인으로 해결하거나 쿼리를 분리하여 해결하고, 안된다면 네이티브 쿼리를 사용해야한다.


## 8. case문

* 일반적인 case문

```java
 List<String> result = queryFactory
                        .select(member.age
                            .when(10).then("열살")
                            .when(20).then("스무살")
                            .otherwise("기타"))
                        .from(member)
                        .fetch();
```

* CaseBuilder를 이용한 case문

 ```java
 List<String> result = queryFactory
                        .select(new CaseBuilder()
                            .when(member.age.between(0, 20)).then("0~20살")
                            .when(member.age.between(21, 30)).then("21~30살")
                            .otherwise("기타"))
                        .from(member)
                        .fetch();
 ```