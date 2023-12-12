---
title:  "JS deep dive 요약"
search: false
categories: 
  - JavaScript
toc: true  
tags:
  - JavaScript
author: 허재원
---

JS deep dive 책을 읽으면서 알게 된 점에 대해 정리하였습니다. 

## <span style="color:#802548">_1. variables_</span>
### <variables style="color:#FFCCFF">variables </span>
- 수학에서 변수란 변하는 수다.

- 프로그래밍에서 변수란?
```
하나의 값을 담기 위한 메모리 공간이다.
간단하게 숫자, 문자만 담을 수도 있지만, 더 많은 걸 담고 싶다면 배열, 객체 같은 자료구조를 사용한다.
자료구조마다 특성이 다르므로, 상황에 맞는 자료구조를 활용해 변수를 만드는 게 좋다.
```
- JS에서는 어떻게 변수를 만들까?
### <span style="color:#FFCCFF">declaration</span>
- 변수의 키워드와 식별자를 선언하면 변수의 메모리 공간(주소)이 확보된다.

- 메모리 공간이 확보된 것이지, 아직 data가 들어간 것은 아니다.
```
키워드는 var, let, const가 있다.
식별자는 키워드 뒤에 나오는 변수명이다.
해당 식별자는 메모리 주소와 binding되어 값을 저장할 수 있다.
```
### <span style="color:#FFCCFF">var</span>
```
legacy project(jsp)에서 보통 많이 보인다. 쓰지 않을 수 있다면 안 쓰는 게 좋다.
```
- function 단위 scope를 가진다. block scope가 무시된다.
- Java에 익숙하다면 아래 console에서 openobject가 나올거라 기대할 것이다. if문에서 선언한 변수는 if문의 block 내에서만 효력을 가지기 때문이다.

- 하지만 실제 결과는 open이다. fn scope라서 block문이 끝나도 변수가 살아있기 때문이다. 따라서 뒤에 있는 변수로 덮어씌워진다.
```javascript
function getCompanyName(companyName) {
    var companyName = "openobject";
    if (companyName === "openobject") {
        var companyName = "open";
    }
    
    return companyName
}
console.log( getCompanyName() ); //"open" 
```

- 그 문제를 해결하려면 let으로 바꿔주면 된다.

- let은 var와 달리 식별자 이름이 같으면 오류를 내어 의도하지 않은 실수를 줄여준다.
```javascript
function getCompanyName(companyName) {
    let companyName = "openobject";
    if (companyName === "openobject") {
        let companyName = "other"; //Identifier 'companyName' has already been declared
    }
    
    return companyName
}
```
---
### <span style="color:#FFCCFF">let</span>
```
선언만 하고 나중에 할당하는 식의 초기화가 가능하다.
block scope를 지닌다.
```
- 선언만 한 변수는 JS 엔진이 undefined로 할당한다.
- undefined라는 원시형은 length라는 instance properties를 갖지 않기 때문에 읽어올 수가 없다. 

- length는 array와 string type에서 자주 사용되는 property다. undefined는 애초에 property를 갖고 있지 않다.
- 따라서 Cannot read properties of undefined (reading 'length')라는 오류가 난다.
```javascript
let companyName;                // let company = undefined;와 동일
console.log(companyName.length) //Cannot read properties of undefined (reading 'length')
```

- let은 block 단위라고 했던 것을 기억할 것이다.

- 아래 companyIndustry 변수는 if문이 끝나면 사라진다. 그런데 return하려고 하면 오류가 날 것이다. 
- block이 끝나 사라졌기에, return 시에는 companyIndustry가 선언하지 않은 변수(식별자)기 때문이다. 

- const도 block단위이므로 똑같은 오류가 난다.
```javascript
function getCompanyName(companyName) {
    let companyName = "openobject";
    if (companyName === "openobject") {
        let companyIndustry = "IT";
    }
    
    return companyIndustry
}
console.log( getCompanyName() ); //companyIndustry is not defined 
```
---
### <span style="color:#FFCCFF">const</span>
```
선언과 할당이 한꺼번에 이뤄져야 한다. 즉, 한번만 할당이 가능하다.
immutable한 것은 아니다. 객체의 property 조작이 가능하다.
```
- 선언과 할당이 한꺼번에 이뤄지지 않으면 오류를 낸다.

- initializer는 초기화를 해주는 것이다. 초기화는 선언과 할당이 모두 완료됨을 의미한다.
- 다시말해 missing initializer는 =를 쓰지 않았다는 것이다.
- =를 써주면 오류가 다른 것으로 바뀐다. 마침내 값을 할당해주면 초기화가 이뤄져 오류가 사라진다.
```javascript
const companyIndustry;      // Missing initializer in const declaration 
const companyIndustry =     //Unexpected end of input 
const companyIndustry = 'IT';
```

- 말했듯 const keyword는 객체의 property 조작까지 불가능 한 것은 아니다.
```javascript
const company = {
    name: 'nothing',
    industry: 'IT'
}

console.log(company.name); //nothing
company.name = "openobject";
console.log(company.name); //openobject
```
- 생성 이후에 객체의 property 조작을 막으려면 특정한 method를 써야한다.

- 보통 객체조작이 불가능하게 바꿀 때는 lodash library의 deepFreeze()를 사용하는 것이 추천된다.
- 직접 구현도 가능하다. 아래는 인터넷에서 가져온 deepFreeze의 구현 사례다. 
- 구현에 쓰인 method가 궁금하다면 JS의 prototype에 대해서 알아보도록 하자.
```javascript
function deepFreeze(target) {
  if (target === null || typeof target !== 'object') {
    return;
  }
  Object.keys(target).forEach((key) => {
    Object.defineProperty(target, key, {
      value: target[key],
      writable: false,
      enumerable: true,
    });
    deepFreeze(target[key]);
  });

  Object.seal(target);
}
```

- 객체의 property 조작을 막는 이유 중 하나는 enum처럼 활용하기 위해서이다.
- 아래와 같이 얼려서 조작 불가능하게 만들어 enum으로 쓸 수 있다.

- 그럼 too long이 console에 찍히는 것을 볼 수 있다.
```javascript
const BANNER_LENGTH = Object.freeze({
    MIN: 1,
	  MAX: 5,
});
const bannerName       = "GENESIS"
const bannerNameLength = bannerName.length;
if (bannerNameLength > BANNER_LENGTH.MAX) {
    console.log('too long')
}
```
- typescript에서는 더 간단하게 만들 수 있다. 이 외에도 ts에서는 enum, const enum을 사용할 수 있다.
```javascript
const TICKET_PRICE = {
  ADULT: 10_000,
  CHILD: 5_000,
  ELDER: 7_000,
} as const;
```
- JS에서는 지원되지 않는다. Unexpected identifier 'as' error가 뜬다.

- JS 엔진이 {}뒤에 자동으로 ;를 삽입해준 뒤에 as를 식별자(변수명)로 해석하려 했기 때문이다.


### <span style="color:#FFCCFF">assignment</span>
- 여태까지 string 원시형 값을 할당할 떄는 ""를 사용했다. 
```
""와 같은 값을 넣기 위한 특정한 형식을 literal이라 한다. 
리터럴이란 사람이 이해할 수 있는 문자나 약속된 기호를 사용해 값을 생성이다.
```
- 하지만 생성자를 사용할 수도 있다. 다만 꼭 new 연산자가 필수인 것은 아니다.

- 오히려 원시형을 만들고 싶다면 new를 붙여서는 안된다. new를 붙이면 원시형이 아니라 객체의 instance가 된다.
```javascript
const str = String("B");    //생성자 방식
const str = "B";            //literal 방식
const str = 'B';            //literal 방식
console.log(str);           // 모두 "B"라는 원시형 값
```


- new가 필요한 경우도 있다. 원시형은 객체의 property를 활용할 수 없기 때문이다.
- 그러나 원시형이 instance properties에 접근하는 경우, JS엔진이 객체로 wrapping해준다. 따라서 소스코드로 우리가 쓸 필요는 없다. Java에서 쓰이는 일종의 autoBoxing과 비슷한 것으로 보인다.
```javascript
const str = "str"; //원시형. property없음.
console.log(str.length); //new String("str").length;처럼 wrapping됨. 3

const num = 5;
console.log(!num.isNaN); //true
```

- switch문은 원시형 문자를 받기 때문에 아래와 같이 하면 console에 찍히지 않는다.
- 아래에서 new를 지워주면 콘솔에 B가 찍히는 것을 볼 수 있다.
```javascript
const str = new String('B');

switch(str) {
  case "A":
    console.log('A');
  break;
  case "B":
    console.log('B');
  break;
  case "C":
      console.log('C');
    break;
  case "D":
      console.log('D');
    break;
  default:
}
```

- 보통 형변환을 명시적으로 보여주고자 할 때 생성자를 사용한다.
```javascript
const num = "123";
const str = String(num); //생성자 방식. 
const str = num + '';    //이렇게 해도 string이 됨.
console.log(str);
```

- 대개 많은 경우 생성자보다는 literal로 할당하는 편이다.
```javascript
const str   = `C`;            //literal 방식
const num   = 20;             //literal 방식
const num   = 20.01;          //literal 방식
const obj   = {};             //literal 방식
const arr   = [];             //literal 방식
const regex = /[A-Z]+/gi;     //literal 방식
const bool  = true;           //literal 방식
function fn(){}               //literal 방식
```

- 아래 같이 new를 호출해 생성자로 생성할 수도 있지만, 추천되지 않는다.
```javascript
const obj   = new Object();                     //생성자 방식
const arr   = new Array("openobject", "IT");    //생성자 방식
const regex = new RegExp("[A-Z]", "gi");        //생성자 방식
const fn    = new Function();                   //생성자 방식
```

- 특히 function은 생성자로 만들면 보기도 어렵고 closure를 활용할 수가 없다.
```javascript
const add = new Function('x','y','return x + y');
console.log( add(2,5) ); //7
```

## <span style="color:#802548">_2. value-expression-statement_</span>
### <span style="color:#FFCCFF">statement</span>
- 문은 프로그램을 구성하는 기본단위이다.
- 문은 token으로 구성되는데, token은 더이상 나눌 수 없는 코드의 기본 요소다. 아래는 자주 쓰이는 token이다.
```
keyword - var, let ,const
identifier - 변수명
operator - 연산자
literal - 값
; - closing statement
```
- token을 한데 모으면 아래와 같은 statement가 만들어진다.

