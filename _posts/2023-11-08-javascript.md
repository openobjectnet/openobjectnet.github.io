---
title: "JavaScript Basic"
search: false
categories:
  - JavaScript
toc: true
tags:
  - JavaScript
author: 서상균
---

# Intro
JavaScript 기본 문법에 대한 내용을 정리했습니다.

---

<br>

# 1. JavaScript 등장 배경

* Scripting 언어 탄생
  1. HTML과 CSS로 간단하게 만들어진 정적인 웹 브라우저 _Netscape Navigator_ 가 등장하게 됩니다.
  2. 기존의 정적인 웹 사이트에서 동적인 웹 사이트를 만들기를 원했고, 동적인 기능을 추가할 수 있는 **Scripting 언어**를 도입하게 됩니다.

* JavaScript 탄생
  1. **JAVA** 는 어렵고 무거웠기 때문에 도입하지 못했고, _Brendan Eich_ 가 새로운 언어 **Mocha** 를 만들게 됩니다.
  2. 이후 **Mocha** 는 **LiveScript** 로 이름이 변경 되었고, 이를 해석할 수 있는 엔진 **Live Script Interpreter** 가 포함된 브라우저가 출시됩니다.
    - 개발자가 **LiveScript** 를 이용해 웹 사이트를 만들면 _Netscape Navigator_ 브라우저가 언어를 이해하고 실행하고자 하는 것에 맞게 DOM 요소들을 조작하는게 가능합니다.
  3. 당시 유행했던 **Java** 의 인기에 편승하여 **JavaScript** 라는 이름으로 변경되었고, **JavaScript** 와 **JavaScript Interpreter** 가 포함된 브라우저가 출시됩니다.

* JavaScript 파편화
  1. **Microsoft** 에서는 자신들만의 브라우저를 갖고자 했고, _Netscape Navigator_ 브라우저를 **Reverse Engineering** 하여 소스코드 복원에 성공했고 기능을 추가하여 **JScript** 언어와 **Internet Explorer** 브라우저를 출시합니다.
    - Reverse Engineering : 이미 만들어진 시스템을 역으로 추적하여 처음의 문서 또는 설계기법 등의 자료를 얻어내는 과정입니다.
  2. **JScript** 와 **JavaScript** 가 표준화 되지 못했고, 브라우저에 따라 웹 페이지에서 **Cross Browsing** 문제가 발생하면서 모든 브라우저에서 동작하는 웹 페이지를 개발하는 일이 어려워졌습니다.
  
* JavaScript 표준화
  1. **JavaScript** 의 파편화를 방지하고 표준화하기 위해 **ECMAScript 1** 이 등장합니다.
    - ECMAScript : 브라우저에서 동작하는 언어를 만들 때 엔진이 이해할 수 있도록 변수를 선언하는 방법이나 함수를 정의하는 방법을 명시한 문서
  2. 이후 다양한 브라우저들이 등장하게 되면서 **jQuery, dojo, mootools** 같은 라이브러리들이 생겼고, 이러한 라이브러리들의 목적은 다른 브라우저의 구현사항을 신경쓰지 않게 개발자가 웹 개발을 할 수 있도록 하는것입니다.

* Chrome의 등장
  1. 2008년 google사에서 개발했고 **JIT** 엔진을 포함하여 **JavaScript** 를 매우 빠르게 실행 시킬 수 있는 브라우저입니다.
   
* JavScript의 성장
  1. Chrome 브라우저 등장 이후 **ECMAScript 5** , **ECMAScript 6** 까지 완성되며 더 이상 **jQuery** 와 같은 라이브러리의 도움 없이 **JavaScript** 와 **웹APIs** 에서 제공하는 API들 만으로 모든 브라우저에서 동작하는 웹 사이트나 웹 어플리케이션을 개발할 수 있게 됩니다.
  2. 이때부터 개발자들은 **ECMAScript**의 최신 기준으로 개발을 하고 배포시에  **BABEL** 을 사용하게 됩니다.
      - BABEL : ECMAScript의 버전을 5 또는 6로 변환해서 코드를 생성해주는 JavaScript transcompiler입니다.
  3. 표준화된 **ECMAScript** 와 _V8_ 엔진으로 인해 **Node JS** 가 등장하게 되었고 백엔드에서도 서비스를 구현할 수 있게 되었습니다.

<br>

# 2. async, defer script

```html
<html>
  <head>
    <script></script>
  </head>
  <body>
    <div> .... </div>

  </body>
  
</html>
```
<br>

![async_defer](../assets/images/javascriptBasic/async_defer.png)

- 브라우저는 위에서부터 HTML 코드를 읽고 분석해서 CSS와 병합하여 DOM요소를 만드는 **Parsing** 과정을 진행합니다.
- script 태그를 만나게 되면 HTML Parsing을 멈추고 script파일을 다운받고, 다운이 완료되면 다시 Parsing을 진행합니다. 
- script 파일을 다운 받는 것은 꽤나 오래 걸리는 작업입니다.
- 이러한 동작 방식 때문에 2가지의 문제를 발생시킬 수 있습니다.
  1. 스크립트 아래에 있는 DOM 요소에 접근할 수 없습니다.
  2. 페이지 상단에 큰 용량의 스크립트가 존재하는 경우 사용자는 스크립트를 다운받고 실행 시킬때 동안 스크립트 아래쪽의 페이지들을 볼 수 없습니다. 
- async와 defer를 통해 script 파일을 다운 받을때 발생하는 문제를 해결할 수 있습니다.

