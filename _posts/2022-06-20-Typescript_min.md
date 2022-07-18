---
title: "Typescript - 왜? 그래서 어떻게?"
categories: 
  - TypeScript
tags:
  - TypeScript
  - Typescript
  - Ts
  - ts
  - js
  - Js
  - javascript
  - Javascript
toc: true
toc_label: "목차"
toc_sticky: true
author: 민홍기
---
# 시작하기에 앞서
해당 문서는 아직 수정중입니다.   
오류 및 이상한 문맥이 있을 수 있습니다.

# Typescript 왜?

## 1-1. Javascript
Javascript는 원래 인터프리터 언어의 한계로 아주 단순한 일만을 처리하였습니다. 하지만 구글에서 V8엔진을 발표, JIT(Just-In-Time) 컴파일러를 적용하며 그 성능을 비약적으로 상승 시켰고 이를 바탕으로 발전하여 이제는 웹페이지 뿐만  아니라 서버프로그램을 비롯한 여러 분야에서도 사용되기 시작하며 원래 단순한 일만을 처리하던 Javascript의 특징이 여러 문제를 발생 시키게 됩니다.

## 1-2. Javascript, 양날의 검
Javascript는 기본적으로 컴파일링되지 않는 인터프리터 언어인데 이로 인해 오류의 사전검출이 __거의__(문법이 틀려도 실행시에만 오류가 발생!) 되지 않는다는 특성을 가지고 여기에 타입 없음, 프로토타입 기반 언어라는 특성까지 겹쳐져 자동완성도 재대로 되지 않아 개발자들은 수많은 오류에 시달려야 했습니다. 그러던 그때 Microsoft에서 Javascript의 흘려내릴듯한 유연함을 조금 굳혀서 만들어 낸것이 바로 Typescript입니다.

## 1-3. Typescript 왜?
> Javascript의 문제 단순한 숙련도 이슈 아닌가?   
단순하게 보자면 위에서 언급한 Javascript의 문제는 그저 개발자의 숙련도 이슈 때문으로 그 개발자를 다른 사람으로 바꿔야 한다고 생각할 수 있습니다.   
사실 어느 정도는 맞습니다. 언어의 문제는 이상한 버그가 아닌 정확한 특성인 이상 개발자의 문제죠.   
오히려 기존의 숙련된 개발자에게 Typescript는 자동완성이 더 잘된다 말고는 오히려 불편한 제약 사항이 많기도 합니다.

__그런데 왜 굳이 Typescript를 사용하게 되었을까요?__

이유는 바로 __좋은 코드__ 때문입니다.

자 어떤 개발자가 사람의 신원을 확인하는 프로그램을 만들었다고 합시다.
```javascript
// 먼저 사람이 있고 각각 이름과 나이가 있습니다.
let human1 = {
  name: 'Foo',
  age: 010,
}
let human2 = {
  name: 'Bar',
  age: 030,
}
// 그리고 신원이 확인 되었는지 확인을 하고...
function isChecked(human) {
  // 갑자기 human에 checked라는 속성이 생겼습니다. 뭐죠?
  let rValue = human.checked;
  delete human.checked;
  return rValue;
}
// 신원을 확인합니다.
function check(human) {
  // 여기서 checked속성이 추가 되었습니다.
  human.checked = human.name === 'Foo';
}
```
저 checked속성이 어디서 추가되고 어디서 사라지는지는 만든 사람만이 알 수 있죠.   
심지어 동적으로 추가된 속성이라 자동완성도 되지 않습니다.
아니면 누군가 협업을 할때 그 사람도 checked속성을 정의 해서 더 난잡하게 만들 수도 있죠
```javascript
function filterHumans(anys) {
  anys.forEach(x=>{
    if(any.name && any.age) {
      //이름과 나이가 있는 것은 사람으로 쳐주고 체크해줍니다.
      any.checked = true;
    }
  });
  return anys.filter(x=>{
    //체크된 것들을 돌려줍니다.
    return x.checked;
  })
}
```
그렇게 저 코드가 수백줄에 걸치는 엄청난 양이 되어서 완성 되고난 뒤에는 고양이가 가지고 논 실뭉치 처럼 코드를 풀어가기가 매우 어려워 질겁니다. 물론 숙련된 개발자는 저런식으로 코드를 짜지 않거나 아주 단순한 부분에서 한정된 처리를 위해 사용하는등 코드를 뒤엉킨 실뭉치로 만들지는 않습니다. 하지만 그런 사람은 전설의 포켓몬 마냥 희귀한 존재고 대부분은 개발자는 적당히 잘되는 코드를 작성한 뒤에 먼 훗날의 누군가가 이런 문제로 고통받고 이런 코드를 만든 개발자를 저주하며 사람이였던 것으로 만들고 싶어 할 겁니다.   
이런 Javascript의 극악의 자유도에서 발생하는 문제를 사전에 막기 위해 Typescript를 사용하게 되죠.