```javascript
const/*keyword*/ company/*identifier*/ =/*operator*/ "openObject"/*literal*/; /*semicolon*/
```

- statement를 잘못 쓰면 아래와 같은 오류가 날 수 있다.

- 객체를 초기화하는 statement가 끝나지 않았는데 종결을 의미하는 ;을 써서 그렇다.
- ;를 ,로 바꿔주면 오류가 사라진다.
```javascript
const company = {
  name: 'openObject'; //unexpected token ;
  industry: 'IT';
}
const company = {
  name:     'openObject',
  industry: 'IT'
}
```

### <span style="color:#FFCCFF">expression</span>
```
식은 값으로 귀결될 수 있는 statement를 의미한다.
연산자가 대표적이다.
```
```javascript
const a = 5;
a = a + 5;
```


- 몇 가지 자주 쓰이는 연산자들이 있다. 그 중의 하나가 삼항연산자다.
```javascript
const id = (a===1) ? 'id' : 'not id';
```

- 만약 truthy인 조건에서만 나오게 하고 싶다면 아래와 같이 줄 수 있다.
```javascript
const id = true && 'id';
```

- falsy에 대비해 default값을 주고 싶다면 아래와 같이 줄 수 있다.
```javascript
const id = response.id || 'id';
```

- null이나 undefined만 대비하고 싶다면 아래와 같이 줄 수 있다.
```javascript
const id = response.id ?? 'id';
```

### <span style="color:#FFCCFF">value</span>
```
값은 식이 평가되어 생성된 결과를 말한다.
다시 말해 우변은 값이다. 함수도 JS에서는 값이다.
```
```javascript
const exampleFunction = function(){
  console.log('example');
}
const exampleNumber = 10;
```
- value가 들어올 곳에는 식과 값만 들어올 수 있다. statement는 들어올 수 없다.
```javascript
const obj = {
	id: if(condition){ // Unexpected token 'if' 
		'msg' 
	}
}

const obj = {
  id: response.id ?? 'id'
}
```

- 객체의 property key와 value의 이름이 같다면 생략도 가능하다.
```javascript
const id = 'id';
const obj = {
  id,
}
```
## <span style="color:#802548">_3. control flow statement_</span>

### <span style="color:#FFCCFF">conditional statement</span>
```
조건문에는 if문과 switch문이 있다.
```
- if문의 기본 구성은 아래와 같다.

```javascript
function getUserType(type){
	if(type ==='ADMIN'){
		return '관리자';
	}else if(type === 'INSTRUCTOR'){
		return '강사';
	}else if(type === 'STUDENT'){
		return '수강생';
	} 
  .
  .
  .
  .
  .
  .
   else {

  }
}
```
- else if문이 길어보인다면 아래와 같이 switch문으로 바꿀수 있다.
```javascript
function getUserType(type){
	switch(type) {
    case "ADMIN":
      return '관리자'
    break;
    case "INSTRUCTOR":
      return '강사';
    break;
    case "STUDENT":
      return '수강생';
    break;
    default:
      return '해당 없음';
  }
}
```

- 만약 요구사항이 계속 바뀌어 추가될 거 같다면 object로 변경하여 관리할 수도 있다.

- 그럼 if문이나 switch문을 늘리지 않고 객체의 property만 늘려나가면 된다.
```javascript
function getUserType(type){
	const USER_TYPE = {
		ADMIN: '관리자',
		INSTRUCTOR: '강사',
		STUDENT: '수강생'
	}

	return USER_TYPE[type] ?? '해당 없음';
}

const userType = getUserType("ADMIN");
```

- 객체도 따로 파일로 빼서 관리한다면 전체 소스코드를 수정할 필요 없이 해당 객체의 property만 수정하면 전체 코드에 적용된다.
```javascript
import USER_TYPE from './constants./...'; //USER_TYPE을 바꿔주면 import하는 모든 js에 적용
function getUserType(type){
	return USER_TYPE[type] ?? USER_TYPE.UN
}
```

- 코드 가독성을 위해 if문을 조작하는 방법의 다른 예시를 살펴보자.

- 만약 if문이 계속 중첩되는 형태라면 depth가 깊어져 코드를 해석하는 데 어려움이 있다.
```javascript
function signIn(isLogin, user){
	if(!isLogin){
		if(!checkToken()){
			if(!user.nickName){
				return registerUser(user);
			}else{
				refreshToken();

				return '로그인 성공';
			}
		}else{
			throw new Error('No Token');
		}
	}
}
```
- 가능하다면 early return, throw error로 좀 더 보기 쉬운 코드를 만드는 것이 좋다.
```javascript
function signIn(isLogin, user){
	if(isLogin){
		return;
	}

	if(!checkToken){
		throw new Error('No Token');
	}

	if(!user.nickname){
		return registerUser(user);
	}

	refreshToken();

	return '로그인 성공';
}
```

- 다음으로 코드 가독성을 위해서는 부정연산자가 아닌 다른 방식으로 바꿀 수 있다면 바꾸는 게 좋다.

- 일례로 isNaN으로 숫자인지 판단하려면 !를 붙여서 해야되는데, 이러면 생각을 여러번 해야 한다.
```javascript
if(!isNaN(3)){
	console.log('숫자입니다');
}
```
- !를 조건 앞에 붙이지 말고 method를 만들어 내부 구현으로 숨기자
```javascript
function isNumber(num){
	return !Number.isNaN(num) && typeof num === 'number';
}

if(isNumber(3)){
	console.log('숫자입니다');
}
```

### <span style="color:#FFCCFF">loop statement</span>
- 반복분에는 for문과 while문이 있다.
- 자주 쓰게 되는 것은 for문이다.

```javascript
let array = [1,2,3];
for (let i = 0; i < array.length; i++) {
  console.log(i);
}
```

- for문을 고차함수로 바꿔 쓰는 것도 가능하다.

- 그 편이 사람이 이해하기도 쉬우며, for, if문 조합으로 로직을 구성하는 것보다 편하다.
```javascript
let array = [1,2,3];
array.forEach(function(element) {
  console.log(element);
})
```

- 그에 관한 아래 사례를 살펴보자.

- for문과 if문의 조합으로 기능을 구현하려면 모두 자신이 직접 구현해야 한다.
```javascript
const price = ['2000','1000','3000','5000','4000'];
function getWonPrice(priceList){
	let temp = [];

	for(let i = 0; i <priceList.length; i++){
		if(priceList[i] > 1000){
				temp.push(priceList[i] + '원'); //원화표기
		}
	}

	if(orderType === 'ASCENDING'){
		someAscendingSortFunc(temp); //sort
	}

	if(orderType === 'DESCENDING'){
		someDescendingSortFunc(temp);
	}
	
	return temp;
}

const intendedPriceList = getWonPrice(price);
```


- 고차함수의 힘을 빌리면 callback function만 넣어주면 쉽게 처리가 가능하다.

- 해당 기능은 js 내에서 이미 구현되어있어 빌리면 되는 것이다.
```javascript
const price = ['2000','1000','3000','5000','4000'];
const suffixWon = (price) => price + '원';
const isOverOneThousand = (price) => Number(price) > 1000;
const ascendingList = (a,b) => a - b;
function getWonPrice(priceList){
	return priceList
		.filter(isOverOneThousand)
		.sort(ascendingList)
		.map(suffixWon);
}

const intendedPriceList = getWonPrice(price); // [2000,3000,4000,5000]
```

## <span style="color:#802548">_4. spread, destructuring문법_</span>
```
spread는 하나로 뭉쳐 있는 여러 값의 집합을 펼쳐서 개별 값의 목록으로 만든다.
destructuring은 값을 각 변수에 할당한다. 
iterable 객체만 spread 문법을 활용할 수 있다. destructuring은 객체면 사용 가능하다.
```

- 객체 리터럴로 선언한 객체는 iterable이 아니다. string, array, set, map 등이 iterable이다.

- iterable 객체가 되는 조건이 궁금하다면 iterable protocol에 관한 정보를 찾아보면 된다.
```javascript
console.log(...[1,2,3]); //1,2,3
console.log(...'hello');//h e l l o
console.log(...new Map([['a','1'],['b','2']])); // ['a','1'], ['b','2']
console.log(...new Set([1,2,3])); //1 2 3

console.log(...{a:1, b:2}); //error. Found non-callable @@iterator
```

- spread가 없었을 때는 아래와 같이 apply, call, bind 등을 활용해 method를 활용했다.
```javascript
var arr = [1,2,3];
var max = Math.max.apply(null, arr); //3
```

- 비구조화 할당을 사용하면 아래와 같이 바로 활용이 가능하다.
```javascript
const arr = [1,2,3];
const max  = Math.max(...arr); //3
```

- 이제 destructuring 문법의 사례를 살펴보자.

- 기존에는 아래와 같이 index로 받아왔어야 했다.
```javascript
var arr = [1,2,3];
var one = arr[0];
var two = arr[1];
var three = arr[2];
```

- 하지만 비구조화 할당을 활용하면 아래와 같이 간결하게 받아올 수 있다.
```javascript
const arr = [1,2,3];
const [one,two,three] = arr;
console.log(one,two,three); // 1 2 3
```

- 비구조화 할당은 기본값 적용이 가능하다.
```javascript
const [a,b,c=3] =[1,2];
console.log(a,b,c) ; //1 2 3. 기본값 적용가능

const [e,f = 10, g = 3] = [1,2];
console.log(e,f,g); // 1 2 3. 할당된 값이 기본값보다 우선
```

- 아래와 같이 쓰면 오류가 나거나 의도하지 않은 결과를 초래할 수 있다.
```javascript
const [a,b] = {} //TypeError: {} is not iterable
const [c,d] = 1;
console.log(c,d); //1 undefined
const [e,f] = [1,2,3];
console.log(e,f); //1 2. 3이 없다. 
```

- 아래는 배열의 destructuring의 사용예제다.
```javascript
function parseURL(url = ''){
    const parsedURL = url.match(/^(\w+):\/\/([^/]+)\/(.*)$/);

    if(!parsedURL) {
      return {};
    }

    const [protocol,host,path] = parsedURL;
    return {
        protocol,
        host,
        path
    }
}

const parsedURL = parseURL('https://developer.mozilla.org/ko/docs/Web/JavaScript');
console.log(parsedURL);
/*
protocol: 'https',
host: 'developer.mozilla.org',
path: 'ko/docs/Web/JavaScript'
*/
```

- 이제는 객체의 destructuring의 예시를 살펴보자.