* async
```html
<html>
  <head>
    <script async src="test.js"></script>
  </head>
  <body>
    <div> .... </div>

  </body>
  
</html>
```
- HTML을 Parsing하는 과정은 JS파일을 다운로드 받는 동안은 멈추지 않습니다. 즉 비동기적으로 스크립트 파일을 다운받습니다.
- 특징으로는 JS파일의 실행순서가 HTML에 명시된 순서가 아닌 다운로드 받는 순서입니다.
- 단점으로는 Parsing이 다 되기 전에 JS가 실행되면서 JS에 아직 생성되지 않은 DOM 요소가 존재할 경우 오류가 발생할 수 있습니다.


* defer
```html
<html>
  <head>
    <script defer src="test.js"></script>
  </head>
  <body>
    <div> .... </div>

  </body>
  
</html>
```
- HTML을 Parsing하는 과정에서 비동기적으로 JS파일을 다운로드 받습니다.
- async와 다른 점은 HTML Parsing과정이 모두 끝난 이후 JS파일을 실행시킵니다.
- 특징으로는 JS파일의 실행순서는 HTML에 명시된 순서입니다.
- 첫번째 단점으로는 용량이 큰 JS파일이 먼저 명시되고 용량이 작은 JS파일이 나중에 명시되었을 경우 용량이 작아서 먼저 다운로드가 완료되었어도 실행을 기다려야하는 것입니다.
- 두번째 단점으로는 JS가 실행되기 전에 페이지가 화면에 출력되어, 사용자가 데이터 노출의 준비 화면과 그래픽 관련 컴포넌트들을 볼 수 있다는 점입니다.
  
**async의 경우 방문자 수 카운터 또는 광고 관련 스크립트같은 독립적인 스크립트와 실행 순서가 중요하지 않은 경우에 적용됩니다**
**defer의 경우 DOM 전체가 필요한 스크립트나 실행 순서가 중요한 경우에 적용됩니다**


# 3. use strict (ES5+)
- Javascript의 유연성 때문에 오류로 처리하지 않는 여러 부분들을 엄격히 검사합니다.
- 디버깅이 쉬워지며, 발생가능한 에러를 예방할 수 있습니다.


# 4. 변수

* let (Mutable data type)
  - ES6부터 추가되었습니다.
  - 값을 변경할 수 있습니다.
  
```javascript
let name = 'poll';
name = 'james';
```
- Application마다 메모리가 제한적으로 할당됩니다.
- name 변수를 정의하면서 name 변수가 가리키는 메모리가 할당되고 해당 메모리에 값을 넣어주는 방식으로 구동됩니다.

* global scope
  - 어디에서나 접근 가능합니다.
  - block scope에 접근 불가능합니다.

* block scope
  - block 안에서만 접근이 가능합니다.
  - global scope에 접근 가능합니다.

```javascript
let name = 'poll';
console.log(name);
console.log(name2); // Error!!
{
  let name2 = 'james';
  console.log(name2);
  console.log(name);
}
```
- block scope에서는 name변수에 접근 가능하지만 block scope 밖에서는 name2 변수에 접근이 불가능 합니다.

* hoisting
  - Javascript 함수는 실행되기 전에 함수 안에 필요한 변수값들을 모두 모아서 유효 범위의 최상단에 위치시킵니다.
  - 함수의 선언부는 컴파일 단계에서 메모리에 추가되며, 실제 함수 선언을 만나기 전에 함수에 접근이 가능한것입니다.
  - 단, 함수 선언식 방법은 호이스팅이 되지만, 함수 표현식 방법은 호이스팅 되지 않습니다.
  - 호이스팅은 오직 선언만 해당하며, 초기화는 해당되지 않기 때문입니다.
  
```javascript
hi();

// 함수 선언식
function hi(){
  console.log('HI');
}

hi2(); // Error!!!

// 함수 표현식
let hi2 = function (){
  console.log('HI2');
}
```
  
* var 
  - ES6 전에 사용하던 변수 선언 방법입니다.
  - let, const와 다르게 호이스팅이 가능합니다.
  - 변수를 선언하고 할당하지 않은채로 변수를 호출할 경우 오류가 발생하지 않으며, _undefined_ 를 반환합니다.
  - block scope가 적용되지 않습니다. block scope 내에 선언을 해도 global scope에서 접근이 가능합니다.
  - 이런 다양한 이유들로 **var** 변수 선언 방식을 사용하는 것을 지양해야합니다.

* const (Immutable data type)
  - 한 번 할당하면 값이 바뀌지 않습니다.
  - 다른 사용자에 의해 값이 바뀌는 것을 방지하는 **보안성** 의 장점이 있습니다.
  - 다야한 스레드들이 변수에 접근해서 동시에 값을 변경할 수 있는 문제를 예방하는 **스레드 안전성** 의 장점이 있습니다.

* 변수 타입
  - Primitive values (원시 값) : Object를 제외한 모든 타입입니다.
    - null, undefined, boolean, number, bigint, string, symbol
  - Object (객체) : 데이터와 데이터와 관련된 동작으로 이루어져있습니다.
    - 윈시 타입을 제외한 모든 것들은 객체입니다.
    - 배열, 함수, 정규표현식 등등
  - function (함수) : 함수는 변수에 할당이 가능하고, 함수 인자로 전달이 가능하고, 함수의 반환값으로 사용이 될 수 있습니다.
  - Primitive values와 Object의 차이
    - 메모리 저장방식이 다릅니다.
    - Primitive values의 경우 메모리에 값 자체가 저장됩니다.
    - Object의 경우 메모리에 ref(레퍼런스)가 저장되며, const로 지정할 경우 Object 자체를 변경하는 것은 불가능하지만, Object가 가진 데이터는 변경할 수 있습니다.
**number**
```javascript
const count = 12; // type : number
const count = 12.7; // type : number

const infinity = 1 / 0; // infinity
const negativeInfinity = -1 / 0; // -infinity
const nAn = 'sample' / 0; // NaN
```
- 다른 언어처럼 크기에 구애받지 않고, 동적으로 감지하기 때문에 number라는 키워드를 사용해 선언하지 않습니다.
- infinity, -infinity, NaN : 특별한 숫자의 값입니다. infinity의 경우 0으로 나눌 경우에 해당합니다. NaN의 경우 숫자가 아닌 경우 연산 결과에 해당합니다.