# Typescript 그래서 어떻게?

이제 Typescript를 사용해야 할 필요성이 느껴지신다면 Typescript의 사용법을 익혀볼 차례입니다.

## 타입

### 2-0. Typescript의 사상
Typescript는 기존의 타입을 지정해야하는 Java, C와 같은 언어와는 조금 다른 부분들이 많습니다.   
그 이유는 Typescript의 원칙과 관련 있는데 Typescript는 Javascrpit의 과한 자유도로 인해 생기는 문제를 사전에 검증할 수 있도록 할 뿐입니다.   
즉 자유로운 코드가 문제가 아니라 그 자유로움에서 발생하는 문제가 진짜 문제인 것이죠 그러한 원칙으로 인해 Javascript에서 가능한 모든 것은 Typescript에서도 가능하도록 디자인 되었고 이러한 특색에 맞춰 Typescript에는 Java와 C같은 기존 언어들에는 없던 여러가지 문법과 타입이 존재하게 되었습니다.   
그럼 이제 진짜로 Typescript의 사용법을 알아 보러 가봅시다.

### 2-1. 타입선언법
Javascript와 동일하게 var, let, const로 변수 선언을 하고 변수이름에 :T형태로 타입을 선언하게 됩니다.   
Javascript의 변수 규칙과 동일하게 var는 전역, let은 지역, const는 지역 상수를 의미 합니다.
```typescript
let a: string = 'A';
```

### 2-2-0. Javascript의 타입
Javascript에도 타입이 있기는 합니다.   
string, number, boolean, undefined, object, function, symbol, bigint, 이렇게 8가지 타입이 있고 null과 undefined를 제외한 6가지 타입은 첫글자가 대문자로 시작하는 레퍼클래스도 있습니다.   
Typescript의 원칙에 따라 Typescript는 해당 타입들 또한 모두 있습니다.   
그 뿐만 아니라 HTMLElement를 비롯한 각종 객체들 또한 구현되어 있죠.

### 2-2. Typescript의 타입
Typescript에는 여러가지 타입이 있는데 그들을 조금 살펴보겠습니다.

- string   
  원시타입인 string입니다.   
  대부분의 타입과 합연산을 했을 경우 해당 타입을 문자열로 변환시켜 합쳐지는 특징이 있습니다.
  ```typescript
  let str: string = 'a';
  str += 1;
  console.log(str); // a1;

  let str2: string = 'b';
  str += {};
  console.log(str); // a[object Object];
  ```

- number   
  원시타입인 number입니다.   
  10진수 뿐만 아니라 8진수나 16진수도 사용가능하지만 값은 10진수 값으로 계산됩니다.
  ```typescript
  let num1: number = 16;    // 16
  let num2: number = 0020;  // 16
  let num3: number = 0x01;  // 16
  ```
- boolean
  원시타입인 boolean입니다.   
  오직 true/false만이 값으로 할당될 수 있습니다.
  ```typescript
  let bool1: boolean = true;
  let bool2: boolean = false;
  ```  

