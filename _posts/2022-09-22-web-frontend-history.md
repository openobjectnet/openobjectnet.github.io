---
title: "웹과 프론트엔드의 변천사 그리고 프론트엔드 프레임워크"
search: false
categories:
  - FrontEnd
toc: true
tags:
  - FrontEnd
author: 김도겸
---

<br>

# Intro
안녕하세요. 김도겸입니다. <br>
웹과 프론트엔드의 변화 과정과 프론트엔드 프레임워크에 대한 간략한 정보를 정리해보았습니다.

<br><br>

# 1. 웹의 변천사 

웹은 다른 학문이나 기술에 비해 굉장히 빠르게 진화하고 있습니다.   
웹의 역사가 30년 정도 되었지만 큰 패러다임이 2번이나 바뀌었고, 웹 전문 분석가들은 패러다임이 바뀌는 분기점을 기준으로 웹을 1.0, 2.0, 3.0으로 구분하고 있습니다.   
<br>
시기별로 특징과 주요 개념 등을 소개하겠습니다.

## - Web 1.0

웹 1.0은 웹이 처음 탄생한 1990년부터 웹 2.0이 유행하기 전인 약 2004년까지의 구간을 의미합니다.   
웹이 등장한 이후로 기존의 아날로그 방식과는 비교할 수 없을 정도로 빠르게 필요한 정보를 탐색하고 볼 수 있게 되었습니다.   
넷스케이프, 인터넷 익스플로러 같은 1세대 웹 브라우저가 탄생하였고, 구글 야후와 같은 검색포털이 유행, 이베이, 아마존 등의 온라인 커머스 서비스도 등장하였습니다.   
핵심 기술은 HTML과 ActiveX가 있습니다.

![web 1.0](/assets/images/web-frontend-history/web-1.0.png)
<br>

이 웹 1.0 시대의 핵심 키워드 <b>Read Only</b>입니다.   
정보 공급자 이외의 대부분의 사용자는 정보를 단순히 읽고 소비하는 기능이 가장 큰 시점이었다는 것을 말합니다.
유저들은 단순히 웹에서 뉴스와, 논문, 궁금한 정보 검색, 원하는 물건들을 탐색하고 구매하는 공간 정도였으니 정보 공급자와 소비자의 역할이 엄격하게 구분되었다는 것이 큰 특징 중 하나입니다.


## - Web 2.0

2004년 이후로는 여러가지 플랫폼 서비스가 등장하며 웹 시장은 큰 혁신이 찾아오게 됩니다. 
웹 2.0에서는 단방향으로 소통하던 웹 1.0과는 달리 양방향 소통이 가능하다는 것이 가장 큰 혁신이라고 볼 수 있는데요.   
크롬, 파이어 폭스 같은 확장 브라우저가 탄생하였고, 위키피디아, 블로그, 싸이월드, 페이스북, 트위터, 유튜브 등과 같은 수많은 소셜 미디어 서비스가 등장하였습니다.
핵심 기술은 AJAX, XML, RSS, Tagging 등이 있습니다.

![web 2.0](/assets/images/web-frontend-history/web-2.0.png)
<br>

웹 2.0의 핵심 키워드는 <b>Read and Write</b>입니다.
사용자들은 게시글을 읽는 소비자임과 동시에 글을 작성해 등록할 수 있는 생산자 또한 될 수 있다는 것을 의미합니다.   
이제 웹에서 더 이상 유저들은 보고 읽기만 하지 않고, 자신이 원하는 것과 알고 있는 것을 다른 사람들에게 전달하고 함 게 소통할 수 있게 되었습니다.
또한 여러 제약이 해결된 스마트폰을 만나며 더욱 활성화하고, 더욱 발전하였습니다.