**bigInt**
```javascript
const bigint = 123456789012345678901234567890n;
```
- JavaScript의 number가 표현할 수 있는 -2^53 ~ 2^53-1 을 넘는 크기에 해당합니다.

**string**
```javascript
const char = 'c';
const greeting = 'hello' + char;
```
- 하나의 문자에 대해서도 string으로 인식합니다.
- **+** 기호를 사용해 합친 문자열로 만들 수 있습니다.

**boolean**
- false : 0, null, undefined, NaN, '' 에 해당하는 값을 false로 간주합니다.
- true : false에 해당하지 않는 값은 모두 true로 간주합니다.

**null, undefined**
```javascript
const nothing = null;
const x;
```
- 직접 null이라고 할당 할 경우 비어있는 값으로 지정이 되었다고 인식합니다.
- 선언은 했지만, 값을 할당하지 않은 경우 undefined입니다.

**symbol**
```javascript
const symbol1 = Symbol('id');
const symbol2 = Symbol('id');
console.log(symbol1 === symbol2); // false

const symbol3 = Symbol.for('id');
const symbol4 = Symbol.for('id');
console.log(symbol3 === symbol4); // true
```
- 자료구조에서 고유한 식별자가 필요하거나 동시 다발적으로 일어나는 코드에서 우선순위를 부여할 때 사용합니다.
- 같은값으로 식별자를 부여했어도 둘은 엄연히 다르게 식별합니다.
- 같은 식별자로 만드는 방법은 **Symbol.for()** 을 사용합니다.

* Dynamic typing : 동적으로 타입 지정
```javascript
let text ='hi'; // type : string
text = 1; // type : number
text = '1' + 1; // type : string
text = '8' / '2'; // type : number
```
- JavaScript는 프로그램이 동작할 때 타입 변동이 가능합니다.
- 마지막 나눗셈의 경우 연산이 number타입에서 사용됨을 인지하고 문자열이 숫자임을 확인해서 number타입으로 간주합니다.

# 5. 함수

**Default 파라미터**
```javascript
function showMessage(message, from = 'unknown'){
  console.log(`${message} by ${from}`);
}

showMessage();
```
- 함수 선언부분에 인자가 넘어오지 않는 경우를 대비해 인자의 default값을 지정할 수 있습니다.
- 해당 기능은 ES6 버전부터 추가 되었습니다.

**Rest 파라미터**
```javascript
function printAll(...args){
  for(let i = 0; i < args.length; i++){
    console.log(args[i]);
  }
}

printAll('a','b','c');
```
- 배열 형태로 파라미터를 전달함을 의미합니다.
- args에 'a', 'b', 'c'가 배열 형태로 담기는것으로 기능합니다.

**콜백함수**
```javascript
function radom(answer, printYes, printNo){
  if(answer === 'right'){
    printYes();
  }else{
    printNo();
  }
}
```
- 함수를 파라미터로 전달한 경우 해당 파라미터를 콜백함수라고 부릅니다.

**익명 함수**
```javascript
const printYes = function () {
  console.log('yes');
}
```
- 함수에 이름이 없는 상태로 function 키워드를 통해 함수를 선언하고 변수에 할당한 경우입니다.

**기명 함수 (Named function)**
```javascript
const printNo = function print(){
  console.log('no');
  // print(); recursions
}
```
- 디버깅할 때, 디버거 안에서 스택 추적이 가능하도록 할 때 사용합니다.
- recursions : 함수 안에서 또 다른 함수를 호출할 때 사용합니다.

**화살표 함수 (Arrow function)**
```javascript
const simplePrint = () => console.log('simplePrint');
const add = (a, b) => a + b;
const simpleAdd = (a, b) => {
  return a + b;
}
```
- 기본의 함수 선언 방식을 화살표 기호를 사용해 구현한 것입니다.
- block을 사용할 경우에는 return (반환)이 필수입니다.

**IIFE (Immediately Invoked Function Expression)**
```javascript
(function hello(){
  console.log('IIFE');
})();
```
- 선언과 동시에 호출하는 방식입니다.
- 뒤에()를 사용하는 것이 특징입니다.


# 6. 클래스

클래스는 객체를 생성하기 위한 template입니다.
클래스는 데이터와 이를 조작하는 코드를 하나로 추상화 합니다.

* 클래스 정의
  - 클래스는 특별한 함수라고도 부릅니다.
  - 함수와 비슷하게 **클래스 표현식** 과 **클래스 선언** 방법을 제공합니다.
  - 함수와 비슷하지만 **hoisting** 은 적용되지 않습니다.
  
* 클래스 선언
```javascript
class Person{

  constructor(name, age){
    this.name = name;
    this.age = age;
  }

  speak(){
    console.log(`${this.name} Hello!`);
  }
}

const p1 = new Person('James', 20);
console.log(p1.name); // James 출력
console.log(p1.age); // 20 출력
p1.speak(); // 클래스에 정의한 메서드 실행
```
- constructor : 클래스 생성자 함수입니다.
- name, age : 클래스의 데이터 입니다. (프로퍼티)
- speak() : 클래스의 데이터를 조작하는 함수입니다. (메서드)
- 클래스를 통해 생성된 객체를 인스턴스라고 합니다.
- 클래스 이름의 첫번째는 항상 대문자로 시작합니다.
- new 키워드를 통해 객체를 생성할 수 있습니다.

