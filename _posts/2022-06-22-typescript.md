---
title: "타입스크립트 핸드북 정리"
search: false
categories:
  - TypeScript
toc: true
last_modified_at: 2022-06-22T18:09:00-05:00
tags:
  - TypeScript
  - JavaScript
author: 전제
---
# Intro
안녕하세요. 전제 사원입니다.
타입스크립트 핸드북 공부하면서 정리한 내용 입니다.

# TypeScript

- 타입 스크립트는 파일 확장자 명으로 ts를 사용합니다.
- 출처 : 캡틴 판교's 타입스크립트 핸드북

# 기본 타입
타입 스크립트는 자바 스크립트에 타입을 추가한 것으로 기본 타입은 크게 12가지가 있습니다.
- Boolean
- Number
- String
- Object
- Array
- Tuple
- Enum
- Any
- Void
- Null
- Undefined
- Never

## String
자바스크립트의 변수의 타입이 문자열인 경우 
```ts
let str: string = 'hi';
```

## Number
타입이 숫자이면 
```ts
let num: number = 10;
```

## Boolean
타입이 진위 값인 경우에는 
```ts
let isLoggedIn: boolean = false;
```
## Object
## Array
타입이 배열인 경우 
```ts
let arr: number[] = [1, 2, 3];
```
또는 제네릭을 사용할 수 있습니다.
```ts
let arr: Array<number> = [1,2,3]
```

## Tuple
튜플은 배열의 길이가 고정되고 각 요소의 타입이 지정되어 있는 배열 형식을 의미합니다.
```ts
let arr: [string, number] = ['hi', 10];
```
만약 정의하지 않은 타입, 인덱스로 접근할 경우 오류가 납니다.
```ts
arr[1].concat('!'); // Error, 'number' does not have 'concat'
arr[5] = 'hello'; // Error, Property '5' does not exist on type '[string, number]'
```

## Enum 
이넘은 C, Java와 같은 다른 언어에서 흔하게 쓰이는 타입으로 특정 값들의 집합을 의미합니다.
```ts
enum Avengers {Capt, IronMan, Thor}
let hero: Avengers = Avengers.Capt;
```
이넘은 인덱스 번호로도 접근할 수 있습니다.
```ts
enum Avengers {Capt, IronMan, Thor}
let hero: Avengers = Avengers[0]
```
만약 원한다면 이넘의 인덱스를 사용자 편의로 변경하여 사용할 수도 있습니다.
```ts
enum Avengers {Capt = 2, IronMan, Thor}
let hero: Avengers = Avengers[2];
let hero: Avengers = Avengers[4];
```

## Any
기존에 자바스크립트로 구현되어 있는 웹 서비스 코드에 타입스크립트를 점진적으로 적용할 때 활용하면 좋은 타입입니다. 단어 의미 그대로 모든 타입에 대해서 허용한다는 의미를 갖고 있습니다.
```ts
let str: any = 'hi';
let num: any = 10;
let arr: any = ['a', 2, true];
```

## Void
변수에는 undefined와 null만 할당하고, 함수에는 반환 값을 설정할 수 없는 타입입니다.
```ts
let unuseful: void = undefined;
function notuse(): void {
    console.log('sth');
}
```

## Never
함수의 끝에 절대 도달하지 않는다는 의미를 지닌 타입입니다.
```ts
function neverEnd(): never {
    while(true) {

    }
}
```
# 함수
## 타입스크립트에서의 함수
웹 애플리케이션을 구현할 때 자주 사용되는 함수는 타입스크립트로 크게 3가지 타입을 정의할 수 있습니다.
- 함수의 파라미터 타입
- 함수의 반환 타입
- 함수의 구조 타입

## 함수의 기본적인 타입 선언
자바스크립트 함수에 타입을 부여해봅시다.
```ts
function sum(a: number, b: number): number {

}
```
기존 자바스크립트 함수의 선언 방식에서 매개변수와 함수의 반환 값에 타입을 추가하였습니다.

## 함수의 인자
타입스크립트에서는 함수의 인자를 모두 필수 값으로 간주합니다. 따라서, 함수의 매개변수를 설정하면 undefined나 null이라도 인자로 넘겨야하며 컴파일러에서 정의된 매개변수 값이 넘어 왔는지 확인합니다. 달리 말하면 정의된 매개변수 값만 받을 수 있고 추가로 인자를 받을 수 없다는 의미입니다.
```ts
function sum(A: number, b: number): number {
    return a + b;
}
sum(10, 20);
sum(10, 20, 30); // Error, Too many parameters
sum(10); // Error, Too few parameters
```
위와 같은 특성은 정의된 매개변수의 갯수만큼 인자를 넘기지 않아도 되는 자바스크립트의 특성과 반대됩니다. 만약 이러한 특성을 살리고 싶다면 ?를 이용해서 
```ts
function sum(A: number, b?: number): number {
    return a + b;
}
sum(10, 20);
sum(10, 20, 30); // Error, Too many parameters
sum(10); 
```
이렇게 정의할 수 있습니다. 매개변수 초기화는 ES6 문법과 동일합니다.

```ts
function sum(a:number, b = '100'): number {
    return a + b;
}
sum(10, undefined);
sum(10, 20, 30); // Error, Too many parameters
sum(10); 
```

## REST 문법이 적용된 매개변수
```ts
function sum(a: number, ...nums: number[]): number {
    const totalOfNums = 0;
    for(let key in nums) {
        totalOfNums += nums[key];
    }
    return a + totalOfNums;
}
```

## this
타입스크립트에서 자바스크립트의 this가 잘못 사용되었을 때 감지할 수 있습니다.  
타입스크립트에서 this가 가리키는 것을 명시하려면
```ts
function 함수명(this: 타입) {

}
```
이렇게 사용합니다.

## 콜백에서의 this
앞에서 살펴본 일반적인 상황에서의 this와는 다르게 콜백으로 함수가 전달되었을 때 this를 구분해줘야 할 때가 있습니다. 그럴 땐 강제할 수 있습니다.
```ts
interface UIElement {
    addClickListener(onclick: (this: void, e: Event) => void): void;
}

class Handler {
    info: string;
    onClick(this: Handler, e: Event) {
        this.info = e.message;
    }
}
let handler = new Handler();
uiElement.addClickListener(handler.onClick);
```
만약 UIElement 인터페이스의 스펙에 맞춰 Handler를 구현하려면
```ts
class Handler {
    info: string;
    onClick(this: void, e: Event) {
        console.log('clicked');
    }
}
let handler = new Handler();
uiElement.addClickListener(handler.onClick);
```
이렇게 변경합니다.

# 인터페이스
인터페이스는 상호 간에 정의한 약속 혹은 규칙을 의미합니다. 타입스크립트에서의 인터페이스는 보통 다음과 같은 범주에 대해 약속을 정의할 수 있습니다.
- 객체의 스펙
- 함수의 파라미터
- 함수의 스펙
- 배열과 객체를 접근하는 방식
- 클래스

## 인터페이스 맛보기
```ts
let person = { name: 'Capt', age: 28}

function logAge(obj: {age : number}) {
    console.log(obj.age);
}
logAge(person);
```
위 logAge() 함수에서 받는 인자의 형태는 age를 속성으로 갖는 객체입니다. 이렇게 인자를 받을 때 단순한 타입뿐만 아니라 객체의 속성 타입까지 정의할 수 있습니다.

여기에 인터페이스를 적용하면 
```ts
interface personAge{
    age: number;
}

function logAge(obj: personAge) {
    console.log(obj.age);
}
let person = { name: 'Capt', age: 28}
logAge(person);
```
이제는 logAge()의 인자가 좀 더 명시적으로 바뀌었습니다. logAge()의 인자는 personAge라는 타입을 가져야합니다.

인터페이스를 인자로 받아 사용할 때 항상 인터페이스의 속성 갯수와 인자로 받는 객체의 속성 갯수를 일치시키지 않아도 됩니다. 다시 말해, 인터페이스에 정의된 속성, 타입의 조건만 만족한다면 객체의 속성 갯수가 더 많아도 상관 없다는 의미입니다. 또한, 인터페이스에 선언된 속성 순서를 지키지 않아도 됩니다.

## 옵션 속성
인터페이스를 사용할 때 인터페이스에 정의되어 있는 속성을 모두 다 꼭 사용하지 않아도 됩니다. 이를 옵션 속성이라고 합니다.

```ts
interface 인터페이스 이름 {
    속성? : 타입;
}
```
이렇게 속성의 끝에 ?를 붙입니다.