하지만 웹 2.0에서도 고민해볼만한 사항이 한 가지 생겼습니다.   
사용하는 서비스에서 발생하는 데이터는 모두 기업이 소유하고, 정보 소유권도 모두 기업이 가져가는 중앙 집권적 구조를 갖는데, 이를 웹 개발자와 전문가들은 데이터를 분산 저장하여 정보 소유권을 해당 소유자에게 전달해주는 방향으로 중앙의 통제에서 벗어날 수 없을 지 고민하였습니다.

## - Web 3.0

웹 3.0은 갑자기 생긴 개념이 아닌 웹 2.0의 시대가 진행된 2004년부터 꾸준히 나온 고민과 단점들을 해결하기 위해 꾸준히 거론되었으며 현재까지도 완전하지 않고 개발 진행 중인 시점이라고 보시면 되겠습니다.   
<br>
현재 진행된 웹 3.0의 핵심 키워드는 3가지가 있는데, <b>Semantic Web</b>, <b>탈중앙화</b>, <b>메타버스</b>입니다.

- <b>Semantic Web</b>   
    시맨틱 웹은 웹에 존재하는 수많은 웹페이지들에 메타데이터를 부여하여, 기존의 잡다한 데이터 집합이었던 웹페이지를 '의미'와 '관련성'을 가진 거대한 DB를 구축하고자 하는 발상입니다.   
    HTML에서 사용하는 시맨틱 태그(form, table, img)들이 브라우저, 검색엔진, 개발자 모두에게 콘텐츠의 의미를 명확히 설명하는 역할을 해준다는 것입니다.

    한가지 예로 들자면 바로 이것입니다.

    ```html
    <font size="6"><b>hello</b></font>
    <h1>hello<h1>
    ```

    위 코드는 동일한 외형을 갖게 되지만, 이는 큰 차이점을 가지고 있습니다.   
    1행의 코드는 아무런 의미가 없이 그냥 font 태그와 b태그를 활용하여 텍스트를 꾸몄지만, 2행의 코드는 header(제목)중 가장 상위 레벨이라는 의미를 가진 h1 태그를 활용하였습니다.

    2행의 요소는 의미를 가지고 있으니 여러가지 측면에서 많은 이점이 있습니다.   
    개발자의 입장에서는 의도한 요소의 의미가 명확히 드러나기 때문에 코드의 가독성을 높이고 유지보수가 용이해집니다.   
    검색엔진에서는 대체로 h1 요소내의 컨텐츠를 웹 문서의 중요한 제목으로 인식하고, 인덱스에 포함 시킬 확률이 높습니다.   

    ![semantic-web](/assets/images/web-frontend-history/semantic-web.jpeg)

    > 위 사진처럼 단순 div 태그가 아닌 영역 구분이 가능한 sematic tag들로 더욱 보기 쉬운 시맨틱 웹을 구축할 수 있습니다.

    <br>

    이처럼 시맨틱 웹은 이전 방식의 웹들보다 훨씬 빠르고 정확한 정보를 탐색하고 수집할 수 있게 되었고, 보다 쉬운 웹이 개발되었다는 큰 발전을 이루었습니다.

- <b>탈중앙화</b>   
    탈중앙화는 위 Web 2.0 파트에서 설명드린 고민 사항에 대한 해결방안으로, 데이터를 일부 기업이나 플랫폼이 독점하는 현상에서 벗어난다는 의미를 가집니다.   
    웹 서비스를 이용하는 과정에서 생성된 데이터를 개인이 온전히 소유해 데이터에 대한 통제권을 사용자 본인이 가져오는 형태를 의미합니다.

    이 탈중앙화의 가장 큰 예로는 바로 '블록체인'입니다.

    ![block-chain](/assets/images/web-frontend-history/block-chain.png)

    블록체인의 개념은 굉장히 복잡하지만 간단하게 설명하서 데이터를 기록한 장부를 데이터를 만드는데 참여한 사람들에게 나눠주는 기술로, 데이터를 만드는 데 기여한 사람들끼리 정보를 나눠 갖는 구조입니다.   
    이렇게 데이터를 분산 저장할 수 있게 되며, 위변조를 위해서는 정보를 가져간 사람 중 과반수의 동의가 필요했기 때문에 해킹 등 외부 위험으로부터 데이터를 안전하게 보관할 수도 있게 되었습니다.
    
