---
title:  "타입스크립트 퀵스타트"
search: false
categories: 
  - TypeScript
toc: true  
last_modified_at: 2022-06-27T08:06:00-05:00
tags:
  - TypeScript
  - JavaScript
---

# Type Script Study
## Intro
안녕하세요. 전제 사원입니다.     
회현 사무실에 있는 타입스크립트 퀵스타트 교재 공부하면서 정리한 내용입니다.
<hr>

## 1. 클래스와 인터페이스
### 1-1. 타입스크립트의 객체지향 프로그래밍 지원
1. OOP (Object Oriented Programming)   
객체지향 프로그래밍은 애플리케이션을 개발할 때 코드 중복을 획기적으로 줄일 수 있는 방법입니다.    
클래스 단위로 나누고 그 클래스들 간의 관계를 추가하면서 똑같은 코드를 사용하는 중복을 최소화하는
개발 방식입니다. 클래스 간의 관계는 상속이나 포함 관계를 고려해 추가합니다.

- 장점
  - 코드 재사용이 용이 : 남이 만든 클래스를 가져와서 이용할 수 있고 상속을 통해 확장해서 사용할 수 있스빈다.
  - 유지보수가 쉽다 : 문제가 발생하는 부분이 클래스 내부의 변수 혹은 메서드로 존재하기 때문에 해당 부분만 수정하면 되기 때문입니다.
  - 대형 프로젝트에 적합 : 클래스 단위로 모듈화 시켜서 개발할 수 있으므로 대형 프로젝트처럼 여러 곳에서 개발 할 때 업무 분담하기 쉽습니다.

- 단점
  - 처리 속도가 상대적으로 느립니다.
  - 객체가 많으면 용량이 커질 수 있습니다.
  - 설계 시 많은 시간과 노력이 필요합니다.

### 1-2. 클래스 선언과 객체 생성
타입스크립트에서 클래스를 선언할 때는 클래스명 앞에 class 키워드를 붙여 선언합니다.
```ts
class Rectangle {
  x: number;
  y: number;
  
  constructor(x: number, y: number) {
    this.x = x;
    this.y = y
  }
  getArea(): number { return this.x * this.y; }
}
```
이렇게 선언한 Rectangle 클래스는 클래스 타입이 됩니다. 이 클래스 타입은 다음의 인터페이스 타입과 동일합니다.

```ts
interface Rectangle {
  x: number;
  y: number;
  getArea(): number;
}
```
클래스 내부에는 생성자인 constructor를 정의합니다.  
생성자는 객체를 생성할 때 클래스에 필요한 설정을 매개변수로 전달받아 멤버 변수를  
초기화 합니다. 클래스를 선언할 때 생성자를 생략하면 기본 생성자를 호출합니다.

#### 1-2-1. <b> 객체 생성 </b>  
클래스는 멤버 변수와 멤버 메서드 등으로 구성된 '틀' 이며,   
클래스를 실제로 사용하려면 객체에 클래스를 할당해주어야 합니다.

```ts
let rectangle = new Rectangle(1, 5);
```

new 키워드를 이용해 Rectangle 객체를 생성해 객체 참조 변수에 할당하였습니다.   
생성된 객체를 실제 메모리에 위치하고 객체의 참조가 객체 참조변수에 할당되는 과정을   
인스턴스화(instantiate)라고 합니다.

클래스는 집의 설계 도면과 같고 설계 도면의 실제는 집이 됩니다.   
객체는 메모리에 존재하는 실제 내용입니다.


위에서 작성한 Rectangle의 객체 선언 후 메서드 사용은
```ts
let area: number = rectangle.getArea();
console.log(area);
```
이런 식으로 하게 되는데, rectangle 객체 선언 시 1, 5를 선언 하였으므로,    
console 로 띄우게 되는 값은 Rectangle 객체의 getArea의 결괏 값인 1 * 5 로   
결과는 5가 나오게 됩니다.

### 1-3. 상속 관계와 포함 관계
객체 지향 프로그래밍에서 클래스 간의 관계는 크게 두 가지로 볼 수 있습니다.
- 상속 관계
- 포함 관계

상속은 코드의 재사용성을 높입니다. IS-A 관계라고도 합니다.  
포함은 한 클래스에 다른 클래스를 멤버 변수로 선언하는 것으로 HAS-A 관계로 표현됩니다.

