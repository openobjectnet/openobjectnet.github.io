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
authors: 서상균
---

# Intro
모든 개발자를 위한 HTTP 웹 기본 지식 강의를 수강하고 Http Header에 대한 내용을 자세히 정리해보겠습니다.

# HTTP Header
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


- 협상 (콘텐츠 네고시에이션)
    - 클라이언트가 선호하는 표현 요청입니다.
    - Accept : 클라이언트가 선호하는 미디어 타입입니다.
    - 협상 헤더의 경우 요청시에만 사용됩니다.
    - Accept-Charset : 클라이언트가 선호하는 문자 인코딩입니다.
    - Accept-Language : 클라이언트가 선호하는 자연 언어입니다.

![그림](https://velog.velcdn.com/images%2Fleemember%2Fpost%2F5410d7b0-970e-4f37-b301-976c06e8df3e%2Fzvzv.JPG)

  - 다중 언어를 지원하는 서버에서 기본 타입의 언어를 en으로 지정하고 있다면, 클라이언트의 요청 헤더에 Accept와 관련한 정보를 담고 있지 않을 경우 다중 언어 지원 서버에서는 en으로 구성된 내용으로 응답을 해줍니다.

  - Accept-Language:ko를 헤더에 담아 클라이언트가 다중 언어 서버에 요청을 한다면, 서버에서는 en을 기본으로 지원하고 있지만 해당 요청 헤더 사항을 보고 ko로 구성된 내용을 응답 해줍니다.
    
<br>