**게터와 세터 (Getter and Setters)**
```javascript
class User {
  constructor(firstName, lastName, age){
    this.firstName = firstName;
    this.lastName = lastName;
    this.age = age;
  }

  get age(){
    return this._age;
  }
  set age(value){
    if(value < 0){
      throw Error('음수 안됨');
    }
    this._age = value;
  }
}

const user1 = new User('Seteve', 'job', 1);
console.log(user1.age);
```
- 접근자 프로퍼티라고 부릅니다.
- get을 통해 값을 저장하고 set을 통해 값을 설정합니다.
- getter와 setter를 사용하는 이유는 객체 내부 속성에 직접 접근하지 않아 객체의 은닉성을 가능하게 해주며 코드의 안전성과 유지보수성을 높일 수 있고 옳지 않은 값을 설정하는 것을 미연에 방지할 수 있습니다.
- setter에 조건을 주어 옳지 않은 값을 객체 내부 속성에 저장하는 것을 방지할 수 있습니다.
- JavaScript 객체 속성에 접근하듯이 접근자 프로퍼티를 호출할 수 있습니다.

**게터와 세터 (getter and setter) 주의점**
게터(getter)만 존재할 경우
  - 값을 할당 할 수 없기 때문에 오류가 발생합니다.

세터(setter)의 무한루프
```javascript
class user{
    constructor(firstName,lastName,age){
        this.firstName = firstName;
        this.lastName = lastName;
        this.age = age;
    }
    get age(){
        return this.age;
    }
    set age(value){
        this.age=value;
    }
}
const user1 = new user('steve','job',-1);
console.log(user1.age);
```
- getter의 경우 프로퍼티를 읽으려고 할 때 실행되고, setter의 경우 프로퍼티에 값을 할당 하려고 할 때 실행됩니다.
- this.age = value가 동작할 때, 계속해서 setter 자기 자신을 호출하게 되면서 무한루프에 빠지게 됩니다.
- 이를 해결하기 위해서 getter와 setter에 사용되는 변수명을 바꿔주는 것입니다. 이때는 보통 **_변수명** 처럼 아래 언더바 기호를 사용해 정의합니다.

**fields (public, private)**
```javascript
class Example{
  publicField = 2;
  #privateField = 0;
}
const example = new Example();
console.log(example.publicField);
```
- private 필드의 경우 클래스 생성자 및 메서드에서만 접근이 가능합니다.
- public 필드의 경우 외부에서도 접근이 가능합니다.

**static**
```javascript

class Article{
  static publisher = 'Dream coding';
  constructor(articleNumber){
    this.articleNumber = articleNumber;
  }

  static printPublisher(){
    console.log(Article.publisher);
  }
}
const article1 = new Article(1);
const article2 = new Article(2);
console.log(article1.publisher); // undefined
console.log(Article.publisher);
Article.printPublisher();
```
- Object마다 할당되는 것이 아니고 클래스 자체에 붙어있는 것입니다.
- 공통적으로 사용할 때 효율적입니다.
- 사용시에 해당 클래스의 인스턴스 객체로 호출하는 것이 아니고 클래스 자체로 호출하는 것입니다.

**상속**
```javascript
class Shape{
  constructor(width, height, color){
    this.width = width;
    this.height = height;
    this.color = color;
  }

  draw(){
    console.log(`drawing ${this.color} color of`);
  }

  getArea(){
    return this.width * this.height;
  }
}

class Rectangle extends Shape{

}
class Triangle extends Shape{ 
  draw(){
    super.draw();
    console.log('past');
  }
  getArea(){ // 오버라이딩
    return (this.width * this.height) / 2;
  }
}
const rect = new Rectangle(20, 20, 'blue');
const tri = new Triangle(20, 20, 'red');
rect.draw();
tri.draw();
console.log(rect.getArea());
console.log(tri.getArea());
```
- 공통 기능에 대한 부분은 부모 클래스로 만들고 해당 클래스를 상속받아 자식 클래스를 만듭니다.
- 부모 클래스에 정의된 메서드는 자식 클래스에서 사용이 가능하며, 자식 클래스에서 재정의하여 사용할 수 있습니다.
- super 키워드를 통해 부모 클래스에 정의된 메서드를 호출할 수 있습니다.
- super 키워드를 사용하지 않고, 자식 클래스에서 재정의한 메서드를 호출한다면, 부모 클래스의 메서드는 호출되지 않습니다.

**instanceof**

위에 코드와 이어서 봐야합니다.
```javascript
console.log(rect instanceof Rectangle); // True
console.log(tri instanceof Rectangle); // False
console.log(tri instanceof Triangle); // True
console.log(tri instanceof Shape); // True
console.log(tri instanceof Object); // True
```
- 상속받은 클래스는 부모 클래스의 인스턴스로 인식합니다.
- JavaScript의 모든 클래스는 JavaScript에 있는 Object를 상속한 것이므로 tri instanceof Object의 결과는 True입니다.


# 7. 오브젝트
JavaScript의 데이터 타입중에 하나이며 관련된 데이터의 집합체 입니다.
오브젝트는 {key : value}로 이루어져 있습니다.

**오브젝트 선언**
```javascript
const obj1 = {};
const obj2 = new Object();
```
- 첫번째의 경우 Object literal 이라고 부릅니다.
- 두번째의 경우 Object constructor 라고 부릅니다.

* 오브젝트 값 호출
```javascript
const test = {name: 'test', age: 20};

console.log(test.name);
console.log(test['name']);
```
- 첫번째 방식의 경우 .을 이용해 value를 호출합니다.
- 두번째 방식의 경우 [] 기호 안에 key 값을 문자열 형태로 넣어 호출합니다.
- 두번째 방식을 사용하는 경우는 키 값으로 어떤 값이 사용될지 모를 경우에 사용합니다.
```javascript
function printValue (obj, key){
  console.log(obj[key]);
}
printValue(test, 'name');
```
- 해당 경우에서 두번 째 호출 방식을 사용합니다.

