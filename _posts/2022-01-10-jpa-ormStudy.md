---
title:  "ORM표준 JPA 프로그래밍 정리"
search: false
categories: 
  - JPA
toc: true  
last_modified_at: 2022-01-07T10:06:00-05:00
tags:
  - JPA
  - Spring Boot
author: 이효진
---

# 1.영속성 컨텍스트
<br><br>

- 엔티티를 영구 저장하는 환경
- EntityManager.persist(Entity)
  <br>
  
  ![영속성컨텍스트](https://media.vlpt.us/images/seho100/post/19547ef2-7cd2-4945-963c-f6ebc4d86790/image.png)

  * jpa는 사용자의 요청이 들어올때마다 EntityManegerFactory에서 EntityManager를 생성한다.
  * EntityManager는 내부적으로 DB 커넥션풀을 사용해서 DB에 붙는다.



<br>


  >  엔티티의 생명주기

  1. 비영속
       - jpa와 상관이 없는 상태
  
        ```java
        Member member = new Member();
        member.setName("member1");
        member.setAge(20);
        ```
  
  2. 영속
       -    객체가 EntityManager에 의해 관리되는 상태
        ```
           EntityManeger.persist(member);
        ````
  
  3. 준영속
        - 객체와 영속성 컨텍스트를 분리
        - detech(), clear(), close() 사용
  4. 삭제
        - 객체를 삭제
        - remove() 사용

        <br>
    >  영속성 컨텍스트의 이점
    1. 1차 캐시
   
        ![영속성컨텍스트](https://github.com/namjunemy/TIL/blob/master/Jpa/inflearn/img/03_persistence_context_cache.PNG?raw=true)

        ```java
        em.persist(member); //1차캐시에 저장
        em.find(Member.class,member.getId);  //1차 캐시에서 조회
        ```
    2. 동일성 보장
   
        ```java
        Member member1 = em.find(Member.class,"id1");
        Member member2 = em.find(Member.class,"id1");

        member1 == member2 // true
        ```

    3. 쓰기 지연
        ```java
        transaction.begin();

        em.persist(Member1);
        em.persist(Member2);

        transaction.commit(); //이때 db에 저장된다.
        ```

    4. 변경감지
   
        ```java
        transaction.begin();

        Member member = em.find(Member.class,"memberA");
        member.setName("newName");

        transaction.commit();

        ```

        ![영속성컨텍스트](https://blog.kakaocdn.net/dn/dG1iTX/btqFa7GzKP5/tCELwKVoc1dz9DgjNN58CK/img.png)
     

# 2.엔티티 매핑

> ### 엔티티 클래스 생성

- @Entity : Jpa가 관리할 객체
- @Table : 매핑할 테이블을 지정
- @Id : db의 pk와 매핑할 필드
- @GeneratedValue : 기본키 생성방법을 지정
  - IDENTITY - 데이터 베이스에 지정 ex) Auto_Increment
  - SEQUENCE - 데이터베이스 오브젝트 스퀀스 사용
  - TABLE - 키 생성용 테이블 사용
  - AUTO - 방언에 따라 지정
- @Column : 필드와 컬럼을 매핑
  - @Column(name = "username")
- @Enumerated : enum타입과 매핑
- @Lob : Blob, Clob 와 매핑
- @@Temporal : 날짜 타입을 매핑하고 싶을떄 사용
- @Transient : 해당 컬럼은 매핑하지 않는다.

    ```java
    @Entity
    @Table(name = "member") 
    public class Member{

        @Id
        @GeneratedValue(strategy = GenerationType.IDENTITY)
        private Long id;

        @Column(name = "username")
        private String name;

        @Enumerated(EnumType.STRING)
        private UserState state;

        ...

    }
    ```

# 3. 연관관계 매핑
> 단방향 연관 관계

<br>

![단방향 연관관계](https://media.vlpt.us/images/asdfg5415/post/11c9ac68-23c3-4d0e-9243-c516a09b2d2b/image.png)

  * 객체의 참조와 테이블의 외래키를 매핑한다.
  * 하나의 팀에는 다수의 멤버가 소속될수 있다.
  * 두 엔티티의 연관관계는 팀의 입장에서 볼때는 일대다(@OneToMany)    ,멤버의 입장에서 볼때는 다대일(@ManyToOne)의 관계이다.

```java
@Entity
public class Member{
    @Id
    @GeneratedValue
    private Long id;

    private String name;

    @ManyToOne
    @JoinColumn(name = "TEAM_ID")
    private Team team;
}
```

> 연관관계 사용 방법

```java
//팀 저장
Team team = new Team();
team.setName("TeamA");
em.persist(team);

Member member = new Memeber();
member.setName("member1");
member.setTeam(team);    //단방향 연관관계 설정, 참조 저장
em.persist(member);

em.find(Member.class,member.getId); //한번에 조인해서 team과 member를 가져온다. fetch lazy로 수정 권장

```

연관관계 수정
```java
//새로운 팀B
Team teamB = new Team();
team.setName("TeamB");
em.persist(teamB);

member.setTeam(teamB);
```

> 양방향 연관 관계

<br>

![단방향 연관관계](https://blog.kakaocdn.net/dn/bbfwZN/btqFNgJPObb/k4SbI2jWxrK66N8h3Wzu51/img.png)
* 테이블 상에서는 바뀌는 것이 없다 .
* Team에서는 members를 가지고있고 member는 team을 가지도록 설계하면 된다.
  
```java
@Entity
public class Team{
    @Id
    @generatedValue
    private Long Id;

    private String name;

    @OneToMany(MappedBy = "team") //team과 연관이 있다는것 명시
    private List<Member> members = new ArrayList<Member>();

}

```

```java
Team findTeam = em.find(Team.class,teamA.getId);
int memSize = findTeam.getMembers.size();

```

> 연관관계의 주인

양방향 연관관계의 패러다임
  
 member.team으로 Fk를 관리해야 할까? 아니면 team.members로 외래키를 관리해야 할까?

  -> Fk를 가진쪽을 연관관계의 주인으로 해야한다. 즉 Member.team이 Fk이므로 Member쪽이 연관관계의 주인이다.

  주인은  joinColumn을 사용하고 주인이 아니면 mappedBy를 사용해서 속성으로 주인을 지정한다.

  연관관계의 주인이 아닌쪽은 조회만 가능 하도록 한다.

<br>

> 연관관계에서 주의 할 점

1. 연관관계의 주인에 값을 입력해야한다.
 
   ```java
    Team team = new Team();
    team.setName("TeamA");
    em.persist(team);
    ​
    Member member = new Member();
    member.setName("member1");
    em.persist(member);

    //이부분을 추가하지 않으면 Fk에 값이 들어가지 않는다.
    //member.setTeam(team);



   ```
    |ID|USERNAME|TEAM_ID|
    |:----------|:----------:|----------:|
    |1|member1|null|

2. toString 사용 시 무한 루프에 빠질수 있으므로 사용하지 않는것을 권장


> 다양한 연관관계 매핑

<br>

## 다대일 연관관계

![단방향 연관관계](https://media.vlpt.us/post-images/conatuseus/0940bdf0-f018-11e9-83db-af073712e5fa/image.png)

1. 테이블에서 외래키는 항상 다 쪽에 있다.
2. 가장  다중적인 매핑

다대일 단방향 매핑

```java 
   @ManyToOne
    @JoinColumn(name = "TEAM_ID")
    private Team team;
```

다대일 양방향 매핑

```java 
   @OneToMany(mappedBy = "team")
    private List<Member> members = new ArrayList<>();

```

엔티티 저장
```java

Team team = new Team();
team.setName("A");
em.persist(team);
System.out.println("-----팀 저장");
​
Member member = new Member();
member.setUsername("NJ");
member.changeTeam(team);
em.persist(member);
System.out.println("-----멤버 저장");
​
tx.commit();

```
결과
```java
Hibernate: 
    /* insert hello.jpa.Team
        */ 
        insert 
        into
            Team
            (id, name) 
        values
            (null, ?)
-----팀 저장
Hibernate: 
    /* insert hello.jpa.Member
        */ 
        insert 
        into
            Member
            (id, age, createdDate, description, lastModifiedDate, roleType, TEAM_ID, name) 
        values
            (null, ?, ?, ?, ?, ?, ?, ?)
-----멤버 저장

```


### 일대다 

일대다 단방향 매핑

![단방향 연관관계](https://lar542.github.io/img/post_img/JPA-2019-09-01-1.png)

- 실무에서는 권장하지 않는다.
- 일대다에서 일이 연관관계의 주인
- 객체와 테이블의 차이때문에 반대편테이블의 외래키를 관리하는 구조
- @joinColum을 사용하지 않으면 새로운 테이블이 생성된다.


```java
//Team

@OneToMany
@JoinColumn(name = "TEAM_ID")
private List<Member> members = new ArrayList<Member>();

```

```java
//Member
//Team 없앰

```

실행문

```java
Member member = new Member();
member.setUsername("NJ");
em.persist(member);
​
System.out.println("-----멤버 저장");
​
Team team = new Team();
team.setName("A");
team.getMembers().add(member);
em.persist(team);
​
System.out.println("-----팀 저장");
​
tx.commit();

```

결과

```java
Hibernate: 
    /* insert hello.jpa.Member
        */ 
        insert 
        into
            Member
            (id, age, createdDate, description, lastModifiedDate, roleType, name) 
        values
            (null, ?, ?, ?, ?, ?, ?)
-----멤버 저장
Hibernate: 
    /* insert hello.jpa.Team
        */
        insert 
        into
            Team
            (id, name) 
        values
            (null, ?)
-----팀 저장
Hibernate: 
    /* create one-to-many row hello.jpa.Team.members */ 
    update
        Member 
    set
        TEAM_ID=? 
    where
        id=?

```
일대다 양방행 매핑

-지원하지 않는다.

## 일대일 연관관계 

![단방향 연관관계](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbAu7Ad%2Fbtq2N2nWR2R%2FqHzLtOFlN9GkMkkjEYkMKK%2Fimg.png)

* 일대일은 반대로 해도 일대일이다.
* 다대일은 다 쪽이 외래키를 가질수 있지만 일대일은 어느쪽에 둘지 선택 가능하다.
  
  ## 일대일 단방향 매핑

  ```java
    public class Member {
    
        @Id @GeneratedValue
        private Long id;
        private String name;
    
        @OneToOne
        @JoinColumn(name = "LOCKER_ID")
        Locker locker;
    }
  ```

  ```java 
    public class Locker {
    
        @Id @GeneratedValue
        private Long id;   
        private String name;
    }
  ```

  Member는  @OneToOne을 사용하여 일대일 단방향 매핑을 해주고 대상테이블인 
  Locker객체를 선언해준다.

  다대일 단방향매핑과 거의 유사하다.

  ## 일대일 양방향 매핑 

  ```java
    public class Locker {

    @OneToOne(mappedBy = "locker")
    private Member member;
    }

  ```

  반대쪽 테이블에 OneToOne 어노테이션을 추가하고 mappedBy를 추가하면 일대일 양방향 매핑을 할수 있다.

    ## 대상테이블에 양방향 매핑

    대상테이블의 양뱡향 매핑을 뒤집었다고 생각하면 된다.매핑 방법은 같다.
    주 테이블은 Member지만 외래키를 대상테이블에서 관리하는 것이다.

      ```java
    public class Member {
    
        @Id @GeneratedValue
        private Long id;
        private String name;
    
        @OneToOne(mappedBy = "member")
        Locker locker;
    }
  ```

  ```java 
    public class Locker {
    
        @Id @GeneratedValue
        private Long id;   
        private String name;
        @JoinColumn(name = "MEMBER_ID")
        private Member member
    }
  ```


    |주 테이블에 외래키를 넣는 경우|대상 테이블에 외래키를 넣는 경우|
    |:----------|:----------:|
    |주테이블에 외래키를 두고 대상테이블을 찾는다. |대상테이블에 외래키 존재|
    |객체지향 개발자들이 선호 | 전통적이 개발자들이 선호|
    |장점: 주 테이블만 조회해도 대상테이블 조회가능|일대다로 변경이 수월하다.|
    |단점 : Null을 허용해야한다.| 단점: 즉시로딩만 가능하다.|

    ## 다대다 연관관계

    2개의 테이블로는 다대다 연관을 나태낼수가없다. 그래서 다대일 일대다로 다대다를 표현하게 된다.

    ![단방향 연관관계](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FdasjjB%2Fbtq2Mk4Lbo5%2FBhxop3kTZlQVTMqlC8UUE1%2Fimg.png)

    @ManyToMany로 다대다를 지정해주고 JoinTable으로 연결테이블을 지정할수 있다.

    다대다 매핑을 하면 컬럼이아니라 연결테이블이 생성된다. 그 테이블에는 Member의 pk와 Product의 pk가 외래키로 들어가며, 이 두값이 pk가 된다.

    ```java
        ...
        @ManyToMany
        @JoinTable(name = "MEMBER_PRODUCT")
        private List<Product> products = new ArrayList<>();
        ...
    ```
    다대다 양방향 매핑을 하고싶으면 아래와같은 코드를 추가하면된다.
    ```java
        ...
        @ManyToMany(mappedBy = "products")
        private List<Member> members = new ArrayList<>();
        ...
    ```
    실행된 sql문
    ```java
       ...
   
    Hibernate: 
        
        create table member_product (
        Member_id bigint not null,
            products_id bigint not null
        )
        
        ...
        
    Hibernate: 
        
        alter table member_product 
        add constraint FK17rh8i9jrsy7yqy2j6e9yijuw 
        foreign key (products_id) 
        references Product
    Hibernate: 
        
        alter table member_product 
        add constraint FK3cjijenmv5sgu1w04p4ofy6ik 
        foreign key (Member_id) 
        references Member
    ```

    중간테이블이 생성된것을 볼수 있다.

    <br>

    > 다대다 매핑을 사용하면 안되는 이유
    * 매핑된 정보만 들어가고 다른 정보는 넣을수 없다.
    * 중간테이블이 숨겨져있어서 예상치 못한 겂이 나온다.
    * 중간테이블 엔테테를 추가하고 일대다 다대일 관계를 추가하는것이 낫다.
    * ![단방향 연관관계](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FdY8fYy%2Fbtq2MOEgrbU%2F7c0I88DPiv6en7ubQcdcBK%2Fimg.png)

  