#### 1-3-1. 상속 관계
상속은 클래스 계층을 만들어서 코드 중복을 줄이는 개체지향 프로그래밍 방법입니다.   
상속에서 부모 클래스를 기반 클래스(base class) 또는 슈퍼 클래스(super class) 라 하며   
이를 상속받는 자식 클래스를 파생 클래스(derived class) 또는 서브 클래스(sub class)라고 합니다.

```ts
class 사람 { ... }
class 전제 extends 사람 { ... }
```

두 클래스 간의 관계를 IS-A 관계로 문장을 만들면 "전제는 사람이다." 이 됩니다.  
이게 어색하지 않다면 부모 클래스와 자식 클래스는 IS-A 관계에 있다고 볼 수 있다.  
타입스크립트는 상속을 위해 extends 키워드를 지원합니다.

- 알아둘 점  
타입스크립트는 클래스에 대해 단일 상속만 지원하므로 자식 클래스는 하나의 부모 클래스만 상속받을 수 있습니다.  
자식 클래스가 부모 클래스를 상속 받을 때에는 자식 클래스 생성자에 super() 메서드를 호출해 부모 생성자를 호출해줘야 합니다.

#### 1-3-2. 포함 관계
포함 관계는 클래스가 다른 클래스를 포함하는 HAS-A 관계입니다. 클래스 내부에 다른 클래스를 포함하는 관계는 대표적으로 두 가지로 나뉩니다.
- 합성 관계
- 집합 관계  

합성 관계는 전체가 부분을 포함하며 강한 관계입니다.  
즉, 수명주기를 함께하므로 강한 관계가 됩니다.

집합 관계는 전체가 부분을 포함하며 약한 관계입니다.  
집합 관계에서는 수명주기를 함께 하지 않기 때문에 약한 관계가 됩니다.

#### 1-3-3. 상속 관계와 포함 관계를 모두 고려해 구현하기
부모 클래스에서는 공통 기능에 해당하는 일반적인 메서드를 추가하고 자식 클래스는 부모 클래스에서 구현하지 못한 세부적인 메서드를 추가해 구현합니다. 상속 관계는 부모 클래스와 부모 클래스를 상속받는 자식 클래스의 관계로 정의라 할 수 있습니다.

extends 키워드를 통해 부모 클래스를 상속 받고, 이러한 상속 관계에서 부모 클래스는 자식 클래스보다 일반적인 기능을 제공하고, 자식 클래스는 부모 클래스보다 구체적인 기능을 제공합니다.

포함 관계는 하나의 클래스가 내부에 다른 클래스를 포함하는 관계입니다.
```ts
class Flashlight {
  constructor(public lightIntensity) { }
}

class SportsCar extends Cars {
  flashlight: FlashLight
}
```
스포츠카가 손전등을 포함하고 있을 때 HAS-A 관계는 위처럼 나타냅니다.


### 1-4. 접근 제한자의 사용법
타입스크립트에서는 객체지향 프로그래밍을 제대로 구현할 수 있도록 대부분의 접근 제한자를 제공합니다. 타입스크립트에서 사용할 수 있는 접근 제한자로는
- public
- protected
- private

이렇게 3가지 정도가 있습니다.  
접근 제한자 중 public 과 protected는 자식 클래스가 부모 클래스로부터 상속받은 멤버 메서드, 멤버 변수에 대한 접근을 허용합니다.  

public 으로 선언된 요소는 상속이 가능해서 자식 클래스에서 접근할 수 있고, 객체를 통한 외부 접근이 가능해 개방 정도가 가장 큽니다.  

private는 상속이 되지 않아 자식 클래스에서 접근할 수 없고, 객체를 통한 외부 접근도 불가능해서 가장 폐쇄적입니다.  

protected는 위의 두가지의 중간 정도의 개방성을 가지고 있습니다. 상속은 가능하나 객체를 통한 접근은 불가능합니다. 

접근 제한자 중 protected와 private은 은닉성을 가지고 있어, 객체를 통한 외부 접근을 제한하며 객체 내부를 캡슐화 합니다.

