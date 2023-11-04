---
title: "Http Header"
search: false
categories:
  - HTTP
toc: true
last_modified_at:
tags:
  - HTTP
  - HEADER
author: 서상균
---

# Intro
모든 개발자를 위한 HTTP 웹 기본 지식 강의를 수강하고 Http Header에 대한 내용을 자세히 정리해보겠습니다.

---

# HTTP Header

<br>

- 정의
    - HTTP 헤더는 클라이언트와 서버가 요청 또는 응답으로 부가적인 정보를 전송할 수 있도록 합니다.
  
- 용도
    - HTTP 전송에 필요한 모든 부가 정보를 포함
    - 메시지 바디의 내용, 메시지 바디의 크기, 압축 방식, 인증, 요청 클라이언트의 정보, 서버 정보, 캐시 관리와 관련한 정보 등을 포함
    - 전달할 데이터를 추가로 헤더에 붙여 전달이 가능합니다.

![그림](https://velog.velcdn.com/images/msung99/post/09663656-54a5-4138-9ffc-43ac28a694b5/image.png)

<br>

- 구성
    - Message Body (Payload) : 메시지 본문을 통해 표현 데이터를 전달합니다.
    - Representation (표현) : 요청 또는 응답에서 전달할 실제 데이터
      - 표현 : 표현 메타데이터 (표현 헤더) + 표현 데이터
      - 표현 헤더 : 표현 데이터를 해석할 수 있는 정보를 제공합니다.
      - 표현 데이터 : 요청 및 응답의 메시지 본문을 뜻합니다. 

- 표현 헤더
    - Content-Type : 표현 데이터 형식입니다.
    - Content-Encoding : 표현 데이터의 압축 방식입니다.
    - Content-Language : 표현 데이터의 자연 언어입니다.
    - Content-Length : 표현 데이터의 길이입니다.

![그림](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FFVq1L%2Fbtrvvbd0Q08%2FBMm9ge8uabDiTq5iAwD7Z0%2Fimg.png)

  - HTTP/1.1 : 현재 웹 페이지의 HTTP 버전을 뜻합니다.
  - 200 OK : HTTP의 상태코드를 뜻합니다. (200의 경우 요청이 정상 처리 되었음을 뜻합니다.)
  - Content-Type 과 Content-Length는 표현 헤더입니다.
  - html 태그와 JSON 형태의 데이터는 표현 데이터입니다.

<br>

- Content-Type
    - 표현데이터를 보낼 타입을 정의하거나 문자 인코딩을 정의합니다.
    - text/html; charset=utf-8 : 데이터의 text형식은 html 이고 utf-8 방식으로 인코딩 한다는 의미입니다.
    - application/json : 파일 형태가 JSON 임을 뜻합니다.
    - image/png : 이미지가 png 형태임을 뜻합니다.

- Content-Encoding
    - 데이터를 압축하는데 사용하는 방식을 뜻합니다.
    - 데이터를 읽는 쪽에서 해당 타입을 참고해 압축 해제를 할 수 있습니다.
    - identity 타입의 경우 압축을 하지 않는다는 의미 입니다.

- Content-Language
    - ko
    - en
    - en-US

<br>

# 협상 (콘텐츠 네고시에이션)

<br>

    - 클라이언트가 선호하는 표현 요청입니다.
    - Accept : 클라이언트가 선호하는 미디어 타입입니다.
    - 협상 헤더의 경우 요청시에만 사용됩니다.
    - Accept-Charset : 클라이언트가 선호하는 문자 인코딩입니다.
    - Accept-Language : 클라이언트가 선호하는 자연 언어입니다.

![그림](https://velog.velcdn.com/images%2Fleemember%2Fpost%2F5410d7b0-970e-4f37-b301-976c06e8df3e%2Fzvzv.JPG)

  - 다중 언어를 지원하는 서버에서 기본 타입의 언어를 en으로 지정하고 있다면, 클라이언트의 요청 헤더에 Accept와 관련한 정보를 담고 있지 않을 경우 다중 언어 지원 서버에서는 en으로 구성된 내용으로 응답을 해줍니다.

![그림](https://velog.velcdn.com/images/pp8817/post/cb28b1ed-2666-41c0-b712-318a445a1ef6/image.png)

  - Accept-Language:ko를 헤더에 담아 클라이언트가 다중 언어 서버에 요청을 한다면, 서버에서는 en을 기본으로 지원하고 있지만 해당 요청 헤더 사항을 보고 ko로 구성된 내용을 응답 해줍니다.
    
![그림](https://velog.velcdn.com/images/pp8817/post/e4325a9e-61a8-49fd-b8bc-d4b0b1bc6965/image.png)

  - 클라이언트가 Accept-Language:ko 를 헤더에 담아 요청했지만 다중 언어를 지원하는 서버에서 ko를 지원하지 않는다면, de(독일어) 보다는 en(영어)를 지원해주기를 바랄 것입니다. 
  - 이러한 경우에 의해 협상의 우선순위가 필요하게 됩니다.

<br>

# 우선순위

<br>

### 1. Quality Values (q)
- Quality Values (q) 값을 사용합니다.
- 0 ~ 1 의 값을 가지며, 1에 가까울 수록 높은 우선순위를 가집니다.
- 생략할 경우 1의 값을 가지게 됩니다.

<br>

![그림](https://velog.velcdn.com/images/pp8817/post/33469337-eee9-425b-9075-c0fc642b9f9f/image.png)

- 우선순위

    1. ko-KR
    2. ko;q=0.9
    3. en-US;q=0.8
    4. en;q=0.7 

<br>

### 2. Quality Values (q)
- 구체적으로 명시할 수록 더 높은 우선순위를 가집니다.

<br>

```
Accept: text/*, text/plain, text/plain;format=flowed, */*
```

- 우선순위

    1. text/plain;format=flowed
    2. text/plain
    3. text/*
    4. `*/*`

<br>

### 3. Quality Values (q)
- 구체적인 것을 기준으로 미디어 타입을 맞춥니다.

```
Accept: text/*;q=0.3, text/html;q=0.7, text/html;level=1, text/html;level=2;q=0.4, */*;q=0.5
```

<br>

![그림](https://raw.githubusercontent.com/smpark1020/tistory/master/Web/%5BHTTP%20%EC%9B%B9%20%EA%B8%B0%EB%B3%B8%20%EC%A7%80%EC%8B%9D%5D%20%EC%BD%98%ED%85%90%EC%B8%A0%20%ED%98%91%EC%83%81/1.PNG)

- 우선순위

  - 서버에서 text/html;level=1의 미디어 타입을 제공하므로 해당 협상 헤더의 우선순위는 1의 값을 가집니다.
  - 서버에서 text/plain의 미디어 타입을 제공하지만, 협상 헤더에 존재하지 않으므로 text/*과 매칭되어 0.3의 q 값을 가집니다.

<br>

# 전송 방식

<br>

### 단순 전송 _Content-Length_

- 클라이언트가 서버에 요청을 하면 서버는 Content-Length를 포함해서 응답 데이터의 길이를 알 수 있도록 합니다.

### 압축 전송 _Content-Encoding_

- 서버에서 응답을 보낼 시 메시지 바디 부분을 압축해서 보냅니다.
- Content-Encoding에 압축 방식 정보를 헤더에 담아 보냅니다.

### 분할 전송 _Transfer-Encoding_

![그림](https://velog.velcdn.com/images/alkwen0996/post/47534fe0-648e-405b-8c51-0429d353f79f/image.png)

- 데이터의 용량이 너무 클 경우 데이터를 분할 해서 보냅니다.
- Transfer-Encoding: chunked를 헤더에 담아 보냅니다.
- 분할 전송 방식을 선택할 경우 Content-Length 정보는 헤더에 담을 수 없습니다.

### 범위 전송 _Range_, _Content-Range_

![그림](https://velog.velcdn.com/images/alkwen0996/post/a8f337db-cbea-4446-934d-bd77e6e03422/image.png)

- 특정 범위를 지정해서 요청에 대한 응답을 받는 전송 방식입니다.
- 클라이언트의 요청 헤더에는 Range 키워드로 범위를 지정해서 요청합니다.
- 서버의 응답 헤더에는 Content-Range 키워드로 응답 데이터의 범위와 전체 데이터의 크기를 나타냅니다.

<br>

# Referer

- 현재 표시하는 웹 페이지가 어떤 웹 페이지에서 요청되었는지 알 수 있는 정보입니다.

### Referer의 중요성

1. 사이트 유입 분석
    - 어떤 외부 사이트나 광고 링크를 통해서 사용자가 유입되는지 분석하여 마케팅 효과를 측정하거나 개선 방향을 설정 할 수 있습니다.
2. 보안
    - 특정 기능이나 페이지에 대한 접근을 통제할 수 있습니다.
    - "상품 구매" 기능을 "상품 상세페이지" 에서만 접근할 수 있도록 설정할 수 있습니다.

### Referer 제어
```html
<a href="https://example.com" rel="noreferrer">링크<a>
```

 - "noreferrer" 키워드를 통해 referer헤더를 제거 할 수 있습니다.