- <b>메타버스</b>   
   요즘 굉장히 핫한 키워드인 메타버스도 web 3.0의 주요한 키워드입니다.   
   과거의 메타버스는 단순히 3차원으로 구현된 가상 공간이었지만 정보통신기술의 혁신이 더해지면서 가상과 현실이 융합된 디지털 세계로 진화했습니다.
   VR, AR, MR 기술의 발전은 현실과 가상의 경계를 허문 사용자 경험을 제공하였고, NFT를 통해 디지털 자산의 소유권 증명이 가능해지는 등 새로운 디지털 생테계를 형성하고 있습니다.   
   <br>
   메타버스는 경제, 사회 활동이 가능한 공간이자 웹 3.0이 제시하는 새로운 삶의 패러다임이며 많은 분야에서 사업화 시도 중입니다.   
   게임, 엔터테인먼트 뿐만 아니라 공공, 교육 등 거의 모든 분야에서 주목하고 있다는 것입니다.

   ![metaverse](/assets/images/web-frontend-history/metaverse.jpeg)

   Web 3.0은 위에서도 말씀드렸다 싶이 현재 진행형입니다.   
   위에서 설명드린 3가지 키워드 뿐만 아니라 인공지능, IOT 등 다양한 기술들이 접목 되어 사회 전반의 엄청나고 새로운 변화와 발전을 이뤄가고 있습니다.   
   Web 3.0이 완성될 때쯤엔 어떤 세상이 되어있을지 굉장히 궁금하고 기대됩니다.

   <br>

# 2. 프론트엔드의 발전

프론트엔드는 웹 서비스 파트에서 갑작스럽게 생겨난 포지션이지만 엄청나게 성장하고 변화하며, 영향력이 확장되고 있습니다.   
비록 프론트엔드라는 포지션은 갑작스럽게 생겨났지만 관련 기술들은 웹이 만들어진 1990년부터 점진적으로 개발되고 발전되고 있었습니다.

모두 세분화하여 개념부터 원리까지 설명하기는 매우 힘들기 때문에 큼직큼직한 기술의 등장과 발전 과정을 간략하게 정리하여 설명드리도록 하겠습니다.

## - Javascript의 탄생

웹이 등장하면서 서버뿐만이 아니라 브라우저에서 실행이 가능한 스크립트 언어가 필요해지면서 Javascript가 만들어졌습니다.   
이제 서버와 통신을 하기 전에 화면을 조작하거나 서버에 데이터를 전달하기 전에 검증을 하는 등의 기능을 할 수 있게 되었습니다.

## - CSS의 등장과 웹 디자인의 발전

HTML에 과도한 스타일이 작성되면서 스타일을 수정하고 싶을때 HTML을 다 뜯어 고쳐야 할 수도 있는 상황이 발생했습니다.   
이런 문제때문에 유지보수가 어려워지면서 '컨텐츠와 서식을 분리하자'라는 의견이 나오며 CSS가 탄생하였습니다.   
CSS가 탄생하면서 간결한 코드를 사용해 유용하게 웹 유지보수가 가능해짐과 동시에 웹 디자인의 엄청난 발전을 이루었습니다.

## - IE 대중화, 웹 상업화

Windows가 대중화되고, IE가 기본 탑재가 되면서 홈페이지가 모든 기업의 필수가 되어가고 있는 시대가 왔었습니다.   
이로 인해 소비자들의 관심을 사로잡기 위해서, 기업으로서의 경쟁력을 가지기 위해서는 전문적으로 HTML과 CSS, javascript를 다루는 웹 페이지 개발자가 필요했습니다.   
이런 웹 개발자들에게는 실력없는 개발자라는 인식이 만들어진 시기기도 했습니다.

