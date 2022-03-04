---
title:  "실전 Spring data JPA"
search: false
categories: 
  - JPA
toc: true  
tags:
  - JPA
author: 이승효
---


# Intro 
인프런 실전! 스프링 데이터 JPA를 듣고 알게된 점을 공유하겠습니다.


## 1. 도메인 생성

![entityclass](/assets/images/jpa/entityclass.PNG)

여러 명의 맴버가 하나의 팀에 소속될 수 있게 예제 엔티티를 만듭니다.

```java
@Entity
@Getter @Setter
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@ToString(of = {"id", "username", "age"})
public class Member {

    @Id @GeneratedValue
    @Column(name = "member_id")
    private Long id;
    private String username;
    private int age;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "team_id")
    private Team team;

}

```
```java
@Entity
@Getter @Setter
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@ToString(of = {"id", "name"})
public class Team extends JpaBaseEntity {

    @Id
    @GeneratedValue
    @Column(name = "team_id")
    private Long id;
    private String name;

    @OneToMany(mappedBy = "team")
    private List<Member> members = new ArrayList<>();

}
```

JPA는 엔티티를 생성할 때 기본 생성자를 만들어줘야 합니다.<br>
JPA는 DB 값을 객체 필드에 주입할 때 기본 생성자로 객체를 생성하는데 기본 생성자가 없으면 예외가 발생한다.<br>
생성자를 private로 생성 시 객체 생성 시 예외가 발생합니다.<br>
그래서 protected, public을 이용해 기본 생성자를 생성해 줘야 합니다. 


연관관계 매핑 시 외래키를 가지고 있는 쪽이 연관관계의 주인인데 주인의 반대편에는 mappedBy를 명시해 준다.<br>
일반적으로 외래키는 ManyToOne이 가지고 있으므로 연관관계의 주인은 ManyToOne으로 생각하면 된다.

@ManyToOne은 fetch타입이 기본 EAGER인데 실무에선 LAZY타입으로 모두 변경해야 한다.

## 2. spring-jpa 사용방법

```java
public interface MemberRepository extends JpaRepository<Member, Long> {
}
```

repository를 만들고 JpaRepository를 상속받으면 spring-data-jpa가 제공하는 구현체(공통 메소드)를 사용할 수 있습니다.

### 2-1. 쿼리 메소드

JpaRepository는 공통 메소드 기능을 제공하는데<br>
공통 메소드로 해결되지 않는 도메인 특화 쿼리가 필요할 때 쿼리 메소드를 사용해 해결하면 된다.


#### 메소드 이름으로 쿼리 생성

JPA에서 제공하는 룰대로 메소드를 만들면
JPA는 메소드 이름을 분석해 JPQL쿼리를 생성하는 기능을 제공한다. 

##### 스프링 데이터 JPA가 제공하는 쿼리 메소드 기능
- 조회: find…By 
- COUNT: count…By 반환타입 long
- EXISTS: exists…By 반환타입 boolean
- 삭제: delete…By, remove…By 반환타입 long
- DISTINCT: findDistinct, findMemberDistinctBy
- LIMIT: findFirst3, findFirst, findTop, findTop3


```
...에는 아무 거나 넣어도 되고 By뒤에는 where절에 사용할 조건을 넣는다.
```

```java
Member findTestByUsername(String member);
//List<Member> findByUsernameAndAgeGreaterThan(String username, int age); 쿼리 조건이 많을 수록 메소드 이름이 길어진다. (가독성 문제)
```

실무에서 복잡한 쿼리를 메소드 이름으로 생성하는 데에는 한계가 있다.<br>
쿼리 메소드는 복잡한 쿼리도 해결할 수 있는 방법을 제공한다.

#### @Query, 리포지토리 메소드쿼리 정의

@Query 어노테이션을 사용하면 실행할 메소드에 쿼리를 직접 작성이 가능하다.<br>
메소드 이름으로 생성하는 쿼리보다 더 복잡한 쿼리를 사용할 수 있으며, 이름도 마음대로 정할 수 있어서 가독성이 좋아진다.

<b>기본조회</b>
```java
 @Query("select m from Member m where m.username = :username and m.age = :age")
  List<Member> findUser(@Param("username") String username, @Param("age") int age);
```

파라미터를 바인딩 할 때 쿼리문에 있는 :변수명을 메소드에 @Param에 명시해 주어야 한다.

<b>IN조회</b>
```java
  @Query("select m from Member m where m.username in :names")
  List<Member> findByNames(@Param("names") List<String> names);
```

컬렉션으로 파라미터를 넘겨 IN으로 조회도 가능하다.


