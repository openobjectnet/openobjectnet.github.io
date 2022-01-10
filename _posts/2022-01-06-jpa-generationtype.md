---
title:  "@GeneratedValue(strategy = GenerationType.IDENTITY) 기본키 조회"
search: false
categories: 
  - JPA
toc: true  
last_modified_at: 2022-01-06T08:06:00-05:00
tags:
  - JPA
  - Spring Boot
author: 김가영
---

JPA에서 Entity 기본키 매핑 방법은 직접할당과 자동할당 방법이 있습니다.

## 직접할당
@Id annotation 만을 사용하여 기본키를 직접 할당해주는 방법

## 자동할당 - 방법 4가지
기본키를 자동으로 생성할 떄에는 @Id와 @GeneratedValue annotation을 함께 사용

> 자동할당 시 아래 4가지 전략이 있다.

|속성값 |설명 |대표 DBMS |
|---|---|--- |
|GenerationType.IDENTITY | 기본키 생성을 데이터베이스에 위임 | MySQL |
|GenerationType.SEQUENCE | 시퀀스 사용, @SequenceGenerator 필요 | ORACLE |
|GenerationType.TABLE | 키 생성용 테이블 사용, @TableGenerator 필요 | 모든 DBMS |
|GenerationType.AUTO | 데이터베이스 방언에 따라 자동 지정(기본값) | |

> 영속성 컨텍스트

{% capture persistenceContextImg %}
![Foo]({{ "/assets/images/generationTypePost/entityManager.png" |relative_url }})
{% endcapture %}

<figure>
    {{ persistenceContextImg | markdownify | remove: "<p>" | remove: "</p>" }}
</figure>

- 영속성 컨텍스트로 관리하려면 무조건 PK 값이 필요하다.
- JPA는 entityManager.persist()을 호출함으로써 영속성 컨텍스트의 쓰기 지연 SQL 저장소에 insert 쿼리를 저장
- transaction.commit()이 호출된 후, DB에 insert 쿼리를 날린다.


위의 내용에 근거하면 auto_increment로 생성된 값은 직접 DB에 인서트 되고 나서야 알 수 있다.

**그럼 commit 하기 전에는 자동생성 된 기본키 값은 사용하지 못하는 걸까??**

결과는 <mark style='background-color: #ffdce0'>사용할 수 있다.</mark>

아래 예시를 보자.

### Product Entity

```java
@Entity
@Getter @Setter
@NoArgsConstructor
public class Product {
	
	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private Long productId;			// 상품ID
	
	private String productName; 	// 상품명
	
	private int price;				// 가격		
	
	public Product(String productName, int price) {
		this.productName = productName;
		this.price = price;
	}
}
```

### 실행 코드

```java
transaction.begin();

Product a = new Product("A", 1000);
Product b = new Product("B", 2000);

System.out.println("///////////////////");
entityManager.persist(a);
System.out.println("a product id : " + a.getProductId());
entityManager.persist(b);
System.out.println("b product id : " + b.getProductId());
System.out.println("///////////////////");

transaction.commit();
```

### 실행 화면

```
///////////////////
Hibernate: 
    insert 
        into
            Product
            (productId, price, productName) 
        values
            (null, ?, ?)
a product id : 1
Hibernate: 
    insert 
        into
            Product
            (productId, price, productName) 
        values
            (null, ?, ?)
b product id : 2
///////////////////
```

정상적으로 productId 값이 출력되었다.

### 결론

@GeneratedValue(strategy = GenerationType.IDENTITY) 사용하였을 떄,

- transaction.commit()을 호출할 때가 아니라 entityManager.persist() 가 호출될 때 DB에 insert 쿼리를 날린다.
- JPA 내부에서 insert 쿼리 실행 후 바로 생성된 productId 값을 리턴 받는다.
- productId를 PK로 영속성 컨텍스트에 저장

### 자동할당 정리

> IDENTITY

```java
@Entity
public class PkEx() {
    
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;
    
}
```

JPA 보통 영속성 컨텐스트에서 객체를 관리하다가 commit이 호출되는 시점에 쿼리문을 실행하게 된다. 하지만 IDENTITY 전략에서는 EntityManager.persist()를 하는 시점에 insert SQL을 실행하여 데이터베이스에 식별자를 조회해온다.

그 이유는 영속성 컨텍스으는 1차 캐시에 PK와 객체를 가지고 관리를 하는데 기본키를 데이터베이스에 위임했기 때문에 EntityManager.persist() 호출 하더라도 데이터베이스에 값을 넣기 전까지 기본키를 모르고 있기 때문에 관리자 되지 않기 떄문이다.

따라서 특별한 경우로 IDENTITY 전략에서는 EntityManager.persist()를 하는 시점에 insert SQL을 실행하여 데이터베이스에서 식별자를 조회하여 영속성 컨텍스트 1차캐시에 값을 넣어주기 때문에 관리자 가능해진다.

> SEQUENCE

```java
@Entity
@SequenceGenerator(
    name = "USER_PK_GENERATOR",
    sequenceName = "USER_PK_SEQ",
    initailValue = 1,
    allocationSize = 50
    )
public class PkEx() {
    
    @Id
    @GeneratedValue(strategy = GenerationType.SEQUENCE,
                generator="USER_PK_GENERATOR")
    private Long id;

    private String name;
    
}
```

SEQUENCE 전략도 IDENTITY 전략과 동일한 문제가 있다. 데이터베이스가 직접 기본키를 생성해 주기 때문이다.

EntityManager.persist() 가 호출 되기 전에 기본키를 가져와야 하므로 하이버네이트에서 hibernate: call next value for USER_PK_SEQ 을 실행하여 기본키를 가져온다.

그 후에 EntityManager.persist()호출하기 때문에 IDENTITY 전략과 다르게 쿼리문을 실행하지 않는다.

하지만 SEQUENCE 값을 계속 DB에서 가져와서 사용해야 하기 때문에 성능에 저하를 일으킬 수 있다 . 따라서 allocationSize 의 크기를 적당히 설정하여 성능 저하를 개선시킬 수 있다.

> TABLE

```java
@Entity
@TableGenerator(
    name = "USER_PK_GENERATOR",
    table = "USER_PK_SEQ",
    pkColumnValue = "USER_SEQ",
    allocationSize = 1
    )
public class PkEx() {
    
    @Id
    @GeneratedValue(strategy = GenerationType.SEQUENCE,
                generator="USER_PK_GENERATOR")
    private Long id;

    private String name;
    
}
```

TABLE 전략은 모든 데이터베이스에서 사용이 가능하지만 최적화 되어있지 않은 테이블을 사용하기 때문에 성능문제에 이슈가 있다.

> AUTO

```java
@Entity
public class PkEx() {
    
    @Id
    @GeneratedValue
    private Long id;

    private String name;
    
}
```

기본 설정 값으로 각 데이터베이스에 따라 기본키를 자동으로 생성한다.