- ES5에서는 해당 문법이 없어 property에 접근할 시 .이나 []를 활용 한다.
```javascript
var user = {
    firstName: 'JaeWon',
    lastName: 'Lee'
}

var firstName = user.firstName;
var lastName = user['lastName'];
console.log(firstName, lastName);
```

- 하지만 ES6에서는 해당 문법을 사용하여 property에 접근할 수 있다.

- 변수를 할당할 때는 key를 기준으로 한다.
```javascript
const user = {
    firstName: 'JaeWon',
    lastName: 'Heo'
}
const {lastName, firstName} = user; //key를 기준으로 하기에, 순서를 뒤바꾸는 것은 아무 의미 없다. 아무 의미 없기에 바꿔 써도 상관 없다.
console.log(firstName, lastName) //JaeWon Heo
```

- 배열과 마찬가지로 기본값 설정도 가능하다.
- 다만 왼쪽에 기본값을 주는 것은 가능해도, 오른쪽에는 안된다.

- 하지만 아래와 같이 쓰면  오류가 난다.
- 오른쪽에는 정해진 값이 할당되어야 하기 때문이다.
```javascript
const {firstName = 'JaeWon', lastName} = {firstName, lastName = 'Heo'};
//Invalid shorthand property initializer
```

- =를 :로 바꿔 객체를 할당하는 올바른 초기화 token을 써주자.
```javascript
const {firstName = 'JaeWon', lastName} = {firstName, lastName : 'Heo'};
console.log(firstName, lastName) //JaeWon Heo
```

- 아래와 같이 별칭을 줄 수도 있다.

- 여기서는 기본값을 주는 게 아니라서 = 대신 :를 사용한다.
```javascript
const {lastName: ln, firstName: fn} = {firstName: 'JaeWon', lastName : 'Heo'};
console.log(fn,ln) //JaeWon Heo
```

- 함수의 parameter로 들어갈 때도 비구조화 할당이 가능하다.
```javascript
function printTodo({content, completed}){
    console.log(`할일 ${content} ${completed ? '완료' : '비완료'} 상태입니다.`);
}
printTodo({id:1, content: 'HTML', completed:true}); //할일 HTML 완료 상태입니다.
```

- nested property를 parameter로 하여 가져올 때도 비구조화 할당으로 가져올 수 있다.

```javascript
const cats = {
  title: "Cats",
  director: "Tom Hooper",
  releaseYear: 2019,
  boxOffice: {
    budget: 95_000_000,
    grossUS: 27_166_770,
    grossWorldwide: 73_833_348,
  },
};

function getProfit({ boxOffice: { grossWorldwide, budget } }) {
  return grossWorldwide - budget;
}
```

- 비구조화 할당을 이용해서 아래와 같이 필수값은 받도록 검사할 수 있다.

- 필수값을 넘기지 않았다면 error를 던져주면 개발자가 더 안전하게 해당 함수를 사용할 수 있다.
```javascript
function createCar(name,{brand,color,type}){ 
    if(!name){
        throw new Error("name은 필수값입니다.");
    }
    return {
        name,
        brand,
        color,
        type 
    }
}

createCar('GENESIS',{});
createCar('GENESIS',{brand: 'Hyundai'});
/*
  {
    "name": "GENESIS",
    "brand": undefined,
    "color": undefined,
    "type": undefined
  }
/*
/*
  {
    "name": "GENESIS",
    "brand": "Hyundai",
    "color": undefined,
    "type": undefined
  }
/*
```

- 중첩 객체의 property를 가져올 때는 위의 중첩된 객체를 parameter로 분해할 때와 동일하게 가져올 수 있다.
```javascript
const user = {
    name: 'Lee',
    address: {
        zipCode: '03068',
        city: 'Seoul'
    }
}

const {address:{city}} = user;
console.log(city) //Seoul
```

- spread와 destructuring은 같이 사용가능하다.
```javascript
const {x, ...rest} = {x: 1, y: 2, z: 3};
console.log(x, rest); //1 {y:2, z:3}
```


## <span style="color:#802548">_5. 함수_</span>
### <span style="color:#FFCCFF">callback</span>
```
JS에서 함수는 객체다.
따라서 property의 value가 될 수 있고, parameter로 함수를 넘길 수도 있다.
이렇게 parameter로 넘겨진 함수를 callback함수라고 하며, 그 함수를 활용하는 원함수를 고차함수라고 한다.
```

- 널리 쓰이는 built-in 객체의 고차함수로는 Array의 reduce, filter, map이 있다.
- 실제로 아래 같이 인터넷에서 고차함수 구현 예제를 볼 수 있다. 

- 아래 예시는 요소의 문자열 길이를 배열로 하여 반환한다.
```javascript
const strArray = ['Javascript', 'Python', 'PHP', 'Java', 'C'];

function mapForEach(arr, fn) {
  const newArray = [];
  for(let i=0; i<arr.length; i++){
    newArray.push(
      fn(arr[i])
    );
  }
  return newArray;
}

const lenArray = mapForEach(strArray, function(item) {
  return item.length;
});

// prints [10, 6, 3, 4, 1]
console.log(lenArray);
```

### <span style="color:#FFCCFF">method</span>
```
property의 value로 함수가 들어가면 이를 method라고 칭한다.
```
- 위에 썼던 예시에서 getCompanyName이 바로 method다.
```javascript
const obj = {
  name : 'company',
  getCompanyName: function(someFunc) {
    someFunc
  }
}

const obj = {
  name : 'company',
  getCompanyName: (someFunc) => someFunc
}
```

- method는 아래와 같이 간결하게 쓸 수도 있다.

- 이를 concise method라고 한다.
```javascript
const obj = {
  name : 'company',
  getCompanyName(someFunc) {
    someFunc
  }
}
```
- method를 변수명에 할당해 일반함수로 만들어 호출할 수도 있다.

- 다만 이는 this와 연관되어 bug를 야기할 수 있다. this 부분에서 살펴보자.
```javascript
const person = {
    name: 'Lee',
    getName(){
        return this.name;
    }
}

const getName = person.getName;
console.log(getName()) // 일반 브라우저에선 ''. codepen에서 test하면 CodePen
```

### <span style="color:#FFCCFF">lexical scope</span>
```
lexical scope는 함수가 호출된 곳이 아니라, 정의된 곳을 기준으로 한다.
```
- 전역에 정의되었기에 전역 companyName을 가져온다.
```javascript
const companyName = 'openobject';
function getCompanyName() {
  const companyName = 'other';
  getCompanyIndustry();
}

function getCompanyIndustry() {
  console.log(companyName);
}

getCompanyName(); //openobject
```
- 하지만 아래에서는 getCompanyName()안에 정의되었다.

- 따라서 해당 fn scope의 getCompanyName를 가져온다.
```javascript
const companyName = 'openobject';
function getCompanyName() {
  const companyName = 'other';

  function getCompanyIndustry() {
    console.log(companyName);
  }

  getCompanyIndustry();
}


getCompanyName(); //other
```

- 이 lexical scope를 기반으로 closure라는 방식의 함수 활용이 가능해진다.

### <span style="color:#FFCCFF">closure</span>
```
클로저는 중첩함수가 외부함수보다 오래 살아남았을 때 + 상위함수를 참조할 때, closure라고 한다.
즉 상위 함수가 종료되어도 상위 함수의 변수, parameter에 접근할 수 있는 함수다.
```
- 아래는 즉시실행함수를 활용한 closure의 예시다.
```javascript
const counter = (function(){
    let num = 0;
    return {
        increase(){
            return ++num;
        },
        decrease(){
            return --num;
        }
    }
}());

console.log(counter.increase()) //1
console.log(counter.increase()) //2
console.log(counter.decrease()) //1
console.log(counter.decrease()) //0
console.log(counter.decrease()) //-1
console.log(counter.decrease()) //-2
console.log(counter.increase()) //-1
```

- 즉시 실행함수는 한 번만 실행되므로 increase가 호출될 때마다 num 변수가 초기화되는 일은 없다.

- 즉시실행함수는 변수를 그냥 쓰는 것만으로도 함수가 호출된다.
```javascript
const counter = (function(){
   console.log('abc')
}());

counter //abc
```

- 원래로 돌아와서 살펴보면 counter를 호출하는 순간 아래와 같은 객체를 return하는 것을 알 수 있다.
```javascript
const counter = (function(){
    let num = 0;
    return {
        increase(){
            return ++num;
        },
        decrease(){
            return --num;
        }
    }
}());

console.log(counter);
/*
  {
    increase(){
        return ++num;
    },
    decrease(){
        return --num;
    }
  }
*/
```

- 따라서 increase와 decrease라는 method를 활용할 수 있게 된다.
```javascript
console.log(counter.increase()) 
console.log(counter.decrease()) 
```

- 그렇다면 남는 것은 num이다. num은 어떻게 +가 되는 것일까? 
- 위에서 말했듯 closure는 상위함수가 종료되어도 해당 lexical scope를 기억한다.
- 여기서 lexical scope에서 중요한 것은 num이라는 변수다. 바로 이 변수가 기억된다는 의미다.

- 따라서 counter.increase()가 호출되면 num이 1이 더해진 상태로 기억된다.
- 똑같이 counter.decrease()가 호출되면 num이 1이 빠진 상태로 기억된다.

```javascript
console.log(counter.increase()); //1
console.log(counter.decrease()); //0
```

- closure의 장점은 변수를 함수 안에 두고 사용하기 때문에 전역으로 만들 필요가 없다는 점이다.
- 전역으로 만들 필요가 없다는 것은 누군가에 의해 수정될 가능성이 없다는 말과 동일하다. 더 안전하다는 의미다.


- 해당 예시에 즉시실행함수를 쓰지 않았다면 어떻게 될까?
- lexical 환경이 서로 달라 num이라는 변수가 공유되지 않는다.
- num이라는 변수가 공유될 필요가 있다면 IIFE로 만들어 한번만 실행되게 해야 한다.
```javascript
const counter = function(){
    let num = 0;
    return {
        increase(){
            return ++num;
        },
        decrease(){
            return --num;
        }
    }
};

console.log(counter().increase()) //1
console.log(counter().increase()) //1
console.log(counter().decrease()) //-1
console.log(counter().decrease()) //-1
console.log(counter().decrease()) //-1
console.log(counter().decrease()) //-1
console.log(counter().increase()) //1
```

- lexical 환경 공유까지 필요하지 않다면 IIFE로 만들 필요는 없다.

