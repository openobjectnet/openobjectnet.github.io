---
title:  "Spring 핵심 기본편"
search: false
categories: 
  - Spring-basic
toc: true  
tags:
  - Spring
author: 이승효
---

# Intro 
Spring 핵심 기본 편을 듣고 알게 된 점을 공유합니다.

# 객체지향 설계

- 요구사항
    - 할인 정책 서비스: VIP 회원 1000원 할인?? 10% 할인??? 고민..

할인 서비스를 만들어야 하는데 만약 고객사의 문제로 할인정책이 아직 확정이 되지 않을 경우<br>
무작정 기다릴 것이 아니라 역할(인터페이스)과 구현(구현체)을 나눈 객체지향 설계로 개발을 시작하면 된다.

인터페이스를 먼저 만들고 구현체를 언제든지 갈아끼울 수 있도록 만들어 보자..

<b>참고:</b> 지금은 스프링 없는 순수한 자바로만 개발.

## 할인 정책 인터페이스(역할)
```java
public interface DiscountPolicy {
	/*
	 * @return 할인 대상 금액
	 */
	int discount(Member member, int price);
}
```

## 1000원 할인 구현체(구현)
```java
@Component
public class FixDiscountPolicy implements DiscountPolicy {

	private int discountFixAmount = 1000;

	@Override
	public int discount(Member member, int price) {
		if(member.getGrade() == Grade.VIP) {
			return discountFixAmount;
		} else {
			return 0;
		}
	}
}
```

## 10% 할인 구현체(구현)
```java
public class RateDiscountPolicy implements DiscountPolicy {

	private int discountPercent = 10; // 할인율
	
	@Override
	public int discount(Member member, int price) {
		if(member.getGrade() == Grade.VIP) {
			return price * discountPercent / 100;
		} else {
			return 0;
		}
	}
}
```

## 주문 서비스 인터페이스(역할)
```java
public interface OrderService {
	Order createOrder(Long memberId, String itemName, int itemPrice); // 주문생성
}
```

## 주문 서비스 구현체(구현)
```java
public class OrderServiceImpl implements OrderService {
  private final MemberRepository memberRepository = new MemoryMemberRepository();
  private final DiscountPolicy discountPolicy = new FixDiscountPolicy();
  
@Override
	public Order createOrder(Long memberId, String itemName, int itemPrice) {
		Member member = memberRepository.findById(memberId); // 회원정보 조회
		int discountPrice = discountPolicy.discount(member, itemPrice);
		
		return new Order(memberId, itemName, itemPrice, discountPrice);
	}
 }
```

### 새로운 할인 정책 적용
```java
public class OrderServiceImpl implements OrderService {

    //private final DiscountPolicy discountPolicy = new FixDiscountPolicy(); // 고정할인
    private final DiscountPolicy discountPolicy = new RateDiscountPolicy(); // 정률할인

}
```

새로운 할인 정책을 적용하려면 FixDiscountPolicy, RateDiscountPolicy 역할 별로 변경하면 된다.
작동하는 데는 지장이 없다. <br><br>
하지만 이 코드는 객체지향 설계 5원칙 DIP와 OCP를 위반하는 코드이다.

- <b>DIP (Dependency inversion principle) 의존관계 역전 원칙</b>

    - 프로그래머는 <b>“추상화에 의존해야지, 구체화에 의존하면 안 된다.”</b> 의존성 주입은 이 원칙을 따르는 방법 중 하나다.
    - 쉽게 이야기해서 구현 클래스에 의존하지 말고, 인터페이스에 의존하라는 뜻

현재 코드는 `DiscountPolicy(추상)`뿐만 아니라 `FixDiscountPolicy, RateDiscountPolicy` 구현 클래스에도 의존하고 있다.

- <b>OCP (Open/closed principle) 개방-폐쇄 원칙</b>

    - 소프트웨어 요소는 <b>확장에는 열려</b> 있으나 <b>변경에는 닫혀</b> 있어야 한다.
    
또한 정책에 따라 `FixDiscountPolicy, RateDiscountPolicy` 코드를 변경해야하기 때문에 OCP도 위반하고 있다.

```java
public class OrderServiceImpl implements OrderService {

    private final DiscountPolicy discountPolicy; // 정률할인, 고정할인

}
```
DIP와 OCP를 지키기 위해선 DiscountPolicy 인터페이스만 의존해야 하는데 이 문제를 해결하려면 `OrderServiceImpl`에 `DiscountPolicy`의 구현 객체를 대신 생성하고 주입해 주어야 한다.

## 관심사의 분리
### AppConfig 등장
애플리케이션 전체 동작 방식을 구성(config) 하기 위해, <b>'구현 객체를 생성'</b>하고, <b>연결</b>하는 책임을 가지는 별도의 설정 클래스를 만들어보자.
```java
public class AppConfig {

	public OrderService orderService() {
		return new OrderServiceImpl(memberRepository(), discountPolicy());
	}

	public DiscountPolicy discountPolicy() {
        return new FixDiscountPolicy();
        // return new RateDiscountPolicy();
    }

	public MemberRepository memberRepository() {
        return new MemoryMemberRepository();
    }
}

```
```java
public class OrderServiceImpl implements OrderService {

    private final MemberRepository memberRepository;
    private final DiscountPolicy discountPolicy;

    public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
        this.memberRepository = memberRepository;
        this.discountPolicy = discountPolicy;
    }

    @Override
    public Order createOrder(Long memberId, String itemName, int itemPrice) {
        Member member = memberRepository.findById(memberId);
        int discountPrice = discountPolicy.discount(member, itemPrice);

        return new Order(memberId, itemName, itemPrice, discountPrice);
    }
}
```
AppConfig는 애플리케이션 실제 동작에 <b>필요한 구현 객체를 생성</b>한다.
할인 정책에 관한 DiscountPolicy를 생성자 주입을 통해 넣어주면 DIP와 OCP위반 문제가 해결된다.


AppConfig 객체는 `FixDiscountPolicy` 객체를 생성하고 그 참조 값을 `OrderServiceImpl`을 생성하면서
생성자로 전달한다.

OrderServiceImpl 입장에서 보면 의존관계를 마치 외부에서 주입해 주는 것 같다고 해서
DI(Dependency Injection) 의존성 주입이라고 한다.