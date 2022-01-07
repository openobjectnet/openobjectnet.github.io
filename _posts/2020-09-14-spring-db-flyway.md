---
title:  "Flyway 를 활용한 DB마이그레이션 feat.spring boot에 적용"
search: false
categories: 
  - DB
toc: true  
last_modified_at: 2020-09-14T08:06:00-05:00
tags:
  - Flyway
  - DB
  - Spring Boot
---

Flyway 를 활용하여 DB마이그레이션 방법과 Flyway를 spring boot에 적용방법을 공유합니다.

보통 프로젝트 소스코드의 경우 형상관리(SVN/GIT)를 통해서 변경이력 관리를 하고 있다.  
하지만 DB의 경우 schema변경을 관리를 마땅히 하지 못하고 있다.  
또한 예를들어 운영배포를 할때, 소스는 적용했으나 DB가 적용이 안되었거나 혹은 필수로 필요한 코드값을 Insert 못하고
소스를 적용하여 종종 오류가 발생하곤 한다.  

이런 schema에 대한 적용 및 이력관리를 Flyway를 통해서 가능합니다.  

**spring 에 flyway 적용은 2번부터 참고.

**커맨드라인을 통한 DB마이그레이션**  
먼저 커맨드 라인을 통해서 Flyway를 테스트 해봅니다.  

## 1-1. Flyway 설치
Flyway설치는 커맨드라인에서 사용할 경우만 필요하고, Spring에서 사용할때는 설치를 안해도 됩니다.(maven dependency로 라이브러리 주입)  
Flyway 다운로드 : https://flywaydb.org/download/


```yaml
cd C:\dev\flyway-6.5.5
```

**Note:** `cd C:\dev\flyway-6.5.5` flyway를 다운받은 해당 경로로 이동합니다.
{: .notice--info}

## 1-2. Flyway DB설정
`C:\dev\flyway-6.5.5\conf\flyway.conf` 파일의 아래의 키값에 적용할 DB정보로 수정합니다.
```yaml
flyway.url=jdbc:mysql://localhost:3306/D_TEST_TEMP?characterEncoding=UTF-8&serverTimezone=Asia/Seoul
flyway.user=root
flyway.password=rootpw
```

## 1-3. Flyway catete table migration
`C:\dev\flyway-6.5.5\sql` 경로에 마이그레이션 할 파일을 생성합니다.
생성할 파일은 `V1__Create_user_table.sql` 로 생성합니다.  

파일명 syntax 는 prefix + version + seperator + description 로 작성합니다.   
prefix: `V` 로 시작  
version: `1_1` 와 같이 숫자와 언더스코어로 버젼정보를 가집니다  
seperator: `__` 더블 언더스코어로 구분  
description: 설명  

```yaml
create table TB_FLYWAY_USER (
    USER_SEQ bigint(11) NOT NULL AUTO_INCREMENT,
    USER_NAME varchar(50) NOT NULL,
    USER_PW varchar(100) NOT NULL,
    PRIMARY KEY (USER_SEQ)
)ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

## 1-4. Flyway 마이그레이션 실행
Flyway가 설치된 경로가 가서 명령어를 실행합니다.
```yaml
cd C:\dev\flyway-6.5.5
flyway migrate
```
실행결과
```yaml
C:\dev\flyway-6.5.5>cd C:\dev\flyway-6.5.5

C:\dev\flyway-6.5.5>flyway migrate
Flyway Community Edition 6.5.5 by Redgate
Database: jdbc:mysql://localhost:3306/D_TEST_TEMP (MySQL 5.7)
Successfully validated 1 migration (execution time 00:00.050s)
Creating Schema History table `D_TEST_TEMP`.`flyway_schema_history` ...
Current version of schema `D_TEST_TEMP`: << Empty Schema >>
Migrating schema `D_TEST_TEMP` to version 1.1 - Create FlywayUser table
Successfully applied 1 migration to schema `D_TEST_TEMP` (execution time 00:00.215s)
```
{% capture databaseImg %}
![Foo]({{ "/assets/images/springDbFlywayPost/springDbFlyway1.png" | relative_url }})
{% endcapture %}
{% capture flywayData %}
![Foo]({{ "/assets/images/springDbFlywayPost/springDbFlyway2.png" | relative_url }})
{% endcapture %}

<figure>
  {{ databaseImg | markdownify | remove: "<p>" | remove: "</p>" }}
  <figcaption>생성된 데이터베이스. flyway_schema_history 테이블에 이력정보가 쌓입니다</figcaption>
</figure>
<figure>
  {{ flywayData | markdownify | remove: "<p>" | remove: "</p>" }}
  <figcaption>flyway_schema_history 테이블의 이력정보</figcaption>
</figure>


**Spring boot에 flyway 적용** 
프로젝트 환경 : spring boot + maven

## 2-1. flyway 라이브러리 추가 및 설정
`pom.xml` 에 flyway 라이브러리를 추가합니다.  
```yaml
		<dependency>
		    <groupId>org.flywaydb</groupId>
		    <artifactId>flyway-core</artifactId>
		</dependency>