- 아래는 일반함수의 closure 사례로, parameter가 closure를 받는다.
```javascript
function add(num1) {
	return function sum(num2) {
		return num1 + num2;
	}
}

const addOne = add(1);

addOne(3); //add(1)으로 add()에 1을 넘긴 상태에서 3을 sum()에 넣은 것. 4
add(1)(3); //위와 동일
```

- 실제 아래와 같이 closure를 활용할 수 있다.

- 원래는 아래와 같이 switch case를 활용하여 분기를 처리했다고 해보자.
```javascript
function log(value, level) {
	switch(level) {
		case "log":
			console.log(value);
			break;
		case "info":
			console.info(value);
			break;
		case "warn":
			console.warn(value);
			break;
		case "error":
			console.error(value);
			break;
	}
}
```

- closure를 이용하면 아래와 같이 바꿀 수 있다.
```javascript
function log(value) {
	return function (fn) {
		fn(value);
	}
}

const logFoo = log;

logFoo('foo')((v) => console.log(v));
logFoo('foo')((v) => console.info(v));
logFoo('foo')((v) => console.warn(v));
logFoo('foo')((v) => console.error(v));
```

- log에 인자를 붙이면 아래와 같이 좀 더 알아보기 쉬운 형태로 바뀐다.
```javascript
function log(value) {
	return function (fn) {
		fn(value);
	}
}

const logFoo = log('foo');

logFoo((v) => console.log(v));
logFoo((v) => console.info(v));
logFoo((v) => console.warn(v));
logFoo((v) => console.error(v));
```

- 이를 IIFE로 바꾸면 아래와 같다.

```javascript
const logFoo = (function log(value) {
	return function (fn) {
		fn(value);
	}
}('foo'))

logFoo((v) => console.log(v));
logFoo((v) => console.info(v));
logFoo((v) => console.warn(v));
logFoo((v) => console.error(v));
```


- closure를 활용하는 것은 고차함수에도 쓰일 수 있다.

- 아래와 같이 써있는 코드가 있다고 해보자.
```javascript
const arr = [1,2,3,'A','B','C'];
const isNumber = (value) => typeof value === 'number';
const isString = (value) => typeof value === 'string';
arr.filter(isNumber);
```

- 아래와 같이 함수로 빼서 중복을 덜하게 만들 수 있다.
```javascript
function isTypeOf(type, value) {
  return typeof value === type;
}
const isNumber = (value) => isTypeOf('number', value);
const isString = (value) => isTypeOf('string', value);
arr.filter(isNumber);
```

- 거기서 더 나아가 closure를 활용하면 앞으로 isTypeOf()에 string형태의 type만 넣어도 기능이 작동한다.

- type검사가 자주 일어날수록 아래와 같이 바꾸는 것이 더 편할 것이다.
```javascript
function isTypeOf(type) {
	return function (value) {
		return typeof value === type;
	}
}
function isTypeOf(type) {
	return (value) => typeof value === type;
}
const isNumber = isTypeOf('number');
const isString = isTypeOf('string');
arr.filter(isNumber); //[1,2,3]
arr.filter(isString);//['A','B','C']
```

### <span style="color:#FFCCFF">parameter</span>
```
parameter는 함수에 넘기는 추상적인 인자다.
반면에 argument는 실제로 넘겨지는 구체적인 값이다.
```
- 아래에서 name은 parameter고, argument는 openobject다.
```javascript
function getCompanyName(name) {
  console.log(name);
}

console.log('openobject');
```

- argument가 몇개가 들어올 지 모를 때는 ...으로 축약하여 사용가능하다.
```javascript
function getSomething(...args) {
  console.log(args); 
}

getSomething('4','5'); //4,5
getSomething('4','5','6'); //4,5,6
```

- parameter에는 기본값을 부여할 수 있다.
- 하지만 parameter에 기본값을 넣은 것으로는 falsy값이 들어오는 것을 막을 수 없다.
- 그럴 때 아래와 같이 || 연산자를 활용하여 기본값을 줄 수도 있다.

- falsy가 아니라 null, undefined만 거를 거라면 ??를 활용할 수도 있다.
```javascript
function sum(x = 1, y = 2){

	return x + y;
}

function sum(x,y){
	x = x || 1;
	y = y || 1;

	return x + y;
}

function sum(x,y){
	x = x ?? 1;
	y = y ?? 1;

	return x + y;
}
```

- 기본값 부여로도 충분치 않다면 아래와 같이 직접 타입검사를 실행할 수도 있다.
```javascript
var add = function(x,y) {
  if(typeof x !== 'number' || typeof y !=='number'){
    throw new TypeError("인수는 모두 숫자값이어야 합니다.");
  }
}
```

- typescript에서는 더 간단하게 해결이 가능하다.
- 아래와 같이 parameter가 들어올 type을 한정해줄 수 있다. 입력을 되돌리지는 못한다.
```javascript
var add = function(x: number,y: number) {

}
```
- 입력 자체를 되돌리고 싶다면 정규식을 사용할 수 있다.
```javascript
var validateInputNumber = function(char){
  const regex = /[^0-9]/gi;
  if( regex.test(char) ){
      char = char.replace(regex,'');
   }
  console.log(char)
}

validateInputNumber('b'); // ''
validateInputNumber('9'); //'9'
```

### <span style="color:#FFCCFF">expression, statement</span>
- 함수를 정의하는 데는 표현식, 선언문 두 가지가 있다.
```
변수에 할당하지 않고 function keyword를 쓰면 선언문이다.
선언문은 hoisting되어 아래에 선언해도 최상단으로 끌어올려진다.
```
- 아래는 선언문이다.
```javascript
function getSomeThing() {

}
```

- 변수에 함수를 할당하면 표현식이다. 다만 함수에 이름이 없는 경우가 많아 보통 익명함수다.
```javascript
const getSomeThing = function() {

}
```

- 표현식은 화살표함수로 바꿔 쓸 수 있다.
```javascript
const getSomeThing = () => {

}
```
- 표현식에서 화살표함수와 일반 표현식은 this에서 차이가 있다.

- 우선 this에 관해 먼저 알아보자.


### <span style="color:#FFCCFF">this</span>
- this binding이란 식별자와 값을 연결하는 과정이다. 
```
this라는 식별자와 this가 가리킬 객체의 메모리 주소를 binding하는 것이다.
this binding은 일반함수, 메서드, 생성자함수, call-apply-bind라는 4가지 맥락에 따라 의미가 다르다.
```

- 일반함수의 경우 scope에 관계없이 무조건 this가 window에 binding된다.
```javascript
var value = 1;

const obj = {
    value: 100,
    function foo(){
      console.log("foo's this: ", this); //method 호출. 객체 자기자신
      console.log("foo's this.value: ", this.value) // 100

      function bar(){
          console.log("bar's this: ", this); //window
          console.log("bar's this: ", this.value); //window, 1
      }
      
      bar(); //일반함수로 호출됐으면 어디서든 window.
    }
}

obj.foo();
```

- callback함수도 일반함수의 형태라면 this가 window에 binding된다.
```javascript
var value = 1;
const obj = {
    value: 100,
    foo(){
        console.log("foo's this: ", this);
        
        setTimeout(function(){
            console.log("callback's this: ",this); // window
            console.log("callback's this.value: ", this.value) //1
        },100);
    }
}

obj.foo();
```

- callback 함수를 상위 스코프에 binding하려면 아래와 같은 방법으로 가능하다.
```javascript
var value = 1;
const obj = {
    value: 100,
    foo(){
        const that = this; //this를 다른 변수에 할당

        setTimeout(function(){
             console.log("callback's this: ",that); // obj
            console.log("callback's this.value: ", that.value) //100
        },100);
    }
}

obj.foo();
```

- 상위스코프에 연동하기 위해, apply, bind, call 등의 함수를 사용할 수도 있다.
```javascript
var value = 1; // var는 전역프로퍼티에 들어가게 됨
const obj = {
    value: 100,
    foo(){
        setTimeout(function(){
            console.log("callback's this.value: ", this.value) //100
        }.bind(this),100);
    }
}

obj.foo();
```

- 또 다른 방법으로는 화살표함수를 사용하는 것이다.
```javascript
var value = 1; // var는 전역프로퍼티에 들어가게 됨
const obj = {
    value: 100,
    foo(){
        setTimeout(() => console.log(this.value), 100);
    }
}

obj.foo();
```

- 참고로 method 호출의 경우, 일반 함수처럼 쓰지 않는 게 좋다.

- 변수명에 할당하는 일반 함수로 바뀌게 되면 this가 window에 binding되는 것으로 바뀐다.
```javascript
const person = {
    name: 'Lee',
    getName(){
        return this.name;
    }
}

console.log(person.getName()) // person객체의 name을 참조한다.

const getName = person.getName;
console.log(getName()) // window객체의 name을 참조한다.
```

## <span style="color:#802548">_6. DOM_</span>

### <span style="color:#FFCCFF">DOM 생성과정 </span>
- DOM을 알기 위해서는 먼저 DOM이 생성되는 과정에 대해 알아야 한다.
```
서버에서 받은 HTML문서는 바이트 형태다. meta태그의 charset을 보고 지정된 인코딩 방식을 기준으로 문자열로 변환한다. 보통 utf-8이다.
변환된 HTML문서는 순수 텍스트이기 때문에, 브라우저가 이해할 수 있는 자료구조(객체)로 변환해 메모리에 저장한다.
HTML 요소는 렌더링 엔진에 의해 파싱돼 노드 객체로 변환된다.
HTML 요소의 attribute는 attribute node로, 텍스트 콘텐츠는 text node로 변환된다.
```
- 이러한 객체 Node들이 트리구조로 되어있는 것이 바로 DOM이다.
- JS에서 DOM은 document를 통해서만 접근할 수 있다.
```html
<!DOCTYPE html>
<html>
    <body>
        <ul>
            <li id="apple">Apple</li>
            <li id="banana">banana</li>
            <li id="orange">orange</li>
        </ul>
        <script>
            const $elem = document.getElementById('banana');
             //document 문서노드 접근 -> getElementsById() 요소 노드 접근
            console.log($elem.id); //banana
            console.log($elem.textContent); //banana
        </script>
        </body>
</html>
```

### <span style="color:#FFCCFF">live와 static </span>
- 요소 노드의 static 상태는 attribute노드가 관리한다
- 요소 노드의 live 상태는 DOM property가 관리한다.
- 둘을 바꿔보면 차이점을 느낄 수 있다. 아래는 attribute노드를 바꾼 것이다.

