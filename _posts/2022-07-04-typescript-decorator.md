---
title:  "타입스크립트 Decorator (@Decorator)"
search: false
categories: 
  - TypeScript
toc: true  
last_modified_at: 2022-06-29T18:06:00-05:00
tags:
  - TypeScript
  - JavaScript
  - Decorator
---

# 타입스크립트 데코레이터
안녕하세요. 전제 사원입니다.
타입스크립트 데코레이터 공부하면서 정리한 내용입니다.

- 데코레이터
- 데코레이터 팩토리
- 데코레이터 합성
- 데코레이터 평가
- 클래스 데코레이터
- 메서드 데코레이터
- 접근자 데코레이터
- 프로퍼티 데코레이터
- 매개변수 데코레이터
- 메타데이터
<hr>

## 데코레이터
데코레이터는 클래스 선언, Method, Accessor, property, parameter에   
첨부할 수 있는 특수한 종류의 선언입니다.
- @Expression 형식 [(at icon) + Function name]을 사용합니다.
<hr>

## 데코레이터 팩토리
데코레이터가 선언에 적용되는 방식을 원하는대로 바꾸고 싶을 때..  
그럴 때 작성하는 것으로 런타임에 호출할 표현식을 반환하는 함수 입니다.

```ts
function Apple(value: string) { //데코레이터 팩토리
  return function (target) { //데코레이터
    // 위에서 가져온 value와 target으로 어떠한 작업을 수행
  }
}
```
<hr>

## 데코레이터 합성
- 단일행 :   
    ```ts 
    @A @B function
    ```
- 다중행 :  
    ```ts
    @A
    @B
    function
    ```

위에서 선언한 @A @B function은 A(B(function))과 같습니다.
1. 위에서 아래로 평가하며, 
    ```
    A call
    B call
    ```
2. 아래에서 위로 함수를 호출합니다.
    ```
    B Complate
    A Complate
    ```
<hr>

## 클래스 데코레이터 (Class Decorator)
클래스 선언 직전에 선언 합니다.  
클래스 정의를 감시, 수정, 교체 하는데 사용 가능합니다.  
* 선언 파일이나 주변 컨텍스트에서 사용할 수 없습니다.
    - 컨텍스트 (Context) : 예) 선언 클래스

클래스 데코레이터는 적용하는 클래스의 생성자를 유일한 인수로 받습니다.
- constructor

클래스 데코레이터가 값을 반환하면 생성자 함수로 컨버팅 됩니다.

예제 Class
```ts
@classDecorator
class sealed {
  data: string;
  constructor(message : string) {
    this.data = message;
  }

  method() {
    return "Hello, " + this.data;
  }
}
```

예제 Decorator
```ts
function classDecorator(constructor: Function) {
  Object.seal(constructor);
  Object.seal(constructor.prototype);
}
```
- 예제에서는 Object.seal() 기능을 이용해서 객체를 밀봉하였습니다.
    - Object.seal() : 객체 밀봉
        - 새로운 속성 추가 x
        - 모든 속성을 설정 불가능 상태로 변경
        - [Object.freeze와 다른 점] 쓰기 가능한 속성의 값은 밀봉 후에도 변경 가능

<hr>

## 메서드 데코레이터 (Method decorator)
메서드 선언 직전에 선언합니다.  
메서드 정의를 감시, 수정, 교체 하는데 사용 가능합니다.  
* 선언 파일이나 주변 컨텍스트에서 사용할 수 없습니다.  

클래스 데코레이터와 달리 런타임에 세 개의 인수와 함께 함수로 호출됩니다.
1. 정적 멤버에 대한 클래스의 생성자 함수 또는 인스턴스 멤버에 대한 클래스의 프로토타입
2. 멤버의 이름
3. 멤버의 프로퍼티 설명자 (property descriptor)

메서드 데코레이터가 값을 반환하면, 메서드의 프로퍼티 설명자로 사용됩니다.

예제 Class
```ts
class example {
    a: string = "Hello";
    get b(): string {
        return `${this.a} World`;
    }
    @decorator
    method(c: string): void {
        console.log(c);
    }
}
```
예제 Decorator
```ts
function decorator() {
    return function (
        target: any,
        propertyKey: string, 
        descriptor: propertyDescriptor
    ) {
        console.log(target); // {b: [Getter], method: [Function (anonymous)]}
        console.log(propertyKey); // d
        console.log(descriptor); 
        /**
         * {
         *  value: [Function (anonymous)]
         *  writable: true,
         *  enumerable: true,
         *  configurable: true
         * }
         */ 
    }
}
```
@decorator 가 실행되면서 예제 코드의 console 결과가 나오는 것입니다.