> 웹 개발은 다른 개발자들에 비해 상대적으로 쉬운 개발 난이도와 로직 자체가 중요하지 않은 부분이었기 때문에 실력없는 개발자라는 인식이 생겼다고 합니다.

## - Google의 프로젝트

구글은 Microsoft에 있는 Office를 웹으로 옮겨오는 시도를 하게 됩니다.   
이 과정에서 IE보다 더 좋은 브라우저가 필요했고, 웹 개발에 용이해야 했기에 디버그 기능에 공을 들이기 시작했고, js의 성능을 올리기 위해 자체적인 자바스크립트 엔진도 만들기 시작합니다.

구글 뿐만 아니라 이 시점에서 IE의 독점에 대항하기 위해 여러가지 브라우저들이 이 시기에 만들어지기 시작했고, IE 강점기의 시대에 있었던 하나의 혁명이되었으며,   
웹 브라우저의 발전과 동시에 프론트엔드라는 포지션의 개념에 한단계 접근하기 시작하게 되었습니다.

## - 프론트엔드의 토대, Ajax

기존에 웹 어플리케이션은 브라우저에서 폼을 채우고 이를 웹서버로 제출하면 하나의 요청으로 웹 서버는 요청 내용에 따라 데이터를 가공하여 새로운 웹페이지를 작성하고, 응답을 되돌려주는 방식이었습니다.  
이 방식은 HTML 코드를 다시 한번 전송받으면서 대역폭의 낭비로 금전적인 손실이 발생할 수도 있고, 상호 반응 서비스를 개발하기엔 어려운 부분도 있었습니다.   

이러한 문제의 해결 방안으로 Ajax가 등장하면서 이제 웹 어플리케이션은 비동기로 필요한 데이터만을 웹서버에 요청하여 클라이언트에서 데이터 처리를 할 수 있게 되었습니다.   
ajax가 생겨나면서 javascript는 자원을 그대로 활용해 동적인 화면들을 만들 수 있다는 점에서 다시 재평가를 받게 되었고, 이로 인해 지금의 프론트엔드 포지션이 대충 잡히기 시작했습니다.

## - 웹 표준, 웹 접근성, 크로스 브라우징

웹 종사자들이 다 같이 웹을 쓸 수 있도록 웹 표준을 만들게 되고, 그에 맞춰 HTML, CSS, Javascript를 표준에 맞게 개발하는 능력이 요구되었으며 이때 웹 표준과 함께 장애인들과 같은 경우에도 쉽게 웹을 사용할 수 있도록하는 웹 접근성에 대해서 강조되었습니다.   
또한 웹 표준이 생기면서 최대한 많은 종류의 웹 브라우저에서 정상적으로 작동하는 웹페이지를 만드는 방법론인 크로스브라우징 개념도 등장하였습니다.

이로써 웹디자인의 개념만 존재했던 HTML, CSS, Javascript는 더욱 더 전문성을 요구하는 시점이 오게 되었습니다.

## - jQuery의 등장

javascript가 웹 개발 도구의 대표격이 되면서 개발자들의 관심을 받게 되었고, 아직 열악했던 개발환경에서 대체재가 없었기 때문에 많은 개발자들의 손을 거쳐가며 라이브러리가 발전하였고,
사용법이 발전하게 되었습니다.   
이러한 노력의 결과로 2008년, 웹 표준 API를 모두 포함하면서 훨씬 더 쉬운 문법과 IE 크로스 브라우징, Ajax까지 해결한 괴물 jQuery가 탄생하면서 웹개발의 제2의 전성시대를 맞이하게 되었습니다.   

> 이때부터 조금씩 프론트엔드에서 웹 디자이너와 웹 개발자의 영역이 조금 더 분리하기 시작하였습니다.
> 웹 디자인의 결과물을 디자이너가 만들어 넘겨주면 개발자가 사용하여 서버와 이어주는 업무를 해주는 프로세스가 탄생하였습니다.