## 옵션 속성의 장점
옵션 속성의 장점은 단순히 인터페이스를 사용할 때 속성을 선택적으로 적용할 수 있다는 것 뿐만 아니라 인터페이스에 정의되어 있지 않은 속성에 대해서 인지시켜줄 수 있다는 점입니다.
```ts
interface CraftBeer {
    name: string;
    hop?: number;
}

let myBeer = {
    name: 'Blanc'
}
function brewBeer(beer: CraftBeer) {
    console.log(beer.brewery);  // Error: Property 'brewery' does not exist on type 'Beer'
}
brewBear(myBeer);
```
위에 인터페이스에 정의되어 있지 않은 속성에 대해서 오류를 표시합니다. 오탈자가 나와도 알려줍니다.

## 읽기 전용 속성
읽기 전용 속성은 인터페이스로 객체를 처음 생성할 때만 값을 할당하고 그 이후에는 변경할 수 없는 속성을 의미합니다. 문법은 readonly 속성을 앞에 붙입니다.
```ts
interface CraftBeer {
    readonly brand: string;
}
```

## 읽기 전용 배열
배열을 선언할 때 ReadonlyArray&#60;T&#62; 타입을 사용하면 읽기 전용 배열을 생성할 수 있습니다.
```ts
let arr: ReadonlyArray<number> = [1,2,3];
arr.splice(0,1);
arr.push(4);
arr[0] = 100;
// all error
```
ReadonlyArray로 선언하면 배열의 내용을 변경할 수 없습니다. 선언하는 시점에만 값을 정의할 수 있으니 주의해서 사용해야 합니다.

## 객체 선언과 관련된 타입 체킹 
타입스크립트는 인터페이스를 이용하여 객체를 선언할 때 엄밀하게 속성 검사를 진행합니다.
```ts
interface CraftBeer {
    brand?: string;
}
function brewBeer(beer: CraftBeer) {

}
brewBeer({brandon: 'what'}) // error: Object literal may only specify known properties, but 'brandon' does not exist in type 'CraftBeer'. Did you mean to write 'brand'?
```
위 코드를 보면 CraftBeer 인터페이스에는 brand라고 선언되어 있지만, brewBeer() 함수에 인자로 넘기는 myBeer 객체에는 brandon이 선언되어 있어 오탈자 점검을 요하는 오류가 납니다.

이런 타입 추론을 무시하고 싶다면 
```ts
let myBeer = {brandon: 'what'}
brewBeer(myBeer as CraftBeer);
```
이렇게 선언하고 만약 인터페이스 정의하지 않은 속성들을 추가로 사용하고 싶을 때는 
```ts
interface CraftBeer {
    brand?: string;
    [propName: string]: any;
}
```
이렇게 하면 됩니다.

## 함수 타입 
인터페이스는 함수의 타입을 정의할 때에도 사용할 수 있습니다.
```ts
interface login {
    (username: string, password: string): boolean;
}
```
함수의 인자의 타입과 반환 값의 타입을 정합니다.
```ts
let loginUser: login;
loginUser = function(id: string, pw: string) {
    console.log('로그인 했습니다.');
    return true;
}
```

## 클래스 타입
C#이나 자바처럼 타입스크립트에서도 클래스가 일정 조건을 만족하도록 타입 규칙을 정할 수 있습니다.
```ts
interface CraftBeer {
    beerName: string;
    nameBeer(beer: string) : void;
}

class myBeer implements CraftBeer {
    beerName: string = 'Baby Guinness';
    nameBeer(b: string) {
        this.beerName = b;
    }
    constructor() {}
}
```
## 인터페이스 확장
클래스와 마찬가지로 인터페이스도 인터페이스 간 확장이 가능합니다.
```ts
interface Person {
    name: string;
}
interface Developer extends Person {
    skill: string;
}
let fe = {} as Developer;
fe.name = 'jai';
fe.skill = 'TypeScript';
```
혹은 여러 인터페이스를 상속받아 사용할 수 있습니다.
```ts
interface Person {
  name: string;
}
interface Drinker {
  drink: string;
}
interface Developer extends Person {
  skill: string;
}
let fe = {} as Developer;
fe.name = 'jai';
fe.skill = 'TypeScript';
fe.drink = 'Beer';
```

## 하이브리드 타입
자바스크립트의 유연하고 동적인 타입 특성에 따라 인터페이스 역시 여러 가지 타입을 조합하여 만들 수 있습니다. 

# 이넘
이넘은 특정 값들의 집합을 의미하는 자료형입니다. 이넘은 다른 프로그래밍 언어를 다뤄본 사람들에게 친숙한 타입으로 타입스크립트에서는 문자형 이넘과 숫자형 이넘을 지원합니다.

## 숫자형 이넘
타입스크립트에서 숫자형 이넘은
```ts
enum Direction {
    Up = 1,
    Down,
    Left,
    Right
}
```
이렇게 선언합니다. 만약에 초기 값을 주면 초기 값부터 차례로 1씩 증가합니다.

## 숫자형 이넘 사용하기
위에서처럼 선언한 이넘은 
```ts
enum Response {
    No = 0,
    Yes = 1
}

function respond(recipient: string, message: Response): void {

}
respond("Captain Pangyo", Respose.Yes);
```

이렇게 사용할 수 있습니다. 그리고 숫자형 이넘에서 주의할 점은 선언할 때 만약 이넘 값에 다른 이넘 타입의 값을 사용하면 선언하는 이넘의 첫 번째 값에 초기화를 해줘야 한다는 점입니다.
```ts
enum Wrong{
    A = getSomeValue(),
    B, // Error, 초기화가 필요합니다.
}
```

## 문자형 이넘 
문자형 이넘은 앞에서 살펴본 숫자형 이넘과 개념적으로는 거의 비슷합니다만, 런타입에서 미세한 차이가 있습니다.   
일단 문자형 이넘은 이넘 값 전부 다 특정 문자 또는 다른 이넘 값으로 초기화 해줘야 합니다.
```ts
enum Direction {
    Up = "Up",
    Down = "Down",
    Left = "Left",
    Right = "Right"
}
```
또한, 문자형 이넘에는 숫자형 이넘과 다르게 auto-incrementing이 없습니다.대신 디버깅을 할 때 숫자형 이넘의 값은 가끔 불명확하게 나올 때가 있지만 문자형 이넘은 항상 명확한 값이 나와 읽기 편합니다.

## 복합 이넘
기술적으로 이넘에 문자와 숫자를 혼합하여 생성할 순 있습니다. 하지만 권고하지 않습니다

## 런타임 시점에서의 이넘 특징
이넘은 런타임시에 실제 객체 형태로 존재합니다. 

## 컴파일 시점에서의 이넘 특징
이넘이 런타임 시점에서는 실제 객체지만 keyof를 사용할 때 주의해야 합니다. 일반적으로 keyof를 사용해야 되는 상황에서는 대신 keyof typeof를 사용합니다.

## 리버스 매핑
리버스 매핑은 숫자형 이넘에만 존재하는 특징입니다. 이넘의 킬 값을 얻을 수 있고, 값으로 키를 얻을 수도 있습니다.

## Union Type
유니온 타입이란 자바스크립트이 OR 연산자와 같이 A이거나 B입니다 라는 의미의 타입입니다. 
```ts
function logText(text: string | number) {

}
```
위 함수의 파라미터 text에는 문자열 타입이나 숫자 타입이 모두 올 수 있습니다. 이처럼 | 연산자를 이용하여 타입을 여러 개 연결하는 방식을 유니온 타입 정의 방식이라고 부른다.

## Union Type의 장점
유니온 타입의 장점  
any를 사용하는 경우 마치 자바스크립트로 작성하는 것처럼 동작을 하고 유니온 타입을 사용하면 타입스크립트의 이점을 살리면서 코딩할 수 있습니다.

## Intersection Type
인터렉션 타입은 여러 타입을 만족하는 하나의 타입을 의미합니다.
```ts
interface Person {
  name: string;
  age: number
}
interface Drinker {
  drink: string;
  skill: number;
}

type Capt = Person & Developer
```
위 코드는 Person 인터페이스의 타입 정의와 Developer 인터페이스의 타입 정의를 & 연산자를 이용하여 합친 후 Capt라는 타입에 할당한 코드입니다.  
이처럼 & 연산자를 이용해 여러 개의 타입 정의를 하나로 합치는 방식을 인터섹션 타입 정의 방식이라고 합니다.