#### 1-4-1. public과 privae 제한자
```ts
class Base {
  public defaultAge = 0;
}

class Member extends Base {
  age = 0;
  public getAge() {
      return this.age + this.defaultAge;
  }
}
let member = new Member();
console.log(member.getAge());
```
상속받은 부모 클래스의 멤버 변수에 접근할 수 있습니다.

```ts
class Base {
  private birthYear = 2017;
}

class Member extends Base {
  private age = 0;
  private getBirthYear() {
    // return this.birthYear; 부모 클래스의 멤버 변수에 접근 불가
  }
  private getAge() {
    return this.age;
  }

}

let member = new Member();
// member.age 외부 접근 불가
// member.getAge(); 외부 접근 불가
```
클래스 멤버 변수나 메서드에 private 을 지정하면 객체를 통한 접근이 비공개로 설정합니다. 오직 클래스 내부 접근만 허용합니다. 자식 클래스에서도 부모 클래스에 private 로 선언된 변수에 접근할 수 없습니다.

#### 1-4-2. 생성자 매개변수에 접근 제한자 추가
생성자의 매개변수에 접근 제한자를 추가하면 매개변수 속성이 돼 멤버 변수가 되는 효과가 있습니다.  
생성자 매개변수에 접근 제한자를 추가한 것만으로도 생성자매개변수가 클래스의 멤버 변수가 되는 효과가 있습니다.

#### 1-4-3. protected 제한자의 사용법
protected는 타입스크립트 1.3에 추가된 특징입니다. protected 접근 제한자는 객체를 통한 외부 접근을 허용하지 않지만, 상속 관계에서는 부모 클래스에 protected로 선언된 메서드나 멤버 변수의 접근을 허용합니다.

#### 1-4-4. 부모 클래스의 멤버를 이용하기
상속 관계가 있을 때 자식 클래스에서 부모 클래스에 선언된 멤버 메서드나 멤버 변수 등을 이용할 수 있는 방법은 super 키워드와 this 키워드를 이용하는 것입니다. super 키워드는 부모 클래스의 공개 멤버에만 접근할 수 있습니다. this 키워드는 부모 클래스에서 상속바든 멤버와 현재 클래스의 멤버 모두에 접근할 수 있습니다.

부모 클래스의 멤버 변수 값을 가져오려면 부모 클래스의 멤버 메서드나 getter를 통해 가져와야 합니다.

#### 1-4-5. 기본 접근 제한자
기본 접근 제한자는 접근 제한자 선언을 생략할 때 적용됩니다. 기본 접근 제한자가 적용될 수 있는 대상은 클래스 멤버 변수, 멤버 메서드, 클래스 Get/Set 프로퍼티, 생성자의 매개변수 입니다.

예
```ts
class Account {
    public balance: number;
    public get getBalance() {}
    public set setBalance(amount: number) {}
    deposite(depositeAmount: number) {}
    constructor(defaultBalance: number = 1000,
                protected backName: string = "happy bank",
                readonly interestRate: number = 0.1) {}
    
    public getInterestRate() { return this.interestRate; }
        getDefaultBalance() { return this.defaultBalance; } //접근 불가
}
```
기본 접근 제한자는 대체로 public입니다. 예외로 생성자 매개변수에 접근 제한자가 생략되면 생성자 내부에서만 접근할 수 있게 됩니다. 그러나 접근 제한자나 readonly가 붙으면 매개변수 속성이 돼 멤버 변수가 됩니다. interestRate는 읽기 전용으로 자식 클래스에서 접근할 수 있고, 외부 접근도 허용됩니다. 매개변수를 제외한 나머지 요소에서 접근 제한자를 생략할 경우 기본 접근 제한자는 public 입니다.


### 1-5. 추상 클래스를 이용한 공통 기능 정의
추상 클래스는 구현 메서드와 추상 메서드가 동시에 존재할 수 있습니다. 구현 메서드는 실제 구현 내용을 포함한 메서드이고 추상 메서드는 선언만 된 메서드 입니다. 이처럼 추상 클래스는 구현 내용이 없는 추상 메서드를 포함하기 때문에 불완전한 클래스 입니다. 따라서 추상 클래스는 단독으로 객체를 생성할 수 없고 추상 클래스를 상속하고 구현 내용을 추가하는 자식 클래스를 통해 객체를 생성해야 합니다.  

