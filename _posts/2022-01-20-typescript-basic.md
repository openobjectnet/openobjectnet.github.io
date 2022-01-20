---
title:  "타입스크립트 정리"
search: false
categories: 
  - TypeScript
toc: true  
last_modified_at: 2022-01-20T08:06:00-05:00
tags:
  - TypeScript
  - JavaScript
---

# Intro
안녕하세요 곽지용 사원입니다.   
캡틴판교님의 타입스크립트 강의를 통해 배운 기본적인 정보와   
다시 한번 확인하면 좋은 부분들을 정리했습니다.   

# 1. 타입스크립트란 ?
타입스크립트는 타입이 부여된 자바스크립트입니다.   
자바스크립트에서 확장된 언어이고    
브라우저가 이를 사용하려면 다시 자바스크립트로 변환 해주어야 합니다.

* 타입스크립트를 써야하는 이유
  1. 에러의 사전방지   
  2. 코드 가이드 및 자동 완성   

## 1-1. 에러의 사전방지   
간단한 sum 함수가 있다고 가정을 해봅니다.   
```javascript
function sum(a, b){
  return a + b;
}
```
자바스크립트 변수에는 타입을 명시할 순 없지만   
데이터 자체의 타입이 있고 그로인해 결과가 달라지는 경우가 많습니다.   
```javascript
sum(10, 20); // 30
sum('10', '20'); // 1020
```
> 자바스크립트에 파라미터 타입은 없지만 원하는 타입은 언제나 존재한다.   

타입스크립트는 타입을 명시해줌으로써 원하는 결과를 도출 할 수 있도록 도와줍니다.
```typescript
function sum(a: number, b: number) {
  return a + b;
}
```
## 1-2. 코드 가이드 및 자동 완성
자바스크립트는 타입이 명시 되어있지 않기 때문에   
개발툴에서는 자동완성을 지원하는 범위가 매우 한정적입니다.

```javascript
var num = 123;
var str = '123';

//타입이 상관없는 경우 (한정적 지원가능)
num.toLocaleString(); // 결과 '123'
str.toLocaleString(); // 결과 '123' 

//타입이 정해져있는 경우 (자동완성 지원불가능)
num.toExponential(); // 결과 '1.23e+2'
str.toExponential(); // Uncaught TypeError 
```
> 타입에러가 나는 함수는 타입이 명시되어있지 않으면   
> 추론이 어렵기 때문에 당연히 개발툴에서 자동완성을 지원하기가 어렵다.   


# 2. 기본 타입   
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
  - Unknown (3.0)

타입스크립트의 기본타입은 13가지가 있습니다.   
그중 주의가 필요한 **Any** 타입과 **Unknown**타입을 살펴보려합니다.   

## 2-1. Any 타입
말 그대로 모든 타입에 대해서 허용하는 타입입니다.   
```typescript
let value: any;
value = 10;
value = '10';
value = ['10', 10, null];
```
> any타입을 그대로 두면 자바스크립트에서 발생했던 문제들이 그대로 이어집니다.   
> 때문에 이러한 any 타입으로부터 타입을 구체화 할 필요가 있습니다.   

## 2-2. Unknown 타입
unknown은 typescript 3.0부터 추가된 타입으로    
any 타입과 같이 모든 타입에 대해서 허용하는 타입입니다.   
하지만 unknown 타입 값은 any와 unknown 타입을 제외한 타입의 변수에 할당이 불가능합니다.  
any 처럼 모든 타입을 받을 수 있지만 타입체킹을 강제 합니다.
```typescript
let anyValue: any = 'any';
let unknownValue: unknown = 'unknown';

let strValue: string; 

strValue = anyValue; //ok
strValue = unknownValue; //error : 타입가드가 필요

strValue = unknownValue as string //ok : 타입명시 완료

if (typeof unknownValue === 'string') { 
  strValue = unknownValue; //ok : 타입검증 완료
}
```
> any의 모든 타입을 수용하는 기능은 필요했으나 안전성의 문제가 있었고   
> 이 때문에 타입체킹을 필요로하는 unknown이 나오게 되었습니다.   


# 3. type, interface
다음과 같이 간단한 오브젝트 배열 타입 데이터를 사용하는 코드가 있다고 가정해봅니다.   
```typescript
let todoItems: { id: number; title: string; done: boolean; }[];

function fetchTodoItems(): { id: number; title: string; done: boolean; } [] {
  return [
    { id: 1; title: '안녕'; done: false; } 
    { id: 2; title: '안녕'; done: false; } 
    { id: 3; title: '안녕'; done: false; } 
  ]; 
}

function addTodo(todo: { id: number; title: string; done: boolean; }): void {
  todoItems.push(todo);
}
```
> 단일 변수에 타입을 주는 것은 쉬운데   
> 간단한 오브젝트여도 안쪽 필드를 일일히 타입을 주는건 힘들어보입니다.   

## 3-1. type, interface 정의하기 
인터페이스는 상호 간 정의한 약속 또는 규칙을 의미합니다.   
위에서 작성했던 오브젝트 스펙을 아래와 같이 인터페이스로 정의할 수 있습니다.   