- 객체 (Object)   
  객체는 객체를 나타낼때 사용되며 특징으로는 타입 체크 방식이 있습니다.   
  객체는 원시 타입인 string이나 number와 다르게 해당 객체의 필수 속성이 할당할 객체의 속성과 부분집합 관계에 있다면 허용됩니다.

  ```typescript
  let ob1 = { name: 'Bar' };
  let ob2 = { name: 'Foo', age: 99 };
  ob1 = ob2;
  ob2 = ob1;
  //'age' 속성이 '{ name: string; }' 형식에 없지만 '{ name: string; age: number; }' 형식에서 필수입니다.ts(2741)
  ```
  B가 A의 부분 집합이라면 참이 되는 구조   
  > **주의**   
  > ob1에 ob2가 할당되었다고 해서 ob2에 있는 age가 없어지는 것은 아닙니다!   

- 배열 (Array)
  배열입니다.   
  ```typescript
  let numList: Array<number> = [1, 2, 3, 4];
  let strList: Array<string> = ['one', 'two', 'three'];
  let boolList: Array<boolean> = [true, false];
  ```

- 튜플 (Tuple)
  타입과 개수가 고정된 배열입니다.   
  단 요소들의 타입이 딱히 같을 필요는 없습니다.
  ```typescript
  let tuple: [string, number] = ['one', 1];
  ```
- 열거형 (Enum) 
  집합을 정의할 수 있습니다.   
  아무런 값을 지정하지 않는다면 순서대로 0, 1, 2, 3...으로 증가된 값을 가집니다.
  ```typescript
  enum Color {
    Red = 'Red',
    Blue = 'Blue',
    Green = 'Green',
  }
  const c = Color.Red;
  ```

- void
  없음을 나타냅니다.   
  보통 함수에서 반환값이 없음을 나타냅니다.   
  변수에서 사용할 경우 undefeind만이 할당 가능합니다.   
  이는 Javascript에서 함수의 동작이 아무것도 반환하지 않을 경우 undefined를 반환하기 때문입니다.
  ```typescript
  const voidValue2: void = undefined;

  function sayHello(): void {
    alert('Hello!!!!!!!!!!!!!!');
  }
  ```

- Null and Undefined
  위의 void와는 다른 Null과 Undefined도 별도의 타입이 존재 합니다.   
  각각 오직 null과 undefeind만이 할당 가능합니다.   
  ```typescript
  const undefinedValue: undefined = undefined;
  const nullValue: null = null;
  ```
- Never
  절대로 발생할 수 없음을 나타냅니다.   
  이게 무슨 소리인지는 다음 예제와 함께 보겠습니다.
  ```typescript
  function error(message: string): never {
    throw new Error(message);
  }
  ```
  다음과 같은 경우 다음 함수는 반환값을 가질 수 없습니다.   
  이러한 상황에서 함수가 끝나지 않는다는 것을 표현하는 것이 naver입니다.

- 교차 타입 (Interserction Types)
  교차 타입은 여러가지 타입을 하나의 타입으로 묶는 타입으로 해당 타입들의 합집함으로 정으 ㅣ됩니다.   
  ```typescript
  let ob: { name: string } & { age: number } = { name: 'Bar' };
  /*
  '{ name: string; }' 형식은 '{ name: string; } & { age: number; }' 형식에 할당할 수 없습니다.
  'age' 속성이 '{ name: string; }' 형식에 없지만 '{ age: number; }' 형식에서 필수입니다.ts(2322)
  */
  ```

- 유니언 타입 (Union Types)
  유니언 타입은 하나의 변수에 2가지 타입이 들어올 수 있는 경우에 사용되는 타입으로 기본적으로 2가지 타입의 교집합으로 정의 됩니다.   
  교집합이 아닌 특정 타입이 필요할 경우에는 타입 단언이나 타입 검사 함수(Array.isArray)등으로 해당 타입이 무엇인지 확인해야 합니다.

  ```typescript
  let ob:{ name: string } | { name: string, age: number }  = { name: 'Bar' };

  (ob as { name:string, age:number }).age; //no error
  if(check(ob)) {
    ob.age // no error
  }
  ob.age
  //'{ name: string; }' 형식에 'age' 속성이 없습니다.ts(2339)

  //해당 함수는 타입 가드라는 표현식으로 뒤에서 다룰 예정입니다.
  function check(ob: { name: string; age?: number; }): ob is { name: string; age: number } {
    return ob.age ? true : false;
  }
  ```