예를 들면 모듈처럼 중복되는 부분이나 공통적인 부분은 미리 다 만들어진 것을 사용하고, 이를 받아 사용하는 쪽에서는 자신에게 필요한 부분만을 재정의하여 사용함으로써 생산성이 향상되고 배포 등이 쉬워지기 떄문입니다.

추상 클래스는 abstract 키워드를 클래스 선언 앞에 붙여서 선언하고 추상 메서드를 서언할 떄도 사용할 수 있습니다. 정의 예시로는
```ts
abstract class Temp {
  abstract tempMethod();
  abstract tempObject: string;
  public method(): void {
    // 공통적으로 사용할 로직을 추가함
    // 로직에서 필요 시 추상 메서드를 호출해 구현 클래스의 메서드가 호출되게 함
    this.tempMethod();
  }
}
```
구현하지 않은 추상 메서드가 선언됐으므로 자식 클래스에서는 추상 메서드를 받아 구현해줘야 합니다.
추상 클래스에 추상 멤버 변수가 선언돼 있으면 자식 클래스에서도 선언해야 합니다. 구현 메서드에서는 추상 멤버 변수나 추상 메서드를 호출할 수 있습니다. 추상 클래스를 작성할 때 유의사항으로 abstract 키워드는 static 이나 private(public, protected는 가능)과 함께 선언할 수 없습니다.

```ts
static abstract a: string; (x)
private abstract a: string; (x)
```
따라서 abstract가 추가된 추상 메서드나 추상 멤버 변수는 자식 클래스에서 구현할 수 있게 모두 public으로 선언해야 합니다.

```ts
abstract a: string;
public abstract a: string;
```
추상 클래스에 선언한 추상 메서드는 오버라이딩 해서 자식 클래스에서 반드시 구현해서 사용해야 합니다.
```ts
class child extends abstractClass {
  public abstractObject: string;
  public method(): void {
    // abstractMethod's real implement content
  }
}
```
추상 클래스는 구현이 완료되지 않은 클래스이므로 구현 클래스를 통해 추상 클래스에 선언된 추상 메서드를 구현해줘야 합니다. 이와 같은 추상 클래스에 기반을 둔 구현 방식은 템플릿 메서드 패턴으로 많이 알려져 있습니다. 이 패턴은 추상 클래스의 구현 메서드에서 추상 멤버 변수나 추상 메서드를 활용해 가상의 공통 로직을 구현해 두고 추상 멤버 변수나 추상 메서드에 대한 세부 로직은 구현 클래스에서 구현합니다.

```ts
abstract class AbstractBird {
  abstract birdName: string;
  abstract habitat: string;

  // 추상 메서드
  abstract flySound(sound: string);

  // 구현 메서드
  fly(): void {
    this.flySound('파다파닥')
  }

  // 구현 메서드
  getHabitat(): void {
    console.log(`<${this.birdName}>의 서식지는 <${this.habitat}> 입니다.`);
  }
}

class WildGoose extends AbstractBird {
  // 추상 멤버 변수를 상속함
  constructor(public birdName: string, public habitat: string) {
    super();
  }

  // 추상 메서드를 오버라이딩
  flySound(sound: string) {
    console.log(`<${this.birdName}>가 <${sound}> 날아갑니다.`);
  }
}

let wildGoose = new WildGoose('기러기', '순천만 갈대밭');
wildGoose.fly();
wildGoose.getHabitat();

//실행 결과
<기러기> 가 <파닥파닥> 날아갑니다.
<기러기> 의 서식지는 <순천만 갈대밭> 입니다.
```
예제는 추상 클래스에 공통 기능을 담은 구현 메서드를 추가하고 추상 메서드는 자식 클래스가 상속해 구현합니다 .이때 추상 클래스에서는 추상 메서드를 호출하는 방식으로 구현하며, 추상 메서드의 실제 동작은 구현 클래스에 추가한 구현 메서드를 통해 이뤄집니다.

## 2. 인터페이스에 대한 이해
### 2-1. 인터페이스 소개
인터페이스는 자바스크립트가 지원하지 않는 타입스크립트만의 특징입니다.  
일반적으로 인터페이스는 타입 체크를 위해 사용되며, 변수, 함수, 클래스에 사용할 수 있습니다.

