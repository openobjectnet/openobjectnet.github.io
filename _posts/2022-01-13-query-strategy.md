---
title:  "DB 쿼리 전략(게시물삭제예정)"
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
    * 테이블 관계 미선언(ex. @ManyToOne, @OneToOne 등 관계 미선언)

2. 기본 CRUD
    * JpaRepository 사용(단일테이블)

3. 조인
    * JPQL, querydsql 혼용
        * JPQL: 단순 쿼리. 테이블 조인 1~2개
        * querydsql: 동적쿼리, 테이블 조인 2~3개 이상
    * 참고
        * https://www.inflearn.com/questions/38771
        * https://velog.io/@neity16/4-%EC%8A%A4%ED%94%84%EB%A7%81-%EB%B6%80%ED%8A%B8%EC%99%80-JPA-%ED%99%9C%EC%9A%A9-4-%EB%8F%99%EC%A0%81-%EC%BF%BC%EB%A6%AC-JPQL-vs-Criteria-vs-QueryDSL
        * https://lelecoder.com/145

4. DB funtion 필요 or 복잡성이 높은 통계쿼리   
    * JPQL, NATIVE 쿼리사용(@Query)

### JPQL vs querydsl 비교
* JPQL
    * 장점: 쿼리문법을 문자열로 적용가능. 
    * 단점: 동적쿼리를 만들때, 쿼리문자열을 붙이는 작업필요.   
    쿼리가 길어질수록 가독성이 떨어져 보일수 있음.   
    문자열이다 보니 문법 오류(ex.오타)로 인한 런타임 오류 발생이 있을 수 있음
* querydsl: 
    * 장점: 쿼리를 객체로 처리. 동적쿼리 적용시 용이.
    * 단점: querydsl repository파일 별도 생성 필요.   
    복잡성이 매우 높은 쿼리를 만들땐 객체코드작성이 어려움.

### querydsql 예시
* Join
```java
    JPQLQuery<RentApplication> query = from(applicationQ);
    query.innerJoin(applicationQ.piSeq, projectQ).fetchJoin();
    query.where(applicationQ.showYn.eq(CommonYNEnum.Y)
            , eqPjNm(param.getPjName()), eqPjPlace(param.getPjPlace())
    query.orderBy(applicationQ.regDt.desc());
```
* Pageable & Sort
```java
    List<Expense> resultList = getQuerydsl().applyPagination(pageable, query).fetch();
```
* Only Sort
```java
    query.orderBy(rentAssetQ.rentDt.asc(), rentAssetQ.assetSeq.assetSeq.desc());
```
* Projection
```java
    List<CodeListRes> codeListResponse = 
        query.select(Projections.fields(CodeListRes.class, commonCode.ccSeq,
            commonCode.ccVal, commonCode.ccNm,
            commonCode.ccUseFlag, commonCode.ccDscrp,
            register.userId.as("regUserId"), commonCode.regDttm,
            modifier.userId.as("mdfyUserId"), commonCode.mdfyDttm,
        commonCodeGroup.ccgNm, commonCodeGroup.ccgSeq)).fetch();
```
* 동적쿼리
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