## Union Type을 쓸 때 주의할 점 
앞에서 유니온 타입과 인터섹션 타입을 살펴보았습니다. 아마 논리적으로 유니온 타입은 OR, 인터섹션은 AND라고 생각할텐데, 인터페이스와 같은 타입을 다룰 때는 논리적 사고를 주의해야 합니다.
```ts
interface Person {
    name : string, 
    age : number
}
interface Developer {
    name: string,
    skill: string
}
function introduce(someone: Person | Developer) {
    someone.name; // o
    someone.age; // x
    someone.skill; // x
}
```
여기서 introduce() 함수의 파라미터 타입을 Person, Developer 인터페이스의 유니온 타입으로 정의하였는데, 유니온타입은 A도 될 수 있고, B도 될 수 있는 타입이라고 생각하면 파라미터의 타입이 Person도 되고 Developer도 될테니까 함수 안에서 당연히 이 인터페이스들이 제공하는 속성들인 age나 skill를 사용할 수 있겠지라고 생각할 수 있습니다만, 타입스크립트 관점에서는 introduce() 함수를 호출하는 시점에 Person 타입이 올지 Developer 타입이 올지 알 수가 없기 때문에 어느 타입이 들어오든 간에 오류가 안 나는 방향으로 타입을 추론하게 됩니다.

결과적으로 introduce() 함수 안에서는 별도의 타입 가드를 이용하여 타입의 범위를 좁히지 않는 이상 기본적으로 Person과 Developer 두 타입에 공통적으로 들어있는 속성인 name만 접근할 수 있게 됩니다.

# 클래스 
## readonly 
클래스의 속성에 readonly 키워드를 사용하면 
```ts
class Developer {
    readonly name: string;
    constructor(theName: string) {
        this.name = theName;
    }
}
let john = new Developer("John");
john.name = "John";
```
접근만 가능합니다. 이처럼 readonly를 사용하면 contructor() 함수에 초기 값 설정 로직을 넣어줘야 하므로 아래와 같이 readonly 키워드를 추가해서 코드를 줄일 수 있습니다.
```ts
class Developer {
    readonly name : string;
    constructor (readonly name: string) {

    }
}
```

## Accessor
타입스크립트는 객체의 특정 속성의 접근과 할당에 대해 제어할 수있습니다. 이를 위해선 해당 객체가 클래스로 생성한 객체여야 합니다.
```ts
class Developer {
    name: string;
}

const jos = new Developer();
josh.name = 'Josh Bolton';
```
위 코드는 클래스로 생성한 객체의 name 속성에 Josh Bolton 이라는 값을 대입한 코드입니다. 이제 josh 라는 객체의 name 속성은 josh bolton이라는 값을 갖습니다

여기서 만약 name 속성에 제약 사항을 추가하고 싶다면 get과 set을 활용하면 됩니다.
- get만 선언하고 set을 선언하지 않는 경우에는 자동으로 readonly로 인식됩니다.

## Abstract Class
추상 클래스는 인터페이스와 비슷한 역할을 하면서도 조금 다른 특징을 갖고 있습니다. 추상 클래스는 특정 클래스의 상속 대상이 되는 클래스이며 좀 더 상위 레벨에서 속성, 메서드의 모양을 정의합니다.
```ts
abstract class Developer {
  abstract coding(): void; // 'abstract'가 붙으면 상속 받은 클래스에서 무조건 구현해야 함
  drink(): void {
    console.log('drink sth');
  }
}

class FrontEndDeveloper extends Developer {
  coding(): void {
    // Developer 클래스를 상속 받은 클래스에서 무조건 정의해야 하는 메서드
    console.log('develop web');
  }
  design(): void {
    console.log('design web');
  }
}
const dev = new Developer(); // error: cannot create an instance of an abstract class
const josh = new FrontEndDeveloper();
josh.coding(); // develop web
josh.drink(); // drink sth
josh.design(); // design web
```

# 제네릭
## 제네릭의 사전적 정의
제네릭은 C#, Java 등의 언어에서 재사용성이 높은 컴포넌트를 만들 때 자주 활용되는 특징입니다. 특히, 한가지 타입보다 여러 가지 타입에서 동작하는 컴포넌트를 생성하는데 사용됩니다.

## 제네릭의 한 줄 정의와 예시
제네릭이란 타입을 마치 함수의 파라미터처럼 사용하는 것을 의미합니다.
```ts
function getText(text) {
    return text
}
```
위 함수는 text 라는 파라미터에 값을 넘겨 받아 text를 반환해줍니다. hi, 10, true 등 어떤 값이 들어가더라도 그대로 반환합니다.

여기에서 제네릭을 살펴봅시다.
```ts
function getText<T>(text: T) : T {
    return text;
}
```
위 함수는 제네릭 기본 문법이 적용된 형태입니다. 이제 함수를 호출할 때 아래와 같이 함수 안에서 사용할 타입을 넘겨줄 수 있습니다.
```ts
getText<string>('hi');
getText<number>(10);
getText<boolean>(true);
```
위 코드 중 getText&#60;string&#62;('hi')를 호출 했을 때 함수에서 제네릭이 어떻게 동작하는지 살펴봅시다.
```ts
function getText<string>(text: T) : T {
    return text;
}
```
먼저 위 함수에서 제네릭 타입이 &#60;string&#62;이 되는 이유는 getText() 함수를 호출할 때 제네릭 값으로 string을 넘겼기 때문입니다.
```ts
getText<string>();
```
그리고 나서 함수의 인자로 hi라는 값을 넘기게 되면 getText 함수는  타입을 정의한 것과 같습니다.  
입력 값의 타입이 string이면서 반환 값 타입도 string 이어야 합니다.

## 제네릭을 사용하는 이유
```ts
function logText(text: string): string {
    return text;
}
```
위 코드는 인자를 하나 넘겨 받아 반환해주는 함수입니다. 마치 리눅스의 echo 명령어와 같은 역할을 합니다. 여기서 이 함수의 인자와 반환 값은 모두 string 으로 지정되어 있지만 만약 여러 가지 타입을 허용하고 싶다면 any를 사용할 수 있습니다.

타입을 바꾼다고 해서 함수의 동작에 문제가 생기진 않습니다. 다만, 함수의 인자로 어떤 타입이 들어갔고 어떤 값이 반환되는지는 알 수가 없습니다. 왜냐하면 any라는 타입은 타입 검사를 하지 않기 때문입니다.

이러한 문제점을 해결할 수 있는 것이 제네릭입니다.
```ts
function logText<T>(text: T) : T {
    return text;
}
```
먼저 함수의 이름 바로 뒤에 &#60;T&#62;라는 코드를 추가했습니다. 그리고 함수의 인자와 반환 값에 모두 T라는 타입을 추가합니다. 이렇게 되면 함수를 호출할 때 넘긴 타입에 대해 타입스크립트가 추정할 수 있게 됩니다. 따라서, 함수의 입력값에 대한 타입과 출력 값에 대한 타입이 동일한지 검증할 수 있게 됩니다.

그리고 선언한 함수는 2가지 방법으로 호출할 수 있습니다.
```ts
const text = logText<string>("Hello Generic");
const text = logText("Hello Generic");
```
보통 두 번째 방법이 코드도 더 짧고 가독성이 좋기 때문에 흔히 사용됩니다. 그렇지만 만약 복잡한 코드에서 두 번째 코드로 타입 추정이 되지 않는다면 첫 번째 방법을 사용하면 됩니다.

## 제네릭 타입 변수
앞에서 배운 내용으로 제네릭을 사용하기 시작하면 컴파일러에서 인자에 타입을 넣어달라는 경고를 보게 됩니다.
```ts
function logText<T>(text: T) : T {
    return text;
}
```
만약 여기서 함수의 인자로 받은 값의 length를 확인하고 싶다면
```ts
function logText<T>(text: T) : T {
    console.log(text.length);
    return text;
}
```
이렇게 작성할 것입니다. 위 코드를 변환하려고 하면 컴파일러에서 에러를 발생시킵니다. 왜냐하면 text에 .length가 있다는 단서는 어디에도 없기 때문입니다.

제네릭 코드의 의미를 살펴보면 함수의 인자와 반환 값에 대한 타입을 정하진 않았지만, 입력 값으로 어떤 타입이 들어왔고 바환 값으로 어떤 타입이 나가는지 알 수 있습니다. 따라서, 함수의 인자와 반환 값 타입에 마치 any를 지정한 것과 같은 동작을 한다는 것을 알 수 있습니다. 그래서 설령 인자에 number 타입을 넘기더라도 에러가 나진 않습니다.이러한 특성 때문에 현재인자인 text에 문자열이나 배열이 들어와도 아직은 컴파일러 입장에서 .length를 허용할 순 없습니다. 왜냐하면 number가 들어왔을 때는 .length 코드가 유효하지 않기 때문입니다.  
그래서 이런 경우에는 제네릭 타입을 줄 수가 있습니다.