**오브젝트 key 값 출력**
```javascript
for(key in test){
  console.log(key); // name, age 출력
}
```
- for..in 문법을 통해 오브젝트의 key값을 출력할 수 있습니다.

**오브젝트 클론**
```javascript
const user = {name: 'ee', age: '20'};
const user2 = user;
user2.name = 'coder';
console.log(user.name); // coder 출력
```
- user 와 user2 가 동일한 ref(레퍼런스)를 가지게 되면서 user2의 name 프로퍼티를 변경했을 경우 user의 name 프로퍼티 또한 변경됩니다.

```javascript
const user4 = {};
const user4 = Object.assign({}, user); 
Object.assign(user4, user);
console.log(user4);
```
- Object의 assign 함수를 통해 앞에 인자는 복사를 할 곳을 지정하며 뒤에 인자는 복사를 할 대상을 지정합니다.

# 8. 배열

**for .. of**
```javascript
const fruits = ['apple','banana'];
for(let fruit of fruits){
  console.log(fruit);
}
```
- 배열의 요소들을 출력하는 방법 중에 하나인 for of 문법입니다.

**forEach**
```javascript
fruits.forEach(function(fruit, index, array){
  console.log(fruit, index, array);
});

fruits.forEach((fruit, index) => console.log(fruit)); // apple, banana 출력
```
- 배열의 요소들을 출력하는 방법 중에 하나인 forEach 문법입니다.
- 콜백함수를 사용하는 것이 특징이며 _값_ , _인덱스_ , _전체배열_ 을 인자로 전달합니다.
- 보통은 _전체배열_ 은 전달하지 않고 사용하며 배열을 돌며 첫번째 값과 인덱스, 두번째 값과 인덱스... 이런식으로 동작합니다.
- 밑에는 Arrow function을 사용해 좀 더 간편하게 구현한 모습입니다.

**push, pop**
```javascript
fruits.push('peach','orange');
console.log(fruits); // apple, banana, peach, orange 출력

fruits.pop();
fruits.pop();
console.log(fruits); // apple, banana 출력
```
- push 함수의 경우 배열의 마지막 요소에 인자로 전달한 값을 추가합니다.
- pop 함수의 경우 배열의 마지막 요소부터 제거합니다.

**unshift , shift**
```javascript
fruits.unshift('orange');
console.log(fruits); // orange, apple, banana 출력

fruits.shift();
console.log(fruits); // apple, banana 출력
```
- unshift 함수의 경우 배열의 첫번째 부터 인자로 넘어온 값을 추가합니다.
- shift 함수의 경우 배열의 첫번째 요소부터 제거합니다.
- unshift와 shift의 경우 push와 pop보다 실행속도가 느립니다. 그 이유는 배열의 첫번째 요소의 변화가 생기며 다른 값들의 인덱스를 모두 변경해주어야 하기 때문입니다.

**splice**
```javascript
fruits.splice(1,2, 'bam','baa','saa','seee');
console.log(fruits);
fruits.splice(2,0,'sss','kkk');
console.log(fruits);
```
- splice함수는 start인덱스부터 몇 개의 데이터를 지울지를 정해 배열에서 값을 지우거나 추가할 수 있습니다.
- 첫번째 함수의 실행결과로는 인덱스 1부터 시작하여 2개를 지우고 시작 인덱스 1부터 시작해서 'bam', 'baa', 'saa', 'seee' 데이터를 배열에 추가합니다.
- 두번째 함수의 실행결과로는 인덱스 2부터 시작하여 0개의 데이터를 지웠고, 시작 인덱스 2부터 'sss', 'kkk' 데이터를 배열에 추가합니다.
- 함수에서 개수를 생략하고 실행한다면, 시작 인덱스 부터 모든 데이터를 지우게 됩니다.

**concat**
```javascript
const fruits1 = ['a','b']
const fruits2 = ['c','d'];
const newF = fruits1.concat(fruits2);
console.log(newF); // a, b, c, d 배열 출력
```
- concat 함수로 두 개의 배열을 붙일 수 있습니다.
- fruits1 배열 뒤에 fruits2 배열을 붙입니다.

**searching**
  - indexOf() : 인자로 넘어간 값이 존재할 경우 첫번 째 인덱스 값을 반환합니다. 데이터가 없을 경우 -1을 반환합니다.
  - includes() : 인자로 넘어간 값이 존재할 경우 true를 반환하며 없을 경우 false를 반환합니다.
  - lastIndexOf() : 인자로 넘어간 값이 존재할 경우 제일 마지막에 해당하는 값의 인덱스를 반환합니다.

**join**
```javascript
const test = ['a','b','c']
console.log(fruits.join('.')); // a.b.c
console.log(fruits.join('')); // abc
console.log(fruits.join('-'));  // a-b-c
```
- 배열의 요소들을 구분자로 구분해서 출력합니다.
- 구분자로는 string 타입의 문자열도 가능합니다.

**some, every**
```javascript
class Student {
  constructor(name, age, enrolled, score) {
    this.name = name;
    this.age = age;
    this.enrolled = enrolled;
    this.score = score;
  }
}
const students = [
  new Student('A', 29, true, 45),
  new Student('B', 28, false, 80),
  new Student('C', 30, true, 90),
  new Student('D', 40, false, 66),
  new Student('E', 18, true, 88),
];

const result = students.some((student) => student.score < 50);
  console.log(result); // true
  
const result2 = !students.every((student) => student.score >= 50);
  console.log(result2); // true
```
- some 함수는 배열의 각 요소 마다 콜백함수가 실행되며 콜백함수의 안에 정의된 조건문에 의해 하나의 요소라도 그 조건을 만족한다면 true를 반환합니다.
- every 함수는 배열의 각 요소마다 콜백함수가 실행되며 콜백함수의 안에 정의된 조건문에 의해 모든 요소가 그 조건을 만족해야 true를 반환합니다.

