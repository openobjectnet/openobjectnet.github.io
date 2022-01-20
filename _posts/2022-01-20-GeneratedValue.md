---
title:  "GenerateType.AUTO MYSQL에서 적용 시 주의 할 점"
search: false
categories: 
  - JPA
toc: true  
last_modified_at: 2022-01-20T10:06:00-05:00
tags:
  - JPA
  - Spring Boot
author: 이효진
---

# GenerateType.AUTO

스프링부트와 하이버네이트
---

- **Hibernate 5.0부터는 MYSQL의 AUTO는 IDENTITY가 아닌 TABLE을 기본 시퀀스 전략으로 선택**
- Spring Boot 1.5x -> Hibernate 5.0x 버전 사용
- Spring Boot 2.0x -> Hibernate 5.2x  버전 사용
- 스프링 부트는 Hibernate의 ID생성 전략을 따를지 말지 결정하는 useNewIdGeneratorMappings 설정이 존재함

    *  spring boot 1.5 : 기본값 false
    *  spring boot 2.0 : 기본값 true
- GenerationType.TABLE : Table sequence 테이블을 두고 모든 테이블의 id를 한 테이블에서 관리

<br>

예시
---

db설정

```yml
  datasource:
    url: jdbc:mysql://localhost:3306/board
    username: root
    password: 1234
    driver-class-name: com.mysql.cj.jdbc.Driver
```
db는 mysql로 설정하였다.

```java
@Entity
public class Board {
	
	@Id @GeneratedValue(strategy = GenerationType.AUTO)
	private Long id;
	private String title;
	private String content;

```
테이블 생성
```java
    create table board (
       id bigint not null,
        content varchar(255),
        title varchar(255),
        primary key (id)
    ) engine=InnoDB
2022-01-20 10:11:25.162 DEBUG 26396 --- [  restartedMain] org.hibernate.SQL                        : 
    
    create table hibernate_sequence (
       next_val bigint
    ) engine=InnoDB
2022-01-20 10:11:25.182 DEBUG 26396 --- [  restartedMain] org.hibernate.SQL                        : 
    
    insert into hibernate_sequence values ( 1 )
```

- board는 AUTO전략을 사용하므로 시퀀스 테이블이 생성된다.
  
```java
@Test
	void boardTest() {
		boardRepository.save(new Board("title1","content1"));
		boardRepository.save(new Board("title2","content2"));
	}
```
결과
```java
Hibernate: 
    select
        next_val as id_val 
    from
        hibernate_sequence for update
            
2022-01-20 11:09:13.425 DEBUG 4076 --- [           main] org.hibernate.SQL                        : 
    update
        hibernate_sequence 
    set
        next_val= ? 
    where
        next_val=?

Hibernate: 
    insert 
    into
        board
        (content, title, id) 
    values
        (?, ?, ?)
```

- 위와같이 sequence table에서 id를 받아와서 insert를 실행한다.
- save 실행 시 id조회해 와서 영속성 컨텍스트에 저장한다.
- 테이블의 id생성을 auto_increment로 설정해도 테이블에서 조회한다.

해결방법 
---
1. 하이버네이트의 id생성방식을 따르지 않도록 use-new-id-generator-mappings를 false로 지정한다.
```yml
  jpa:
    hibernate:
      ddl-auto: create
      use-new-id-generator-mappings: true
```
2. generateType.IDENTITY로 설정해준다.

```java
@Entity
public class Board {
	
	@Id @GeneratedValue(strategy = GenerationType.IDENTITY)
	private Long id;
	private String title;
	private String content;
```

use-new-id-generator-mapping : false 결과
---
```
Hibernate: 
    insert 
    into
        board
        (content, title) 
    values
        (?, ?)

Hibernate: 
    insert 
    into
        board
        (content, title) 
    values
        (?, ?)
```
아까와는 달리 sequence테이블을 조회하지 않는다.