```ts
function logText<T>(text: T[]) : T[] {
    console.log(text.length);
    return text;
}
```
위 코드가 기존의 제네릭 코드와 다른 점은 인자의 T[] 부분입니다. 이 제네릭 함수 코드는 일단 T 라는 변수 타입을 받고, 인자 값으로는 배열 형태의 T를 받습니다 예를 들면, 함수에 [1,2,3] 처럼 숫자로 이뤄진 배열을 받으면 반환 값으로 number를 돌려주는 것입니다. 이런 방시긍로 제네릭을 사용하면 꽤 유연한 방식으로 함수의 타입을 정의해줄 수 있습니다.

## 제네릭 타입 
제네릭 인터페이스에 대해 알아보겠습니다.
```ts
function logTexT<T>(text: T): T {
    return text;
}
let str : <T>(text: T) => T = logText;
let str: {<T>(text: T) : T} = logText;
```
위와 같은 변형 방식으로 제네릭 인터페이스 코드를 작성할 수 있습니다. 그리고 같은 방식으로 제네릭 인터페이스 뿐만 아니라 클래스도 생성할 수 있습니다. 다만. 이넘과 네임스페이스는 제네릭으로 생성할 수 없습니다.

## 제네릭 클래스 
제네릭 클래스를 선언할 때 클래스 이름 오른쪽에 &#60;T&#62;를 붙여줍니다. 그리고 해당 클래스로 인스턴스를 생성할 때 타입에 어떤 값이 들어갈 지 지정하면 됩니다.

위에서 살펴본 인터페이스처럼 제네릭 클래스도 클래스 안에 정의된 속성들이 정해진 타입으로 잘 동작하게 보장할 수 있습니다.

## 제네릭 제약 조건
인자의 타입에 선언한 T가 아직 어떤 타입인지 구체적으로 정의되지 않았을 때 length 코드에서 오류가 납니다. 이럴 때 만약 해당 타입을 정의하지 않고도 length 속성 정도는 허용하려면
```ts
interface LengthWise {
    length: number;
}

function logText<T extends LengthWise>(text : T): T {
    console.log(text.length);
    return text;
}
```
위와 같이 작성하게 되면 타입에 대한 강제는 아니지만 length에 대해 동작하는 인자만 넘겨받을 수 있습니다.

### 객체의 속성을 제약하는 방법
두 객체를 비교할 때도 제네릭 제약 조건을 사용할 수 있습니다.
```ts
function getProperty<T, O extends keyof T>(obj: T, key : O) {
    return obj[key];
}
let obj = {a : 1, b : 2, c : 3};

getProperty(obj, "a");
getProperty(obj, "z");
```
제네릭을 선언할 때 <O extends keyof T> 부분에서 첫 번째 인자로 받는 객체에 없는 속성들은 접근할 수 없게끔 제한하였습니다.

# 타입 추론
타입 추론이란 타입스크립트가 코드를 해석해 나가는 동작을 의미합니다.

## 타입 추론의 기본
타입 스크립트가 타입 추론을 해나가는 과정은 다음과 같습니다.
```ts
let x = 3;
```
위와 같이 x에 대한 타입을 따로 지정하지 않더라도 일단 x는 number로 간주됩니다. 이렇게 변수를 선언하거나 초기화 할 때 타입이 추론됩니다. 이외에도 변수, 속성, 인자의 기본 값, 함수의 반환 값 등을 설정할 때 타입 추론이 일어납니다.

## 가장 적절한 타입 
타입은 보통 몇 개의 표현식을 바탕으로 타입을 추론합니다. 그리고 그 표현식을 이용하여 가장 근접한 타입을 추론하게 되는데 이 가장 근접한 타입을 Best Common Type이라고 합니다.

```ts
let arr = [0, 1, null]
```
위 변수 arr의 타입을 추론하기 위해서는 배열의 각 아이템을 살펴봐야 합니다. 배열의 각 아이템의 타입은 크게 number와 null로 구분됩니다. 이 때 Best Common Type 알고리즘으로 다른 타입들과 가장 잘 호환되는 타입을 선정합니다.

## 문맥상의 타이핑
타입스크립트에서 타입을 추론하는 또 하나의 방식은 바로 문맥상으로 타입을 결정하는 것입니다. 이 문맥상의 타이핑은 코드의 위치를 기준으로 일어납니다.

```ts
window.onmousedown = function(mouseEvent) {
    console.log(mouseEvent.button);
    console.log(mouseEvent.kangaroo)
}
```
위 코드를 타입스크립트 검사의 관점에서 보면 window.onmousedown에 할당되는 함수의 타입을 추론하기 위해 window.onmousedown 타입을 검사합니다. 타입 검사가 끝나고 나며 함수의 타입이 마우스 이벤트와 연관이 있다고 추론하기 때문에 mouseEvent 인자에 button 속성은 있지만 kangaroo 속성은 없다고 결론을 내립니다.

```ts
window.onscroll = function(uiEvent) {
    console.log(uiEvent.button);
    console.log(uiEvent.kangaroo);
}
```
앞의 예제와 마찬가지로 오른쪽의 함수는 window.onscroll에 할당되었기 때문에 함수의 인자 uiEvent는 UIEvent로 간주됩니다. 그래서 위에서 봤던 MouseEvent와 다르게 button 속성이 없다고 추론합니다. 따라서 uiEvent.button에서 에러가 납니다.  
여기서 만약 문맥상 타이핑을 좀 더 이해하고자 한다면 
```ts
const handler = function(uiEvent) {
    console.log(uiEvent.button);
}
```
오른쪽 함수 표현식이 앞의 에제와 동일하지만 함수가 할당되는 변수만으로는 타입을 추정하기 어렵기 때문에 아무 에러가 나지 않습니다

## 타입스크립트의 타입 체킹
타입 체킹에 있어서 타입스크립트의 지향점은 타입 체크는 값의 형태에 기반하여 이루어져야 한다는 점입니다. 이걸 Duck Typing 또는 Structural Subtyping이라고 합니다.
- Duck Typing : 객체의 변수 및 메서드의 집합이 객체의 타입을 결정하는 것을 의미, 동적 타이핑의 한 종류
- Structural Subtyping : 객체의 실제 구조나 정의에 따라 타입을 결정하는 것을 의미합니다.

# 타입 호환
타입호환이란 타입스크립트 코드에서 특정 타입이 다른 타입에 잘 맞는지를 의미합니다. 예를 들어서 
```ts
interface Ironman {
    name: string;
}
class Avengers {
    name: string;
}

let i : Ironman;
i = new Avengers;
```
C#이나 Java였다면 위 코드에서 에러가 날 것입니다. Avengers 클래스가 명시적으로 Ironman 인터페이스를 상속받아 구현하지 않았기 때문입니다.

하지만 위와 같은 코드가 타입스크립트에서는 정상적으로 동작하는데, 기본적으로 자바스크립트는 객체 리터럴이나 익명 함수 등을 사용하기 때문에 명시적으로 타입을 지정하는 것보다는 코드의 구조 관점에서 타입을 지정하는 것이 더 잘 어울립니다.

## 구조적 타이핑 예시
구조적 타이핑이란 코드 구조 관점에서 타입이 서로 호환되는지의 여부를 판단하는 것입니다.
```ts
interface Avengers {
    name: string;
}
let hero: Avengers;
// 타입 스크립트가 추론한 y의 타입은 {name: string; location: string;}입니다.
let capt = {name: "Captain", location: "Pangyo"};
hero = capt;
```
위 코드에서 capt가 hero 타입에 호환될 수 있는 이유는 capt의 속성 중에 name이 있기 때문입니다. 
Avengers 인터페이스에서 name 속성을 갖고 있기 때문에 capt는 Aventers 타입에 호환될 수 있습니다.  
함수를 호출할 때도 마찬가지 입니다.
```ts
function assemble(A: Avengers) {
    console.log("어벤져스 모여~", a.name);
}
assemble(capt);
```
capt 변수에 이미 name 속성 뿐만 아니라 location 속성도 있기 때문에 assemble 함수의 호출 인자로 넘길 수 있습니다.

## Soundness란 ?
타입스크립트는 컴파일 시점에서 타입을 추론할 수 없는 특정 타입에 대해서 일단 안전하다고 보는 특성이 있습니다. 이걸 "들리지 않는다"라고 표현합니다.

