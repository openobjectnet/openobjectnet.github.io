---
title:  "Spring 핵심 기본편2"
search: false
categories: 
  - Spring-basic
toc: true  
tags:
  - Spring
author: 이승효
---


# Intro 
인프런 Spring 핵심 기본 편을 듣고 알게 된 점을 공유합니다.

# 1. @Configuration의 싱글톤 보장

``` java
@Configuration
public class AppConfig {

    @Bean
    public MemberService memberService() {
        System.out.println("call AppConfig.memberService");
        return new MemberServiceImpl(memberRepository());
    }

    @Bean
    public MemberRepository memberRepository() {
        System.out.println("call AppConfig.memberRepository");
        return new MemoryMemberRepository();
    }

    @Bean
    public OrderService orderService() {
        System.out.println("call AppConfig.orderService");
        return new OrderServiceImpl(memberRepository(), discountPolicy());
    }

    @Bean
    public DiscountPolicy discountPolicy() {
        return new FixDiscountPolicy();
//        return new RateDiscountPolicy();
    }

}
```

코드를 보면 Bean 객체를 생성할 때 new를 통해 새로운 객체를 반환해주고 있다.<br>
코드상으론 싱글 톤 보장이 안될 것 같다…. 한번 테스트를 해보았는데

``` java
public class ConfigurationSingletonTest {

    @Test
    void configurationTest() {
        ApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class); // 빈 등록
    }

}
```

설정 파일 대로라면 <br>
memberService 빈 등록 시 call AppConfig.memberService 가 찍히고 <br>
memberRepository를 호출해 call AppConfig.memberRepository 가 찍히고 <br>
memberRepository 등록 시 call AppConfig.memberRepository가 찍히고 <br>
orderService 등록 시 call AppConfig.orderService 가 찍히고 <br>
memberRepository를 호출하면서 call AppConfig.memberRepository가 한 번 더 찍혀야 할 것 같은데...

싱글톤 보장 안될 시 예상 로그
```
call AppConfig.memberService
call AppConfig.memberRepository
call AppConfig.memberRepository
call AppConfig.orderService
call AppConfig.memberRepository
```

테스트 로그
```
call AppConfig.memberService
call AppConfig.memberRepository
call AppConfig.orderService
```


테스트 시 싱글톤이 보장되고 있다.<br>
싱글톤이 보장되는 비밀은 @Configuration 애노테이션에 있다.<br>
AppConfig 객체를 로그에 찍어 보았다.

```java
@Test
void configurationDeep() {
    ApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);
    AppConfig bean = ac.getBean(AppConfig.class);

    System.out.println("bean = " + bean);
}
```
```
bean = hello.core.AppConfig$$EnhancerBySpringCGLIB$$390333c8@d71adc2
```
클래스 명 뒤에 $$EnhancerBySpringCGLIB$$ 가 붙는다.<br>
@Configuration 애노테이션을 사용하면 CGLIB라는 바이트코드 조작 라이브러리를 사용해<br> 
AppConfig 클래스를 상속받은 임의의 다른 클래스를 만들고, 그 다른 클래스를 스프링 빈으로 등록해 준다.<br>
그 임의의 클래스가 바로 싱글톤이 보장되도록 해준다.

(CGLIB 예상 로직)
```java 

if (memoryMemberRepository가 이미 스프링 컨테이너에 등록되어 있으면?) {
        return 스프링 컨테이너에서 찾아서 반환;
    } else { //스프링 컨테이너에 없으면
        기존 로직을 호출해서 MemoryMemberRepository를 생성하고 스프링 컨테이너에 등록
        return 반환
}
```

@Bean이 붙은 메서드마다 이미 스프링 빈 이 존재하면 존재하는 빈을 반환하고, <br>
스프링 빈이 없으면 생성해서 스프링 빈으로 등록하고 반환하는 코드가 동적으로 만들어진다.<br>
덕분에 싱글톤이 보장된다.

@Configuration 을 주석 처리하고 로그를 찍어 테스트를 해보았다.

테스트 로그
```
bean = hello.core.AppConfig@1b9ea3e3
```
$$EnhancerBySpringCGLIB$$가 없어졌다.
```
call AppConfig.memberService
call AppConfig.memberRepository
call AppConfig.memberRepository
call AppConfig.orderService
call AppConfig.memberRepository
```
예상대로 싱글톤이 적용되지 않은 로그가 찍혔다.