@decorator를 좀 바꿔본다면
```ts
function decorator ( target: any, propertyKey: string, descriptor: PropertyDescriptor): void {
    const method = descriptor.value;
    descriptor.value = function () {
        try {
            method();   
        } catch (error) {
            console.log("error 핸들링 로직");
        }
    }
}
```
이렇게 바꾸고 반영해서 실행한다면!!

예제 class 중
```ts
...
@decorator
method(c: string): void {
    console.log(c);
    throw new Error();
}
...
//데코레이터
...
new example().method("오..");

```
console
```
오..
error 핸들링 로직
```

이라는 결과가 나온다.

<hr>

## 접근자 데코레이터 (Accessor Decorator)
접근자 선언 직전에 선언  
접근자 정의를 감시, 수정, 교체 하는데 사용 가능합니다.
* 문서 순서대로 지정한 첫 번째 접근자에 적용해야 합니다.
    - 각각의 선언이 아닌 결합한 property descriptor에 적용되기 때문입니다.

메서드 데코레이터와 같이 세개의 인수와 함께 함수로 호출합니다.  
접근자 데코레이터가 값을 반환하면 프로퍼티 설명자로 사용됩니다.

예제 class (typescript handbook 中)
```ts
class Point {
    private _x: number;
    private _y: number;

    constructor(x: number, y: number, public size: string = "100") {
        this._x = x;
        this._y = y;
    }

    @configurable(false)
    get x() { return this._x }

    @configurable(false)
    get y() { return this._x }
}
```

예제 decorator
```ts
function configurable(value: boolean) {
    return function (target: any, propertyKey: string, descriptor: PropertyDescriptor) {
        descriptor.configurable = value;
    }
}
```

```ts
// point 객체 인스턴스 생성
const point = new Point(100, 150);

// 속성 제거 가능
delete point.size;

// [오류] delete 연산자의 피연산자는 읽기 전용 속성일 수 없습니다.
delete point.x;

```
이 코드에서 생성된 객체 인스턴스의 접근 제어자 속성을 제거하려 시도하면 오류가 발생합니다.

메서드 데코레이터와 동일하나 decription의 configurable 속성을 바꿉니다.

<hr>

## 프로퍼티 데코레이터 (Property decorator)
프로퍼티 선언 직전에 선언합니다.  

앞선 메서드 & 접근자 데코레이터와 달리 두 개의 인수와 함께 함수로 호출합니다.
1. 정적 멤버에 대한 클래스의 생성자 함수 또는 인스턴스 멤버에 대한 클래스의 프로토타입
2. 멤버의 이름

예제 class
```ts
class Car {
    private name: string;
    private price: number;
    private type: string;

    constructor(name: string, price: number) {
        this.name = name;
        this.price = price;
    }

    public toString() {
        return `${this.name}, ${this.type}, ${this.price}`
    }
}
```

객체를 주입할 때 사용할 간단한 컨테이너를 정의하고 객체를 넣어둡니다.
```ts
class Container {
    private static map: {[key: string]: any} = {};

    static add(key: string, value: string) {
        Container.map[key] = value;
    }

    static get(key: string): string {
        return Container.map[key];
    }
}

Container.add('myType', 'Classic');
console.log(Container.get('myType')); // 'Classic'
```

이제 예제에서 사용할 데코레이터를 정의합니다.
예제 decorator
```ts
function Inject(param: string) {
    return function (target: any, propertyKey: string) {
        console.log(target); // {toString: f, constructor: f}
        console.log(propertyKey); // type
        target[propertyKey] = Container.get(param); //target 오브젝트에 property 값 할당
    }
}
```

이렇게 정의한 데코레이터는 클래스를 정의할 때 사용할 수 있습니다.

```ts
class Car {
    private name: string;
    private price: number;
    @Inject('myType') //값 주입, 위에서 이미 myType에 Classic이라는 값을 넣어놨음
    private type: string;
}

let myCar = new Car('AMG GT', 15000); // type말고 나머지 name과 price에 값 할당
console.log(myCar.toString()); // AMG GT, Classic, 15000
```

여기서 클래스가 정의될 때 데코레이터 함수가 실행되어 클래스의 프로퍼티 값이 지정되기 때문에 클래스를 위와 같이 정의하면 컨테이너의 값을 수정해도 처음 값이 그대로 출력됩니다.

```ts
Container.add('myType', 'Custom');
let myCar = new Car('918 Spider', 150000);
console.log(myCar.toString()); // 918Spider, Classic, 150000
```

그래서 Car 클래스의 Type property가 값이 아닌 함수를 가지고 있게 수정 하고, 값이 아닌 함수를 return 하게 Inject function을 수정하게 된다면 수정된 컨테이너의 값이 반영 됩니다.

```ts
function Inject(param: string) {
    ...
    tartget[propertyKey] = () => Container.get(param); //값이 아닌 함수를 리턴
}
```