## Enum 타입 호환 주의 사항
이넘 타입은 number 타입과 호환되지만 이넘 타입끼리는 호환되지 않습니다
```ts
enum Status { Ready, Waiting};
enum Color { Red, Blue, Green};

let status = Status.Ready;
status = Color.Green;
```
## Class 타입 호환 주의 사항
클라스 타입은 클래스 타입끼리 비교할 때 스태틱 멤버와 생성자를 제외하고 속성만 비교합니다.
```ts
class Hulk {
    handSize: number;
    constructor(name: string, numHand: number) {}
}
class Captain {
    handSize: number;
    constructor(numHand: number) {}
}
let a: Hulk;
let s: Captain;

a = s;
s = a;
```
### Generics
제네릭은 제네릭 타입 간의 호환 여부를 판단할 때 타입 인자 &#60;T&#62;가 속성에 할당 되었는지를 기준으로 합니다. 
```ts
interface Empty<T> {
}
let x: Empty<number>;
let y: Empty<string>;
x = y;
```
위 인터페이스는 일단 속성이 없기 때문에 x와 y는 같은 타입으로 간주됩니다. 그런데 만약 아래 코드와 같이 인터페이스에 속성이 있어서 제네릭의 타입 인자가 속성에 할당된다면 얘기는 다릅니다.
```ts
interface NotEmpty<T> {
    data: T;
}
let x : NotEmpty<number>;
let y : NotEmpty<string>;

x = y;
```
인터페이스 NotEmpty에 넘긴 제네릭 타입 <T>이 data 속성에 할당되었으므로 x 와 y는 서로 다른 타입으로 간주됩니다.

# 타입 별칭
타입 별칭은 특정 타입이나 인터페이스를 참조할 수 있는 타입 변수를 의미합니다. 
```ts
const name: string = 'capt';

type MyName = string;
const name: MyName = 'capt';
```
위와 같이 string, number와 같은 간단한 타입 뿐만 아니라 interface 레벨의 복잡한 타입에도 별칭을 부여할 수 있습니다.
```ts
type Developer = {
    name: string;
    skill: string;
}
```
타입 별칭에 제네릭도 사용할 수 있습니다.
```ts
type User<T> = {
    name : T
}
```

## 타입 별칭의 특징
타입 별칭은 새로운 타입 값을 하나 생성하는 것이 아니라 정의한 타입에 대해 나중에 쉽게 참고할 수 있게 이름을 부여하는 것과 같습니다. 이러한 특징은 vscode 상의 프리뷰 상태로 다른 타입과 어떤 차이점이 있는지 확인해볼 수 있습니다.

## type vs interface
타입 별칭과 인터페이스의 가장 큰 차이점은 타입의 확장 가능 / 불가능 여부입니다. 인터페이스는 확장이 가능한데 반해 타입 별칭은 확장이 불가능 합니다. 가능한한 type 보다는 interface로 선언해서 사용하는 것을 추천합니다.

# 모듈
타입스크립트에서 모듈이라는 개념은 ES6+의 Modules 개념과 유사합니다. 모듈은 전역 변수와 구분되는 자체 유효 범위를 가지며 export, import 와 같은 키워드를 사용하지 않으면 다른 파일에서 접근할 수 없습니다.

## Export
ES6의 export와 같은 방식으로 변수, 함수, 타입, 인터페이스 등에 붙여 사용합니다.
```ts
export interface Triangle {
    width : number;
    height : number;
}

import { Triangle } from './math.ts';

class SomeTriangle implements Triangle {

}
```

## Import
ES6의 import 와 동일한 방식으로 사용합니다.
```ts
import { WheatBeerClass } from './index.js';

class Cloud extends WheatBeerClass {

}
```

## 타입스크립트는 모듈 코드를 어떻게 변환해주는가 ?
tsconfig.json 파일에 설정한 컴파일러 모드에 따라 모듈 코드가 각기 다르게 변환됩니다.

SimpleModule.ts
```ts
import m = require("mod")
export let t = m.something + 1;
```
AMD / RequireJS SimpleModule.js
```js
define(["require","exports","./mod"], function (require, exports, mod_1) {
    exports.t = mode_1.something + 1;
})
```
CommonJS / Node SimpleModule.js
```js
var mode_1 = require("./mod");
exports.t = mode_1.something + 1;
```
UMD SimpleModule.js
```js
(function (factory) {
  if (typeof module === "object" && typeof module.exports === "object") {
    var v = factory(require, exports); if (v !== undefined) module.exports = v;
  }
  else if (typeof define === "function" && define.amd) {
    define(["require", "exports", "./mod"], factory);
  }
})(function (require, exports) {
  var mod_1 = require("./mod");
  exports.t = mod_1.something + 1;
});
```
System SimpleModule.js
```js
System.register(["./mod"], function(exports_1) {
  var mod_1;
  var t;
  return {
    setters:[
      function (mod_1_1) {
        mod_1 = mod_1_1;
      }],
    execute: function() {
      exports_1("t", t = mod_1.something + 1);
    }
  }
});
```
타입 스크립트 컴파일 명령어를 칠 때 컴파일러 모드를 부여할 수 있습니다.
```
tsc --module commonjs Test.ts

tsc --module amd Test.ts
```

# d.ts 파일 
## 타입스크립트 선언 파일
타입스크립트 선언 파일 d.ts는 타입스크립트 코드의 타입 추론을 돕는 파일입니다. 예를 들어, 전역 변수로 선언한 변수를 특정 파일에서 import 구문 없이 사용하는 경우 해당 변수를 인식하지 못합니다. 그럴 때 아래 코드와 같이 해당 변수를 선언해서 에러가 나지 않게 할 수 있습니다.
```ts
declare const global = 'sth';
```

## 전역 변수와 전역 함수에 대한 타입 선언
해당 타입 스크립트 파일에서 사용할 순 있지만 선언되어 있지 않은 전역 변수나 전역 함수는 아래 코드와 같이 선언할 수 있습니다.
```ts
// 전역 변수
declare const pi = 3.14;

// 전역 함수
declare namespace myLib {
    function greet(person: string): string;
    let name: string;
}
myLib.greet('캡틴');
myLib.name = '타노스';
```

# 인덱싱
## 타입스크립트에서 배열과 객체를 인덱싱 하는 방법
타입스크립트에서 배열 요소와 객체의 속성을 접근할 때는 인터페이스를 사용하면 됩니다.

## 배열 요소 접근
자바스크립트에서는 아래 코드와 같이 배열의 요소를 접근했습니다.
```js
const arr = ['Thor', 'Hulk'];
arr[0];
```
타입스크립트에서는 인터페이스를 이용하여 아래 코드와 같이 인덱싱 타입을 정의할 수 있습니다.
```ts
interface StringArray {
    [index: number]: string;
}

const arr: StringArray = ['Thor', 'Hulk'];
arr[0];
```
배열의 인덱스의 타입은 숫자이고 이 인덱스로 특정 요소를 접근했을 때 해당 요소의 타입은 string이라는 것을 명시했습니다.

## 타입으로 배열 변경 제한하기
배열에 강한 타입을 적용함과 동시에 배열의 요소를 변경하지 못하게 하려면
```ts
interface ReadonlyStringArray {
    readonly [index: number] : string;

}
const arr: ReadonlyStringArray = ['Thor', 'Hulk'];
arr[2] = 'Capt';
```
이렇게 합니다.

# 유틸리티 타입
유틸리티 타입은 이미 정의해 놓은 타입을 변환할 때 사용하기 좋은 타입 문법입니다. 유틸리티 타입을 꼭 쓰지 않더라도 기존의 인터페이스, 제네릭 등의 기본 문법으로 충분히 타입을 변환할 수 있지만 유틸리티 타입을 쓰면 훨씬 더 간결한 문법으로 타입을 정의할 수 있습니다.

## 자주 사용되는 유틸리티 타입 몇 개 알아보기 
### Partial
파셜 타입은 특정 타입의 부분 집합을 만족하는 타입을 정의할 수 있습니다.
```ts
interface Address {
    email : string;
    address : string;
}

type MayHaveEmail = Partial<Address>;
const me: MayHaveEmail = {}
const you: MayHaveEmail = {email: 'test@abc.com'};
const all: MayHaveEmail = { email: 'capt@hero.com', address : 'Pangyou'};
```

### Pick
픽 타입은 특정 타입에서 몇 개의 속성을 선택하여 타입을 정의할 수 있습니다.
```ts
interface Hero {
    name: string;
    skill: string;
}
const human: Pick<Hero, 'name'> = {
    name: '스킬이 없는 사람'
};
```
```ts
type HasThen<T> = Pick<Promise<T>, 'then' | 'catch'>;
let hasThen: HasThen<number> = Promise.resolve(4);
hasThen.th
```