**filter**
```javascript
const result = students.filter((student) => student.enrolled === true);
  console.log(result); 
```
- filter 함수는 배열의 요소마다 콜백함수가 실행되며 콜백함수의 조건에 맞는 요소들을 배열로 모아 출력합니다.
- 위 코드의 경우 배열의 각 요소는 student라는 변수의 이름으로 적용되며 그 student에 enrolled가 true의 값을 가진 요소들만 모아 배열로 반환합니다.

**map**
```javascript
const result = students.map((student) => student.score);
  console.log(result);

let arr = [1,2,3,4];
let result = arr.map((element) => element * 2);
console.log(result); // 2,4,6,8
```
- 배열에 있는 모든 요소들의 값을 변경해서 만든 새로운 배열이 필요할 때 사용합니다.
- 조건에 따라 반환되는 배열이 다양하지만, 일반적인 number가 들어있는 배열의 요소들에 연산이 필요할 때나, 객체가 들어있는 배열에서 필요한 값만 가지고와서 배열로 만들때 사용됩니다.
- 위 코드의 경우 모든 배열 요소를 돌며 콜백 함수가 실행되고 콜백함수의 조건에 따라 student 객체의 score 값만 가지고와 배열로 반환합니다.

**reduce**
```javascript
const result = students.reduce((prev, curr) => {
    return prev + curr.score;
  }, 0);
  console.log(result / students.length);
```
- reduce 함수는 배열의 각 요소를 순회하며 콜백함수를 실행한 값을 누적하여 하나의 결과값을 반환합니다.
- prev : 초기 값이 존재할 경우 초기 값을 전달 받고 이전 값의 연산 결과를 가집니다.
- curr : 현재 배열의 요소값 입니다.
- 0 : 초기값 입니다.
- **reduceRight** 라는 함수도 존재하며 이는 **reduce** 함수와는 반대로 실행되는 함수입니다.
```
prev : 0 , curr : 45
prev : 45, curr : 80
prev : 125, curr : 90
prev : 215, curr : 66
prev : 281, curr : 88
prev : 281, curr : 88
return 369
```

# 9. JSON

**JSON 정의**

- JavaScript Object Notation 의 약어입니다.
- 자바스크립트의 객체 표기업을 제한하여 만든 텍스트 기반의 데이터 교환 표준입니다.
- JSON을 사용하는 이유는 특정 언어에 종속되지 않으며, 대부분의 프로그래밍 언어에서 JSON 포맷의 데이터를 다룰수 있고 사람과 기계가 모두 이해하기 쉬운 형태로 제공되기 때문입니다.

**JSON 형식**
```javascript
{
  "name" : "홍길동",
  "age" : 25,
  "married" : false,
  "family" : {"father" : "홍판서", "mother" : "춘섬"},
  "hobbies" : ["독서", "축구"],
  "jobs" : null
}
```
- 객체, 배열, 숫자, 문자열, Boolean, null과 같은 다양한 데이터를 나타낼 수 있습니다.
- JSON문자열의 경우 _"_ 기호를 이용하는 것이 특징입니다.

**JSON.parse()**
- JSON문자열을 JavaScript 객체로 변환할 때 해당 메서드를 사용합니다.
- JSON문자열을 인자로 받으며 JavaScript 객체를 반환합니다.

```javascript
const jsonStr = {
  "name" : "홍길동",
  "age" : 25,
  "married" : false,
  "family" : {"father" : "홍판서", "mother" : "춘섬"},
  "hobbies" : ["독서", "축구"],
  "jobs" : null
}

const obj = JSON.parse(jsonStr);
```
- 해당 메서드를 통해서 JSON문자열을 JavaScript의 객체로 변환할 수 있습니다.
- obj.name을 통해 name에 할당된 값에 접근할 수 있으며
- obj.hobbies[1]을 통해 배열 객체에 접근할 수 있습니다.
이렇게 JSON문자열을 JavaScript 객체로 변환하는 것을 _역직렬화_ 라고 부릅니다.


**JSON.stringify()**
- JavaScript 객체를 JSON문자열로 변환할 때 사용하는 메서드입니다.
- _JSON.parse()_ 메서드와는 반대로 . 이나 _[]_ 기호를 통해 데이터에 접근이 불가능합니다.

```javascript
const obj = {
  name: "홍길동",
  age: 25,
  married: false,
  family: {
    father: "홍판서",
    mother: "춘섬",
  },
  hobbies: ["독서", "도술"],
  jobs: null,
}

const str = JSON.stringify(obj);
```

이렇게 JavaScript 객체를 JSON문자열로 변환하는 것을 _직렬화_ 라고 부릅니다.

**JSON문자열로 변환할 경우 모두 string 형태로 저장이 되기 때문에 JSON문자열을 JavaScript 객체로 변환해서 데이터를 가공할 경우에는 데이터 타입에 맞게 재설정을 해야합니다**


# 10. 동기 비동기

**동기와 비동기를 알기위해 알아야 할 JavaScript 동작원리**

대중적인 자바스크립트의 엔진은 구글의 V8 엔진입니다. V8 엔진은 크롬과 노드 안에서 동작합니다.

![JavaScript동작원리](../assets/images/javascriptBasic/callStack.png)

- **Memory Heap** : 변수와 객체에 대한 모든 메모리 할당이 일어나는 곳입니다.
- **Call Stack** : 코드가 실행될 때 스택이 쌓이는 곳입니다.
- 브라우저는 단순히 엔진 하나만으로 구성되어있지 않습니다. _DOM_ , _AJAX_ , _setTimeout_ 등의 브라우저에서 제공하는 **Web API** 라는 것들이 존재합니다.
- 이러한 **Web API** 호출을 통제하기 위한 _Event Queue_ 와 _Event Loop_ 가 존재합니다.