기본적인 인터페이스 선언 형태는
```ts
interface test {
  test: string
}
```
자식 인터페이스는 extends 키워드를 사용해 부모 인터페이스를 상속해 확장할 수 있습니다.
- 자식 인터페이스는 여러 부모 인터페이스를 다중 상속할 수 있습니다.

```ts
interface Cat {
  run(): void;
  getStatus(): { runningSpeed: number; };
}

interface Tiger {
  run(): void;
  getStatus(): { eattingSpeed: number; };
}

interface CatTiger extends Cat, Tiger {
  getStatus(): { runningSpeed: number, eattingSpeed: number }
}
```
이때 CatTiger 인터페이스는 Cat, Tiger 인터페이스의 서브 타입이 됩니다. 인터페이스 정의를 마치면 implements 키워드를 이용해 인터페이스를 구현하는 클래스를 작성합니다.

인터페이스의 구현 클래스는 인터페이스에 선언된 속성을 모두 구현해줘야 합니다.

### 2-2. 인터페이스의 역할과 컴파일 결과 분석
자바 스크립트의 객체는 구조를 고정할 수 없고 쉽게 변화하는 특성이 있습니다.
객체는 유지보수와 확장 그리고 안전성을 고려해 선언과 동시에 고정할 필요가 있습니다. 인터페이스를 이용하면 객체의 구조를 고정할 수 있습니다.
- 인터페이스는 컴파일 과정에서 타입 검사의 용도로 사용하고 컴파일 후에는 제거 됩니다.    
따라서 typeof로 조사가 불가능합니다.

타입스크립트가 ES6로 변환될 때 인터페이스는 타입 검사 용도가 끝나면 ES6에서 지원하는 클래스 선언은 남습니다. 따라서 typeof ~~~ 같은 타입 질의가 불가능 합니다.

결론적으로 인터페이스는 타입스크립트를 컴파일할 때 클래스와 객체의 타입 안전성을 확보하기 위한 용도로 사용되며 컴파일 후에 사라집니다.

### 2-3. 클래스를 배열 요소 타입으로 지정함
클래스는 객체 리터럴의 타입으로 사용할 수 있습니다. 배열 요소가 객체 리터럴이라면 배열 타입을 선언할 때 클래스를 이용할 수 있습니다.  
#### 2-3-1. 배열 타입을 지정하지 않는 문제점
배열 요소로 객체 리터럴이 올 수 있습니다. 배열 타입을 지정하지 않는다면 배열 타입의 요소로 사용된 객체 리터럴의 구조가 임의의 형태가 될 수 있습니다.
- 객체 리터럴 : 객체 생성 방식 중 가장 일반적이고 간단한 방법으로, 컨텐츠를 그대로 대입하는 방법을 말한다.

배열 요소가 객체 리터럴일 때 구조를 일관되게 하려면 배열 요소의 타입을 객체 리터럴 타입으로 선언해야 합니다.

#### 2-3-2. 배열 요소 타입을 객체 리터럴 타입으로 사용하기
배열의 요소가 객체 리터럴이면 배열 타입을 선언할 때 배열 요소의 타입을 객체 리터럴로 지정해 타입 안전성을 강화할 수 있습니다.

```ts
var person: { name: string, age: number }[];
```

배열 타입은 요소 타입에 []을 붙여 선언했습니다. 따라서 요소 타입에 따라 { name: string, age: number } 의 형태여야 합니다.

예제
```ts
let person2: { name: string, city: string }[];
person2 = [
  { name: "a", city: "seoul" },
  { name: "b", city: "seoul" },
  { name: "c", city: "seoul" }
];
console.log(person2);
```
예제에서 배열 요소의 타입은 {name: string, city: string}으로 선언돼 있으므로 이에 맞춰 선언해야 합니다. 따라서 배열 요소인 객체 리터럴은 name과 city 속성만을 사용해야 합니다.

위의 예제에서는 배열 요소가 모두 요소 타입을 따릅니다. 그런데 이처럼 맞춰 입력하기 쉽지 않을 때는 type 에일리어스를 이용합니다.
- type aliases (타입 별칭) : 새로운 타입을 정의한다.