## - 크롬브라우저, V8 엔진

웹을 통한 OS와 웹 어플리케이션을 다음 목표로 삼았던 구글은 위에서 말했던 과제인 성능 개선에 초점을 두게 되었습니다.   
javascript의 성능을 개선한 V8엔진을 만들어내게 되고 오픈소스로 공유하였습니다.
그리고 그 당시 apple의 safari 오픈소스 웹브라우저 엔진인 webkit을 결합시킨 크롬 브라우저가 탄생하였습니다.   
크롬 브라우저는 매우 빠른 속도와 함께 풍부한 디버깅 환경을 제공함으로써 웹 개발자들의 희망이 되었습니다.

> 크롬이 IE를 완전히 넘어서기엔 한참의 시간이 걸렸습니다만, 웹이 하나의 OS가 되었고, 디버깅 환경으로 인해 웹 개발의 수준이 많이 상승하였습니다.

## - node, npm의 등장

javascript의 발전은 무궁무진이었습니다. 결국엔 javascript를 가지고도 서버 사이드 환경도 개발이 가능한 Node가 개발되었습니다.   
js 개발자 입장에선 새로운 언어의 학습도 필요가 없고, 반대로 서버개발자 역시 js를 활용해 겸사겸사 브라우저 개발도 할 수 있도록 영역이 넓어졌습니다.   
또한 node에서 module이라는 방법을 채택하게 되면서 js에도 module방식의 개념이 조금 더 보편화하게 됩니다.   

> module 개념은 필요한 함수들의 집합을 의미하고, 이는 파일을 쪼갬으로써 프로젝트의 규모를 키울 수 있는 계기가 되었습니다.


이외에도 프론트엔드의 발전과정에 수많은 기술들이 있었고 수많은 개념들이 존재하지만, 영향력이 컸고, 성장의 핵심 시점들을 정리해보았습니다.   

<br>

# 3. 프론트엔드 프레임워크

프론트엔드의 영역이 확장되며 전문적인 개발자들이 생겨나기 시작했습니다.   
또한 웹 개발에 있어 여전히 수많은 개발자들은 불편한 것들과 단점들을 줄이고, 최대한 빠르고 효율적인 웹페이지를 만들기 위해 노력했습니다.   
그러면서 등장한 것이 바로 프론트엔드 프레임워크입니다.

![frontend-framework](/assets/images/web-frontend-history/frontend-framework.png)

이 파트에선 모든 프론트엔드 프레임워크를 설명하기보단, 대표하는 프론트엔드 프레임워크인 3가지를 비교 분석하고, 새롭게 치고 올라오는 프론트 프레임워크에 대한 간단한 정보까지 소개드리겠습니다.   

## - SPA(Single Page Application)
    영어 그대로 페이지가 하나인 어플리케이션을 의미합니다.   
    페이지 이동 시 동적 데이터를 요청하고(API), 서버에서 받은 결과를 기본 틀만 담긴 View에 바로 뿌려 페이지를 동적으로 렌더링하는 것입니다.
    하나하나 화면 전체를 렌더링할 필요가 없기때문에 화면 이동이 빠르고, 화면에 필요한 부분의 데이터만 받아 렌더링하므로 처리 과정이 효율적입니다.   

    이 SPA를 만들기위해 사용하는 대표적인 프론트엔드 프레임워크가 바로 Angular, Vue, React입니다.


## - Angular, Vue, React 비교 분석

Angular와 React와 Vue의 특징을 간단히 비교 분석해보겠습니다.

### Angular

- 구글이라는 전세계적인 기업의 지원
- 가장 빠르게 릴리즈된 JS 프레임워크(2010년)
- 큰 커뮤니티
- 가장 체계적이고 잘 정리되어있는 문서
- 양방향 데이터 바인딩
  

### React