### Omit
오밋 타입은 특정 타입에서 지정된 속성만 제거한 타입을 정의해줍니다.
```ts
interface AddressBook {
    name: string;
    phone: number;
    address: string;
    company: string;
}
const phoneBook: Omit<AddressBook, 'address'> = {
    name: '재택근무',
    phone: 12342223333,
    company : '내 방'
}
const chingtao: Omit<AddressBook, 'address'|'company'> = {
    name : '중국집',
    phone: 44455557777
}
```

# 맵드 타입
맵드 타입이란 기존에 정의되어 있는 타입을 새로운 타입으로 변환해 주는 문법을 의미합니다. <br> 마치 자바스크립트 map() API 함수를 타입에 적용한 것과 같은 효과를 가집니다.

## 자바스크립트의 map 함수란?
자바스크립트의 map API는 배열을 다룰 때 유용한 자바스크립트 내장 API입니다.
```js
var arr = [{id : 1, title: '함수'}, {id : 2, title: '변수'}, {id : 3, title : '인자'}];
var result = arr.map(function(item) {
    return item.title;
});
console.log(result);
```
위 코드는 3개의 객체를 요소로 가진 배열 arr 에 .map() API를 적용한 코드입니다. <br> 배열의 각 요소를 순회하여 객체(id, title)에서 문자열로 변환하였습니다.

## 맵드 타입의 기본 문법
맵드 타입은 위에서 살펴본 자바스크립트의 map 함수를 타입에 적용했다고 보면 됩니다. <br> 이를 위해서는 아래 코드와 같은 형태의 문법을 사용해야 합니다.
```ts
{ [ P in K] : T}
{ [ P in K] ? : T}
{readonly [ P in K ] : T}
{readonly [ P in K ] ? : T}
```

## 맵드 타입 기본 예제
맵드 타입의 간단한 예제를 보겠습니다. 아래와 같이 헐크, 토르, 캡틴을 유니온 타입으로 묶어주는 Heroes 라는 타입이 있다고 하겠습니다.
```ts
type Heroes = 'Hulk' | 'Thor' | 'Capt';
```
여기서 이 세 영웅의 이름에 각각 나이까지 붙인 객체를 만들고 싶다고 한다면 아래와 같이 변환할 수 있습니다.
```ts
type HeroProfiles = { [K in Heroes] : number}
const heroInfo: HeroProfiles = {
    Hulk : 54,
    Thor : 1000,
    Capt : 33
}
```
위 코드에서 [K in Heroes] 부분은 마치 자바스크립트의 for in 문법과 유사하게 동작합니다.<br>앞에서 정의한 Heroes 타입의 3개의 문자열을 각각 순회하여 number 타입을 값으로 <br>가지는 객체의 키로 정의가 됩니다.   
따라서 위의 원리가 적용된 HeroProfiles 의 타입은 아래와 같이 정의됩니다.
```ts
type HeroProfiles = {
    Hulk : number;
    Thor : number;
    Capt : number;
}
```

## 맵드 타입 실용 예제
위에서 살펴본 예제는 맵드 타입의 문법과 동작을 이해하기 위해 간단한 코드를 사용했습니다. <br>실제로 서비스를 개발할 때는 위와 같은 코드보다는 아래와 같은 코드를 더 많이 사용하게 됩니다.
```ts
type Subset<T> = {
    [K in keyof T]? : T[K];
}
```
위 코드는 키와 값이 있는 객체를 정의하는 타입을 받아 그 객체의 부분 집합을 만족하는 타입으로 변환해주는 문법입니다.
예를 들면 만약 아래와 같은 인터페이스가 있다고 할때
```ts
interface Person {
    age : number;
    name : string;
}
```
위 Subset 타입을 적용하면 아래와 같은 객체를 모두 정의할 수 있습니다.
```ts
const ageOnly : Subset<Person> = { age : 23};
const nameOnly : Subset<Person> = { name : 'Tony'};
const ironman : Subset<Person> = { age : 23, name : 'Tony'}
const empty : Subset<Person> = {};
```

## 맵드 타입 실용 예제 2
아래와 같이 사용자 프로필을 조회하는 API 함수가 있다고 했을 때 
```ts
interface UserProfile {
    username : string;
    email: string;
    profilePhotoUrl: string;
}
function fetchUserProfile() : UserProfile {

}
```
이 프로필의 정보를 수정하는 API는 아마 아래와 같은 형태일 것입니다.
```ts
interface UserProfile Update {
    username? : string;
    email? : string;
    profilePhotoUrl? : string;
}
function updateUserProfile(parmas: UserProfileUpdate) {

}
```
이 때 아래와 같이 동일한 타입에 대해서 반복해서 선언하는 것을 피해야 합니다.
```ts
interface UserProfile {
    username : string;
    email : string;
    profilePhotoUrl: string;
}

interface UserProfileUpdate {
    username? : string;
    email? : string;
    profilePhotoUrl? : string;
}
```
위의 인터페이스에 반복되는 구조를 아래와 같은 방식으로 재활용 할 수 있습니다.
```ts
type UserProfileUpdate = {
    username? : UserProfile['username'];
    email? : UserProfile['email'];
    profilePhotoUrl? : UserProfile['profilePhotoUrl'];
}
```
혹은 좀 더 줄여서 아래와 같이 정의할 수도 있습니다.
```ts
type UserProfileUpdate = {
    [p in 'username' | 'email' | 'profilePhotoUrl']? : UserProfile[p]
}
```
여기서 keyof를 적용하면 더 줄일 수 있습니다.
```ts
type UserProfileUpdate = {
    [p in keyof UserProfile]? : UserProfile[p]
}
```

# tsconfig
## 타입스크립트 설정 파일
타입스크립트 설정 파일은 타입스크립트를 자바스크립트로 변환할 때의 설정을 정의해놓는 파일입니다. <br> 프로젝트에서 tsc라는 명령어를 치면 타입스크립트 설정 파일에 정의된 내용을 기준으로 변환 작업을 진행합니다.

## tsc 명령어
tsc 명령어는 타입스크립트를 자바스크립트로 변환할 때 사용하는 명령어입니다.<br>  아래와 같이 입력하면 app.ts 파일이 app.js로 변환됩니다.
```
tsc app.ts
```

## 타입스크립트 설정 파일 인식 기준
tsc 명령어를 대상 파일을 지정하지 않고 실행하면 현재 폴더에 있는 타입스크립트 설정 파일을 기준으로 <br> 변환 작업을 수행합니다. 만약 현재 폴더에 타입스크립트 설정 파일이 없습니다면 프로젝트 폴더 내에서<br> 상위 폴더의 경로를 검색해 나갑니다.

tsconfig.json 파일의 경로를 명시적으로 지정하고 싶다면 아래 명령어를 사용하면 됩니다.
```
# 형식 예시
tsc -- project 상대 경로

tsc --project ./src
tsc -p ./src
```

## 타입스크립트 설정 파일 속성
### files
타입스크립트 변환 명령어를 입력할 때마다 대상 파일의 경로를 지정하지 않고 설정 파일에 <br> 미리 정의해놓을 수 있습니다.
```ts
{
    "files": ["app.ts", "./utils/main.ts"]
}
```
### include
files와 같이 파일을 개별로 지정하지 않고 include 옵션으로 변환할 폴더를 지정할 수 있습니다.
```ts
{
    "include": ["src/**/*"]
}
```
와일드 카드 패턴
- '*' : 해당 디렉토리의 모든 파일 검색
- '?' : 해당 디렉토리 안에 파일의 이름 중 한 글자라도 맞으면 해당
- '**' : 하위 디렉토리를 재귀적으로 접근

### exclude
include와 반대되는 속성으로 변환하지 않을 폴더 경로를 지정합니다.
```ts
{
    "exclude" : ["node_modules"]
}
```

### @types 라이브러리와 typeRoots
타입스크립트 설정 파일은 기본적으로 node_modules를 제외하지만 써드 파티 라이브러리의 타입을 정의해놓는 @types 폴더는 컴파일에 포함됩니다.

### extends
특정 타입스크립트 설정 파일에서 다른 타입스크립트 설정의 내용을 가져와 추가할 수 있는 속성입니다.
```ts
// config/base.json
{
    "compilerOptions" : {
        "noImplicitAny" : true
    }
}
```
```ts
// tsconfig.json
{
    "extends" : "./config/base"
}
```
확장한 파일의 내용을 가져다가 덮어쓰거나 새로 정의할 수도 있습니다.