- DOM property를 바꿔도 attribute node의 값은 변하지 않는다. 둘은 각자 개별로 관리된다는 의미이다.
```html
<!DOCTYPE html>
<html>
    <body>
        <input id="user" type="text" value="car">
    </body>
    <script>
        const $input = document.getElementById('user');

        $input.oninput = () => {
            console.log('value 프로퍼티 값', $input.value);
        }

        $input.value = 'foo';
        console.log('value 어트리뷰트 값', $input.getAttribute('value')); 
        // car. foo로 나오지 않음.
    </script>
</html>
```

- DOM을 가져오는 특정 method의 경우, live상태를 바꿀 수 있는 객체를 반환한다. 이 경우, 주의하여 다룰 필요가 있다.
- 아래 예시를 살펴보자. 의도한 것은 className이 전부 txt-red가 되는 것인데, 실제로 그렇지 않게 되는 버그가 발생한다.

- DOM property를 바꾸는 순간, getElementsByClassName()의 반환 값인 HTMLCollection이 실시간으로 변동해 for문의 index가 엉키게 된다.
- 1번이 txt-red로 바뀌면, 그 다음 2번이 바뀌어야 하겠지만, live객체라서 2번이 1번자리인 [0]으로 내려오기 때문이다.
- [1]이었어야 할 2번이 [0]이 되어 아래와 같은 방식으로는 접근이 불가능하다.
```html
<!DOCTYPE html>
<html>
  <head>
    <style>
      .txt-blue { color: blue; }
      .txt-red { color: red; }
    </style>
  </head>
    <body>
        <ul class="list">  
          <li class="txt-blue">1번</li>  
          <li class="txt-blue">2번</li>  
          <li class="txt-blue">3번</li>
        </ul>  
        <script>
           const $li = document.getElementsByClassName('txt-blue'); 
           for (let i = 0; i < $li.length; i++) {  
              $li[i].className = 'txt-red';
           }
        </script>
        </body>
</html>
```

- 이러한 버그를 예방하려면 for문을 역순으로 끝에서부터 도는 것이 좋다.

- 아니면 배열로 만들어 live 상태가 바뀌지 않게끔 방지한 뒤 className을 바꾸면 된다.
```javascript
for(let i = $li.length -1; i >= 0; i--){
  $li[i].className = 'txt-red';
}

[...$li].forEach(item => { 
    $li[i].className = 'txt-red';
})
```

### <span style="color:#FFCCFF">innerHTML</span>
- innerHTML은 사용하지 말고, textContent나 insertAdjacentText를 사용하자.

- innerHTML은 xss공격에 취약하다. 파싱과정에서 그대로 실행될 수 있기 때문이다.
- 다만 textContent는 그 아래 자식 노드를 전부 지워버리고 text만 추가해주기 때문에 조심해서 사용해야 한다.
```html
<!DOCTYPE html>
<html>
    <body>
        <div id="foo">Hello</div>
    </body>
    <script>
        const $foo = document.getElementById('foo');

        console.log(document.getElementById('foo').textContent); //Hello
        document.getElementById('foo').textContent = 'Hi <span>there!</span>'; //HTML parsing이 되지 않기 때문에 xss공격에 안전.
        /*
        <html>
            <body>
            'Hi<span>there!</span>' //tag 달고 그대로 출력됨
            </body>
        </html>
        */
    </script>
</html>
```


- 자식노드를 살리면서 요소를 추가하고 싶다면 insertAdjacentHTML 사용하자.
- 위치는 beforebegin, afterbegin, beforeend, afterend으로 다양하게 넣을 수 있다. 
```html
<!DOCTYPE html>
<html>
    <body>
        <div id="foo">Hello</div>
    </body>
    <script>
        const $foo = document.getElementById('foo');

        $foo.insertAdjacentHTML('beforebegin','Hi <span>there!</span>'); 
        /*
        <html>
            <body>
            Hi there!
            Hello
            </body>
        </html>
        */
    </script>
</html>
```

### <span style="color:#FFCCFF">data property</span>
- 과거에는 자신이 원하는 형식의 attribute를 사용할 때 아래와 같이 사용했다. 
```html
<!DOCTYPE html>
<html>
    <body>
        <div id="foo" mapId="mapId">Hello</div>
    </body>
    <script>
        const $foo = document.getElementById('foo').getAttribute("mapId"); 
        //$("#foo").attr("mapId");와 같이 Jquery로도 쓸 수 있다.
        console.log($foo); //mapId
    </script>
</html>
```

- 이제는 규격화되어 html에 내가 정의한 attribute를 넣고 싶다면 data-를 활용한다.

- data-로 시작하는 kebab case로 html에 넣고, js로 가져올 때는 dataset.camelCase로 가져온다.
```html
<!DOCTYPE html>
<html>
    <body>
        <div id="foo" data-map-id="mapId">Hello</div>
    </body>
    <script>
        const $foo = document.getElementById('foo').dataset.mapId;
        console.log($foo); //mapId
    </script>
</html>
```


## <span style="color:#802548">_7. event_</span>
```
이벤트 핸들러는 이벤트가 발생했을 때 호출되는 함수며, 비동기이다.
이벤트가 발생했을 떄 브라우저에게 이벤트 핸들러의 호출을 위임하는 게 이벤트 핸들러 등록이다
```
### <span style="color:#FFCCFF">event attribute</span>
- 이벤트 핸들러를 등록하는 방법 중 HTML에 직접 attribute로 등록하는 방법이다.

- React나 Vue같은 front framework에서 많이 쓰인다.
```javascript
<button onclick="sayHi('Lee')">click me!</button>
function sayHi(name) {
    console.log(`Hi! ${name}`);
    console.log(`Hi! ${this}`); //일반함수여서 window. 
}

<button onclick="handleClick(this)">click me!</button>                     
//attribute에서 직접 this를 사용하면 event의 target. button 자신
<button onclick={sayHi('Lee')}>click me!</button>
//React
<button @onclick="sayHi('Lee')">click me!</button>
//Vue
```
### <span style="color:#FFCCFF">event property</span>
- 이벤트 핸들러를 등록하는 방법 중 하나로, 변수를 할당하듯이 활용한다.
- 하지만 event handler 등록은 비동기라서 callback fn을 받는 것과 동일하다.
```
하나의 이벤트 핸들러만 바인딩 가능하다.
this를 쓰면 이벤트를 바인딩한 DOM요소를 가리킨다.
```
```javascript
<button>click me!</button>
const $button = document.querySelector('button');

$button.onclick = function() { 
  //$button은 eventTarget. onclick은 on + 이벤트type
    console.log('button click'); // function은 event handler
}

$button.onclick = null; //event 핸들러 제거
```
### <span style="color:#FFCCFF">eventListener</span>
- 이벤트 핸들러를 등록할 때 바닐라 js에서 가장 많이 쓰이는 방식으로 callback fn 형태로 받는다.

- Vue나 React에서도 동적으로 형성한 HTML은 이 방법을 많이 사용한다고 한다.
```
하나의 이벤트 핸들러 이상 바인딩이 가능하다.
this를 쓰면 이벤트를 바인딩한 DOM요소를 가리킨다.
remove를 하려면 함수를 식별자에 할당하는 형태여야 한다.
```
```javascript
$button.addEventListener('click',function() {
    console.log('button click1');
})
const onClick = () => {
    console.log('button click2')
}

$button.addEventListener('click',onClick)
$button.removeEventListener('click', function() {
    console.log('button click1')
}) //변수로 callback을 넣는 게 아니면 제거 불가.

$button.removeEventListener('click',onClick); //변수로 callback을 넣어서 제거 가능
```

### <span style="color:#FFCCFF">bubbling</span>
- 이벤트 phase는 캡처링, 타깃, 버블링이 있다.
- 캡처링은 window -> document -> html -> body -> ul -> li로 event가 내려오는 과정이다.
- 타깃은 event target에 이벤트가 실제 일어나는 단계다.
- 버블링은 다시 li -> ul -> body -> html -> document -> window로 올라가는 과정이다.

- 버블링의 특성을 이용하면 이벤트 핸들러를 많이 등록하지 않고 똑같이 event를 걸 수 있다.
- 아래는 버블링의 특성을 이용하지 않고 이벤트 핸들러를 걸었다.
- 따라서 eventListener를 모든 li마다 등록해야한다.
```html
<!DOCTYPE html>
<html>
    <body>
        <nav>
            <ul id="fruits">
                <li id="apple" class="active">Apple</li>
                <li id="banana">Banana</li>
                <li id="orange">Orange</li>
            </ul>
        </nav>
        <div>선택된 내비게이션 아이템:
            <em class="msg">apple</em>
        </div>
        <script>
            const $fruits = document.getElementById('fruits');
            const $msg = document.querySelector('.msg');

            function activate({target}) {
                [...$fruits.children].forEach($fruit => {
                    $fruit.classList.toggle('active', $fruit === target);
                    $msg.textContent = target.id;
                })
            }

            document.getElementById('apple').onclick = activate; //nav마다 eventListener 등록..
            document.getElementById('banana').onclick = activate;//nav마다 eventListener 등록..
            document.getElementById('orange').onclick = activate;//nav마다 eventListener 등록..
        </script>
    </body>
</html>
```

- 이제 이벤트 위임을 사용해 중복을 제거해보자.
- 버블링을 이용하면 상위 DOM에 걸면 하위 DOM에는 걸 필요가 없다.

- 하위 DOM에도 event를 걸면 둘다 발동된다. 
- span은 li가 아니라 원하는 target이 아니므로 무시한다.
```html
<!DOCTYPE html>
<html>
    <body>
        <nav>
            <ul id="fruits">
                <li id="apple" class="active">Apple</li>
                <li id="banana">Banana</li>
                <li id="orange">Orange</li>
                <span>hi</span>
            </ul>
        </nav>
        <div>선택된 내비게이션 아이템:
            <em class="msg">apple</em>
        </div>
        <script>
            const $fruits = document.getElementById('fruits');
            const $banana = document.getElementById('banana');
            const $msg = document.querySelector('.msg');

            function activate({target}) {
                if(!target.matches('#fruits > li')){ //span은 무시
                    return;
                }

                [...$fruits.children].forEach($fruit => {
                    $fruit.classList.toggle('active', $fruit === target);
                    $msg.textContent = target.id;
                })
            }

            function bananaActivate() {
               console.log("event not ignored");
            }

            $fruits.addEventListener('click', activate);
            $banana.addEventListener('click', bananaActivate);
        </script>
    </body>
</html>
```