- gzip파일로 43KB 밖에 되지않는 크기와 빠른 속도, 많은 기능을 가짐
- 가상 DOM을 사용하여 필요한 node만을 re-rendering한다.
- 단방향 데이터 바인딩
- 서버 사이드 렌더링
- 번들링, 트리 쉐이킹 지원
- 작업의 출력을 모니터링하고 전체 개발 프로세스를 개선하는 것이 쉬움.
- MVC를 모두 구현하지 않음


### Vue

- 비교적 배우기 쉬움
- 효율적이고 빠르게 정교한 SPA를 생성 가능
- 압축 후 18KB 밖에 되지 않아 매우 빠르고, 많은 기능을 가짐
- 잘 정리된 공식문서
- 손쉬운 프로젝트 통합


![framework-compare](/assets/images/web-frontend-history/framework-compare.png)
 


### Svelte

스벨트는 2016년에 리치 해리스가 제작한 새로운 접근 방식의 프론트엔드 웹 프레임워크입니다.   
비교적 최근 업계에 등장한 언어 중 하나이며 최근 조사에 따르면 개발자의 86%가 스벨트를 사용하는 데 만족감을 표시했다고 합니다.   
다른 3가지 프레임워크에 비해 채택률은 떨어지나 사랑받는 프레임워크로도 선정되었다는 것을 보니 사용해본 개발자들의 입장에서 쓸만하다고 여겨지고 있는 것 같습니다.

스벨트는 아래 6가지 정도의 특징이 존재합니다.

- 뛰어난 성능을 보여준다.
    스벨트는 런타임이 아닌 빌드 타임에 어플리케이션 코드를 해석한다고 합니다.   

- 가상 DOM 비교 처리가 없다.
    리액트와 뷰의 성공에 영향을 준 가상 DOM을 스벨트는 포기하였습니다.   
    스벨트는 자동화된 프로세스로 진정한 반응형 언어라는 것을 보여주었습니다.
 
- 반응성이 뛰어나나다. 
    컴포넌트의 단계가 변경되면 DOM을 업데이트하지만, 업데이트 명령을 받았을 때 동작하며 그 전까지 발생한 모든 변경 사항이 한 번에 처리됩니다.   
    반응형 선언문은 각각 업데이트 발생 시 자동으로 로직을 재계산하고, 반응형 변수는 선언 시 변경이 발생할 때마다 연관단 다른 변수도 자동으로 변경됩니다.
     
- 많은 외부 라이브러리가 딱히 필요하지 않다.
    앱의 용량을 늘리지 않으면서도 시각 효과, 화면 전환, 애니메이션 등을 내장하고 있고 필요한 부분만을 불러오게 됩니다.

- 가볍다.
    압축된 버전의 스벨트는 1.6KB에 불과합니다.   
    크기가 작기 때문에 웹 어플리케이션에 더 빨리 로드되고 높은 응답성을 가졌습니다.

- 코드가 짧고, 간단하며, 가독성이 좋다.
    예제 코드를 보여드리겠습니다.

    ![svelte-code](/assets/images/web-frontend-history/svelte-code.png)

    script에서 일반적으로 변수 선언하고, 사진과 같이 중괄호로 묶으면 됩니다.

    굉장히 간단해보이죠??

하지만 단점도 존재합니다.

- 운영 주체가 기업이 아니라서 오래갈 수 있을 지 미지수이다.

- 커뮤니티 규모가 작아 플러그인, 통합, IDE에 대한 부족한 지원이 발생한다.

- 대규모 웹에는 적합하지 않다.

스벨트는 아직 대중화되기에는 갈 길이 먼 프레임워크이라고 생각합니다.   
하지만 프레임워크가 발전하고 변화하는 속도가 어마어마하게 빠르다는 것을 보면, 스벨트가 더욱 개선되서 기존 프레임워크를 대체하고, 개발자들은 더욱 쉽고 빠른 개발을 할 수도 있을 것이라고 생각합니다.