### target
타입스크립트 파일을 컴파일 했을 때 빌드 디렉토리에 생성되는 자바스크립트의 버전을 의미합니다. <br> 기본 값인 es3 부터 es5, es6 등 가장 최신 버전인 esnext까지 있습니다.
```ts
//tsconfig.json
{
    "target" : "esnext"
}
```

### lib
타입스크립트 파일을 자바스크립트로 컴파일 할 때 포함될 라이브러리의 목록입니다.<br>이 속성을 활용하는 가장 대표적인 사례는 async 코드를 컴파일 할 때 Promise 객체가 필요하므로<br> 아래와 같은 설정을 해줘야 합니다.
```ts
//tsconfig.json
{
    "lib": ["es2015", "dom", "dom.iterable"]
}
```
여기서 es2015는 프로미스 객체를 타입스크립트에서 인식할 수 있게 필요한 속성이고,<br>dom 관련 속성은 DOM API를 사용하는 경우 필요합니다.

### allowJs
타입스크립트 컴파일 작업을 진행할 때 자바스크립트 파일도 포함될 수 있는지를 설정해주는 속성입니다.<br>주로 이미 기존에 존재하는 자바스크립트 프로젝트에 타입스크립트를 점진적으로 적용할 때 사용하면 <br> 좋은 속성입니다.

# Types 라이브러리
## @types 라이브러리란?
자바스크립트로 만들어진 써드 파티 라이브러리를 타입스크립트에서 사용하려면 각 기능에 대한 타입이 <br>정의되어 있어야 합니다. 그 이유는 제이쿼리 라이브러리의 내부 코드에 대한 타입이 정의되어 있지 않아 <br>이 라이브러리를 들고 와서 사용할 때 타입 스크립트 파일에서 타입 추론을 할 수 없기 때문입니다.<br>이런 경우에는 @types라는 라이브러리를 설치하면 됩니다.
```
npm i -D @types/jquery
```
대중적으로 흔히 사용되는 자바스크립트 라이브러리는 대부분 @types 라는 별칭으로 타입스크립트 추론이 가능한 보조 라이브러리를 제공합니다. 만약 이 라이브러리가 없는 경우에는 스스로 선언하거나 다른방법을 찾아봐야 합니다.

## @types 라이브러리의 내부 구조
types 라이브러리는 일반적으로 index.d.ts 파일과 package.json 파일로 구성되어 있습니다.  
package.json 파일 안에는 types 속성이 정의되어 있습니다.

# 타입
타입스크립트에서의 타입이라는 개념은 크게 아래 5가지 범주를 의미합니다.
- 타입 별칭 type sn = number | string;
- 인터페이스 interface I { x : number[]; }
- 클래스 class C { }
- 이넘 enum E { A, B, C}
- 타입을 가리키는 import 구문

# JS 에 TS 적용하기
자바스크립트 코드에 타입스크립트를 적용할 때 주의해야 할 점
- 기능적인 변경은 절대 하지 않을 것
- 테스트 커버리지가 낮을 떈 함부로 타입스크립트를 적용하지 않을 것
- 처음부터 타입을 엄격하게 적용하지 않을 것 

## 자바스크립트 프로젝트에 타입스크립트 적용하는 절차
- 타입 스크립트 환경 설정 및 ts 파일로 변환
- any 타입 선언
- any 타입을 더 적절한 타입으로 변경

1. 타입스크립트 프로젝트 환경 구성
- 프로젝트 생성 후 NPM 초기화 명령어로 package.json 파일을 생성합니다.
- 프로젝트 폴더에서 npm i typescript -D 로 타입스크립트 라이브러리를 설치합니다.
- 타입스크립트 설정 파일 tsconfig.json 을 생성하고 기본 값을 추가합니다.
```json
{
    "compilerOptions" : {
        "allowJs": true,
        "target" : "ES5",
        "outDir" : "./dist",
        "moduleResolution" : "Node",
        "lib" : ["ES2015", "DOM", "DOM.Iterable"]
    },
    "include": ["./src/**/*"],
    "exclude" : ["node_modules", "dist"]
}
```
- 서비스 코드가 포함된 자바스크립트 파일을 타입스크립트 파일로 변환합니다.
- 타입스크립트 컴파일 명령어 tsc로 파일스크립트 파일을 자바스크립트 파일로 변환합니다.

2. 엄격하지 않은 타입 환경에서 프로젝트 돌려보기
- 프로젝트에 테스트 코드가 있습니다면 테스트 코드가 통과하는지 먼저 확인합니다.
- 프로젝트의 js파일을 모두 ts 파일로 변경합니다.
- 타입스크립트 컴파일 에러가 나는 것 위주로만 먼저 에러가 나지 않게 수정합니다.
  - 여기서, 기능을 사소하게라도 변경하지 않도록 주의합니다.
- 테스트 코드가 성공하는지 확인합니다.

3. 명시적인 any 선언하기
- 프로젝트 테스트 코드가 통과하는지 확인합니다.
- 타입스크립트 설정 파일에 noImplicitAny : true를 추가합니다.
- 가능한 타입을 적용할 수 있는 모든 곳에 타입을 적용합니다.
  - 라이브러리를 쓰는 경우 DefinitelyTyped에서 @types 관련 라이브러리를 찾아 설치합니다.
  - 만약, 타입을 정하기 어려운 곳이 있으면 명시적으로라도 any를 선언합니다.
- 테스트 코드가 통과하는지 확인합니다.

4. strict 모드 설정하기
- 타입스크립트 설정 파일에 아래 설정을 추가합니다.
```json
{
    "strict" : true,
    "strictNullChecks" : true,
    "strictFunctionTypes" : true,
    "strictBindCallApply" : true,
    "strictPropertyInitialization" : true,
    "noImplicitThis" : true,
    "alwaysStrict" : true
}
```
- any 로 되어 있는 타입을 최대한 더 적절한 타입으로 변환합니다.
- as 와 같은 키워드를 최대한 사용하지 않도록 고민해서 변경합니다.

<hr>

# Vue에서 타입스크립트 사용하기
- 내용 출처 : https://blog2.deliwind.com/categories/Front-end-VueJS/ <br>
vue-cli 2 버전에서는 ts를 지원하지 않았지만, 3버전대에서 공식적으로 지원합니다.

```
npm install -g @vue/cli
vue를 설치해주고 프로젝트 생성은
vue create project-name
이렇게 하면 됩니다.

원하는대로 설정해주고 나서 cd project-directory 로 해당 프로젝트 파일로 이동하고
npm run serve 하면 실행이 됩니다.
```

생성된 파일을 열어보게 되면 여러 폴더들이 보일 것입니다.
- src : 소스 폴더
- assets : static한 이미지 등의 파일 위치
- components : 컴포넌트들이 위치합니다. 화면을 구성하는 작은 조각들이라고 생각하면 됩니다.
- views : 컴포넌트들을 모아 구성되는 화면들의 view
- App.vue : 앱의 기본 컴포넌트
- main.ts : 앱이 호출되면 실행되는 엔트리 포인트입니다. 
- router.ts : 앱의 라우팅 설정이 들어간 파일입니다.
- store.ts : 앱의 상태관리를 담당하는 store입니다.
- package.json : 패키지매니저에서 관리하는 dependency 관련 설정 파일입니다.
- tsconfig.json : typescript 컴파일 관련 설정 파일입니다.

일단 클래스 스타일 컴포넌트를 좀 더 편하게 사용하기 위해 vue-property-decorato를 사용합니다.  
@Component 데코레이터 외에 우리가 자주 사용하게 될 @Prop, @Emit 등의 데코레이터를 제공합니다.

@Component 데코레이터 안에, 이 컴포넌트에서 사용하는 다른 컴포넌트들을 components: 라는 값으로 나열해 줍니다.  
추가해주었기 때문에 상단 플랫폼 영역에서 컴포넌트를 사용할 수 있게 됩니다.

HelloWorld.vue 파일을 열어보면
```vue
<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from "vue-property-decorator";

@Component
export default class HelloWorld extends Vue {
  @Prop() private msg!: string;
}
</script>
```
msg라고 선언한 변수 앞에 @Prop()이라는 데코레이터를 볼 수 있습니다. 바로 Prop이라는 데코레이터와 함께 변수를 선언 하면, 해당 변수 명의 prop을 바인딩 해서 받아올 수 있습니다.

## vue-property-decorator
vue-property-decorator는 vuejs에서 typescript로 개발할 때, 클래스 컴포넌트 스타일로 개발하기 쉽게 도와주는 데코레이터 들입니다. 제공해 주는 데코레이터들로는
- @Emit
- @Inject
- @Model
- @Prop
- @Provide
- @Watch
- @Component
가 있습니다. 이 중 @Component 데코레이터와 @Prop 데코레이터는 위에서 보았습니다.