```typescript
interface Todo { //파스칼 케이스로 작성
  id: number; 
  title: string; 
  done: boolean;
}

let todoItems: Todo[]; //반복되는 부분을 해소시켜준다.

//반환값이 오브젝트
function fetchTodoItems(): Todo [] { //반복되는 부분을 해소시켜준다.
  return [
    { id: 1; title: '안녕'; done: false; } 
    { id: 2; title: '안녕'; done: false; } 
    { id: 3; title: '안녕'; done: false; } 
  ]; 
}

function addTodo(todo: Todo): void { //반복되는 부분을 해소시켜준다.
  todoItems.push(todo);
}
```

type 또한 비슷한 방식으로 정의되며 비슷한 기능으로 수행됩니다.

```typescript
type Todo = { //파스칼 케이스로 작성
  id: number; 
  title: string; 
  done: boolean;
}
```

## 3-2. type, interface 비교
type과 interface는 기본적인 정의 방식도 비슷하고 기능도 비슷합니다.   
하지만 구분해서 사용해야 하는 이유가 존재합니다.

### **interface는 확장이 되고 type은 안된다?**   
interface는 아래 코드와 같이 extends 문법을 통해 확장이 가능합니다.   
하지만 type은 extends 문법을 사용 할 수 없습니다.   
때문에 강의에서는 type는 확장이 안되는 것처럼 설명되어있는데 둘 다 확장은 가능합니다.   

* interface 확장   
    ```typescript
    interface Todo {
      id: number; 
      title: string; 
      done: boolean;
    }
    interface Todo2 extends Todo { //1. extends를 통해 확장
      name: string;
    }
    interface Todo { //2. 동일 명칭을 통한 선언 병합 확장
      name: string;
    }
    ```
* type 확장
    ```typescript
    //type case
    type Todo = {
      id: number; 
      title: string; 
      done: boolean;
    }
    type Todo2 = Todo & { // &(Intersection) 연산자를 통해 확장
      name: string;
    }
    ```
> 확장이 불가능한 것은 아닙니다. 


* **그렇다면 둘 중 무엇을 써야하는가?**   
이 부분에 대해서는 명확한 답은 없는 것 같습니다...   
일반적인 경우에는 interface를 쓰고   
복잡한 타입의 경우 type을 병행하는 것을 고려해야 할 것 같습니다.   

  1. interface를 써야하는 이유   
    - 선언적 병합이 가능하기 때문에 협업이나 외부모듈화를 진행할 때 유연한 합성이 가능   
    - type은 확장시 캐시를 생성하지 않기 때문에 유효성을 판단할 때 모든 구성요소의 타입을 체크하므로 interface보다 성능이 떨어지는 부분도 존재   

  2. type을 써야하는 이유   
    - interface의 경우 유니온 타입을 지원하지 않기 때문에 복잡한 타입의 경우 유리   
    - type의 경우 튜플이나 배열 지원   

# 4. JS에서 TS로 변환
절차는 아래와 같습니다.

    1. 타입스크립트 환경 설정 및 ts 파일로 변환   
    2. any 타입 선언   
    3. any 타입을 더 적절한 타입으로 변경   

1. **먼저 프로젝트에 타입스크립트를 설치합니다.**    
    npm i typescript -D   
2. **타입스크립트 설정파일을 만듭니다.**   
tsconfig.json를 생성하고 기본값을 추가합니다.   
```json
{
  "compilerOptions": {   
        "allowJs": true,
        "target": "ES5",
        "outDir": "./dist",
        "moduleResolution": "Node",
        "lib": ["ES2015", "DOM", "DOM.Iterable"]
  },
  "include": ["./src/**/*"],
  "exclude": ["node_modules", "dist"]
}
```
3. **js 파일을 ts로 변경**   
.js 파일들을 .ts로 모두 변경하고 컴파일 에러가 나는 것들을 수정합니다.   

4. **any로 선언**   
tsconfig.json에 noImplicitAny: true 옵션을 추가합니다.   
가능한한 타입을 지정할 수 있는 곳은 선 지정합니다.   
라이브러리의 타입지정의 경우 https://definitelytyped.org/ 에서 @types 관련 라이브러리를 찾아서 설치합니다.   
> 타입지정이 어려운곳은 명시적으로 any 타입으로 선언하고 이후 점진적으로 타입을 지정해나갑니다.   

5. **strict 모드 설정**   
만약에 전체적인 모듈화를 진행한다면 필요없지만   
엄격모드가 필요한경우 아래의 코드를 타입스크립트 설정파일에 추가합니다. 
```json
{ 
  "strict": true,
  "strictNullChecks": true,
  "strictFunctionTypes": true,
  "strictBindCallApply": true,
  "strictPropertyInitialization": true,
  "noImplicitThis": true,
  "alwaysStrict": true,
}
```
> any로 되어 있는 타입을 최대한 더 적절한 타입으로 변환합니다.   
> as와 같은 키워드를 최대한 사용하지 않도록 고민해서 변경합니다.   