- 비슷한 예시를 살펴보자.
- li를 클릭 했을 때 해당 li의 text를 출력한다고 해보자.
- element마다 달아주면 일일이 달아줘야 한다.
```html
<!DOCTYPE html>
<html>
  <head>
    <style>
      .txt-blue { color: blue; }
      .txt-red { color: red; }
    </style>
  </head>
    <body>
        <ul class="list">  
          <li class="txt-blue">1번</li>  
          <li class="txt-blue">2번</li>  
          <li class="txt-blue">3번</li>
          <span>hi</span>
        </ul>  
        <script>
          const $li = document.querySelectorAll('li'); 
          for(let i = 0; i < $li.length; i++){
             $li[i].addEventListener('click', function() {
              console.log(this.textContent)
           })
          }
          
        </script>
      </body>
</html>
```

- bubbling을 이용하면 아래와 같이 달아준다.
- 다만 this가 아니라 target이다. this로 하면 ul이 this라 원하는 대로 나오지 않는다.
```html
<!DOCTYPE html>
<html>
  <head>
    <style>
      .txt-blue { color: blue; }
      .txt-red { color: red; }
    </style>
  </head>
    <body>
        <ul class="list">  
          <li class="txt-blue">1번</li>  
          <li class="txt-blue">2번</li>  
          <li class="txt-blue">3번</li>
          <span>hi</span>
        </ul>  
        <script>
          const $li = document.querySelector('ul'); 
          $li.addEventListener('click', function({target}) {
            if(!target.matches('.list > li')){ //span은 무시된다.
                  return;
            }

            console.log(target.textContent) 
          })
          
        </script>
        </body>
</html>
```

- 버블링을 막는 방법도 있다.
- stopPropagation()는 이벤트 버블링을 중단시킨다.

- 따라서 이벤트 위임 방식으로는 eventListener가 작동하지 않는다.
```html
<!DOCTYPE html>
<html>
    <body>
        <div class="container">
            <button class='btn1'>Button 1</button>
            <button class='btn2'>Button 2</button>
            <button class='btn3'>Button 3</button>
        </div>
        <script>
            document.querySelector('.container').onclick = ({target}) => {
                if(!target.matches('.container > button')) { 
                    return;
                }
                target.style.color = 'red';
            }

            document.querySelector('.btn2').addEventListener('click',(e) => {
                e.stopPropagation();            
                // 버튼 중 btn2는 버블링 전파 불가.
                e.target.style.color = 'blue';  
                //따라서 red로 컬러가 바뀌지 않음. blue로 바뀜 
            })
        </script>
    </body>
</html>
```


### <span style="color:#FFCCFF">custom event</span>
- 커스텀 이벤트를 만들 수도 있다.
- 기본값은 버블링X, cancelableX(preventDefault 호출해도 적용X)다.

- 새로운 event type이라면 CustomEvent로 생성한다.
- 기존의 Event에는 detail이라는 instance properties가 없지만, CustomEvent에는 존재한다.
- 또한 addeEventListener()만으로는 안되고 dispatchEvent()라는 함수도 호출해야 한다.

```html
<!DOCTYPE html>
<html>
<body>
    <button class="btn">Click me</button>
    <script>
        const $button = document.querySelector('.btn');

        $button.addEventListener('click',e => {
            console.log(e);
            alert(`{e} Clicked`);
        })

        const customEvent = new CustomEvent('foo', {
            detail: { //detail이라는 property
                message: 'Hello'
            }
        });
        $button.dispatchEvent(customEvent); 
        //그냥 addEventListerner 등록만으로는 안 됨. dispatch 호출해야함.
    </script>
</body>
</html>
```

- 아래는 mdn의 CustomEvent example이다.

- 기존에 있는 event에 또 엮어서 customEvent를 만든다.
```html
<!DOCTYPE html>
<html>
<body>
    <form>
      <input>
     </form>
    <script>
      const form = document.querySelector("form");
      const input = document.querySelector("input");

      form.addEventListener("awesome", (e) => console.log(e.detail.text()));
      input.addEventListener("input", function () {
        this.dispatchEvent(
          new CustomEvent("awesome", {
            bubbles: true,
            detail: { text: () => input.value },
          }),
        );
      });
    </script>
</body>
</html>
```

## <span style="color:#802548">_8. 비동기_</span>

### <span style="color:#FFCCFF">call stack, task queue, event loop</span>
- 우선 브라우저의 구성에 대해 이해할 필요가 있다.
```
JS 엔진은 단 하나의 call stack을 가진다. 
JS 엔진은 위와 같이 싱글 스레드로 동작하지만 브라우저는 멀티 스레드로 동작한다.
비동기함수(setTimeout, promise 등)의 callback함수는 call stack에 바로 들어가지 않고 event queue에 들어간다.
event queue에 들어간 callback함수는 call stack이 비어있을 때 event loop에 의해 call stack으로 불려와 호출된다.
push된 함수는 call stack에서 실행이 끝나면 pop된다.
```

- 코드로 이해하는 것이 빠를 것이다. 아래 코드를 보자.

- console에 찍힌 것은 step1, step2, step3이 아니다. step1, step3, step2다.
```javascript
function step1() {
    console.log('step1');
}

function step2() {
  setTimeout(function() {
    console.log('step2');
  },0);
}

function step3() {
    console.log('step3');
}

step1();
step2();
step3();
/*
step1
step3
step2
*/
```

- 그 원리를 설명하자면 아래와 같다.
- step1, step2, step3 순서대로 call stack에 push된다.
- step1이 가장 먼저 push되어 실행되고 pop된다.

- step2가 그 다음 call stack에 push된다. setTimeout이라는 함수가 실행되고, callback fn이 timer로 이동한다.
- step2가 call stack에서 사라졌으니 step3가 push된다.
- 4ms정도(0초라도 실제론 지연시간이 있다) 지난 step2의 callback함수가 event queue로 이동한다. step 3도 실행되어 pop된다.
- step3가 call stack에서 사라지고 나서 callback함수가 event loop에 의해 call stack으로 이동한다.
- callback 함수인 console.log('step2')가 실행되고 call stack에서 pop된다.

### <span style="color:#FFCCFF">setTimeout</span>

- rendering엔진은 JS엔진이 소스코드 평가-실행 상태인 경우 동작하지 않는다. 즉 call stack에 함수가 있으면 작동하지 않는다.
- setTimeout을 사용하게 되면 event queue에서 callback 함수가 call stack으로 이동하는 시간 동안 call stack이 비어있다.

- 그 때 rendering 엔진이 작동하여 UI 변화가 그려진다.
- 더 자세한 내용은 https://www.youtube.com/watch?v=8aGhZQkoFbQ를 참고하자.
- 쉽게 이해할 수 있는 예시를 들어보자.
- 아래와 같은 eventListener가 있다고 해보자.
```javascript
$(".btn").click(function() {
  showLoadingBar();
  takesManyTime();
  displayNoneLoadingBar();
  updateHTML();
});
```
- 안에서 다음과 같은 일이 벌어진다.
```
1. 로직이 오래 걸리는 함수인데, 해당 함수 이후에 UI가 변화해야한다.
2. 기다리는 시간이 오래걸리므로 loading bar를 띄우게끔 했다.
3. 하지만 예상과 다르게 loading bar가 떠오르지 않는다.
4. showLoadingBar 함수의 실행이 끝나고 렌더링 엔진이 렌더링을 하려해도,태스크 큐에서 takesManyTime()가 실행되고 있다. 
5. 렌더링 엔진은 JS엔진이 작동할 때 대기상태이므로 JS코드가 전부 실행될 때까지 기다리게 된다.
6. 그럼 takesManyTime이 끝나고,  displayNoneLoadingBar가 끝나고, updateHTML가 끝난 뒤에야 rendering 엔진이 작동하며, 이 때는 loading bar가 이미 hide 되었다.
7. 따라서 repaint할 loading bar 관련 DOM 요소가 없다. JS에 의해 영향을 받은 DOM요소가 있어야 렌더링 엔진이 render가 이뤄지는데, 해당 시점에서 loading bar DOM요소가 없다.
8. 따라서 JS를 통해 text나 attribute가 변경된 DOM만 repaint가 이뤄진다. takesManyTime이후 변경되는 DOM들이 다시 rendering되어 화면이 출력된다. 거기엔 loading bar는 없다.
```
- 사라진 loading bar를 띄워보려면 어떻게 할까?
- 간단하게 아래와 같이 바꿀 수 있다.

- setTimeout을 이용하는 것이다.
```javascript
$(".btn").click(function() {
  showLoadingBar();
  setTimeout(function() {
    takesManyTime();
    displayNoneLoadingBar();
    updateHTML();
  }, 0);
});
```
- setTimeout을 적용하면 어떤 일이 벌어질까?

```
1. showLoadingBar가 호출된다.
2. 그 이후 setTimeout이 호출되어 call stack이 잠시 비게 된다.
3. 그동안 rendering 엔진이 UI를 repaint한다. 즉 loading bar가 나타난다.
4. 렌더링이 끝나고 event queue에 있던 3개의 함수가 차례로 call stack에 push된다.
5. takesManyTime가 호출되고, fetch가 끝나면 displayNoneLoadingBar가 호출되며 마지막으로 updateHTML이 호출된다.
6. 따라서 loading bar가 사라지고, 바뀐 data로 repaint하여 HTML이 렌더링된다.
```
- 위는 이해를 돕기 위한 간단한 예시고, 실제 구현을 살펴보자.
- 시작을 누르고 진행 확인을 누르면 반응이 없는 것을 볼 수 있다.