```

`application.yaml` or `application.properties` 에 flyway 에서 사용할 db정보를 추가합니다.  
```yaml
flyway:
  user: "root"
  password: "rootpw"
  url: "jdbc:mysql://localhost:3306/D_TEST_TEMP?characterEncoding=UTF-8&serverTimezone=Asia/Seoul"
  locations: "classpath:db/migration"
```
**Note:** `locations` 은 flyway migration 파일 경로. `ex. V1_1__Create_FlywayUser_table.sql`
{: .notice--info}

## 2-2 마이그레이션 파일 추가
`locations: "classpath:db/migration"` 경로에 migration 파일을 추가.  
migration 파일명 규칙은 `1-3. Flyway catete table migration` 을 참고.  

## 2-3 마이크레이션 적용 및 테스트
서버를 start 시켜주면 자동으로 migration 경로에 있는 파일을 읽어서 migration을 진행합니다.  

{% capture flywayPackageImg %}
![Foo]({{ "/assets/images/springDbFlywayPost/springDbFlyway3.png" | relative_url }})
{% endcapture %}
<figure>
  {{ flywayPackageImg | markdownify | remove: "<p>" | remove: "</p>" }}
  <figcaption>패키지구조</figcaption>
</figure>

`V1_1__Create_FlywayUser_table.sql` 파일
```yaml
create table TB_FLYWAY_USER (
    USER_SEQ bigint(11) NOT NULL AUTO_INCREMENT,
    USER_NAME varchar(50) NOT NULL,
    USER_PW varchar(100) NOT NULL,
    PRIMARY KEY (USER_SEQ)
)ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

`V1_2__Add_FlywayUser.sql` 파일
```yaml
insert into TB_FLYWAY_USER (USER_NAME, USER_PW) values ('SeHoon', '1212');
insert into TB_FLYWAY_USER (USER_NAME, USER_PW) values ('Suwong', '2323');
insert into TB_FLYWAY_USER (USER_NAME, USER_PW) values ('Mkie', '3434');
```

실행결과  

cosole 로그
```yaml
INFO  20-09-15 11:23:46 [DatabaseFactory:49]                     - Database: jdbc:mysql://localhost:3306/D_TEST_TEMP (MySQL 5.7)
INFO  20-09-15 11:23:46 [DbValidate:49]                          - Successfully validated 2 migrations (execution time 00:00.054s)
INFO  20-09-15 11:23:46 [JdbcTableSchemaHistory:49]              - Creating Schema History table `D_TEST_TEMP`.`flyway_schema_history` ...
INFO  20-09-15 11:23:46 [DbMigrate:49]                           - Current version of schema `D_TEST_TEMP`: << Empty Schema >>
INFO  20-09-15 11:23:46 [DbMigrate:49]                           - Migrating schema `D_TEST_TEMP` to version 1.1 - Create FlywayUser table
INFO  20-09-15 11:23:46 [DbMigrate:49]                           - Migrating schema `D_TEST_TEMP` to version 1.2 - Add people
INFO  20-09-15 11:23:46 [DbMigrate:49]                           - Successfully applied 2 migrations to schema `D_TEST_TEMP` (execution time 00:00.272s)
```


{% capture flywayHistResult1 %}
![Foo]({{ "/assets/images/springDbFlywayPost/springDbFlyway4.png" | relative_url }})
{% endcapture %}
<figure>
  {{ flywayHistResult1 | markdownify | remove: "<p>" | remove: "</p>" }}
  <figcaption>flyway이력</figcaption>
</figure>

{% capture flywayHistResult2 %}
![Foo]({{ "/assets/images/springDbFlywayPost/springDbFlyway5.png" | relative_url }})
{% endcapture %}
<figure>
  {{ flywayHistResult2 | markdownify | remove: "<p>" | remove: "</p>" }}
  <figcaption>migration 된 테이블</figcaption>
</figure>