**Call Stack**
- JavaScript는 단일 스레드 프로그래밍 언어이므로, 단일 호출 스택이 존재하고 이것은 하나의 일만 처리할 수 있습니다.
- 프로그램에서 현재 위치를 기록하는 데이터 구조입니다.
- 함수를 실행하면 해당 함수의 기록을 스택 맨 위에 추가하며, 함수의 결과를 반환하면 스택에 쌓여있는 함수는 제거됩니다.
```javascript
function test(x){
  console.log(x);
  return x;
}
text(3);
```
1. test(3) 함수가 맨 처음 쌓이게 됩니다.
2. console.log(x)가 쌓이게 되고 실행후 스택에서 제거됩니다.
3. 3의 결과를 반환하며 test(3) 함수는 스택에서 제거됩니다.
- 이러한 단일 스레드의 구조를 가지기 때문에 함수의 종료 조건 없이 재귀적으로 호출할 경우 _스택 오버플로우_ 에러가 발생하게 됩니다.

**단일 호출 스택의 문제**
- 브라우저에서 복잡한 이미지를 처리할 경우 단일 호출 스택의 동작 방식에 의해서 이미지 처리 작업 스택을 차지하게 되고, 후속 작업들은 처리를 하지 못하는 경우가 발생하게 됩니다.

**비동기 콜백**
- 단일 호출 스택의 문제를 해결하는 방법 중 하나입니다.
- 특수한 시점에서 실행되는 콜백 함수이므로 스택안에 쌓일 필요가 없습니다.
- 해당 콜백들은 호출 스택에서 관리되는 것이 아니고 _이벤트 큐_ 에서 관리가 됩니다.

**이벤트 큐**
- 처리할 메시지 목록과 실행할 콜백 함수들의 리스트입니다.
- _Web API_ 에 해당하는 함수들이 호출되면, 해당 콜백함수를 _이벤트 큐_ 에 넣습니다.
- _이벤트 큐_ 는 대기하다가 스택이 비는 시점에 _이벤트 루프_ 를 돌리게 됩니다.
- _이벤트 루프_ 는 큐와 스택을 지켜보다가 스택이 비는 시점에 콜백을 실행 시켜주는 것입니다.

**비동기 콜백의 동작 과정**

![비동기 콜백 1](../assets/images/javascriptBasic/async1.png)

1. 코드가 실행되면 글로벌 스택 프레임이 호출 스택에 쌓이게 됩니다.
2. _setTimeout_ 함수가 호출 스택에 쌓이게 됩니다.
3. 브라우저에서 제공하는 _Web API_ 이기 때문에 WebAPIs 부분에 _timeout, 1000_ 이 들어가게 됩니다.
4. 이후 호출 스택에서는 _setTimeout_ 함수가 제거되지만, 해당 함수로 호출된 _Web API_ 는 요청된 시간동안 대기하게 됩니다.
5. JavaScript 코드가 더 이상 실행할 것이 없으므로 호출 스택은 비워지게 됩니다.
6. _timeout_ 이 만료가 되면 _Web API_ 는 _이벤트 루프_ 에 코드를 추가하여 JavaScript에 알릴 수 있습니다.
7. _이벤트 루프_ 는 호출 스택에 이미 실행중인 코드가 있을 수 있음을 인지하고 대기중인 _Function_ 을 호출 스택에 바로 쌓지 않습니다.

![비동기 콜백 2](../assets/images/javascriptBasic/async2.png)

1. 호출 스택을 비워있을 경우 JavaScript의 RunTime 환경은 _이벤트 루프_ 에 대기중인 것이 있는지 확인하며, 대기중인 항목이 있을 경우 호출 스택으로 쌓습니다.
2. console.log('hi'); 함수가 호출 스택에 쌓이게 됩니다.
3. 해당 함수가 실행되고 콘솔에는 hi가 출력되며 호출 스택에서 해당 함수를 제거합니다.
4. 마지막으로 함수에서 실행할 다른 명령이 없으므로 최종적으로 호출 스택에서 제거됩니다.


**동기**
- JavaScript는 동기적으로 처리하는 언어입니다.
- 동기적으로 처리한다는 것은 한 작업이 실행되는 동안 다른 작업은 멈춘 상태를 유지하고 자신의 차례를 기다리는 것입니다.
```javascript
console.log('h1');
console.log('h2');
console.log('h3');
```
- 해당 코드의 경우 h1, h2, h3 순서대로 콘솔에 출력되게 됩니다.
- 동기적으로 처리한다는 의미가 바로 순서대로 코드를 읽고 실행한다는 것입니다.
- 서버에 데이터를 요청하고 응답을 받아야 하는 작업이 있다면, 응답이 올 때까지 다른 작업을 하지 못하고 대기해야 하는 문제를 발생시킬 수 있습니다.

**비동기**
- 메인 스레드가 작업을 다른 곳에 인가하여 처리하고, 그 작업이 완료되면 콜백 함수를 받아 실행하는 방식입니다.
- 작업을 백그라운드에 요청하여 처리하게 하면서 작업을 동시에 처리하는 것입니다.
- JavaScript에서는 보통 _setTimeout()_ 함수나 _fetch()_ 함수가 비동기적으로 동작하는 대표적인 함수입니다.

```javascript
console.log('1');
setTimeout(function(){
  console.log('2');
}, 1000);
console.log('3');

function printImmediately(print){
  print();
}
printImmediately(()=>console.log('hello'));
```
- 동기적인 처리로 콘솔에는 1과 3 그리고 _printImmediately_ 함수의 실행으로 hello가 출력되며 마지막으로 _setTimeout_ 함수에 의해 2가 출력됩니다.
- _setTimeout_ 함수는 해당 함수를 1초 뒤에 실행한다는 의미이며 JavaScript는 순차적으로 명령을 실행하며 1초가 지난 시점에서 _setTimeout_ 함수에 넘겨진 함수를 비동기적으로 실행합니다.

