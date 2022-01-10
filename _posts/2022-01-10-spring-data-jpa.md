---
title:  "실전! 스프링 데이터 JPA 정리"
search: false
categories: 
  - JPA
toc: true  
last_modified_at: 2022-01-10T08:06:00-05:00
tags:
  - JPA
  - Spring Boot
author: 곽지용
---

# Intro 
안녕하세요? 곽지용 사원입니다.
김영한씨의 '실전! 스프링 데이터 JPA' 강의를 듣고나서
꼭 필요했던 부분이나 알아두면 좋은 부분, 예전에 JPA를 사용하면서 느꼈던 점을 정리해보았습니다.

> [참고]
> 강의는 MacOS, IntelliJ 환경이지만
> 글작성자는 Windows, VSCode 환경에서 따라해보았습니다.
>> https://www.inflearn.com/course/스프링-데이터-JPA-실전



# 0. 강의에 들어가기 전에
해당 강의는 실전편 강의라서 JPA에 대한 기본적인 설명은 간략하게 끝내거나 코드로 대체하는 식입니다.
기본편 강의를 먼저 듣고 시작하는 것을 추천드립니다.

# 1. 프로젝트 환경설정
현재 스프링 부트 버전과 강의시점의 버전이 맞지않아 라이브러리 버전이 상이할 수 있습니다.
p6spy 라이브러리 버전만 알맞게 변경해주시면 될 것 같습니다. 
> 단순히 SQL로그를 위한 라이브러리라서 알맞은 버전을 찾지못한다면 안써도 그만이긴합니다.

