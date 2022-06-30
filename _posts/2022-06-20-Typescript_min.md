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
해당 문서는 아직 최적화가 이루어 지지 않았습니다.   
읽기 불편할 수도 있으며 이상한 문맥과 맞춤법이 당신을 불편하게 할 수도 있습니다.


# Typescript 왜?

# 1-1. Javascript
Javascript는 원래 인터프리터 언어의 한계로 아주 단순한 일만을 처리하였습니다. 하지만 구글에서 V8엔진을 발표, JIT(Just-In-Iime) 컴파일러를 적용하며 웹에서 C와 동등한 속도를 가지게 해주었고 이를 바탕으로 발전하여 이제는 웹페이지만이 아닌 서버프로그램을 작성하는데도 사용될 정도로 널리 쓰이게 되며 Javascript의 특징이 여러 문제를 발생 시키게 됩니다.

# 1-2. Javascript, 양날의 검
Javascript는 기본적으로 컴파일링되지 않는 인터프리터 언어인데 이로 인해 오류의 사전검출이 __거의__(문법이 틀려도 실행시에만 오류가 발생!) 되지 않는다는 특성을 가지고 여기에 타입 없음, 프로토타입 기반 언어라는 특성까지 겹쳐져 자동완성도 재대로 되지 않아 정말 잘 다루지 않는 이상 수 많은 오류 특히 Uncaught TypeError가 반겨 주었고 그마저도 발생하면 다행인 undefined와 null, 문자, 숫자가 연산 되어 합쳐 지면 정말 그만큼 시간 갈아 버리는 일이 없었고 이렇게 환장할 일이 가득한 Javascript지만 수많은 장점이 있었기에 미처 버리지는 못해 Javascript의 양날의 검이자 가장 큰 특징인 타입없음과 프로토타입기반의 기능을 크게 제한시켜 Microsoft에서 만들어 낸것이 바로 Typescript입니다.

# 1-3. Typescript 왜?
> Javascript의 문제 단순한 숙련도 이슈 아닌가?   
단순하게 보자면 위에서 언급한 Javascript의 문제는 그저 개발자의 숙련도 이슈 때문으로 그 개발자를 다른 사람으로 바꿔야 한다고 생각할 수 있습니다.   
사실 어느 정도는 맞습니다. 언어의 문제는 이상한 버그가 아닌 정확한 특성인 이상 개발자의 문제죠.   
오히려 기존의 숙련된 개발자에게 Typescript는 자동완성이 더 잘된다 말고는 오히려 불편한 제약 사항이 많기도 합니다.

__그런데 왜 굳이 Typescript를 사용하게 되었을까요?__

가장 큰 이유는 __좋은 코드__ 때문입니다.

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
그렇게 저 코드가 수백줄에 걸치는 엄청난 양이 되어서 완성 되고난 뒤에는 고양이가 가지고 논 실뭉치 처럼 코드를 풀어가기가 매우 어려워 질겁니다. 물론 숙련된 개발자는 저런식으로 코드를 짜지 않거나 아주 단순한 부분에서 한정된 처리를 위해 사용하는등 코드를 뒤엉킨 실뭉치로 만들지는 않습니다. 하지만 그런 사람은 전설의 포켓몬 마냥 희귀한 존재고 대부분은 개발자가 적당히 잘되는 코드를 작성한 뒤에 먼 훗날의 누군가가 이런 문제로 고통받아 이런 코드를 짠 개발자를 개발자 였던것으로 만들고 싶어 하죠.   
Javascript의 극악의 자유도에서 발생하는 이러한 문제를 사전에 막기 위해 Typescript를 사용합니다.

# Typescript 그래서 어떻게?

이제 Typescript를 사용해야 할 필요성이 느껴지신다면 Typescript의 사용법을 익혀볼 차례입니다.

# 2-1. 타입선언법
Javascript와 동일하게 var, let, const로 변수 선언을 하고 변수이름에 :T형태로 타입을 선언하게 됩니다.
```typescript
let a: string = 'A';
```

# 2-2-0. Javascript의 타입
Javascript에도 타입이 있기는 합니다.   
string, number, boolean, undefined, object, function, symbol, bigint, 이렇게 8가지 타입이 있고 null과 undefined를 제외한 6가지 타입은 첫글자가 대문자로 시작하는 레퍼클래스도 있습니다. Typescript는 이러한 차이를 구분하기 때문에 Java에서 int와 Integer가 다른 것처럼 number와 Number를  다른 것으로 취급합니다.

# 2-2. Typescript의 타입
Typescript에는 여러가지 타입이 있는데 그중 흥미로운 몇가지만 살펴보겠습니다.

- string   
  원시타입인 string입니다.   
  대부분의 타입과 합연산을 했을 경우 해당 타입을 문자열로 변환시켜 합쳐지는 특징이 있습니다.
  ```typescript
  let str = 'a';
  str += 1;
  console.log(str); // a1;

  let str2 = 'b';
  str += {};
  console.log(str); // a[object Object];
  ```

- number   
  원시타입인 number입니다.   
  10진수 뿐만 아니라 8진수나 16진수도 사용가능하지만 값은 10진수 값으로 계산됩니다.
  ```typescript
  let num1 = 16;    // 16
  let num2 = 0020;  // 16
  let num3 = 0x01;  // 16
  ```

- 객체 타입   
  객체 타입은 객체를 나타낼때 사용되며 특징으로는 타입 체크 방식이 있습니다.   
  객체는 원시 타입인 string이나 number와 다르게 해당 객체 타입의 필수 속성이 할당할 객체의 속성과 부분집합 관계에 있다면 허용됩니다.

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

- 유니언 타입   
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

# 2-2. 타입 추론
Typescript는 타입선언을 굳이 하지 않더라도 초기화된 값에서 자동적으로 타입을 얻습니다. 앞에서 보았던 const name = 'Godzilla'가 'Godzilla'타입으로 정의되었던 것 처럼요.   
이는 변수 뿐만 아니라 함수의 반환값이나 enum의 자동할당과 같은 부분에도 적용됩니다.   
이렇게 추론된 변수에 타입을 선언하는 것은 불필요한 코드이기 때문에 lint와 같은 보조도구를 사용한다면 변수의 경우 단순 추론가능한 타입의 타입 선언을 하였을 경우 다음과 같은 오류를 표시합니다.
```typescript
let num: number = 0;
 //error    Type number trivially inferred from a number literal, remove type annotation  @typescript-eslint/no-inferrable-types
```
하지만 함수의 경우는 오히려 타입 단언을 지정하는 것을 권장합니다.   
그 이유는 변수에는 값을 넣고 그 값을 활용하는 것이 주된 목적인 반면 함수는 어떠한 작동을 거쳐 원하는 값을 반환하는 것이 주 목적입니다.
그렇기에 변수는 값에 타입을 맞추고 함수는 반환 타입에 값을 맞춰야 합니다.