####  2-3-3. 클래스를 배열 요소로 보고 배열 타입을 선언하기
배열 타입을 사용할 때 요소 타입으로 기본 타입 뿐 아니라 클래스도 선언할 수 있습니다. 클래스를 요소 타입으로 선언하면 클래스 구조와 동일한 객체를 배열 요소로 받을 수 있습니다.

### 2-4. 인터페이스를 배열 타입으로 지정함
인터페이스는 객체 리터럴을 정의하는 타입으로 사용될 수 있습니다. 먼저 객체 리터럴의 구조를 인터페이스로 예시처럼 정의해봅시당
```ts
interface Jai {
  name: string;
  city: string;
}
```
인터페이스는 객체의 타입으로 지정돼 객체의 구조를 고정할 수 있습니다. 만약 배열 요소가 객체 리터럴이라면 Jai 인터페이스를 이용해 배열 타입을 다음처럼 선언할 수 있습니다.
```ts
let Jai2: Jai[];
```
위와 같이 선언하면 배열 요소로 인터페이스와 동일한 구조의 객체 리터럴만을 할당받을 수 있게 됩니다.

- 인터페이스는 타입 선언이 많아도 컴파일 후에는 모두 사라지므로 런타임 성능에 영향을 미치지 않습니다.

### 2-5. 인터페이스에 함수 타입을 정의하기
인터페이스는 클래스의 구조를 정의하기도 하지만 자바 스크립트의 객체 모양을 정의하기도 합니다. 인터페이스는 역할이 꽤 다양한데, 익명 함수에 대한 함수 타입을 정의할 수 있는 기능도 있습니다.

```ts
interface IFormat {
  (data: string, toUpper?: boolean): string;
}
```
인터페이스에 익명 함수를 선언할 수 있습니다. 이렇게 선언한 인터페이스는 익명 함수를 할당받는 변수의 타입으로 선언할 수 있습니다.

```ts
let format: Iformat = function (data: string, toUpper: boolean) {} 
```

위 코드에서 format 변수는 익명 함수를 할당받고 있으며 익명 함수의 타입으로 인터페이스인 IFormat 을 선언했습니다. 이때 눈여겨볼 점은 함수 타입의 매개변수 이름은 정확히 일치하지 않아도 된다는 점입니다.

## 3. 클래스와 인터페이스의 활용
### 3-1. 오버라이딩으로 메서드를 재정의하기
오버라이딩(overriding) 은 부모 클래스에 정의된 메서드를 자식 클래스에서 새로 구현하는 것을 일컫는 개념입니다. 여기서 오버라이딩할 대상이 있는 부모 클래스를 오버라이든 클래스라 합니다. 오버라이든 클래스에는 오버라이든 메서드가 존재합니다. 오버라이든 메서드는 파생 클래스에 정의된 메서드에 오버라이딩돼 오버라이딩 메서드로 새롭게 재정의됩니다.

```ts
class Person {
  walking(kmDistance: number = 0) { }
}

class Jai extends Person {
  //매개 변수명이 달라져도 되나, 변수의 타입은 같은 타입이거나 하위 타입이어야 함
  walking(kmDistance2: number) { }
}
```
오버라이든 클래스는 Person 입니다. Person에 정의된 메서드는 오버라이든 메서드입니다. 오버라이든 클래스인 Person에 선언된 walking 메서드는 파생 클래스에 선언된 walking 메서드로 오버라이딩 됩니다. 오버라이딩으로 메서드가 재정의되려면 기본적으로 오버라이든 메서드와 오버라이딩 메서드는 서로 이름이 같아야 합니다. 그리고 오버라이딩을 위해 다음 두 조건을 만족해야 합니다.

- 조건 1 : 오버라이든 메서드의 매개변수 타입은 오버라이딩 메서드의 매개변수 타입과 같거나 상위 타입이어야 한다.

- 조건 2 : 오버라이든 메서드의 매개변수 개수가 오버라이딩 메서드의 매개변수 개수와 같거나 많아야 한다.

매개 변수 개수는 같지만 조건을 동시에 성립하지 않으면 오버라이딩이 되지 않습니다.

### 3-2. 오버로딩을 구현하는 여러 방법
메서드 오버로딩은 메서드의 이름이 같지만 매개변수의 타입과 개수를 다르게 정의하는 방법을 일컫는 말입니다.