<b>DTO로 직접 조회</b>
```java
@Query("select new study.datajpa.dto.MemberDto(m.id, m.username, t.name) " +
        "from Member m join m.team t")
  List<MemberDto> findMemberDto();
```

> DTO로 직접 조회하기 위해서는 `new`명령어를 사용해야 한다. 그리고 생성자가 일치하는 DTO가 필요하다.

```java
@Data
public class MemberDto {
  private Long id;
  private String username;
  private String teamName;
  
  public MemberDto(Long id, String username, String teamName) {
    this.id = id;
    this.username = username;
    this.teamName = teamName;
  } 
}
```

### 2-2. 페이징
스프링 JPA는 페이징과 정렬 기능도 제공한다.

```java
  Page<Member> findByAge(int age, Pageable pageable);
```
```java
  PageRequest pageRequest = PageRequest.of(0, 3, Sort.by(Sort.Direction.DESC, "username"));
  Page<Member> page = memberRepository.findByAge(age, pageRequest);
```
페이징을 할 경우 pageable을 파라미터로 넘겨주면 되는데
pageable을 상속받는 PageRequest를 만들어서 파라미터로 넘겨주면 된다.
현재는 0 페이지 3 사이즈 username으로 내림차순 설정이 되어있다.

<b>주의점</b><br>
페이징 시 주의할 점은 Page타입으로 반환값을 받을 시
토탈 카운트를 가져오기 위한 카운트 쿼리를 날리는데 
```java
  @Query(value = "select m from Member m left join m.team t")
  Page<Member> findByAge(int age, Pageable pageable)
```
조인을 할경우 카운트쿼리도 조인을 해서 가져온다. (성능에 좋지 않다.)<br>
굳이 카운트를 조인해서 가져오지 않아도되는 left outer join의 경우에는 
```java
  @Query(value = "select m from Member m left join m.team t",
          countQuery = "select count(m) from Member m")
  Page<Member> findByAge(int age, Pageable pageable)
```
카운트 쿼리를 따로 정의해서 사용하면 된다.


## 3. N + 1문제

스프링 jpa를 사용하다 보면 N + 1문제에 직면하게 되는데
이 문제는 연관 관계에서 발생하는 이슈로 연관 관계가 설정된 엔티티를 조회할 경우에 조회된 데이터 갯수(n) 만큼의
쿼리가 추가로 발생하여 데이터를 읽어오게 되는 문제이다.

<b>발생시점</b><br>
두 개의 엔티티가 1:N 관계를 가지며 JPQL로 객체를 조회할 때

- EAGER 전략으로 데이터를 가져오는 경우
- LAZY 전략으로 데이터를 가져온 이후에 가져온 데이터에서 하위 엔티티를 다시 조회하는 경우


### 3-1. fetchjoin
N + 1문제 해결 방안으로는 fetchjoin이 있는데
```java
@Query("select m from Member m left join fetch m.team")
List<Member> findMemberFetchJoin();
```
join 뒤에 fetch를 붙여주면 된다.
fetch join을 하면 한방 쿼리로 연관관계 데이터들을 가져올 수 있다.

### 3-2. EntityGraph
EntityGraph는 fetchjoin을 좀 더 편리하게 사용하도록 도와준다.

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

<b>EntityGraph 정리</b>

- 사실상 페치 조인의 간편 버전
- LEFT OUTER JOIN 사용 

## 4. Auditing
만약 여러 테이블에서 공통으로 사용하는 속성이 있을 경우 테이블마다 추가해 주기에는
생산성이 떨어지게 된다. 이러한 문제를 해결 주는 게 Auditing이다.
Auditing은 공통으로 사용해야 할 속성을 주입해 준다고 생각하면 된다.

예제로 등록일, 수정일을 만들어 보았습니다.

<b>설정</b>

- `@EnableJpaAuditing 스프링 부트 설정 클래스에 적용해야함`
- `@EntityListeners(AuditingEntityListener.class) 엔티티에 적용`
- `@MappedSuperclass 엔티티에 적용`

```java
@EntityListeners(AuditingEntityListener.class)
@MappedSuperclass //부모 클래스에 선언하고 속성만 상속 받아 사용
@Getter
public class BaseTimeEntity {

    @CreatedDate //Entity가 생성되어 저장될 때 시간이 자동 저장.
    @Column(updatable = false)
    private LocalDateTime createdDate;

    @LastModifiedDate //Entity의 값을 변경할 때 시간이 자동 저장.
    private LocalDateTime lastModifiedDate;

}
```
```java
public class Member extends BaseTimeEntity {
```

BaseTimeEntity 상속받으면 Member 테이블에 createdDate, lastModifiedDate 컬럼이 추가되고
엔티티가 생성, 수정될 때 값을 자동으로 넣어준다.