**비동기 문제**
- 비동기 적으로 실행되는 코드가 존재하고, 그 다음 동기적 코드에서 비동기로 실행되는 코드의 결과를 이용해야할 경우, 아직 전달 받지 않은 데이터를 다루게 될 문제가 발생할 수 있습니다.
- 이를 해결하기 위한 것이 _콜백 함수_ 입니다.

```javascript
function getDB() {
    let data;
    setTimeout(() => {
        data = 100;
    }, 3000);
    return data;
}

function main() {
    let value = getDB();
    value *= 2;
    console.log('value의 값 : ', value);
}
main(); 
```
- main 함수가 실행되는 시점에서 data의 값이 전달 받지 않아 오류가 발생하는 코드입니다.

```javascript
function getDB(callback) {
    setTimeout(() => {
        const value = 100;
        callback(value);
    }, 3000);
}
function main() {
    getDB(function(value) {
        let data = value * 2;
        console.log('data의 값 : ', data);
    });
}
main();
```
- 위에서 발생하는 문제를 콜백 함수를 통해 해결하는 코드입니다.
- 콜백 함수를 인자로 넘김으로써 비동기적으로 받아오는 결과를 넘겨 해결하는 것입니다.
- 하지만, 해당 코드는 간단하게 되어있어 눈으로 쉽게 파악할 수 있지만 코드들이 더 복잡해지고 콜백 함수에서 또 콜백 함수를 받는 구조가 더 깊어진다면 _디버깅_ , _오류해결_ , _유지보수_ 가 어려워질 수 있습니다.

**무차별 적인 콜백 함수의 문제를 해결하기 위한 프로미스 객체**
- 비동기 처리를 위한 전용 객체로 _Promise_ 를 사용합니다.
- 비동기 작업의 성공 또는 실패와 그 결과값을 나타내는 객체입니다.
- 프로미스는 3가지의 상태를 가집니다.
  - pending : 프로미스 객체가 만들어져서 지정한 작업을 수행중인 상태입니다.
  - fulfilled : 수행을 완료한 상태입니다.
  - rejected : 수행중 오류가 발생한 상태입니다.
- Producer : 기능을 수행해서 해당하는 데이터를 만들어내는 객체를 의미합니다.
- Consumer : 만들어진 데이터를 소비하는 객체를 의미합니다.

**producer**
- Promise 객체에 콜백 함수를 전달해야합니다.
- 이 콜백 함수는 _resolve_ 와 _reject_ 라는 콜백 함수를 전달해야합니다.
- Promise를 만드는 순간 _producer_ 가 전달한 콜백 함수를 실행합니다.
```javascript
const promise = new Promise((resolve, reject) => {
  console.log('something'); 
  setTimeout(()=>{
    resolve('ellie');
    reject(new Error('no network'));
  }, 2000);
});
```
- Producer의 Promise 객체의 기본 구조입니다.
- resolve : 기능을 정상적으로 수행해서 최종적으로 수행할 콜백 함수입니다.
- reject : 기능을 수행하다가 문제가 생기면 호출하게되는 콜백 함수입니다.
- _resolve_ 콜백 함수에는 비동기적으로 받아온 데이터를 가공해서 전달하는 것이 일반적입니다.


**Consumer**
- Promise 객체에 의해 기능이 잘 수행되었고 _resolve_ 콜백 함수에서 전달해준 데이터가 _then_ 함수의 인자로 넘어가게 됩니다.
- Promise 객체에 의해 기능이 수행되지 않았고 에러가 발생할 경우 _reject_ 콜백 함수로 에러를 핸들링 해줌으로써 _catch_ 함수로 에러를 다룰수 있게 됩니다.
- finally 의 경우 Promise 객체의 기능 수행 결과에 구애 받지 않고 무조건적으로 실행되는 함수입니다.
```javascript
promise
.then((value) => {
  console.log(value);
})
.catch(error => {
  console.log(error);
})
.finally(()=>{console.log('finally')});
```

**Promise Chaining**
- 여러 개의 프로미스 객체를 연결하여 사용할 수 있습니다.
- _then_ 함수를 실행하면 새로운 프로미스 객체를 반환해서 연결 할 수 있습니다.
```javascript
const getHen = () => new Promise((resolve, reject) => {
  setTimeout(() => resolve('chicken'), 1000);
});
const getEgg = hen => new Promise((resolve, reject) => {
  setTimeout(() => reject(new Error(`Error ${hen} => egg`)), 1000);
});
const cook = egg => new Promise((resolve, reject) => {
  setTimeout(() => resolve(`${egg} => chickEgg`), 1000);
});

getHen()
.then(getEgg)
.catch(()=> {
  return 'bread';
})
.then(egg => cook(egg))
.then(meal => console.log(meal))
.catch(console.log)
```
- getHen() 함수를 실행하여 'chicken' 이라는 문자열이 반환되어 getEgg 함수의 _hen_ 이라는 곳에 전달됩니다.
- getEgg() 함수를 실행하면서 에러를 발생시켰고 reject 함수를 명시하여 에러를 핸들링합니다.
- catch 메서드에서 에러를 핸들링하게되고 return 문에 의해 에러를 발생시키는대신 'bread'라는 문자열을 반환합니다.
- bread 라는 문자열이 egg 에 전달되며 cook(egg) 함수를 실행합니다.
- egg는 bread라는 문자열의 값이고, cook 함수의 결과로 'bread => chickEgg'라는 문자열이 다음 then 메서드에 전달됩니다.
- meal 이라는 값에 cook 함수의 결과값이 담기게 되고 console.log(meal) 함수에 의해 콘솔에 'bread => chickEgg' 라는 값이 출력됩니다.
