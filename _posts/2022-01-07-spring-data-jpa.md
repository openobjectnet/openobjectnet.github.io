---
title:  "실전! 스프링 데이터 JPA 정리"
search: false
categories: 
  - JPA
toc: true  
last_modified_at: 2022-01-07T08:06:00-05:00
tags:
  - JPA
  - Spring Boot
author: 곽지용
---


실전! 스프링 데이터 JPA 정리 - 곽지용사원 20220107
======================
> [참고] 
> 강의는 MacOS, IntelliJ 환경이지만
> 글작성자는 Windows, VSCode 환경에서 따라해보았습니다.
>> https://www.inflearn.com/course/스프링-데이터-JPA-실전

# 1. 프로젝트 환경설정

### 1-1. 프로젝트 생성

* 스프링 부트 스타터(https://start.spring.io/) 에서 간단하게 생성 가능하다.
    - SpringBootVersion: 2.2.1        //현재 설정 불가  *2022-01 기준 2.6.2 버전이 최신
    - groupId: study
    - artifactId: data-jpa

* Gradle 설정
IntelliJ는 들어가자마자 자동으로 Gradle Dependency를 확인하고 빌드가 되는데
VSCODE의 경우 안된다면 Gradle 확장팩 플러그인을 설치 후에 따로 빌드해야 한다.

    ```
    plugins {
        id ‘org.springframework.boot’ version ‘2.2.1.RELEASE’
        id ‘io.spring.dependency-management’ version ‘1.0.8.RELEASE’
        id ‘java’
    }
    group = ‘study’
    version = ‘0.0.1-SNAPSHOT’
    sourceCompatibility = ‘1.8’
    configurations {
        compileOnly {
            extendsFrom annotationProcessor
        }
    }
    repositories {
        mavenCentral()
    }
    dependencies {
        implementation ‘org.springframework.boot:spring-boot-starter-data-jpa’
        implementation ‘org.springframework.boot:spring-boot-starter-web’
        implementation ‘com.github.gavlyukovskiy:p6spy-spring-boot-starter:1.5.7’ //작성자는 1.8.0으로 설정
        compileOnly ‘org.projectlombok:lombok’
        runtimeOnly ‘com.h2database:h2’
        annotationProcessor ‘org.projectlombok:lombok’
        testImplementation(‘org.springframework.boot:spring-boot-starter-test’) {
            exclude group: ‘org.junit.vintage’, module: ‘junit-vintage-engine’
        }
    }
    test {
        useJUnitPlatform()
    }
    ```


* 실행속도 관련설정
영상에서는 IntelliJ 를 IDE로 사용한다. //작성자는 VSCode를 사용했기에 해보진 못했다.
IntelliJ가 영상기준 최신에선 Gradle로 실행하는 것이 Default로 잡혀있어서
IntelliJ IDE로 실행하는 방법을 알려준다. 

    ```
    > Preferences Build, Execution, Deployment Build Tools Gradle
    > Build and run using: Gradle IntelliJ IDEA
    > Run tests using: Gradle IntelliJ IDEA
    ```

* 롬복 설정
아래는 IntelliJ의 롬복 설정방법이다.
    ```
    1. Preferences plugin lombok 검색 실행 (재시작)
    2. Preferences Annotation Processors 검색 Enable annotation processing 체크 (재시작)
    3. 임의의 테스트 클래스를 만들고 @Getter, @Setter 확인
    ```
아마 이클립스의 경우 따로 library에 있는 롬복을 실행시켜서 init 파일에 설정을 추가해야 할 것이다.
VSCode의 경우 Lombok Annotations Support for VS Code 플러그인을 설치했다.


### 1-2. H2 데이터베이스 설치
특이하게 H2라는 데이터베이스를 사용하는데 엄청 간단하고 용량도 6MB 밖에 안된다.
> 설치
> https://www.h2database.com/html/main.html
처음에 특이하게 콘솔에서 'jdbc:h2:~/디비명' 같은형식으로 접속해서 DB파일을 생성하고
이후에는 'jdbc:h2:tcp://localhost/~/디비명' 로 접속해서 사용한다.
'jdbc:h2:~/디비명'와 같은 형식은 파일 직접 접근이라 블러킹 된다고 한다.


### 1-3. 스프링 데이터 JPA와 DB 설정, 동작확인
프로퍼티 파일은 지우고  application.yml 파일 생성 후 시작한다. 
> 탭이 아니라 공백으로 2칸씩 구분해야 한다.

    ```
    ------ application.yml 파일 ------ 

    spring:
      datasource:
        url: jdbc:h2:tcp://localhost/~/datajpa
        username: sa
        password:
        driver-class-name: org.h2.Driver
      jpa:
        hibernate:
          ddl-auto: create //실행시 테이블을 드랍시키고 재생성하는 옵션
        properties:
          hibernate:
            # show_sql: true   //SQL 실행시 SQL문이 콘솔에 프린트 되기 때문에 지워준다.
            format_sql: true
    logging.level:
      org.hibernate.SQL: debug  //디버깅 로그에 SQL이 노출되도록 해준다.
      # org.hibernate.type: trace
    ```

# 2. JPA 동작 TEST

* 회원 엔티티

    ```
    @Entity
    @Getter @Setter //롬복에서 게터세터를 생성해준다.
    public class Member {
        @Id @GeneratedValue //GeneratedValue 어노테이션은 AutoIncrement해주는 옵션
        private Long id;
        private String username;
        ...
    }
    ```

### 2-1. 회원 JPA 리포지토리

    ```
    @Repository
    public class MemberJpaRepository {
        @PersistenceContext
        private EntityManager em;

        public Member save(Member member) {
            em.persist(member);
            return member;
        }
        public Member find(Long id) {
            return em.find(Member.class, id);
        }
    }
    ```

### 2-2. 스프링 데이터 JPA 리포지토리

    ```
    public interface MemberRepository extends JpaRepository<Member, Long> {
    }
    ```
> 아무것도 없는 인터페이스의 모습 ..

### 2-2. 스프링 데이터 JPA 기반 테스트

    ```
    @SpringBootTest
    @Transactional
    @Rollback(false)
    public class MemberRepositoryTest {
        @Autowired
        MemberRepository memberRepository;
        @Test
        public void testMember() {
            Member member = new Member("memberA");
            Member savedMember = memberRepository.save(member);
            Member findMember =
            memberRepository.findById(savedMember.getId()).get();
            Assertions.assertThat(findMember.getId()).isEqualTo(member.getId());

            Assertions.assertThat(findMember.getUsername()).isEqualTo(member.getUsername());
            Assertions.assertThat(findMember).isEqualTo(member); //JPA 엔티티 동일성 보장
        }
    }
    ```
> 이전의 MemberRepository 인터페이스에 따로 구현해주지 않았지만 이미 사용가능한 모습을 볼 수 있다!

* 쿼리 파라미터 로그 남기기
yml 설정으로 남긴 로그는 파라미터가 '?' 로 표현되고 파라미터 내용이 나중에 하나씩 나오기 때문에 보기 힘든데
p6spy를 사용하면 파라미터내용이 박혀있는 SQL로그를 확인가능하다.

    ```
    아래 내용을 build.gradle에 추가
    implementation 'com.github.gavlyukovskiy:p6spy-spring-boot-starter:1.5.7'  //p6spy 버전이 Gradle 버전과 호환되는지 확인 필요 이 때문에 작성자의 경우 1.8.0 사용
    ```

# 3. 예제 도메인 모델

### 3-1. Member 엔티티

    ```
    package study.datajpa.entity;
    import lombok.AccessLevel;
    import lombok.Getter;
    import lombok.NoArgsConstructor;
    import lombok.Setter;
    import javax.persistence.*;
    @Entity
    @Getter @Setter  //실무에서는 Setter는 사용하지 않는다.
    @NoArgsConstructor(access = AccessLevel.PROTECTED) //기본생성자는 있어야해서 PROTECTED로 선언가능하도록 만들어준다.
    @ToString(of = {"id", "username", "age"}) //객체가 String으로 출력될때 설정한 필드만 출력이 된다. #Team까지 출력할경우 Team안의 Member를 출력할 수도 있어 순환참조 될 수있다.
    public class Member {
        @Id
        @GeneratedValue
        @Column(name = "member_id")
        private Long id;
        private String username;
        private int age;

        @ManyToOne(fetch = FetchType.LAZY) //기본적으로 LAZY 타입(지연로딩) 방식을 사용해야 한다. 필요할 때에만 Fetch 조인으로 가져와야한다.
        @JoinColumn(name = "team_id")
        private Team team;  //지연로딩시 프록시객체는 비어있다.

        public Member(String username) {
            this(username, 0);
        }
        public Member(String username, int age) {
            this(username, age, null);
        }
        public Member(String username, int age, Team team) {
            this.username = username;
            this.age = age;
            if (team != null) {
                changeTeam(team);
            }
        }
        public void changeTeam(Team team) {
            this.team = team;
            team.getMembers().add(this);
        }
    }
    ```

### 3-2. Team 엔티티

    ```
    import lombok.AccessLevel;
    import lombok.Getter;
    import lombok.NoArgsConstructor;
    import lombok.Setter;
    import javax.persistence.*;
    import java.util.ArrayList;
    import java.util.List;
    @Entity
    @Getter @Setter
    @NoArgsConstructor(access = AccessLevel.PROTECTED)
    @ToString(of = {"id", "name"})
    public class Team {
        @Id @GeneratedValue
        @Column(name = "team_id")
        private Long id;
        private String name;

        @OneToMany(mappedBy = "team")  //멤버와 다르게 반대로 OneToMany로 관계방향을 정확히 명시해야 함, 또한 Team은 연관관계의 주인이 아님 members는 읽기만 가능
        List<Member> members = new ArrayList<>();

        public Team(String name) {
            this.name = name;
        }
    }
    ```

### 3-3. 데이터 확인 테스트
    ```
    package study.datajpa.entity;
    import org.junit.jupiter.api.Test;
    import org.springframework.boot.test.context.SpringBootTest;
    import org.springframework.test.annotation.Rollback;
    import org.springframework.test.context.junit4.SpringRunner;
    import org.springframework.transaction.annotation.Transactional;
    import javax.persistence.EntityManager;
    import javax.persistence.PersistenceContext;
    import java.util.List;
    @SpringBootTest
    public class MemberTest {
        @PersistenceContext
        EntityManager em;

        @Test
        @Transactional
        @Rollback(false) //테스트시 데이터를 롤백시키기 때문에 커밋하도록 따로 설정한다.
        public void testEntity() {
            Team teamA = new Team("teamA");
            Team teamB = new Team("teamB");
            em.persist(teamA);
            em.persist(teamB);
            Member member1 = new Member("member1", 10, teamA);
            Member member2 = new Member("member2", 20, teamA);
            Member member3 = new Member("member3", 30, teamB);
            Member member4 = new Member("member4", 40, teamB);
            em.persist(member1);
            em.persist(member2);
            em.persist(member3);
            em.persist(member4);

            //초기화
            em.flush(); //DB에 반영
            em.clear(); //영속성 컨텍스트를 비워주고 DB와 동기화해주어야 정확히 확인이 가능

            //확인
            List<Member> members = em.createQuery("select m from Member m", Member.class).getResultList();
            for (Member member : members) {
                System.out.println("member=" + member);
                System.out.println("-> member.team=" + member.getTeam());
            }
        }
    }
    ```

# 4. 공통 인터페이스 기능

### 4-1. 순수 JPA 기반 리포지토리와 스프링 데이터 기반 리포지토리 비교

* 순수 JPA 기반 리포지토리
> CRUD를 직접 생성해야 한다.

* 스프링 데이터 JPA 기반 MemberRepository
> 위에서 확인했었듯이 스프링 데이터 JPA 인터페이스는 순수 JPA처럼 직접 구현하지 않아도 
> 공통 인터페이스를 통해 구현클래스가 따로 만들어져 바로 사용할 수 있도록 해준다.
> 사용가능한 메소드 형식이 존재한다.
> 주요 메소드
> > - save(S) : 새로운 엔티티는 저장하고 이미 있는 엔티티는 병합한다
> > - delete(T) : : 엔티티 하나를 삭제한다. 내부에서 EntityManager.remove() 호출
> > - findById(ID : : 엔티티 하나를 조회한다. 내부에서 EntityManager.find() 호출
> > - getOne(ID) : : 엔티티를 프록시로 조회한다. 내부에서 EntityManager.getReference() 호출
> > - findAll(…) : 모든 엔티티를 조회한다. 정렬( Sort )이나 페이징( Pageable ) 조건을 파라미터로 제공할 수 있다.

# 5. 쿼리 메소드 기능

### 5-1. 메소드명으로 쿼리생성

* 순수 JPA 방식
순수 JPA를 사용할 때엔 쿼리도 직접 작성해야한다.

    ```
    ...생략
    em.createQuery("select m from Member m where m.username = :username and m.age > :age")
    .setParameter("username", username)
    .setParameter("age", age)
    .getResultList();
    
    ```

* 스프링 데이터 JPA 방식
규칙에 맞게 메소드명만 맞춰주면 서술한내용을 분석해서 JPQL을 생성, 실행한다.

    ```
    public interface MemberRepository extends JpaRepository<Member, Long> {
        List<Member> findByUsernameAndAgeGreaterThan(String username, int age);
    }
    ```
> 규칙에 따라 조회, count, exist, 삭제등 다양한 기능을 수행가능하다.
> 규칙에 맞지않으면 어플리케이션 단에서 에러가 난다. 
> SQL 수행시점이 아닌 어플리케이션 로딩 시점에서 에러가 나는 것은 큰장점

### 5-2. JPA NamedQuery
어노테이션으로 Named 쿼리 정의해서 사용가능
> 잘 안쓰이는 기법으로 동적쿼리는 지원하지 않는다.
> 애플리케이션 실행 시점에 문법 오류를 발견할 수 있음

    ```
    @Entity
    @NamedQuery(
    name="Member.findByUsername",
    query="select m from Member m where m.username = :username")
    public class Member {
    ...
    }
    ```
* JPA 직접 사용해서 Named 쿼리 호출

    ```
    public class MemberRepository {
        public List<Member> findByUsername(String username) {
            ...
            List<Member> resultList =
                em.createNamedQuery("Member.findByUsername", Member.class) //Named 사용하여 쿼리 호출 
                .setParameter("username", username)
                .getResultList();
        }
    } 
    ```

* 스프링 데이터 JPA로 NamedQuery 사용

    ```
    @Query(name = "Member.findByUsername")
    List<Member> findByUsername(@Param("username") String username);
    ```

### 5-3. @Query, 리포지토리 메소드에 쿼리 정의하기
위와같이 NamedQuery를 직접 등록하기 보다는 
아래와 같이 메소드에 쿼리를 직접 정의하는 경우가 많다. 

* 메서드에 JPQL 쿼리 작성
    메소드에 바로 쿼리를 작성할 수 있다. 
    > NamedQuery 쿼리처럼 애플리케이션 실행 시점에 에러가 발생한다.

    ```
    public interface MemberRepository extends JpaRepository<Member, Long> {
        @Query("select m from Member m where m.username= :username and m.age = :age")
        List<Member> findUser(@Param("username") String username, @Param("age") int age);
    }
    ```

* DTO로 직접 조회
사실 엔티티를 직접 조회하는 것은 실제개발에서는 하면 안되는 방식이고
DTO를 따로 만들어 비즈니스로직에 필요한 부분만 가져오는 방법을 사용한다.

    ```
    @Query("select new study.datajpa.dto.MemberDto(m.id, m.username, t.name) from Member m join m.team t")
    List<MemberDto> findMemberDto();
    ```
> DTO의 패키지명까지 모두 써야해서 굉장히 지저분한 모습이다...

DTO는 다음과 같은 생성자가 일치하는 DTO가 필요하다.
```
    import lombok.Data;
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

### 5-4. 파라미터 바인딩
* 이름기반 파라미터 바인딩
위치기반 파라미터 바인딩도 있지만 권장되지않는다.
    ```
    @Query("select m from Member m where m.username = :name")
    Member findMembers(@Param("name") String username);
    ```

* 컬렉션 파라미터 바인딩
in절을 지원한다.
    ```
    @Query("select m from Member m where m.username in :names")
    List<Member> findByNames(@Param("names") List<String> names);
    ```

### 5-5. 반환타입
- 컬렉션
- 단일 엔티티
- Optional<T>
정도가 있고

* 반환된 결과 타입보다 많거나 없으면
    + 컬렉션
     - 결과 없음: 빈 컬렉션 반환
    + 단건 조회
     - 결과 없음: null 반환
     - 결과가 2건 이상: javax.persistence.NonUniqueResultException 예외 발생

# 6. 순수 JPA 페이징과 정렬

# 6-1 JPA 페이징과 정렬 소개
JPA에서는 특수한 파라미터만 넘기면 알아서 페이징한 결과를 반환한다. 

* 페이징과 정렬 파라미터
    + org.springframework.data.domain.Sort : 정렬 기능
    + org.springframework.data.domain.Pageable : 페이징 기능 (내부에 Sort 포함)

* 페이징에서 쓰이는 반환 타입
    + org.springframework.data.domain.Page : 추가 count 쿼리 결과를 포함하는 페이징
    + org.springframework.data.domain.Slice : 추가 count 쿼리 없이 다음 페이지만 확인 가능
    + List<T> :  count 쿼리 없이 결과만 반환

* 사용방식 
    ```
    PageRequest pageRequest = PageRequest.of(0, 3, Sort.by(Sort.Direction.DESC,"username"));
    Page<Member> page = memberRepository.findByAge(10, pageRequest);
    ```

# 6-2. 페이징반환 타입 인터페이스 분석
* Slice 인터페이스
    ```
    public interface Slice<T> extends Streamable<T> {
        int getNumber(); //현재 페이지
        int getSize(); //페이지 크기
        int getNumberOfElements(); //현재 페이지에 나올 데이터 수
        List<T> getContent(); //조회된 데이터
        boolean hasContent(); //조회된 데이터 존재 여부
        Sort getSort(); //정렬 정보
        boolean isFirst(); //현재 페이지가 첫 페이지 인지 여부
        boolean isLast(); //현재 페이지가 마지막 페이지 인지 여부
        boolean hasNext(); //다음 페이지 여부
        boolean hasPrevious(); //이전 페이지 여부
        Pageable getPageable(); //페이지 요청 정보
        Pageable nextPageable(); //다음 페이지 객체
        Pageable previousPageable();//이전 페이지 객체
        <U> Slice<U> map(Function<? super T, ? extends U> converter); //변환기
    }
    ```
* Page 인터페이스
    ```
    public interface Page<T> extends Slice<T> {
        int getTotalPages(); //전체 페이지 수
        long getTotalElements(); //전체 데이터 수
        <U> Page<U> map(Function<? super T, ? extends U> converter); //변환기
    }
    ```
> 보다시피 Slice를 상속받아서 전체 페이지수, 전체 개체수를 추가한 것이 Page 인터페이스이다.


* 페이지를 유지하면서 엔티티를 DTO로 변환하기
결국 우리가 반환하는 타입은 엔티티가 아니라 DTO로 반환해야 한다.

    ```
    Page<Member> page = memberRepository.findByAge(10, pageRequest);
    Page<MemberDto> dtoPage = page.map(m -> new MemberDto());
    ```

# 7. 벌크성 수정 쿼리
벌크 연산은 다량의 수정, 삭제 쿼리를 일컫는다.
* 순수 JPA의 벌크연산
    ```
    public int bulkAgePlus(int age) {
        int resultCount = em.createQuery(
                "update Member m set m.age = m.age + 1" +
                "where m.age >= :age")
            .setParameter("age", age)
            .executeUpdate();
        return resultCount;
    }
    ```
* 스프링 데이터 JPA를 사용한 벌크연산
    ```
    @Modifying(clearAutomatically = true) // clearAutomatically -> 자동으로 벌크연산후 영속성컨텍스트 초기화옵션
    @Query("update Member m set m.age = m.age + 1 where m.age >= :age")
    int bulkAgePlus(@Param("age") int age);
    ```
> 벌크연산은 영속성 컨텍스트와 2차 캐시를 무시하고 다이렉트로 DB에 반영된다.
> 때문에 연산 후에는 영속성컨텍스트와 DB는 값이 상이해지기 때문에 조회시 초기화가 필요하고 
> 가급적이면 컨텍스트에 연산이 쌓이기 전에 먼저 벌크연산을 실행해야 한다.


# 8. @EntityGraph
연관된 엔티티를 한번에 조회하는 방법이다.
대부분 실무자들이 JPA에서 연관관계에 있는 필드는 지연로딩시켜놓기 때문에
따로 가져오고 싶은 연관관계 필드가 있다면 Fetch조인이나 EntityGraph를 사용한다.
    ```
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

# 9. JPA Hint & Lock
JPA 구현체에게 제공하는 힌트

* 쿼리 힌트 사용

    ```
    @QueryHints(value = @QueryHint(name = "org.hibernate.readOnly", value ="true"))
    Member findReadOnlyByUsername(String username);
    ```
> readonly 힌트를 주었기 때문에 반환된 Member는 변경되어도 DB에 반영되지 못한다.

* Lock
트랜잭션의 충돌을 예상하고 데이터에 대한 Lock을 거는 옵션
해당 데이터자원을 Lock하면 해당 자원을 요구하는 다른 트랜잭션이 일시적으로 대기상태가 되어 지연이 발생한다.
지연이 발생하는경우 일정시간 지연시 탈출하는 옵션을 추가로 설정해야한다고 한다.

    ```
    @Lock(LockModeType.PESSIMISTIC_WRITE)
    List<Member> findByUsername(String name);
    ```

# 10. 확장 기능

### 10-1. 사용자 정의 리포지토리 구현
1. 사용자 정의 인터페이스 > 인터페이스 구현체
2. 사용자 정의 인터페이스 상속

* 사용자 정의 인터페이스
사용중인 리포지토리에 상속시킬 인터페이스 생성
    ```
    public interface MemberRepositoryCustom {
        List<Member> findMemberCustom();
    }
    ```

* 사용자 정의 인터페이스 구현 클래스
    ```
    @RequiredArgsConstructor
    public class MemberRepositoryCustomImpl implements MemberRepositoryCustom {
    private final EntityManager em;
        @Override
        public List<Member> findMemberCustom() {
            return em.createQuery("select m from Member m")
                .getResultList();
        }
    }
    ```
> 클래스이름을 사용자 정의 인터페이스 이름 + Impl로 명명해야 함
> > 기존 스프링 데이터 1.x 버전에서는 JpaRepository로 사용중인 리포지토리명에 Impl를 붙여야했지만 위 방법이 추가되었다.

* 사용자 정의 인터페이스 상속
    기존 리포지토리에 상속시켜 기능 추가
    ```
    public interface MemberRepository extends JpaRepository<Member, Long>, MemberRepositoryCustomImpl {
    }
    ```

### 10-2. Auditing
엔티티의 생성과 수정시에 등록수정일, 등록수정자를 자동으로 추적하는 기능을 구현
Auditing 해줄 엔티티를 생성하고 해당 엔티티를 상속하는 방식으로 사용

* 스프링 데이터 Auditing 적용 -등록일, 수정일, 등록자, 수정자

    ```
    @EntityListeners(AuditingEntityListener.class)
    @MappedSuperclass
    public class BaseEntity {
        @CreatedDate
        @Column(updatable = false) //수정시에는 Auditing X
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

* 등록자, 수정자를 처리해주는 AuditorAware 스프링 빈 등록

    ```
    @Bean
    public AuditorAware<String> auditorProvider() {
        return () -> Optional.of(UUID.randomUUID().toString());
    }
    ```

> 등록수정일은 필수지만 등록수정자는 필요없는경우가 많기 때문에
> base로 등록수정일 두고 등록수정자를 상속하는방식을 많이 사용


### 10-3. Web 확장 - 도메인 클래스 컨버터

    ```
    @RestController
    @RequiredArgsConstructor
    public class MemberController {
        private final MemberRepository memberRepository;
        @GetMapping("/members/{id}")
        public String findMember(@PathVariable("id") Member member) { // 엔티티의 아이디로 엔티티 객체를 찾아서 바로 바인딩
            return member.getUsername(); 
        }
    }
    ```

### 10-4. Web 확장 - 페이징과 정렬

* 페이징과 정렬 예제
스프링 JPA에는 파라미터를 Pageable로 받으면 페이징을 하는 구현체가 존재한다.

    ```
    @GetMapping("/members")
    public Page<Member> list(Pageable pageable) {
        Page<Member> page = memberRepository.findAll(pageable); 
        return page;
    }
    ```
 + Pageable 파라미터
    - page: 현재 페이지, 0부터 시작한다.
    - size: 한 페이지에 노출할 데이터 건수
    - sort: 정렬 조건을 정의한다. 예) 정렬 속성,정렬 속성...(ASC | DESC) (default ASC)
    
* 페이징 설정 - 글로벌
    ```
    application.yml

    ...
    spring.data.web.pageable.default-page-size=20 /# 기본 페이지 사이즈/
    spring.data.web.pageable.max-page-size=2000 /# 최대 페이지 사이즈/
    ```

* 페이징 설정 - 개별
@PageableDefault 어노테이션 사용
    ```
    @RequestMapping(value = "/members_page", method = RequestMethod.GET)
    public String list(@PageableDefault(size = 12, sort = “username”, direction = Sort.Direction.DESC) Pageable pageable) {
        ...
    }
    ```

# 11 스프링 데이터 JPA 분석

### 11-1. 스프링 데이터 JPA 구현체 분석

* 공통 인터페이스 구현체 
    - org.springframework.data.jpa.repository.support.SimpleJpaRepository 

    ```
    @Repository //JPA Exception을 Spring Exception으로 변환
    @Transactional(readOnly = true) //기본적으로 트랜잭션으로 묶여있음 + ReadOnly(장점: 플러시를 안 함 )
    public class SimpleJpaRepository<T, ID> ...{
        @Transactional
        public <S extends T> S save(S entity) {
            if (entityInformation.isNew(entity)) { //기본으로 pk가 비워져있으면 새로운 엔티티로 인식
                em.persist(entity);
                return entity;
            } else {
                return em.merge(entity); // 수정되는 엔티티면 병합
            }
        }
        ...
    }
    ```
> merge는 단독으로는 쓰이지않고 트랜잭션 중간에 영속성 컨텍스트에서 잠시 벗어나는 경우나 다른 특수한 경우에만 단독으로 사용됨


* Persistable 구현 
위에서 save했을 때 기본적으로 @Id 필드 값을 통해 새로운 엔티티를 식별하는 것을 알 수 있었다.
새로운 엔티티를 식별하는 방식을 커스텀할 수 있는데
Persistable 인터페이스를 엔티티에 상속받아 구현한다.

    - org.springframework.data.domain.Persistable를 상속

    ```
    public class Item implements Persistable<String> {
       
        ...

        @Override
        public String getId() {
            return id;
        }
        @Override
        public boolean isNew() {
            return createdDate == null;
        }
    }
    ```


# 12. 나머지 기능들 

### 12-1. Specifications (명세)
JPA Creteria를 통해 쿼리의 WHERE절 조건을 쉽게 조합해서 사용할 수 있도록 도와준다. 

* 술어(predicate)
    - 참 또는 거짓으로 평가
    - AND OR 같은 연산자로 조합해서 다양한 검색조건을 쉽게 생성(컴포지트 패턴)

* 명세 기능 사용 방법
    - JpaSpecificationExecutor 인터페이스 상속

    ```
    public interface MemberRepository extends JpaRepository<Member, Long>, JpaSpecificationExecutor<Member> {
    }
    ```
* 명세 기능 사용
    ```
    ...
    Specification<Member> spec = MemberSpec.username("m1").and(MemberSpec.teamName("teamA"));
    List<Member> result = memberRepository.findAll(spec);
    List<Member> result = memberRepository.findAll(spec);
    ```
* MemberSpec 명세 정의 
    ```
    public class MemberSpec {
        public static Specification<Member> teamName(final String teamName) {
            return (Specification<Member>) (root, query, builder) -> {
                if (StringUtils.isEmpty(teamName)) {
                    return null;
                }
                Join<Member, Team> t = root.join("team", JoinType.INNER); //회원과 조인
                return builder.equal(t.get("name"), teamName);
            };
        }
        public static Specification<Member> username(final String username) {
            return (Specification<Member>) (root, query, builder) ->
                builder.equal(root.get("username"), username);
        }
    }
    ```
> JPA Criteria는 사용되지않는다고 한다. 
> 그냥 Querydsl에서 BooleanBuilder를 쓰면 될 것 같다.

### 12-2. Projections
엔티티 > DTO 변환에서 많이 쓰이게 되는 기법이다.

* 인터페이스로 Getter 추상화
    - 게터
    ```
    public interface UsernameOnly {
        String getUsername();
    }
    ```
    - 인터페이스에 추가 
    ```
    public interface MemberRepository ... {
        List<UsernameOnly> findProjectionsByUsername(String username);
    }
    ```
    - 사용
    ```
     //when
    List<UsernameOnly> result = memberRepository.findProjectionsByUsername("m1");
    ```

* SpEL문법도 지원한다.
    ```
    public interface UsernameOnly {
    @Value("#{target.username + ' ' + target.age + ' ' + target.team.name}")
        String getUsername();
    }
    ```
> SQL조회중 계산된게 아니라 나중에 계산되기 때문에 결과가 많을 경우 성능최적화에 문제가 있다고한다.

* 클래스 기반 Projection
생성자의 파라미터 이름으로 매칭

    ```
    public class UsernameOnlyDto {
        private final String username;
        public UsernameOnlyDto(String username) {
            this.username = username;
        }
        public String getUsername() {
            return username;
        }
    }
    ```

* 중첩 구조 처리
연관관계 필드도 Projection 처리가 가능

    ```
    public interface NestedClosedProjection {
        String getUsername();
        TeamInfo getTeam();
        interface TeamInfo { //안에서 다시 추상화해서 Team.name만 뽑아냄
            String getName();
        }
    }
    ```
> 복잡한 쿼리는 처리하기 힘듬

### 12-3. Native Query
네이티브 쿼리는 가급적이면 사용을 하지않는게 좋다고 한다.
네이티브 쿼리도 Projections을 통해 반환이 가능하다.
> JPQL처럼 애플리케이션 로딩 시점에 문법 확인 불가

* Projection 활용 예제 
    ```
    Query(value = "SELECT m.member_id as id, m.username, t.name as teamName " +
        "FROM member m left join team t",
        countQuery = "SELECT count(*) from member",
        nativeQuery = true)
    Page<MemberProjection> findByNativeProjection(Pageable pageable);
    ```

* 동적 네이티브 쿼리
기본적으로 스프링 데이터 JPA의 네이티브 쿼리는 동적쿼리를 지원하지않는다..
때문에  JdbcTemplate, myBatis, jooq 같은 외부라이브러리를 통해 동적쿼리를 구현하는 방법이 있다.
    - 하이버네이트 기능 사용 예제
    ```
    String sql = "select m.username as username from member m";
    List<MemberDto> result = em.createNativeQuery(sql) //네이티브 쿼리
        .setFirstResult(0)
        .setMaxResults(10)
        .unwrap(NativeQuery.class)
        .addScalar("username")
        .setResultTransformer(Transformers.aliasToBean(MemberDto.class))
        .getResultList();
    ```

끝
====