# 2. @ComponentScan
스프링은 수동(@Bean)으로 빈을 등록할 수도 있고 자동으로도 등록 가능한데,
@ComponentScan 사용하면 @Component 애노테이션을 찾아 자동으로 빈으로 등록해 준다.

```java
@Configuration
@ComponentScan
public class AutoAppConfig {
}
```

```java
@Component
public class MemberServiceImpl implements MemberService {

    private final MemberRepository memberRepository;

    @Autowired
    public MemberServiceImpl(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }
}

```
@ComponentScan으로 빈 등록할 경우 MemberServiceImpl라는 클레스가 빈으로 등록되는데
의존관계가 있다면 주입을 해줘야 한다. 이 경우 자동 의존관계 주입 @Autowired를 사용해 의존관계를 주입해 준다.

```java
@SpringBootApplication
public class CoreApplication {

	public static void main(String[] args) {
		SpringApplication.run(CoreApplication.class, args);
	}

}
```
스프링 부트의 경우 @SpringBootApplication이 가장 기본적인 설정으로 적용되어 있는데 애노테이션 안으로 들어가 보면

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
		@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication {
```

기본적으로 @ComponentScan이 적용되어 있는 걸 확인할 수 있다.
그렇기 때문에 우리가 자주 사용하는 @Controller, @Service, @Repository 애노테이션이 붙어있으면 빈으로 생성되고
@Autowired를 통해 의존성 주입 사용이 가능해진다.

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component
public @interface Controller {
```
```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component
public @interface Service {
```
```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component
public @interface Repository {
```

@Controller, @Service, @Repository을 까보면 모두 @Component가 붙어있는 걸 확인할 수 있다.


# 3. 생성자 주입 장점??

## 3-1. 순수자바 코드 테스트 가능
필드 주입
```java
public class OrderServiceImpl implements OrderService {
    
    @Autowired
    private MemberRepository memberRepository;
    
    @Autowired
    private DiscountPolicy discountPolicy;

    @Override
    public Order createOrder(Long memberId, String itemName, int itemPrice) {
        Member member = memberRepository.findById(memberId);
        int discountPrice = discountPolicy.discount(member, itemPrice);

        return new Order(memberId, itemName, itemPrice, discountPrice);
    }

}
```

사용하기 편해서 필드 주입으로 사용하고 있는데 이런 식으로 사용하면 
순수한 자바 코드로 테스트할 때 의존성 주입이 불가능하다.

```java
@Test
void fieldInjectionTest() {
    OrderServiceImpl orderService = new OrderServiceImpl();
    orderService.createOrder(1L, "Test", 1000);
}
```

테스트 시 NullPointException이 발생한다. 
memberRepository와, discountPolicy에 의존성 주입을 해줄 수가 없기 때문이다.

## 3-2. 불변

대부분의 의존관계 주입은 한번 일어나면 애플리케이션 종료 시점까지 의존관계를 변경할 일이 없다.<br>
오히려 대부분의 의존관계는 애플리케이션 종료 전까지 변하면 안된다. (불변해야 한다.)<br>
수정자 주입을 사용하면, setXxx 메서드를 public으로 열어두어야 한다.<br>
누군가 실수로 변경할 수도 있고, 변경하면 안되는 메서드를 열어두는 것은 좋은 설계 방법이 아니다.<br>
생성자 주입은 객체를 생성할 때 딱 1번만 호출되므로 이후에 호출되는 일이 없다. <br>
따라서 불변하게 설계할 수 있다.

## 3-3. final 키워드

```java
public class OrderServiceImpl implements OrderService {

    private final MemberRepository memberRepository;
    private final DiscountPolicy discountPolicy;

    @Autowired
    public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
        this.memberRepository = memberRepository;
        this.discountPolicy = discountPolicy;
    }
}

```
생성자 주입을 사용하면 필드에 final 키워드를 사용할 수 있다.<br>
그래서 생성자에서 혹시라도 값이 설정되지 않는 오류를 컴파일 시점에 막아준다.<br>
생성자 주입 방식을 선택하는 이유는 여러가지가 있지만, <br>프레임워크에 의존하지 않고, 순수한 자바 언어의
특징을 잘 살리는 방법이기도 하다.