* 스프링 부트 스타터(https://start.spring.io/) 에서 간단하게 생성
    - SpringBootVersion: 2.2.1        //현재 설정 불가  *2022-01 기준 2.6.2 버전이 최신
    - groupId: study
    - artifactId: data-jpa
    - dependency : Spring Web, jpa, h2, lombok

* Gradle 설정
IntelliJ는 들어가자마자 자동으로 Gradle Dependency를 스캔하는데
VSCODE의 경우 안된다면 Gradle 확장팩 플러그인을 설치 후에 따로 빌드해야 합니다.

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

* 롬복 설정
아래는 IntelliJ의 롬복 설정방법입니다.
    ```
    1. Preferences plugin lombok 검색 실행 (재시작)
    2. Preferences Annotation Processors 검색 Enable annotation processing 체크 (재시작)
    3. 임의의 테스트 클래스를 만들고 @Getter, @Setter 확인
    ```
> 아마 이클립스의 경우 따로 library에 있는 롬복을 실행시켜서 init 파일에 설정을 추가해야 했습니다.
> VSCode의 경우 Lombok Annotations Support for VS Code 플러그인을 설치했습니다.

# 2. JPA 기본작성법 설명, 순수 JPA 리포지토리와 Spring Data 리포지토리의 비교
다음은 JPA의 기본적인 엔티티, 리포지토리 작성법과
순수 JPA 리포지토리와 Spring Data 리포지토리의 차이도 설명해보려고 합니다.

### 2-1. JPA 기본구조

* 회원 엔티티
레거시 코드에서도 계속 쓰였던 DTO 클래스와 다를바 없는 형식입니다.

    ```java
    @Entity
    @Getter @Setter //롬복에서 게터세터를 생성해준다.
    public class Member {
        @Id @GeneratedValue //@GeneratedValue 어노테이션은 AutoIncrement해주는 옵션
        private Long id;
        private String username;
        ...
    }
    ```

* 회원 JPA 리포지토리
순수 JPA 리포지토리 형식으로 직접 구현해서 영속성컨텍스트를 사용하는 코드입니다.

    ```java
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

* 스프링 데이터 JPA 리포지토리

    ```java
    public interface MemberRepository extends JpaRepository<Member, Long> {
    }
    ```
> 아무것도 없는 인터페이스의 모습 ... 
> 그러나 이 리포지토리를 바로 사용가능합니다.

* 스프링 데이터 JPA 기반 테스트

    ```java
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
> 이전의 MemberRepository 인터페이스에 따로 구현해주지 않았지만 이미 사용가능한 모습을 볼 수 있습니다.

### 2-2. 순수 JPA 기반 리포지토리와 스프링 데이터 기반 리포지토리 비교

* 순수 JPA 기반 리포지토리
레거시 방식의 라이브러리보다는 간결해졌지만 CRUD를 직접 구현한다.

* 스프링 데이터 JPA 기반 MemberRepository
직접 구현하지 않아도 공통 인터페이스를 통해 구현클래스가 따로 만들어져 바로 사용할 수 있도록 해준다.
특정 메소드형식으로 조합하면 그 또한 사용가능하기 때문에 
미래에 내가 만들고싶은 메소드가 이미 만들어져있는 것과 다름 없습니다.
덕분에 생산성이 비약적으로 늘어납니다.

> [주요 메소드]
> - save(S) : 새로운 엔티티는 저장하고 이미 있는 엔티티는 병합한다
> - delete(T) : : 엔티티 하나를 삭제한다. 내부에서 EntityManager.remove() 호출
> - findById(ID : : 엔티티 하나를 조회한다. 내부에서 EntityManager.find() 호출
> - getOne(ID) : : 엔티티를 프록시로 조회한다. 내부에서 EntityManager.getReference() 호출
> - findAll(…) : 모든 엔티티를 조회한다. 정렬( Sort )이나 페이징( Pageable ) 조건을 파라미터로 제공할 수 있다.


# 3. FetchType
연관관계 매핑의 Fetch 타입은 즉시로딩(Eager) 방식과 지연로딩(Lazy) 방식이 있습니다.
Default Type은 Eager로 설정되어있어 이를 Lazy타입으로 설정해두어야합니다.
Lazy 타입으로 설정시 빈 프록시 객체를 만들고 해당하는 연관관계 필드에서 데이터를 조회하려고하면
그때 다시 쿼리를 날려서 원하는 정보를 조회합니다.
> 이런 경우 N+1 쿼리 문제가 발생합니다.

### 3-1. Member,Team의 1:N관계 코드

    ```java
    @Entity
    @Getter @Setter  
    @NoArgsConstructor(access = AccessLevel.PROTECTED) 
    @ToString(of = {"id", "username", "age"}) 
    public class Member {
        @Id
        @GeneratedValue
        @Column(name = "member_id")
        private Long id;
        private String username;
        private int age;

        @ManyToOne(fetch = FetchType.LAZY) //기본적으로 LAZY 타입(지연로딩) 방식을 사용해야 한다.
        @JoinColumn(name = "team_id")
        private Team team;  //지연로딩시 team에는 빈 프록시객체가 담겨있다. 
    }
    ```
> 만약에 연관관계필드도 모두 조회되길 원한다면
> 타입으로 Eager로 바꾸는게 아니라
> Fetch 조인으로 원하는 연관관계의 정보만 추가로 가져옵니다.

### 3-2. FetchJoin
일반적인 조인에서는 메인이 되는 테이블만 영속 시켜 연관관계 필드는 지연로딩되지만
패치조인의 경우 연관관계 필드도 함께 영속상태로 만들어 바로 조회되도록합니다. 

    ```java
    @Query("SELECT distinct t FROM Team t join fetch t.members")
    public List<Team> findAllWithMemberUsingFetchJoin();
    ```
> 일반 조인도 연관관계필드를 조회하는 것이 아닌 
> 연관관계만 이용할 것이라면 사용해야합니다.
> ex) Team 이름이 '웹서버개발'로 시작하는 팀의 멤버만 조회하시오.

# 4. @Transactional
@Transactional은 JPA에서 DB 트랜잭션을 구현해주는 어노테이션입니다.
헷갈릴 때가 은근히 많아서 좀 더 설명드리겠습니다.

기본적으로 클래스 뿐 아니라 3가지 경우에 사용가능합니다.
1. 클래스
2. 인터페이스
3. 메소드

트랜잭션, 말그대로 DB 상태변경 작업 단위를 구현해줍니다.
@Transactional 안의 영속성 컨텍스트를 상태관리해서 변경사항이 있을 경우 쿼리를 쌓아놓고 마지막에 DB에 flush() 해줍니다.
어노테이션이 달린 작업에서 문제가 생겼을시 해당 작업의 롤백도 가능하게 해줍니다.

* 순수 JPA 리포지토리 테스트 코드 

    ```java
    @SpringBootTest
    public class MemberTest {
        @PersistenceContext
        EntityManager em;

        @Test
        @Transactional(rollbackFor = Exception.class) // (rollbackFor = Exception.class)를 붙여야 예외상황시 롤백
        @Rollback(false)
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
> @Transactional이 알아서 디비에 반영해 줄 것 같았지만 작업이 끝난뒤에 반영되기 때문에
> 작업 도중엔 실제 DB와 영속성컨텍스트는 상태가 다르다.
> 때문에 작업 도중에 반영된 값을 조회하고 싶다면 flush()를 통해 DB반영과 clear()를 통한 초기화가 이루어져야 한다.

* 스프링 데이터 JPA 리포지토리 테스트 코드

    ```java
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
            Member findMember = memberRepository.findById(savedMember.getId()).get();
            Assertions.assertThat(findMember.getId()).isEqualTo(member.getId());
        }
    }
    ```
> save() 뒤에 바로 조회했는데 조회가 정상적으로 됩니다. 
> 쉽게 생각했을 땐 save() 구현체에 flush()와 clear()가 있을 것 같습니다.


* 스프링 데이터 JPA 공통 인터페이스 구현체
    - org.springframework.data.jpa.repository.support.SimpleJpaRepository 
위 인터페이스 구현체를 까보면 save()에 flush()와 clear()는 구현되어있지 않습니다.
대신, @Transactional이 달려있어 작업이 완료되면 flush() 하게 됩니다.
때문에 Spring Data JPA를 사용할 때엔 flush를 따로 신경쓰지않고 DB에 바로 반영할 수 있습니다.

    ```java
    @Repository //JPA Exception을 Spring Exception으로 변환
    @Transactional(readOnly = true) //여기서 readOnly는 상태추적하지않도록 하는 것
    public class SimpleJpaRepository<T, ID> ...{
        @Transactional //save는 기본적으로 트랜잭션으로 묶여있어 상태추적됨
        public <S extends T> S save(S entity) {
            if (entityInformation.isNew(entity)) { 
                em.persist(entity);
                return entity;
            } else {
                return em.merge(entity);
            }
        }
        ...
    }
    ```

* @Transactional(readOnly = true) 옵션은 왜 쓰는가??
readOnly = true 옵션은 더티체킹을 하지않아 성능향상을 꾀하는 옵션입니다.

얼핏보면 그렇구나 싶은데 ...
자세히 생각해보면 트랜잭션과 ReadOnly가 공존하고있는 모습이 이상합니다.
내가 아는 트랜잭션은 더티 체킹을 하기위해 있는데
readOnly는 더티체킹을 안한다고합니다...?

> 숟가락을 만들었지만 음식은 먹지못하게 하면...
> 이 숟가락은 왜 만든거지...?

* 제가 찾은 (readOnly=true)를 쓰는 이유는 다음과 같습니다.

1. *데이터 일관성 보장*
트랜잭션이 없는 경우 조회시 다른 트랜잭션에 의해 실시간으로 영향을 받은 데이터를 조회하지만
트랜잭션이 시작할 때 스냅샷을 찍어 스냅샷에서 조회하기 때문에 일관성을 보장합니다.
> 사실 이건 트랜잭션의 이점입니다.
2. 트랜잭션 ID를 부여하지않음
트랜잭션마다 부여되는 ID를 부여하지않아 오버헤드가 발생하지않습니다.
한마디로 진짜로 일해야하는 트랜잭션들 사이에 끼여서 작업을 방해 하지않는다는 말입니다.
3. 스냅샷의 수가 적어진다.
트랜잭션 안에서는 데이터일관성을 위해 스냅샷을 찍어서 사용합니다.
읽기쓰기 모드보다는 읽기 모드에서 생성되는 스냅샷의 수가 더 적습니다.
4. 더티체킹을 하지않는다.
상태가 변경되었는지 추적할 필요가 없다면 안하는게 좋겠지요?

> 사실 이것들 이외에 flush_manual 모드니 dbms마다 isolation이 다르게 설정된다느니 말이 많은데
> 내용도 많고 너무 깊은 내용이라 저도 설명을 못하니 ... 넘어가도록 하겠습니다.

***결론은 조회시에는 (readOnly=true)를 붙여야한다는 것입니다.***


# 5. 대체 클래스로 반환하기
기본적인 스프링 데이터 JPA는 반환타입을 엔티티를 기반으로 사용이 가능합니다.
엔티티 그대로 사용하면 되지않나 생각이 들지만 ?
사실 엔티티 안의 모든 데이터가 필요한 경우는 많지않고 민감한 데이터도 저장되어있어 조회할 일이 없는 필드가 많습니다.
조회용으로 사용할 DTO를 만들고 필요한 데이터만 담아서 전달하는 비즈니스 로직을 구현해야 합니다.


### 5-1. @Query로 DTO를 반환하는 쿼리메소드 작성하기
* 메서드에 JPQL 쿼리 작성
    ```java
    public interface MemberRepository extends JpaRepository<Member, Long> {
        @Query("select m from Member m where m.username= :username and m.age = :age")
        List<Member> findUser(@Param("username") String username, @Param("age") int age);
    }
    ```

* DTO로 직접 조회
    ```java
    @Query("select new study.datajpa.dto.MemberDto(m.id, m.username, t.name) from Member m join m.team t")
    List<MemberDto> findMemberDto();
    ```
> DTO의 패키지명까지 모두 써야해서 굉장히 지저분한 모습이다...

DTO는 다음과 같은 생성자가 일치하는 DTO가 필요하다.

    ```java
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