- 또한 percentage가 한번에 확 올라가버린다.
```html
<html>
	<head>
	<style>
		#progressBar { position: relative; width: 100%; height: 30px; 
           background-color: #ddd; }
		#progressPercent { position: absolute; width: 0%; height: 100%;
           background-color: #ff9933; }
	</style>
	</head>

	<body>
		<button type="button"  onclick="alert('실행중');">진행확인</button>
		<button type="button"  onclick="execute();">시작</button>

		<div id="progressBar" style="margin-top:20px;">
			<div id="progressPercent"></div>
		</div>
	</body>
	<script>
    let dataList = [[]];
    let availableDataList = [];
		let targetList = [1];
    let allCount = 100_000;
		let remainCount = 100_000 ;
		

    //longTakingLogic을 위해 data를 만든다
		function initData() {
			for (let i = 0; i < 10; i++) {
				dataList[i] = [];

				for (let j = 0; j < 2000; j++) {
					dataList[i][j] = (i + j);
				}
			}
		}

		// 오래 걸리는 작업
		function longTakingLogic() {
			for (let i = 0; i < targetList.length; i++) {
				for (let j = 0; j < dataList.length; j++) {
					for (let k = 0; k < dataList[j].length; k++) {
						if (dataList[j][k] == targetList[i]) {
							availableDataList.push(targetList[i]);
						}
					}
				}
			}
		}

    //버튼을 누르면 실행되는 작업
		function execute() {
			let progressElement = document.getElementById("progressPercent");
			let isCompleted;
			
			initData();
			
			for (let i = 0; i < allCount; i++) {
				longTakingLogic();
				remainCount = remainCount - 1;
				isCompleted = (i+1) % 10000 == 0;
				
				if (isCompleted) {
					// progress 표시
					progressElement.style.width = ((allCount - remainCount) / allCount) * 100 + '%';
				}
			}
		}

		</script>
</html>
```
- 왜 이런 일이 벌어지는지 로직을 살펴보자.
```
1. 시작을 누르면, execute()가 call stack에 push된다.
2. execute 안에서 initData()가 call stack에 push된다.
3. for문을 돌면서 longTakingLogic()이 push된다. if문의 로직이 실행되어 DOM property가 변경된다.
4. 하지만 곧바로 longTakingLogic()이 다시 push된다. JS엔진이 실행 중이라 if문의 로직이 실행되어 DOM property가 변경되어도 렌더링 엔진이 repaint할 수 없다. 
5. 맨 마지막에 isCompleted가 false가 되면 for문을 빠져나오고 execute()가 종료되고 call stack이 빈다.
6. 그럼 마지막 Dom property 조작만 rendering 엔진이 인식하여 repaint한다.
7. 따라서 한꺼번에 쫙 차오르게 된다. 또한 실행중 버튼을 눌러도 call stack이 차있어 event queue에서 event handler인 alert함수가 대기하게 된다.
8. 마찬가지로 call stack이 비게 되면 alert가 뜨게 된다. 따라서 repaint도, alert(실행중)도 execute()가 끝나기 전까지 작동하지 않는다.
9. 눌러놨던 숫자만큼 alert()가 call stack에 push되고 실행된다.
```

- 아래와 같이 setTimeout을 넣으면 의도한대로 작동하게 된다. 

- percentage가 계속 순차적으로 올라가고, 진행확인을 누르면 alert가 반응한다.
```javascript
//버튼을 누르면 실행되는 작업
function execute() {
    let progressElement = document.getElementById("progressPercent");
    let isCompleted;
    
    initData();
    
    for (let i = 0; i < allCount; i++) {
      //오래 걸리는 작업, UI를 변화시키는 DOM 조작을 모두 setTimeout에 집어넣음.
      setTimeout(function(){
        longTakingLogic();
        remainCount = remainCount - 1;
        isCompleted = (i+1) % 10000 == 0;
      
        if (isCompleted) {
          progressElement.style.width = ((allCount - remainCount) / allCount) * 100 + '%';
        } 
      },0)
    }
  }
```

- 이건 왜 작동할까? 로직을 살펴보자.

```
1. 시작을 누르면, execute()가 call stack에 push된다.
2. execute 안에서 initData()가 call stack에 push된다.
3. for문을 돌면서 setTimeout()이 call stack에 push된다. 
4. setTimeout에 있는 callback function이 timer를 거쳐 event queue에 push된다.
5. 그동안 call stack이 비어있다. 따라서 변경된 DOM property에 맞게 렌더링 엔진이 repaint한다. 
6. rendering 엔진이 repaint가 끝나면 JS 엔진이 나머지 for문을 다시 돈다.
7. 이러한 과정이 반복된다.
```
- 아래와 같이 중간(별표주석자리)에 console.log를 넣어주면 UI가 update되는 속도가 현저히 느려진다. 

- console.log가 추가되면서 JS엔진이 더 오랜 시간을 점유하고 있기에 렌더링 엔진이 repaint하는 시점이 그만큼 느려진 것이다.
```javascript
function execute() {
    let progressElement = document.getElementById("progressPercent");
    let isCompleted;
    
    initData();
    for (let i = 0; i < allCount; i++) {
      setTimeout(function(){
        longTakingLogic();
        remainCount = remainCount - 1;
        isCompleted = (i+1) % 10000 == 0;
      
        if (isCompleted) {
          progressElement.style.width = ((allCount - remainCount) / allCount) * 100 + '%';
        } 
      },0)
     /**/ console.log((allCount - remainCount) / allCount); 
    }
  }
```

### <span style="color:#FFCCFF">debounce와 throttle</span> 
- setTimeout을 활용해 event handler를 덜 호출하게 만들 수 있다.
- 아래와 같이 input이 바뀔 때마다 text가 바뀌게끔 해보자.

- 그럼 매번 input이 바뀔 때마다 browser가 repaint하여 rendering이 이뤄진다.
- 이렇게 event handler가 과도하게 호출되면 성능 손실로 이뤄진다.

```html
<!DOCTYPE html>
<html>
  <body>
    <input type="text">
    <div class= "msg"></div>
    <script>
      const $input = document.querySelector('input');
      const $msg = document.querySelector('.msg');

      const debounce = (callback, delay) => {
        let timerId;
        return (...args) => {
          if (timerId) {
            clearTimeout(timerId);
          }
          timerId = setTimeout(callback, delay, ...args);
        }
      }

      $input.addEventListener('input', e => {
        $msg.textContent = e.target.value;
      },300)
    </script>
  </body>
</html>
```

- scroll, input, mousemove가 특히 그러하다. 이 경우에, 일정 시간 경과 이후 event handler를 호출하게끔 할 수있다. 이것을 debounce라고 한다.

- 0.3 초 내에 입력이 다시 이뤄지면 다시 timer를 등록하고, 0.3초가 지나도록 입력이 없으면 event handler가 호출된다.
```html
<!DOCTYPE html>
<html>
  <body>
    <input type="text">
    <div class= "msg"></div>
    <script>
      const $input = document.querySelector('input');
      const $msg = document.querySelector('.msg');

      const debounce = (callback, delay) => {
        let timerId;
        return (...args) => {
          if (timerId) {
            clearTimeout(timerId);
          }
          timerId = setTimeout(callback, delay, ...args);
        }
      }

      $input.addEventListener('input', debounce(e => {
        $msg.textContent = e.target.value;
      },300))
    </script>
  </body>
</html>
```

- 아래는 input이 아닌 scroll의 예시다.
- scroll은 delay 시간이 경과했을 때 event가 발생하면 callback 함수를 호출하고 다시 timer를 재설정한다.

- throttle이 없는 함수에서는 delay 시간 간격이 아니라 매번 event handler가 호출된다.
- throttle이 있는 함수에서는 delay 시간 간격마다 event handler가 호출된다.
```html
<!DOCTYPE html>
<html>
  <head>
    <style>
      .container {
        width: 300px;
        height: 300px;
        background-color: rebeccapurple;
        overflow: scroll;
      }

      .content {
        width: 300px;
        height: 1000vh;
      }
    </style>
  </head>
  <body>
    <div class="container">
      <div class="content"></div>
    </div>
    <div>
      일반 이벤트 핸들러가 scroll 이벤트를 처리한 횟수:
      <span class="normal-count">0</span>
    </div>
    <div>
      스로틀 이벤트 핸들러가 scroll 이벤트를 처리한 횟수:
      <span class="throttle-count">0</span>
    </div>

    <script>
      const $container = document.querySelector('.container');
      const $normalCount = document.querySelector('.normal-count');
      const $throttleCount = document.querySelector('.throttle-count');

      const throttle = (callback, delay) => {
        let timerId;

        return (...args) => {
          if (timerId) {
            return;
          }

          timerId = setTimeout(()=> {
            callback(...args);
            timerId = null;
          }, delay);
        };
      };

      let normalCount = 0;
      $container.addEventListener('scroll',() => {
        $normalCount.textContent = ++normalCount;
      })

      let throttleCount = 0;
      $container.addEventListener('scroll', throttle(() => {
        $throttleCount.textContent = ++throttleCount;
      },100))
    </script>
  </body>
</html>
```

- 다만 실무에서는 직접 구현보다는 lodash의 debounce()와 throttle()을 활용하는 게 더 좋다고 한다. 


### <span style="color:#FFCCFF">주의점</span>
- setTimeout에서 주의할 점은 try ~ catch로 감싸면 안 된다는 점이다.
- setTimeout은 비동기 함수라서 콜백함수가 호출되는 것을 기다리지 않고 종료된다.
- setTimeout의 callback 함수는 event queue로 push되어 call stack으로 브라우저에 의해 push된다.

- 따라서 callback 함수의 호출자가 없고, 콜백함수에서 난 error가 setTimeout()으로 전파될 수도 없다. 이미 종료되어 call stack에서 pop되었기 때문이다. 다시 말해 상위 호출자인 setTimeout으로 error가 전파되지 않아 error가 catch되지 않는다.
- catch되지 않은 error가 있어 프로그램이 강제 종료된다.
```javascript
try { 
    setTimeout(() => {
        throw new Error('Error!');
    }, 1000)
} catch(err) {
    console.error('캐치한 에러',err); //catch 되지 않는다. 따라서 console에 찍히지 않는다.
}
```

### <span style="color:#FFCCFF">Promise</span>
- promise는 비동기처리에 사용되는 객체다. setTimeout의 callback hell이라는 문제점을 극복하고자 하는 시도에서 탄생했다.

- 주로 서버과 통신해 api response를 받아올 떄 쓰인다.
- 문법은 아래와 같다.

```
성공(fulfilled)이라면 resolve()에 비동기처리 결과를 담는다.
실패(rejected)라면 reject()에 비동기처리 결과를 담는다.
```

- 아래는 성공했을 경우에 대한 예시다.
```javascript
function getData() {
  return new Promise(function(resolve, reject) {
    var data = 100;
    resolve(data);
  });
}

// resolve()에 argument로 넣은 data를 callback function의 parameter로 가져온다.
getData().then(function(resolvedData) {
  console.log(resolvedData); // 100
});
```