- 리터럴 타입   
  리터럴 타입은 특정한 값만이 올 수 있음을 지칭합니다.   
  대표적인 사례는 원시값으로 선언된 const로 해당 원시값을 리터럴 타입으로 가지게 됩니다.
  ```typescript
  const name = 'Godzilla';
  //const name: "Godzilla"
  ```
  리터럴 타입은 보통 유니온 타입으로 선언하여 특정한 값만이 오도록 정의합니다.
  ```typescript
  let name: 'Godzilla' | 'Mothra' | 'King Ghidorah';
  names = 'Godzilla';
  names = 'Mothra';
  names = 'Hedorah';
  //'"Hedorah"' 형식은 '"Godzilla" | "Mothra" | "King Ghidorah"' 형식에 할당할 수 없습니다.ts(2322)
  ```

- 유틸리티 타입
  Typescript는 일반적인 타입 변환을 쉽게 하기 위한 유틸리티 타입을 제공합니다.   
  워낙 다양한 유틸리티 타입이 존재하기 때문에 링크로 대체합니다.
  [유틸리티 타입 - Typescript 핸드북](https://www.typescriptlang.org/ko/docs/handbook/utility-types.html)

- any
  되도록이면 멀리 하십쇼.   
  이 타입은 모든 것을 허용합니다. null, undefeind, object, array... 모두다요.   
  그렇기 때문에 Typescript의 타입의 의미를 퇴색시키고 타입 추론을 어렵게 합니다.   
  금단의 흑마술서 같은 친구니 봉인해 두시기를 바랍니다.   
  > **tip**   
  > 가끔 보면 함수의 반환 타입을 알 수 없거나 해당 타입을 사용할 수 없는 경우가 있습니다. (ex setTimeout의 NodeJS.Timeout)   
  > 그럴때는 typeof연산자를 사용하시는 것을 추천합니다. 가져올 수 없는 타입도 추론이 가능해지고 나중에 반환 타입이 추가되거나 바뀌었을 때에 바뀐 타입으로 추론이 가능하기 때문입니다.

### 2-2. 타입 추론
Typescript는 타입선언을 굳이 하지 않더라도 초기화된 값에서 자동적으로 타입을 얻습니다. 앞에서 보았던 const name = 'Godzilla'가 'Godzilla'타입으로 정의되었던 것 처럼요.   
이는 변수 뿐만 아니라 함수의 반환값이나 enum의 자동할당과 같은 부분에도 적용됩니다.   
이렇게 추론된 변수에 타입을 선언하는 것은 불필요한 코드이기 때문에 lint와 같은 보조도구를 사용한다면 변수의 경우 단순 추론가능한 타입의 타입 선언을 하였을 경우 다음과 같은 오류를 표시합니다.
```typescript
let num: number = 0;
 //error    Type number trivially inferred from a number literal, remove type annotation  @typescript-eslint/no-inferrable-types
```
하지만 함수의 경우는 오히려 타입 단언을 지정하는 것을 권장하는데.   
그 이유는 변수에는 값을 넣고 그 값을 활용하는 것이 주된 목적인 반면 함수는 어떠한 작동을 거쳐 원하는 값을 반환하는 것이 주 목적입니다.
그렇기에 변수는 값에 타입을 맞추고 함수는 타입에 값을 맞춰야 합니다.

### 2-3. 타입을 다루는 방법
타입을 다루는 방법에 대하여.   
해당 챕터에서는 제네릭 타입과 같은 특수한 타입 유형들을 활용하여, 타입 별칭과 인터페이스같은 타입을 다루는 방법에 대하여 알아 보겠습니다.

- 타입 별칭과 인터페이스   
  가장 먼저 타입 별칭과 인터페이스가 무엇이지 부터 알아 봅시다.   
  먼저 타입 별칭이란 위에서 보았던 { name: string, age: number }과 같은 복잡한 타입을 간편하게 사용하기 위한 기능으로 하나의 타입으로 정의하는 것이 쓰기 편하게 변수에 저장하는 것에 더 가깝습니다.   
  다음 예제를 보면
  ```typescript
  type tString = string;
  const a: tString = 'a';
  //const a: string
  ```
  tString으로 선언된 a가 string으로 추론됩니다.   
  이는 tString이 별도의 타입이 아닌 string의 또다른 이름으로 사용되는 것이기 때문입니다.   

  겉모습과 사용방법만 보자면 인터페이스도 상당히 비슷합니다.   
  하지만 인스페이스는 새로운 타입 유형을 정의한다는 개념적인 차이가 있습니다.   
  이로 인해 차이가 발생하는데 다음은 타입과 인터페이스의 확장 예제입니다.   
  ```typescript
  interface Foo {
    human: { name:string };
  }
  interface Far extends Foo{
    human: { age:number };
  }
  
  type Bar = {
    human: {
      name: string
    };
  }
  type Boo = {
    human: {
      age: number;
    }
  } & Bar
  
  const a:Boo = {
    human: {
      name: 'No!!!!!!!',
      age: -1,
    }
  };
  ```
  보다시피 인터페이스인 Far에서는 확장하려는 객체를 위반할 수 없지만 타입인 Boo는 기존 타입을 신경쓰지 않습니다.   
  이것이 바로 인터페이스와 타입의 차이라고 말하는 '확장가능성'입니다.   
  타입은 확장을 하지 못합니다.   
  저 확장은 Javascript에서 사용하는 오브젝트를 합치는데 사용되는 { ...Object1, ...Object2 }처럼 2가지의 타입을 하나의 타입으로 합치는 것뿐 입니다.   
  이는 기존의 타입을 확장하는 것이 아닌 그저 새로운 타입을 만든는 것이죠.

  그렇다면 인터페이스와 타입 별칭은 언제 어디에서 사용해야 할까요?   
  사실 딱히 정해져 있는 부분은 없습니다.   
  둘다 정규 문법이 아닌 편의성을 위한 기능일 뿐이니까요.   
  그래도 다음에 코드를 읽는 사람을 위해 최소한 상속 여부 정도의 구분은 해주는 것이 좋다고 생각합니다.   

  > **Tip**
  > 타입을 &연산자로 합칠때 원시타입같은 경우에는 아예 naver(절대로 할당될 수 없음)타입으로 선언됩니다.
  > ```typescript
  > type Bar = {
  >   name: string;
  > }
  > type Boo = {
  >   name: number
  > } & Bar
  > 
  > const a:Boo = {
  >   name: 'string? number?'
  >   // 'string' 형식은 'never' 형식에 할당할 수 없습니다.ts(2322)
  > };
  >```

- 선택적 타입과 읽기 전용 속성과 인덱스 서명
  오브젝트 형식의 타입 선언에서 사용되는 편리한 기능들입니다.
  - 선택적 타입
    앞서 '필수 속성'이라는 단어를 보셨을 겁니다.   
    그렇다면 필수가 아닌 속성도 있겠죠 그게 바로 선택적 타입입니다.   
    선택적 타입은 해당 속성이 선언되었을 수도 아닐 수도 있는 옵션같은 존재로 표시합니다.   
    그렇기 때문에 필수 속성과는 달리 생략이 가능하지만 해당 속성에 접근할 때에는 타입과 undefined의 유니온 타입으로 추론됩니다.
    ```typescript
    type Foo = {
      name?: string;
      age: number;
    };

    const foo:Foo = {
      age:10,
    }

    const str:string = foo.name;
    /*
    'string | undefined' 형식은 'string' 형식에 할당할 수 없습니다.
      'undefined' 형식은 'string' 형식에 할당할 수 없습니다.ts(2322)
    */
    ```
    - 선택적 타입을 편리하게 다루는 방법
      1. object.property?.method();
        object의 property가 null, undefined와 같이 빈 값이라면 이어지는 체인을 생략합니다.
      2. anyValue ?? defaultValue;
        anyValue가 null, undefined와 같이 빈 값이라면 defaultValue를 반환합니다.   
        3항 연산자를 대신하기 좋습니다.
      3. object.property!.method();
        object의 property가 정의되었음을 보증합니다. 물론 보증이 틀렸는지는 추적하지 못합니다.

  - 읽기 전용 속성
    해당 속성이 생성 이후에는 할당될 수 없음을 나타냅니다.
    ```typescript
    type Foo = {
      readonly name: string;
    };

    const foo:Foo = { name: 'just' };

    foo.name = 'newValue';
    //읽기 전용 속성이므로 'name'에 할당할 수 없습니다.ts(2540)
    ```
  - 인덱스 서명
    여태까지 보았던 형식들은 전부 키가 무엇인지 알고 있었습니다.   
    하지만 키가 무엇이 올지 모르거나 가변형일 수도 있죠 그러한 상황에서 사용하는 것이 바로 인덱스 서명입니다.   
    인덱스 서명은 키를 타입수준으로 정의할 수 있는 기능입니다. (물론 key로 할당 가능한 타입만 가능합니다.)   
    ```typescript
    type Foo = {
      [key: string]: string;
    };
    type Bar = {
      [key: Foo]: number;
      //인덱스 시그니처 매개 변수 형식은 'string', 'number', 'symbol' 또는 템플릿 리터럴 형식이어야 합니다.ts(1268)
    };
    ```
    - 인덱스 서명 더 활용하기
      인덱스 서명은 단순하게는 Map처럼 사용가능한 오브젝트를 만들 뿐이지만 여러 연산자와 유틸리티 타입을 활용한다면 좀 더 다체롭고 깔끔하게 정의가 가능합니다.
      해당 예제는 asdf, qwer, zxcv를 키로 number값을 가지는 Foo입니다.
      ```typescript
      type Foo = {
        [key in 'asdf' | 'qwer' | 'zxcv']: number;
      };
      ```
      이런식으로 타입을 연관시킨다면 나중에 Foo의 키가 수정되었을때 Bar의 키도 같이 바뀌게 됩니다.   
    - 인덱스 시그니처
      타입의 하위 요소의 타입을 참조할 수 있습니다.
      ```typescript
      type foo = {
        name: string;
      }

      const a:foo['name'] = 12;
      //'number' 형식은 'string' 형식에 할당할 수 없습니다.ts(2322)
      ```
  - 맵드 타입
    이러한 옵션들을 이미 정의되어 있는 타입에서 가져오고 싶을 수도 있습니다.   
    그럴때 사용하는 것이 맵드 타입입니다.   
    맵드 타입은 기존의 정의된 타입을 재정의 할 수 있습니다.
    ```typescript
    type Foo = {
      [key in 'asdf' | 'qwer' | 'zxcv']: number;
    };

    type Bar = {
      [key in keyof Foo]: string;
    }
    ```

- 제네릭 타입
  Array와 같은 특수한 타입의 선언에서 사용되는 타입입니다.   
  아무런 타입이나 올 수 있지만 any가 아닌 받은 타입을 정의할 때 사용합니다.   
  ```typescript
  type Box<T> = {
    item: T
  };
  
  const numBox:BoX<number> = 'string';
  //'string' 형식은 'Box<number>' 형식에 할당할 수 없습니다.ts(2322)
  ```
  함수식에서는 제네릭의 정의를 하지 않아도 아규먼트의 타입에서 자동 추론됩니다.
  - 제네릭 제약조건
    아무것이나 올 수 있기에 제약을 걸 수도 있습니다.   
    제약이라기 보다는 특정한 타입의 확장형이라고 생각하면 좀 더 쉬울 것입니다.
    ```typescript
    type Boxs<T extends Array<any>> = {
      items: T;
    };

    const boxs:Boxs<Array<number>> = {
      items: ['string'],
      //'string' 형식은 'number' 형식에 할당할 수 없습니다.ts(2322)
    }
    ```
    함수식에서 나머지 매개변수를 정의할때도 사용 가능합니다.
    ```typescript
    function runFucntion<T extends any[], U>(fn: (...args: T) => U, ...args: T): U {
      return fn(...args);
    }

    // function runFucntion<[string], void>(fn: (args_0: string) => void, args_0: string): void
    runFucntion(alert, 'Say hello', 'Say good');
    // 1-2개의 인수가 필요한데 3개를 가져왔습니다.ts(2554)
    ``` 
  - 조건부 타입
    타입을 삼항식으로 표현합니다.   
    ```typescript
    function fn<T extends boolean>(x: T): T extends ture ? string : number;
    ```
    T가 U에 할당 가능하다면 x로 아니라면 y가 되는 형태입니다.   
  - 분산 조건부 타입
    단어 자체로는 상당히 추측하기 어려운데 해당 타입은 맵드 타입과 같은 여러 키와 값을 정의하는 부분에서 조건부 타입을 사용 하는 것 입니다.   
    ```typescript
    type BoxedValue<T> = { value: T };
    type BoxedArray<T> = { array: T[] };
    type Boxed<T> = T extends any[] ? BoxedArray<T[number]> : BoxedValue<T>;

    type T22 = Boxed<string | number[]>;
    // T22: BoxedValue<string> | BoxedArray<number>
    ```
    >**tip**
    > never타입을 반환한다면 해당 타입이 제거됩니다.
    > ```typescript
    > type BoxedArray<T> = { array: T[] };
    > type Boxed<T> = T extends any[] ? BoxedArray<T[number]> : never;
    >
    > type T22 = Boxed<string | number[]>;
    > /*
    >   type T22 = {
    >     array: number[];
    >   }
    > */
    > ```
- 선언 병합
  같은 이름으로 선언된 여러개의 개별적인 선언이 하나로 합쳐지는 것을 의미 합니다.   
  이는 다음과 같은 규칙으로 이루어 집니다.   
  클래스와 클래스는 병합할 수 없습니다.
  - 인터페이스의 병합
    가장 대표적인 예시로 각 선언의 하위 속성을 모두 가지도록 합쳐집니다.   
    이렇게만 본다면 타입의 & 연산자와 비슷하지만 & 연산자와 다른점은 같은 이름의 하위 속성은 같은 속성이여야 합니다.   
    & 연산에서는 타입이 합쳐졌지만 선언 병합에서는 이들이 합쳐지지 않고 오류를 발생시킵니다.
    ```typescript
    interface Foo {
      human: {
        name: string;
      }
    }
    interface Foo {
      human: {
        age: number;
        //후속 속성 선언에 같은 형식이 있어야 합니다. 'human' 속성이 '{ name: string; }' 형식이어야 하는데 여기에는 '{ age: number; }' 형식이 있습니다.ts(2717)
      }
    }
    ```
    하위 메소드에 경우에는 약간 다른데 중복된 선언을 동일 함수에 대한 오버로드로 취급합니다. 이때 나중에 선언되었을 수록 우선도가 높습니다.
    ```typescript
    interface Foo {
      sayMessage(message: string): void;
    }
    interface Foo {
      sayMessage(message: string): string;
    }
    let foo:Foo = {
      sayMessage(message: string) {
      /*
      '(message: string) => void' 형식은 '{ (message: string): void; (message: string): string; }' 형식에 할당할 수 없습니다.
        'void' 형식은 'string' 형식에 할당할 수 없습니다.ts(2322)
      */
        alert(message);
      }
    }
    ```
    단 예외도 있습니다.   
    단일 문자열 리터럴 타입인 매개변수가 있을 경우 해당 선언이 가장 높은 우선순위를 가지게 됩니다.
    ```typescript
    interface Foo {
      
    }
    ```
  - 네임스페이스의 병합
      기보적으로 인터페이스와 동일하게 동작 하지만 약간 다른 부분이 있습니다.   
      export된 부분만 통합되며 export되지 않은 부분은 오로지 해당 선언의 인터페이스 에서만 접근 가능합니다.   
      ```typescript
      namespace Foo {
        let use = true;

        export function sayUsed() {
          console.log(use);
        }
      }
      namespace Foo {
        export function getUse() {
          return use;
          //'use' 이름을 찾을 수 없습니다.ts(2304)
        }
      }
      ```
  - 클래스, 함수, 열거형과 네임스페이스의 병합
      네임스페이스의 선언이 병합할 선언을 따라야 합니다.   
      - 클래스
        ```typescript
        class Foo {
          human= Foo.human;
        }
        namespace Foo {
          export class human { };
        }

        ```
      - 함수
        ```typescript
        function Foo() {
          alert(Foo.age)
        }
        namespace Foo {
          export let age = 'foo';
        }
        ```
      - 열거형
        ```typescript
        enum Foo {
          name = 'Foo'
        }

        namespace Foo {
          export function sayName() {
            aert(Color.name);
          }
        }
  - 사용 이유
      그렇다면 어째서 이런 기능이 필요 할까요?   
      그 이유는 크게 2가지로
        1. 너무 길어지는 소스코드
          하나의 객체에 수많은 기능이 포함될 경우 코드 가독성이 현저히 떨어지게 됩니다.   
          이때 선언 병합을 사용한다면 기존처럼 기능을 몇개씩 묶은 새로운 객체를 생성할 필요 없이 그저 보기 좋게 나누면 됩니다.   
          ```typescript
          interface Foo {
            name: string;
            sayMyName(): void;
          }

          interface Foo {
            age: number;
            sayMyAge(): void;
          }
          ```

        2. 외부 코드에 대한 확장
          아주 가끔 Javascript의 기본 객체나 외부 모듈을 Prototype로 확장할 일이 있습니다.   
          이럴때 기존의 방식대로 할 경우 확장하고나 하는 속성이 없다며 오류를 발생 시킵니다.   
          이때 선언 병합을 사용해 문제를 해결 할 수 있습니다.
          ```typescript
          import { Bar } from '@/Bar';

          declare module '@/Bar' {
            interface Bar {
              phone: string;
              sayMyPhone(): void;
            }
          }
          Bar.prototype.phone = '010-1234-5678';
          Bar.prototype.sayMyPhone = ()=>{ alert(this.phone); };
          ```
- 믹스인
    딱히 기능은 아니고 프로그래밍 패턴의 일종입니다.   
    다른 클래스의 구현을 가져옵니다.   
    ```typescript
    class Foo {
      name?: string;
      sayName() {
        alert(this.name);
      }
    };
    class Bar {
      age?: number;
      sayAge() {
        alert(this.age);
      }
    };
    
    interface Far extends Foo, Bar { };
    
    class Far {
      sayName = Foo.prototype.sayName;
      sayAge = Foo.prototype.sayAge;
      
      introduce() {
        this.sayName();
        this.sayAge();
      }
    }
    ```
    공식 추천 방법 - 모든 구현을  가져옵니다.
    ```typescript
    function applyMixins(derivedCtor: any, baseCtors: any[]) {
      baseCtors.forEach(baseCtor => {
          Object.getOwnPropertyNames(baseCtor.prototype).forEach(name => {
              Object.defineProperty(derivedCtor.prototype, name, Object.getOwnPropertyDescriptor(baseCtor.prototype, name)!);
          });
      });
    }
    ```
### 2-4. 네임스페이스
  > **<span style="color:red">!주의</span>**   
  > 해당 구문은 '구식'으로 취급됩니다.   
  > [Eslint no-namespace](https://github.com/typescript-eslint/typescript-eslint/blob/main/packages/eslint-plugin/docs/rules/no-namespace.md)   
  
  코드를 묶을때 사용합니다.   
  선언병합이 하나의 객체를 여러 선언으로 나누어 코드 가독성을 높였다면 네임 스페이스는 관련 코드를 한대 모아 가독성을 높였습니다.
  ```typescript
  namespace SayHello {
    export interface HelloJect {
      sayHello(): void;
    }

    export class Foo implements HelloJect {
      sayHello() {
        alert('Hello my name is Foo');
      }
    }

    export class Bar implements HelloJect {
      sayHello() {
        alert('Hello');
      }
    }
  }
  ```
  네임스페이스는 글로벌선언을 하지 않아도 여러 파일에 걸쳐 작성 가능합니다.   
  ```typescript
  /// <reference path="namespace.ts" />
  namespace SayHello {
    export class Stew implements HelloJect {
      sayHello() {
        alert('Hellow?');
      }
    }
  }
  ```
  namespace의 별칭을 지정할 수 있습니다.   
  변수 선언을 해도 되는데 별칭 선언을 하는 이유는 별칭 선언의 경우 값의 변경시 원본의 값도 같이 변경되지만 변수 선언은 서로 다른 참조로 취급되기 때문에 값이 변경되지 않습니다.
  ```typescript
  import Foo = SayHello.Foo;
  ```

## 특수 기능

### 데코레이터
클래스 데코레이터입니다.   
> **주의**   
> 해당 기능은 '실험적'입니다. 향후 변경될 수 있습니다.   