```ts
class Car {
    ...
    @Inject('myType')
    private type: Function; //'type'은 값이 아니라 함수
    ...
}
```
```ts
Container.add('myType', 'Custom');
let myCar = new Car('918 Spider', 150000);
console.log(myCar.toString()); // 918Spider, Custom, 150000
```


## 매개변수 데코레이터 (Parameter decorator)
매개 변수 선언 직전에 선언 합니다.  
세 개의 인수와 함께 함수로 호출 합니다.
1. 정적 멤버에 대한 클래스의 생성자 함수 또는 인스턴스 멤버에 대한 클래스의 프로토타입
2. 멤버의 이름
3. 함수 매개 변수 목록에 있는 서수 색인 (Ordinal index)

매개 변수 데코레이터의 반환값은 무시 됩니다.

정리하면 파라미터 데코레이터는 옵저빙(감시), 값 변경이 안되기에 metadata를 정의할 때 사용합니다. 그렇기에 reflect metadata랑 같이 사용합니다.

## 데코레이터 호출 순 
```ts
function cd() {
    console.log('class');
    ...
}

function md() {
    console.log('method');
    ...
}

function prod() {
    console.log('property');
    ...
}

function pramd() {
    console.log(parameter);
    ...
}

@cd()
class example {
    @prod()
    property = "property";

    @md()
    test(
        @paramd() param: string
    ) {
        console.log('test');
    }
}
```
결과는
```
property
method
parameter
class
```
순으로 데코레이터 호출 순서는

Property decorator =>  
Method decorator =>  
Parameter decorator =>  
Class decorator 입니다.

<hr>

## Annotation vs Decorator
### 자바 어노테이션과 다른 점
문법적으로 봤을 때 java의 annotation과 큰 차이는 없습니다. 다만 차이점이 있다면  
decorator는 runtime에서만 역할을 한다는 점 입니다. 

java의 annotation은 Retention이라는 게 있어 compiler에게만 보이고, runtime에는 없어지게 하거나 runtime에도 살아남아서 jvm에 의해 참조할 수 있게 할수도 있습니다.

- @Retention : 어느 시점까지 어노테이션의 메모리를 가져갈 지 설정합니다.
    - 어노테이션의 라이프 사이클
- Jvm (Java Virtual Machine) : Java 컴파일러가 bytecode로 변환한 것을 os가 이해해줄 수 있도록 해석해주는 것

typescript에서는 정적인 타입이 없기 떄문에 애초에 compile time에 기능을 할 수 없습니다. 이런 측면에서 runtime에서만 활용할 수 있는 annotation이라고 볼 수 있습니다.

- Compile Time : 개발자에 의해 작성된 소스코드를 컴퓨터가 인식할 수 있는 기계어 코드로 변환되어 실행 가능한 프로그램이 되는 과정을 의미
- Run Time : 컴파일 과정을 마친 응용 프로그램이 사용자에 의해서 실행되는 때을 의미

<hr>

## 메타데이터 Metadata
- 데이터의 데이터

### Reflection
동일한 시스템(또는 그 자체)의 다른 코드를 검사할 수 있는 코드를 설명하는데 사용됩니다.

### Typescript에서의 reflection API
Typescript에서는 reflect-metadata 패키지를 사용하여 메타데이터 리플렉션 API를 사용할 수 있습니다.
```console
npm install reflect-metadata
```

Typescript의 tsconfig.json 파일의 emitDecoratorMetadat를 true로 설정해야 합니다.  
이제 프로퍼티 데코레이터로 한번 적용 해본다면..

예제 decorator
```ts
function logtype(target: any, key: string) {
    let t = Reflect.getMetadata("design:type", target, key);
    console.log(`${key} type : ${t.name}`);
}
```
그리고 클래스에 적용한다면
```ts
class izone {
    @logType
    public wizone: string;
}
```

이제 이렇게 작성한 예제 코드를 실행 한다면
```console
// console
wizone type: String
```

파라미터 데코레이터에 적용 해봅시당
```ts
function logParamTypes(target: any, key: string) {
    let types = Reflect.getMetadata("design:paramtypes", target, key);
    let s = types.map(a => a.name).join();
    console.log(`${key} param types: ${s}`)
}
```

이제 클래스의 메서드 중 하나에 이 함수를 적용해보면
```ts
class Ive {}
interface Dive {}

class Lovedive{
  @logParamTypes
  narcissistic(
    yujin: string,
    gaeul: number,
    rei: Dive,
    wonyoung: Ive,
    liz: Function,
    leeseo: { baby: string },
  ): number {
    return 1
  }
}
```
이제 위 예제를 실행하게 되면
```console
//console
narcissistic param types : string, number, Object, Ive, Function, Object
```

design: returntype 이라는 메타 데이터 키를 사용하여 메서드의 반환 유형에 대한 정보를 얻을 수도 있습니다.

이 외의 메타데이터 활용 예제는 아직 제대로 이해를 하지 못해서.. 나중에 추가하도록 하겠습니다.