#### 3-2-1. 오버라이딩 메서드를 오버로딩하기
클래스의 상속을 고려해 오버로딩을 구현하려면 부모 클래스에 상위 타입을 가지는 오버라이든 메서드를 선언해 두고 파생 클래스에서 오버라이딩 메서드를 선언해 구현할 수 있습니다. 이 때 오버라이딩 메서드가 오버로딩을 수행하려면 오버라이딩 메서드 위에 오버로드를 추가하면 됩니다.

오버로드는 함수 이름은 같지만 매개변수 선언 형태가 다른 특성이 있습니다.

```ts
typeCheck(value: number): void;
typeCheck(value: string): void;
typeCheck(value: any): void {}
```
any 타입은 모든 타입을 받을 수 있을 것 같지만, 실제로는 number와 string 타입 값만을 매개변수로 받을 수 있습니다.

#### 3-2-2. 인터페이스를 클래스에서 구현하여 오버로딩 하기
인터페이스를 이용해 오버로딩을 하려면 인터페이스에 오버로딩할 기본 메서드를 선언해 줍니다. 그리고 인터페이스를 구현할 클래스에서 기본 메서드를 구현해줍니다.
```ts
interface IPoint {
  getX(x: any): any;
}

class Point implements IPoint {
  getX(x?: number | string): any {
    if (typeof x === "number") {
      return x;
    } else if (typeof x === "string") {
      return x;
    }
  }
}

let p = new Point();
console.log(p.getX());
console.log(p.getX("hello"));
console.log(p.getX(123));
```
위의 예제에서 인터페이스 IPoint를 이용해 메서드 getX를 선언했고 클래스 Point에서 IPoint 인터페이스를 구현하고 있습니다. 이때 getX 메서드는 인터페이스에 정의된 getX 메서드의 선언을 변형해 여러 매개변수(number, string)를 가지는 형태로 오버로딩 하고 있습니다.

```ts
getX(x?: number | string): any {}
```
매개변수 x에 ?를 추가해 선택 매개변수가 되어 입력값이 없는 호출을 받을 수 있도록 했습니다.

인터페이스를 이용하면 선언과 구현을 분리하고 구현부의 구조를 강제할 수 있습니다. 이 점에서 로직과 구조가 섞여 있는 클래스를 상속해 오버로딩 하는 것보다 구조만을 포함하고 이쓴ㄴ 인터페이스를 이용하는 것이 복잡도가 낮습니다.

### 3-3. 클래스와 인터페이스 기반의 다형성 구현하기
#### 3-3-1. 다형성에 대한 소개
프로그래밍 언어에서 다형성이란, 여러 타입을 받아들임으로써 여러 형태를 가지는 것을 의미합니다.
타입스크립트에서는 

- 1. 클래스의 다형성
- 2. 인터페이스의 다형성
- 3. 매개변수의 다형성

정도가 있습니다.

1번 유형의 다형성은 부모가 자식의 변수 타입으로 지정 되면 자식 클래스의 객체에 할당될 수 있습니다. 이때 부모는 자신을 상속하는 어떤 자식이라도 받아들일 수 있는 다형 타입이 되고 다형성을 띠게 합니다.

2번 유형의 다형성은 A 가 있고 A 를 구현한 B가 있을 때 B 가 A 타입으로 지정된 변수에 할당될 때 생긱는 다형성입니다.

3번 유형의 다형성은 메서드의 매개변수가 여러 타입을 받아들이면서 생기는 다형성입니다. 여러 서브 타입을 받아들이면 해당 타입이 서브 타입 다형성이 됩니다. 반대로 자바스크립트의 매개변수처럼 타입을 지정하지 않고 여러 타입을 받아들이면 매개 변수 다형성이 됩니다.

#### 3-3-2. 클래스의 다형성
자식 클래스가 부모 클래스를 상속하고있을 때 부모 클래스를 타입으로 가지는 객체 참조 변수에 자식 클래스의 객체가 할당됨으로써 다형성을 지니게 됩니다.

상속 관계에서 부모 클래스의 타입으로 지정된 객체 참조변수는 자식 클래스의 객체를 할당받더라도 실제 동작은 부모 클래스를 기준으로 실행됩니다.

