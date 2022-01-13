---
title: "DB 쿼리 전략(게시물삭제예정)"
search: false
categories:
  - JPA
toc: true
last_modified_at: 2022-01-13T08:06:00-05:00
tags:
  - JPA
author: 최세훈
---

### DB 쿼리 전략

1. entity

   - 테이블 관계 미선언(ex. @ManyToOne, @OneToOne 등 관계 미선언)

2. 기본 CRUD

   - JpaRepository 사용(단일테이블)

3. 조인

   - JPQL, querydsl 혼용
     - JPQL: 단순 쿼리. 테이블 조인 1~2개와 같이 단순한 쿼리 하나 둘 정도 JPQL로 작성해야 하는 상황
     - querydsl: 동적쿼리, 테이블 조인
   - 참고
     - <https://www.inflearn.com/questions/38771> (자바 ORM 표준 JPA 프로그래밍 저자. 우아한형제들 개발팀장)
     - <https://velog.io/@neity16/4-%EC%8A%A4%ED%94%84%EB%A7%81-%EB%B6%80%ED%8A%B8%EC%99%80-JPA-%ED%99%9C%EC%9A%A9-4-%EB%8F%99%EC%A0%81-%EC%BF%BC%EB%A6%AC-JPQL-vs-Criteria-vs-QueryDSL> (동적 쿼리 (JPQL vs Criteria vs QueryDSL))
     - <https://lelecoder.com/145> (querydsl 문법 및 JPQL비교)

4. DB funtion 필요 or 복잡성이 높은 통계쿼리
   - JPQL, NATIVE 쿼리사용(@Query)

### JPQL vs querydsl 비교

- JPQL
  - 장점: 쿼리스트링 그대로 적용가능.
  - 단점: 동적쿼리를 만들때, 쿼리문자열을 붙이는 작업필요.  
    쿼리가 길어질수록 JAVA 줄바꿈 문자로 인해 가독성이 떨어져 보일 수 있음.  
    문자열이다 보니 문법 오류(ex.오타)로 인한 런타임 오류 발생이 있을 수 있음
- querydsl:
  - 장점: 쿼리를 코드로 작성. 동적쿼리 적용시 용이. 컴파일시점 문법오류 발견할수있음
  - 단점: querydsl repository파일 별도 생성 필요.  
    복잡성이 높은 쿼리(통계쿼리)를 만들땐 코드작성이 어려움.

### querydsql 예시

- Join

```java
  QFaq boFaq = QFaq.faq;
  QBackOfficeUser boRegUser = QBackOfficeUser.backOfficeUser;
  QCommonCode commonCode = QCommonCode.commonCode;

  JPQLQuery<Faq> query = from(boFaq);
  query.leftJoin(boRegUser).on(boRegUser.userSeq.eq(boFaq.regUserSeq))
    .leftJoin(commonCode).on(boFaq.faqCtgryGrpCd.eq(commonCode.ccVal))
    .where(boFaq.faqTitle.like("%" + faqSearchRequest.getSearchText() + "%"))
    .fetchJoin();
```

- Pageable & Sort

```java
    List<Expense> resultList = getQuerydsl().applyPagination(pageable, query).fetch();
```

- Only Sort

```java
    query.orderBy(rentAssetQ.rentDt.asc(), rentAssetQ.assetSeq.assetSeq.desc());
```

- Projection

```java
    List<CodeListRes> codeListResponse =
        query.select(Projections.fields(CodeListRes.class, commonCode.ccSeq,
            commonCode.ccVal, commonCode.ccNm,
            commonCode.ccUseFlag, commonCode.ccDscrp,
            register.userId.as("regUserId"), commonCode.regDttm,
            modifier.userId.as("mdfyUserId"), commonCode.mdfyDttm,
        commonCodeGroup.ccgNm, commonCodeGroup.ccgSeq)).fetch();
```

- 동적쿼리

```java
    private BooleanExpression eqAssetItemCode(String assetItemCode) {
        if (StringUtil.isEmpty(assetItemCode)) {
            return null;
        }
	    return assetQ.assetItemCode.eq(assetItemCode);
	}
...
	JPQLQuery<RentAsset> query = from(rentAssetQ);
	query.innerJoin(rentAssetQ.assetSeq, assetQ).fetchJoin();
	query.where(eqAssetCode(param.getAssetCode())
		  , eqAssetItemCode(param.getAssetItemCode())
		  , eqApplyState(param.getApplyStateCode())
		  , eqUser(param.getUserSeqList()));
	query.orderBy(rentAssetQ.rentDt.asc(), rentAssetQ.assetSeq.assetSeq.asc());
```