- 아래는 비동기 처리 결과가 오류일 때에 대한 예시다.
```javascript
function getData() {
  return new Promise(function(resolve, reject) {
    reject(new Error("Request is failed"));
  });
}

// reject()의 결과 값 Error를 err에 받음
getData().then().catch(function(err) {
  console.log(err); // Error: Request is failed
});
```

- 아래는 실제 서버에 요청하는 request를 비동기로 보내는 간단한 예시다.
- status가 200이면 성공이므로 resolve 처리한다.

- resolve(성공)한 데이터를 then()으로 받는다. 
```javascript
const promiseGet = url => {
    return new Promise((resolve, reject) => {
        const xhr = new XMLHttpRequest();
        xhr.open('GET', url);
        xhr.send();

        xhr.onload = () => { 
          //onload는 event property 방식의 event handler.
          //event queue에 callback 함수를 push
            if(xhr.status === 200) {
                resolve(JSON.parse(xhr.response));
            } else {
                reject(new Error(xhr.status));
            }
        }
    })
}

promiseGet('https://jsonplaceholder.typicode.com/posts/1').then(res => console.log(res));
/*
{
  "userId": 1,
  "id": 1,
  "title": "sunt aut facere repellat provident occaecati excepturi optio reprehenderit",
  "body": "quia et suscipit\nsuscipit recusandae consequuntur expedita et cum\nreprehenderit molestiae ut ut quas totam\nnostrum rerum est autem sunt rem eveniet architecto"
}
*/
```

- Promise에서는 catch에서 error를 처리한다.

- then은 성공했을 떄의 처리를 담당한다.
- finally는 어떤 경우에도 일어나야만 할 때 필요하다.
```javascript
promiseGet('https://jsonplaceholder.typicode.com/posts/1')
    .then(res => console.log(res))
    .catch(err => console.error(err))
    .finally(() => console.log('Bye!'));
```

- 위에서는 XMLHttpRequest를 사용했지만, 최신 api로 fetch가 있다.

- fetch는 XMLHttpRequest와 기능이 같다. 다만 HTTP error의 경우, reject하지 않는다.
- 대신 resolve()로 받은 response의 ok 값이 false로 온다. 따라서 서버에러를 catch가 아닌 then에서 처리해줘야 한다. 
```javascript
const request = {
    get(url){
        return fetch(url);
    }
}

function getResponse() {
  request.get('https://jsonplaceholder.typicode.com/todos/1')
    .then(response => {
        if(!response.ok) {
            throw new Error(response.statusText); 
        }

        return response.json();
    })
    .then(todos => console.log(todos))  
    // response.json()을 받은게 todos
    .catch(err => console.error(err)); 
    //오프라인 네트워크 장애, CORS 설정 에러 등만 잡힘
}
getResponse();
```

- Promise를 동기처럼 처리하고 싶다면 async - await를 쓸 수도 있다.

- async await의 경우, try ~ catch문에서 error를 처리하는 경우가 흔하다. 
- .catch()보다 가독성이 좋기 때문이라고 한다.
```javascript
const request = {
    get(url){
        return fetch(url);
    }
}

async function getResponse() {
  try{
    const result = await request
                            .get('https://jsonplaceholder.typicode.com/todos/1')
                            .then(res => res.json()).catch(err => err);
    console.log(result);
  } catch(e) {
    console.log(e);
  }
}

getResponse(); 
/*
{
  "userId": 1,
  "id": 1,
  "title": "delectus aut autem",
  "completed": false
}
*/
```

- 아래의 예시를 살펴보자. async 함수 범위안의 console에는 찍히지만, 밖에서 console을 찍어 결과를 가져오면 결과를 가져오지 못한다.
- console.log의 위치를 async 밖으로 빼내면 원하는 데이터가 나오지 않는 이유가 무엇일까?

- 그 이유는 async fn 내부에서만 await 함수가 끝나는 것을 기다리기 때문이다. 
- 즉 getResponse()의 await가 붙은 함수가 실행이 끝나 response가 올 때까지 기다려주지 않는다.
```javascript
const request = {
    get(url){
        return fetch(url);
    }
}

async function getResponse() {
  try{
    const result = await request.get('https://jsonplaceholder.typicode.com/todos/1').then(res => res.json());

    return result;
  } catch(e) {
    console.log(e);
  }
}

console.log(getResponse()); //Promise
```

- 그를 해결하려면 간단하다. getResponse()에도 await를 붙여준다.
```javascript
const request = {
    get(url){
        return fetch(url);
    }
}

async function getResponse() {
  try{
    const result = await request.get('https://jsonplaceholder.typicode.com/todos/1').then(res => res.json());

    return result;
  } catch(e) {
    console.log(e);
  }
}

console.log(await getResponse());
/*
{
  "userId": 1,
  "id": 1,
  "title": "delectus aut autem",
  "completed": false
}
*/
```


- 이와 똑같은 맥락으로 then문 이전과 이후를 나눠서 변수로 만들면 데이터가 나오지 않는다.
```javascript
const request = {
    get(url){
        return fetch(url);
    }
}

async function getResponse() {
  try{
    const result = await request.get('https://jsonplaceholder.typicode.com/todos/1');
    console.log(result instanceof Response) //true
    console.log(result instanceof Promise) //false

    const data = result.then(res => res.json()); //result.then is not a function
  } catch(e) {
    console.log(e);
  }
}
```

- then문을 나눴다면 이미 resolve되어 Response 객체가 되었으므로 Response라는 prototype에 있는 json()을 통해 결과값을 받아와야 한다.

- 하지만 아래와 같이 하면 그냥 Promise 객체만 도달한다. 결과값을 얻어오는 것도 비동기처리의 일환이라 그렇다.
```javascript
const request = {
    get(url){
        return fetch(url);
    }
}

async function getResponse() {
  try{
    const result = await request.get('https://jsonplaceholder.typicode.com/todos/1');
    const data = result.json(); 
    console.log(data)
  } catch(e) {
    console.log(e);
  }
}

getResponse(); //Promise<pending>
```

- 비동기처리를 동기처럼 바꿔주기 위해 json() 함수에도 await를 붙여주면 값이 정상 출력된다.
```javascript
const request = {
    get(url){
        return fetch(url);
    }
}

async function getResponse() {
  try{
    const result = await request.get('https://jsonplaceholder.typicode.com/todos/1');
    const data = await result.json(); 
    console.log(data)
  } catch(e) {
    console.log(e);
  }
}

getResponse();
/*
{
  "userId": 1,
  "id": 1,
  "title": "delectus aut autem",
  "completed": false
}
*/
```


- promise를 병렬로 가져오는 방법도 있다.
- 아래의 예시를 살펴보자. 한꺼번에 병렬로 가져오면 1초면 되는 작업이다.

- 하지만 동기로 처리되어 이전의 처리 결과를 기다리게 된다. 그럼 10초가 걸린다.
```javascript
function setTimeoutPromise(ms) {
  return new Promise((resolve, reject) => {
    setTimeout(() => resolve(), ms);
  });
}

async function fetchAge(id) {
  await setTimeoutPromise(1000);
  console.log(`${id} 사원 데이터 받아오기 완료!`);
  return parseInt(Math.random() * 20, 10) + 25;
}

async function startAsyncJobs() {
  let ages = [];
  for (let i = 0; i < 10; i++) {
    let age = await fetchAge(i);
    ages.push(age);
  }

  console.log(
    `평균 나이는? ==> ${
      ages.reduce((prev, current) => prev + current, 0) / ages.length
    }`,
  );
}

startAsyncJobs();
```

- 따라서 비동기 작업이 순차를 기다리지 않아도 되는 경우에는 아래와 같이 Promise.all을 사용하는 것이 더 좋다.
```javascript
function setTimeoutPromise(delay) {
  return new Promise((resolve) => setTimeout(resolve, delay));
}

async function fetchAge(id) {
  await setTimeoutPromise(1000);
  console.log(`${id} 사원 데이터 받아오기 완료!`);
  return Math.round(Math.random() * 20) + 25;
}

async function startAsyncJobs() {
  const ids = Array.from({ length: 10 }).map((_, index) => index);

  const promises = ids.map(fetchAge);

  const ages = await Promise.all(promises);

  console.log(
    `평균 나이는? ==> ${
      ages.reduce((prev, current) => prev + current, 0) / ages.length
    }`,
  );
}

startAsyncJobs();
```

- async - await는 비동기 함수 중 Promise 함수를 사용했을 때만 사용가능하다.
- async 함수가 붙으면 반드시 resolve된 Promise를 return해야 하기 때문이다.

- async keyword가 붙으면 await도 써주는 게 좋다.
- 아래를 보면 await가 붙어있지 않다. 그러자 catch문에 error가 catch되지 않는다.
- 색깔도 warn이라 노란색이어야 하는데 빨간색으로 나온다.
```javascript
async function thisThrows() {
    throw new Error("Thrown from thisThrows()");
}

async function myFunctionThatCatches() {
    try {
        return thisThrows();
    } catch (e) {
        console.warn(e,'catch');
    } finally {
        console.log('We do cleanup here');
    }
    return "Nothing found";
}

async function run() {
    const myValue = await myFunctionThatCatches();
    console.log(myValue);
}

run(); //We do cleanup here
/*
  Uncaught (in promise) Error: Thrown from thisThrows()
  at thisThrows (<anonymous>:2:11)
  at myFunctionThatCatches (<anonymous>:7:16)
  at run (<anonymous>:17:27)
  at <anonymous>:21:1
*/
```

- await를 쓰면 아래에서 error가 catch되는 모습을 볼 수 있다. 
- 색깔도 warn으로 노란색이다.
```javascript
async function thisThrows() {
    throw new Error("Thrown from thisThrows()");
}

async function myFunctionThatCatches() {
    try {
        return await thisThrows();
    } catch (e) {
        console.warn(e, 'catch');
    } finally {
        console.log('We do cleanup here');
    }
    return "Nothing found";
}

async function run() {
    const myValue = await myFunctionThatCatches();
    console.log(myValue);
}

run();  
/*
  Error: Thrown from thisThrows()
  at thisThrows (<anonymous>:2:11)
  at myFunctionThatCatches (<anonymous>:7:22)
  at run (<anonymous>:17:27)
  at <anonymous>:21:1 'catch'
*/
//We do cleanup here
//Nothing Found

```

- 즉 async를 쓰면 await도 써야 try ~ catch에서 error catch가 정상으로 작동한다고 볼 수 있다.
- eslint를 활용해서 await를 안쓰면 error를 낼 수도 있다.
```
  "rules": {
    .
    .
    .
    "require-await": "error",
  }
```