- 오버라이든 메서드보다 오버라이딩 메서드가 우선으로 호출 된다.

런타임 시에 호출될 메서드가 결정되는 특성을 런타임 다형성이라 합니다. 

부모 클래스가 추상 클래스이고 이를 상속받는 자식 클래스 간에도 다형성이 존재합니다.

객체 참조 변수는 추상 클래스에 선언된 요소에는 접근할 수 있지만, 할당 객체의 타입에 선언된 요소에는 접근할 수 없습니다.

#### 3-3-3. 인터페이스의 다형성
클래스가 인터페이스를 구현하고 있을 때 해당 인터페이스를 타입으로 가지는 객체 참조변수가 구현 클래스의 객체를 참조함으로써 다형성을 지니게 됩니다.

```ts
interface IPerson {
  height: number;
  getAlias: () => string;
  getAge(): number;
}

class PoliceOfficer implements IPerson {
  height: number;
  getAlias = () => "happy";
  getAge(): number {
    return 10;
  }
  hasClub() {
    return true;
  }
}

let policeMan: IPerson = new PoliceOfficer();
console.log(policeMan.getAlias());
console.log(policeMan.getAge());
```

예제를 보면 인터페이스를 구현 클래스가 구현하고 있으므로 객체 참조변수의 타입에 IPerson을 지정하고 구현 클래스를 할당할 수 있습니다.

객체 new PoliceOffier가 본래 PoliceOfficer 타입이지만 객체 참조변수로 할당됨으로써 인터페이스를 기준으로 접근이 이뤄지게 됩니다. 따라서 객체 참조변수는 인터페이스에 정의된 height 멤버 변수, getAlias 메서드, getAge 메서드에 접근할 수 있지만 구현 클래스에 새롭게 추가된 hasClub 메서드에는 접근할 수 없습니다.

#### 3-3-4. 매개변수의 다형성(유니언 타입 이용)
메서드의 매개변수 타입을 유니언 타입을 이용함으로써 객체가 다형성의 성질을 띄도록 만들 수 있습니다.

```ts
class MonitorDisplaytest {
  display(data: string | number) {
    if (typeof data === "string") {
        return "string " + data;
    } else {
        return "number " + data;
    }
  }
}

let displayTest = new MonitorDisplayTest();
displayTest.display("happy");
displayTest.display(123);
```

display 메서드는 string 또는 number 타입을 받아들이는 오버로딩 메서드입니다.
display 메서드는 여러 타입을 받아들이므로 typeof 로 타입 검사를 수행하는 타입 가드를 추가해야 합니다. 이와 비슷하게 매개 변수에 클래스 타입을 유니언 타입으로 선언해 여러 클래스 타입을 받아 instanceof 로 타입 가드를 추가해야 합니다.

```ts
class MonitorDisplayTest {
  display1 (monitor: Led | Oled) {
    if (monitor instanceof Led) {
        let myMonitor: Led = <Led>monitor;
        return myMonitor.getName();
    }
    else if (monitor instanceof Oled) {
        let myMonitor: Oled = <Oled>monitor;
        return myMonitor.getName();
    }
  }
}
```
display1 메서드는 여러 클래스 타입을 받고 instanceof를 통해 타입에 맞는 처리를 합니다. 호출 할 때는 다음과 같이 유니언 타입에 따라 여러 클래스 객체를 전달할 수 있는 다형성이 있습니다.

```ts
displayTest.display1(new Led("LED TV"));
displayTest.display1(new Oled("Oled TV"));
```
문제는 유니언 타입에 클래스가 하나 추가됐을 떄입니다. class 추가에 조건검사를 위한 else if가 추가되어야 하기 때문입니다.

클래스 타입이 추가될 때마다 type alias도 else if 문을 매번 업데이트 해줘야 하기 때문에 type alias를 도입하는 것은 근본적인 해결책이 될 수 없습니다. 인터페이스를 이용해 다형성을 구현해 줄 필요가 있다.

#### 3-3-5. 매개변수의 다형성(인터페이스 타입 이용)
메서드의 매개변수 타입을 이용함으로써 객체가 다형성의 성질을 띄우도록 만들 수 있습니다.
<hr>

## 모듈
## 비동기 처리
## 서드파티 라이브러리와 타입 정의 파일
 