이 데코레이터들 중 가장 많이 사용하는 Prop에 대해 알아보겠습니다.
### @Prop()
```ts
@Prop(options: (PropOptions | Constructor[] | Constructor) = {}) decorator
```
이런 형식으로 사용합니다. 코드로 보면
```ts
import { Vue, Component, Prop } from 'vue-property-decorator'

@Component
export default class YourComponent extends Vue {
  @Prop(Number) propA!: number
  @Prop({ default: 'default value' }) propB!: string
  @Prop([String, Boolean]) propC!: string | boolean
}
```
이 코드가 
```ts
export default {
  props: {
    propA: {
      type: Number
    },
    propB: {
      default: 'default value'
    },
    propC: {
      type: [String, Boolean]
    },
  }
}
```
이 코드와 동일합니다. default 옵션을 통해 기본값을 지정할 수도 있습니다.

### @Emit()
Emit 데코레이터는 자식 -> 부모 컴포넌트로 이벤트 바인딩을 할 때 주로 사용합니다.
```ts
@Emit(event? : string)
```
이런 형식으로 선언되며, Emit 하고자 하는 event 명을 string 형식으로 넣어 선언하면 됩니다.
```ts
@Emit('charge')
private onChange(value : string) {}
```
과 같이 선언하며, 'change'와 같이 emit 되는 이벤트 명을 넣지 않을 경우, 함수 명을 이벤트 명으로 사용하게 됩니다. (이벤트 명을 넣지 않아서 자동으로 선언된 경우에는 카멜 케이스의 함수명이 케밥형으로 변경되어 적용됩니다.)  
선언하는 함수의 args를 전달하고 싶은 데이터 형식으로 넣어 전달하면 됩니다.


### @Model()
Model 데코레이터는 @Prop 데코레이터 다음으로 많이 쓰이게 될 데코레이터입니다. Prop은 일반적으로 단방향 바인딩인데 반해, Model은 양방향 바인딩을 제공합니다.
```ts
@Model(event?: string, options: (PropOptions | Constructor[] | Constructor) = {}) decorator
```
이런 형식으로 선언됩니다.
```ts
import { Vue, Component. Model} from 'vue-property-decorator'

@Component
export default class Component extends Vue {
    @Model("change", {type : Boolean}) checked!: boolean
}
```
이 코드가 
```ts
export default {
  model: {
    prop: 'checked',
    event: 'change'
  },
  props: {
    checked: {
      type: Boolean
    },
  },
}
```
이 코드와 동일합니다.

### @Watch
@Watch 데코레이터는 값이 변경되는지를 감시하고 있습니다가, 변경 됐을 때 같이 선언한 함수를 실행시켜주는 데코레이터 입니다.
```ts
@Watch('count')
onChangeCount(val: number, oldVal : number) {
    //함수 내용
}
```
### 이외 데코레이터 (@Inject / @Provide)
Inject와 Provide는 서비스 주입 형태 사용시 사용되게 되는데, vuex 패턴으로 프로젝트를 디자인하게 될 경우, 사용빈도가 낮아져서 따로 설명은 없습니다. (참고 내용 저자)

@Inject  
단순히 수정에서 그치는 것이 아니라 연관된 추가 메서드들, 그리고 해당 클래스를 사용하는 외부의 코드까지도 전부 수정해야 될 경우에 이를 해결하는 좋은 방법 중 하나가 의존성 주입입니다.

inject 옵션은 문자열의 배열이거나 키가 로컬 바인딩 이름을 나타내는 객체이고 사용 가능한 주입에서 검색할 키 값이어야 합니다.

@Provide   
provide 옵션은 문자열의 배열이거나 키가 로컬 바인딩 이름을 나타내는 객체이고 사용 가능한 주입에서 검색할 키 값이어야 합니다.
- provide와 inject 바인딩은 반응형이 아닙니다. 이는 의도적한 것입니다. 그러나 감시중인 객체를 전달하면 해당 객체의 속성은 계속 반응형입니다 (vue js 공식 문서)

### Vuex
VueJS를 사용하면서 상태를 관리하는 대표적인 방법은 Vuex를 사용하는 방법이 있습니다. 이 챕터에서는 Vuexfmf Typescript 로 사용하는 방법에 대해 알아보겠습니다.

vue-cli를 통해 프로젝트를 생성 하면서 vuex를 선택하게 되면 src/store.ts 라는 파일에 store가 정의가 됩니다.

이 store.ts 파일을 지우고, stores 폴더를 만들어 store를 각각 namespace 별로 모듈화 하여 분리하고, 각각 스토어 모듈은 클래스 형태로 정의해서 쓰도록 합니다.

이를 위해 vuex-module-decoratores와 vuex-class 라는 라이브러리를 사용합니다.

```
npm i vuex-module-decorators
npm i vuex-class
```
src 폴더 하단에 stores 폴더를 만들고, index.ts 파일을 새로 생성합니다.

그리고 기존 store.ts 파일을 import 하고 있던 main.ts 파일을 열어 바꾼다.  
import store from "./stores";

그리고 stores/index.ts 파일을 열어 내용을 넣습니다
```ts
import Vue from 'vue';
import Vuex from 'vuex';

Vue.use(Vuex);

export default new Vuex.Store({
  modules: {

  },
});
```

이렇게 하면 기본적인 세팅은 끝났다. 이제 원하는대로 모듈을 작성하고 Modules 밑에 넣어주기만 하면 됩니다.


```ts
import { Module, VuexModule, Mutation, Action } from 'vuex-module-decorators';

@Module({namespaced : true})
export default class CountStore extends VuexModule {
    public count: number = 0;

    get doubledCount() {
        return this.count * 2;
    }

    @Mutation
    public increment(delta : number) {
        console.log(`increment mutation: ${delta}`);
        this.count += delta;
    }

    @Action({ commit : 'increment'}) 
    public incr(delta : number) {
        console.log(`increment action : ${delta}`);
        return delta;
    }
}
```
state, getter, mutation, action 들은 위와 같은 형식으로 적용하여 사용합니다. 컴포넌트에서 incr 액션함수를 호출하면, 함수가 return 하는 delta라는 값으로 increment mutation을 commit 하게 됩니다. 결국 위에 선언한 incr이라는 이름의 Action은 아래 코드와 동일합니다.
```ts
actions : {
    incr : (context: any, delta : number) => {
        context.commit("increment", delta);
    }
}
```

stores/index.ts 파일을 열어 방금 만든 CountStore 모듈을 추가하는 구문을 넣어줍니다.

이제 CountStore 라는 StoreModule을 완성했습니다.  
이제 원하는 컴포넌트에서 CountStore를 연결하여 사용합니다.

```ts
<template>
  <div class="home">
    <p>{{count}} * 2 = {{doubledCount}}</p>
    <button @click="incr(1)">incr</button>
  </div>
</template>

<script lang="ts">
import { Component, Vue, Watch } from 'vue-property-decorator';
import { namespace, State, Action, Getter } from 'vuex-class';

const CountStoreModule = namespace('CountStore');

@Component
export default class Home extends Vue {

  @CountStoreModule.State('count')
  private count!: number;

  @CountStoreModule.Getter('doubledCount')
  private doubledCount!: number;

  @CountStoreModule.Action('incr')
  private incr!: (delta: number) => void;

}
</script>
```

CountStoreModule을 namespace를 통해 연결하고, state와 getter를 통해 상태값을 가져와 화면에 바인딩 합니다. 그리고 버튼을 incr 액션 생성함수와 연결해줍니다.

<hr>

# 에러 사항
간단하게 프로젝트를 하나 만들고 보니 겪었던 사항들이 몇가지 있는데 정리해보면

## ES Lint 귀찮음
코드를 작성하다보면 포맷을 지키지 않고 작성할 때가 있는데 이 도구가 자꾸 그 부분을 수정하라고 해서 했습니다..

## 내용이 빈 function 에러
내용 없이 값만 받는 function을 몇개 작성 하였는데 그 때마다 ES Lint에서 또 에러를 나오게 해서 검색을 해본 결과 세팅 json 부분에 "@typescript-eslint/no-unused-vars": "off" 이 옵션을 주게 되면 해결 할 수 있었습니다.

만들면서 느낀 것은 자바스크립트와는 확실히 달랐고, 좀 체계적으로 작성해야되는 부분에서 신경을 많이 쓰게 되었습니다.

## 공식 문서에서의 Vue Typescript