### 5-2. Projections

* 인터페이스로 Getter 추상화
    - 게터

    ```java
    public interface UsernameOnly {
        String getUsername();
    }
    ```
    - 인터페이스에 추가 

    ```java
    public interface MemberRepository ... {
        List<UsernameOnly> findProjectionsByUsername(String username);
    }
    ```
    - 사용

    ```java
     //when
    List<UsernameOnly> result = memberRepository.findProjectionsByUsername("m1");
    ```

* SpEL문법 지원

    ```java
    public interface UsernameOnly {
        @Value("#{target.username + ' ' + target.age + ' ' + target.team.name}")
        String getUsername();
    }
    ```
> 위 문법을 사용하면 SQL조회중 계산되는 것이 아니라 나중에 리스트를 순회하면서 계산되기 때문에 
> 결과가 많을 경우 성능최적화에 문제가 있습니다.

* 클래스 기반 Projection
생성자의 파라미터 이름으로 매칭

    ```java
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

    ```java
    public interface NestedClosedProjection {
        String getUsername();
        TeamInfo getTeam();
        interface TeamInfo { //안에서 다시 추상화해서 Team.name만 뽑아냄
            String getName();
        }
    }
    ```
> 복잡한 쿼리는 처리하기 힘듬

# 6. Native Query
JPA에서도 네이티브 쿼리를 사용할 수 있습니다.
네이티브 쿼리도 Projections을 통해 반환이 가능합니다.
사실 날짜관련 내부함수를 사용하거나 복잡한 연관관계의 쿼리는 네이티브 쿼리를 사용을 했었습니다.
Querydsl를 사용하면 네이티브 쿼리를 안써도 대부분 해결됩니다.

> JPQL와는 다르게 애플리케이션 로딩 시점에 문법오류를 확인 하지못합니다.

* Projection 활용 예제 

    ```java
    Query(value = "SELECT m.member_id as id, m.username, t.name as teamName " +
        "FROM member m left join team t",
        countQuery = "SELECT count(*) from member",
        nativeQuery = true)
    Page<MemberProjection> findByNativeProjection(Pageable pageable);
    ```

* 동적 네이티브 쿼리
기본적으로 스프링 데이터 JPA의 네이티브 쿼리는 동적쿼리를 지원하지않습니다.
때문에  JdbcTemplate, myBatis, jooq 같은 외부라이브러리를 통해 동적쿼리를 구현하는 방법이 있습니다.
    - 하이버네이트 기능 사용 예제

    ```java
    String sql = "select m.username as username from member m";
    List<MemberDto> result = em.createNativeQuery(sql) //네이티브 쿼리
        .setFirstResult(0)
        .setMaxResults(10)
        .unwrap(NativeQuery.class)
        .addScalar("username")
        .setResultTransformer(Transformers.aliasToBean(MemberDto.class))
        .getResultList();
    ```


# 7. Auditing
Auditing은 반복작업을 줄여주는 숨은 공신입니다.
엔티티를 생성하다보면 필수적으로 등록수정일이나 등록수정자를 반복적으로 추가하곤 합니다.
엔티티의 생성과 수정시에 등록수정일, 등록수정자를 자동으로 추적하는 기능을 구현해줍니다.
Auditing 해줄 엔티티를 생성하고 해당 엔티티를 상속하는 방식으로 구현합니다.

* 스프링 데이터 Auditing 적용 -등록일, 수정일, 등록자, 수정자

    ```java
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

    ```java
    @Bean
    public AuditorAware<String> auditorProvider() {
        return () -> Optional.of(UUID.randomUUID().toString()); //사용자 식별값을 넣으면 됩니다.
    }
    ```

> 등록수정일은 필수지만 등록수정자는 필요없는경우가 많기 때문에
> base class로 등록수정일을 생성해서 이를 상속하는방식을 많이 사용합니다.