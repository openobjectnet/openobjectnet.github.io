---
title: "김영한 SpringMVC 기초(web, was, servler, http request&response)"
search: false
categories:
  - springMVC
toc: true
tags:
  - web
  - was
  - servlet
  - request
  - response
author: 이승효
---

# Intro
안녕하세요. 오픈오브젝트 이승효입니다.<br>
인프런 스프링 MVC 강의를 듣고 알게 된 점을 공유합니다.

# 웹 서버, 웹 애플리케이션 서버

### 웹 서버(Web Server)

- Http 기반으로 동작
- 정적 리소스 제공, 기타 부가기능
- 정적(파일) HTML, CSS, JS, 이미지, 영상
- 예) NGINX, APACHE

### 웹 애플리케이션 서버(WAS)

- Http 기반으로 동작
- 웹 서버 기능 포함 + (정적 리소스 제공 가능)
- 프로그램 코드를 실행해서 애플리케이션 로직 수행
- 예) 톰캣, Undertow

### 웹 시스템 구성

웹 시스템 보통 정적 리소스만을 제공하는 웹 서버, 애플리케이션 로직이 동작하는 WAS 서버, DB로 구성된다.

### 서버를 나눠 놓으면 장점

#### 효율적인 리소스 관리

- 정적 리소스가 많이 사용되면 WEB 서버를 증설
- 애플리케이션 리소스가 많이 사용되면 WAS를 증설

#### 효율적인 서버 관리

- 정적 리소스를 제공하는 WEB 서버는 잘 죽지않고
- 애플리케이션 로직이 동작하는 WAS 서버는 잘 죽음
- WAS, DB 장애 시 WEB 서버가 오류 화면 제공 가능

# 서블릿

서블릿은 개발자가 비즈니스 로직에만 집중해 코딩할 수 있도록 웹을 만들기 위해 필요한 기술들을 자동화 시켜준다.

- urlPatterns의 URL이 호출되면 서블릿 코드가 실행
- HttpServlet을 상속받아 서블릿클래스를 구현
- HTTP 정보를 제공하는 HttpServletRequest, HttpServletResponse를 편하게 사용
- 개발자는 HTTP 스펙을 매우 편리하게 사용

#### HTTP란?

텍스트 기반의 통신 규약으로 인터넷에서 데이터를 주고받을 수 있는 프로토콜이다. 이렇게 규약을 정해두었기 때문에 모든 프로그램이 이 규약에 맞춰 개발해서 서로 정보를 교환할 수 있게 되었다.

### HTTP 요청, 응답 흐름

#### HTTP 요청시

- WAS는 Request, Response 객체를 새로 만들어서 서블릿 객체 호출
- 개발자는 Request 객체에서 HTTP 요청 정보를 편리하게 꺼내서 사용
- 개발자는 Response 객체에 HTTP 응답 정보를 편리하게 입력
- WAS는 Response 객체에 담겨있는 내용으로 HTTP 응답 정보를 생성

### 서블릿 컨테이너

- 톰캣처럼 서블릿을 지원하는 WAS를 서블릿 컨테이너라고 함
- 서블릿 컨테이너는 서블릿 객체를 생성, 초기화, 호출, 종료하는 생명주기 관리
- 서블릿 객체는 싱글톤으로 관리
- 고객의 요청이 올 때 마다 계속 객체를 생성하는 것은 비효율
- 최초 로딩 시점에 서블릿 객체를 미리 만들어두고 재활용
- 모든 고객 요청은 동일한 서블릿 객체 인스턴스에 접근
- 공유 변수 사용 주의
- 서블릿 컨테이너 종료시 함께 종료
- JSP도 서블릿으로 변환 되어서 사용
- 동시 요청을 위한 멀티 쓰레드 처리 지원

# 쓰레드

- 애플리케이션 코드를 하나하나 순차적으로 실행하는 것은 쓰레드
- 자바 메인 메서드를 처음 실행하면 main이라는 이름의 쓰레드가 실행
- 쓰레드가 없다면 자바 애플리케이션 실행이 불가능
- 쓰레드는 한번에 하나의 코드 라인만 수행
- 동시 처리가 필요하면 쓰레드를 추가로 생성

### 쓰레드 생성 방식

#### 요청 마다 쓰레드 생성

##### 장점

- 동시 요청을 처리할 수 있다.
- 리소스(CPU, 메모리)가 허용할 때 까지 처리가능
- 하나의 쓰레드가 지연 되어도, 나머지 쓰레드는 정상 동작한다.

##### 단점

- 쓰레드는 생성 비용은 매우 비싸다.
- 고객의 요청이 올 때 마다 쓰레드를 생성하면, 응답 속도가 늦어진다.
- 쓰레드는 컨텍스트 스위칭 비용이 발생한다.
- 쓰레드 생성에 제한이 없다.
- 고객 요청이 너무 많이 오면, CPU, 메모리 임계점을 넘어서 서버가 죽을 수 있다

### 쓰레드풀

#### 요청 마다 쓰레드 생성의 단점 보완

##### 특징

- 필요한 쓰레드를 쓰레드 풀에 보관하고 관리한다
- 쓰레드 풀에 생성 가능한 쓰레드의 최대치를 관리한다. 톰캣은 최대 200개 기본 설정 (변경 가능)

##### 사용

- 쓰레드가 필요하면, 이미 생성되어 있는 쓰레드를 쓰레드 풀에서 꺼내서 사용한다.
- 사용을 종료하면 쓰레드 풀에 해당 쓰레드를 반납한다.
- 최대 쓰레드가 모두 사용중이어서 쓰레드 풀에 쓰레드가 없으면 기다리는 요청은 거절하거나 특정 숫자만큼만 대기하도록 설정할 수 있다.

#### 쓰레드 풀의 적정 숫자

- 애플리케이션 로직의 복잡도, CPU, 메모리, IO 리소스 상황에 따라 모두 다름
- 성능 테스트
  - 최대한 실제 서비스와 유사하게 성능 테스트 시도
  - 툴: 아파치 ab, 제이미터, nGrinder

### WAS의 멀티 쓰레드 지원

- 멀티 쓰레드에 대한 부분은 WAS가 처리
- 개발자가 멀티 쓰레드 관련 코드를 신경쓰지 않아도 됨
- 개발자는 마치 싱글 쓰레드 프로그래밍을 하듯이 편리하게 소스 코드를 개발
- 멀티 쓰레드 환경이므로 싱글톤 객체(서블릿, 스프링 빈)는 주의해서 사용

# SSR, CSR

##### SSR - 서버 사이드 렌더링

- HTML 최종 결과를 서버에서 만들어서 웹 브라우저에 전달
- 주로 정적인 화면에 사용
- 관련기술: JSP, 타임리프 -> 백엔드 개발자

##### CSR - 클라이언트 사이드 렌더링

- HTML 결과를 자바스크립트를 사용해 웹 브라우저에서 동적으로 생성해서 적용
- 주로 동적인 화면에 사용, 웹 환경을 마치 앱 처럼 필요한 부분부분 변경할 수 있음
- 예) 구글 지도, Gmail, 구글 캘린더
- 관련기술: React, Vue.js -> 웹 프론트엔드 개발자

##### 참고

- React, Vue.js를 CSR + SSR 동시에 지원하는 웹 프레임워크도 있음
- SSR을 사용하더라도, 자바스크립트를 사용해서 화면 일부를 동적으로 변경 가능

<hr>

# 서블릿 프로젝트 생성

##### 사전 준비물

- Java 11 설치
- IDE: IntelliJ 또는 Eclipse 설치

이클립스는 사용해 보았기 때문에 IntelliJ 무료 버전으로 진행해 보았습니다.
<br>

빌드도구또한 메이븐을 사용해 보았기 때문에 그래들로 진행해 보았습니다.

프로젝트 생성은 https://start.spring.io/ 했으며 스프링 부트, 자바, 그래들 프로젝트로 생성했습니다.

프로젝트를 생성하게되면 메이븐 프로젝트는 POM.XML이 생성되어 버전정보, dependencies 주입이 가능한데<br>
그래들 프로젝트는 build.gradle 파일에서 주입이 가능합니다.

##### build.gradle

```gradle
plugins {
    id 'org.springframework.boot' version '2.4.3'
    id 'io.spring.dependency-management' version '1.0.11.RELEASE'
    id 'java'
    id 'war'
}
    group = 'hello'
    version = '0.0.1-SNAPSHOT'
    sourceCompatibility = '11'
    configurations {
    compileOnly {
        extendsFrom annotationProcessor
    }
}
repositories {
    mavenCentral()
}
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web'
    compileOnly 'org.projectlombok:lombok'
    annotationProcessor 'org.projectlombok:lombok'
    providedRuntime 'org.springframework.boot:spring-boot-starter-tomcat'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
}
test {
    useJUnitPlatform()
}
```

### 스프링 부트 서블릿 환경 구성

스프링 부트는 서블릿을 직접 등록해서 사용할 수 있도록 @ServletComponentScan 을 지원한다. 다음과
같이 추가하자

```java
package hello.servlet;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.web.servlet.ServletComponentScan;

@ServletComponentScan //서블릿 자동 등록
@SpringBootApplication
public class ServletApplication {

    public static void main(String[] args) {
        SpringApplication.run(ServletApplication.class, args);
    }
}
```

### 서블릿 등록하기

```java
@WebServlet(name = "helloServlet", urlPatterns = "/hello")
public class HelloServlet extends HttpServlet {

    @Override
    protected void service(HttpServletRequest req, HttpServletResponse res) throws ServletException, IOException {

        System.out.println("HelloServlet.service");
        System.out.println("req = " + req);
        System.out.println("res = " + res);

        String username = req.getParameter("username");
        System.out.println("username = " + username);

        res.setContentType("text/plain");
        res.setCharacterEncoding("utf-8");
        res.getWriter().write("<h1>hello</h1>" + username);

    }
}
```

@WebServlet 서블릿 애노테이션

- name: 서블릿 이름
- urlPatterns: URL 매핑

HTTP 요청을 통해 매핑된 <U>URL이 호출</U>되면 서블릿 컨테이너는 다음 메서드를 실행한다.
```java
protected void service(HttpServletRequest request, HttpServletResponse response)
```
localhost:8080/hello?username=world 호출 시
콘솔 실행결과
```java
HelloServlet.service
request = org.apache.catalina.connector.RequestFacade@5e4e72
response = org.apache.catalina.connector.ResponseFacade@37d112b6
username = world
```
서블릿이 잘 동작 되는 것을 확인하였고, 브라우저에 응답이 잘 나오는 것도 확인하였다.

##### HttpServletRequest 역할

HTTP 요청 메시지를 개발자가 직접 파싱해서 사용해도 되지만, 매우 불편할 것이다. 서블릿은 개발자가
HTTP 요청 메시지를 편리하게 사용할 수 있도록 개발자 대신에 HTTP 요청 메시지를 파싱한다. 그리고 그
결과를 `HttpServletRequest` 객체에 담아서 제공한다

###### HTTP 요청 메시지
```
    POST /save HTTP/1.1
    Host: localhost:8080
    Content-Type: application/x-www-form-urlencoded

    username=kim&age=20
```
- START LINE

  - HTTP 메소드
  - URL
  - 쿼리 스트링
  - 스키마, 프로토콜

- 헤더

  - 헤더 조회

- 바디
  - form 파라미터 형식 조회
  - message body 데이터 직접 조회

HttpServletRequest 객체는 추가로 여러가지 부가기능도 함께 제공한다.

##### 임시 저장소 기능

- 해당 HTTP 요청이 시작부터 끝날 때 까지 유지되는 임시 저장소 기능
  - 저장: `request.setAttribute(name, value)`
  - 조회: `request.getAttribute(name)`

##### 세션 관리 기능

- `request.getSession(create: true)`

##### start-line 정보

```java
//start line 정보
private void printStartLine(HttpServletRequest request) {
    System.out.println("--- REQUEST-LINE - start ---");
    System.out.println("request.getMethod() = " + request.getMethod()); //GET
    System.out.println("request.getProtocal() = " + request.getProtocol()); //HTTP/1.1
    System.out.println("request.getScheme() = " + request.getScheme()); //http
    // http://localhost:8080/request-header
    System.out.println("request.getRequestURL() = " + request.getRequestURL());
    // /request-header
    System.out.println("request.getRequestURI() = " + request.getRequestURI());
    // username=world
    System.out.println("request.getQueryString() = " + request.getQueryString());
    System.out.println("request.isSecure() = " + request.isSecure()); //https 사용 유무
    System.out.println("--- REQUEST-LINE - end ---");
    System.out.println();
}
```

##### 결과

```
--- REQUEST-LINE - start ---
request.getMethod() = GET
request.getProtocal() = HTTP/1.1
request.getScheme() = http
request.getRequestURL() = http://localhost:8080/request-header
request.getRequestURI() = /request-header
request.getQueryString() = username=world
request.isSecure() = false
--- REQUEST-LINE - end ---
```

##### 헤더 정보

```java
//Header 모든 정보
private void printHeaders(HttpServletRequest request) {
    System.out.println("--- Headers - start ---");
/*
    Enumeration<String> headerNames = request.getHeaderNames();
    while (headerNames.hasMoreElements()) {
        String headerName = headerNames.nextElement();
        System.out.println(headerName + ": " + request.getHeader(headerName));
    }
*/
    request.getHeaderNames().asIterator()
        .forEachRemaining(headerName -> System.out.println(headerName + ":
" + request.getHeader(headerName)));
    System.out.println("--- Headers - end ---");
    System.out.println();
}
```

##### 결과

```
--- Headers - start ---
host: localhost:8080
connection: keep-alive
cache-control: max-age=0
sec-ch-ua: "Chromium";v="88", "Google Chrome";v="88", ";Not A Brand";v="99"
sec-ch-ua-mobile: ?0
upgrade-insecure-requests: 1
user-agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 11_2_0) AppleWebKit/537.36 
(KHTML, like Gecko) Chrome/88.0.4324.150 Safari/537.36
accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/
webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
sec-fetch-site: none
sec-fetch-mode: navigate
sec-fetch-user: ?1
sec-fetch-dest: document
accept-encoding: gzip, deflate, br
accept-language: ko,en-US;q=0.9,en;q=0.8,ko-KR;q=0.7
--- Headers - end ---
```

이 외의 쿠키 정보, 기타 정보 들도 받을 수 있다.

### HTTP 요청 데이터 - 개요

HTTP 요청 메시지를 통해 클라이언트에서 서버로 데이터를 전달하는 방법

#### 주로 3가지 방법을 사용한다.

- GET - 쿼리 파라미터

  - /url?username=hello&age=20
  - 메시지 바디 없이, URL의 쿼리 파라미터에 데이터를 포함해서 전달
  - 예) 검색, 필터, 페이징등에서 많이 사용하는 방식

- POST - HTML Form

  - content-type: application/x-www-form-urlencoded
  - 메시지 바디에 쿼리 파리미터 형식으로 전달 username=hello&age=20
  - 예) 회원 가입, 상품 주문, HTML Form 사용

- HTTP message body에 데이터를 직접 담아서 요청

  - HTTP API에서 주로 사용, JSON, XML, TEXT

- 데이터 형식은 주로 JSON 사용

  - POST, PUT, PATCH

#### HTTP 요청 데이터 - GET 쿼리 파라미터

- 메시지 바디 없이, URL의 쿼리 파라미터를 사용해 전달된다.
  예) 검색, 필터, 페이징등에서 많이 사용하는 방식

쿼리 파라미터는 URL에 다음과 같이 ? 를 시작으로 보낼 수 있다. 추가 파라미터는 & 로 구분하면 된다.

- `http://localhost:8080/request-param?username=hello&age=20`

서버에서는 HttpServletRequest 가 제공하는 다음 메서드를 통해 쿼리 파라미터를 편리하게 조회할 수
있다.

##### 쿼리 파라미터 조회 메서드

```
String username = request.getParameter("username"); //단일 파라미터 조회
Enumeration<String> parameterNames = request.getParameterNames(); //파라미터 이름들 모두 조회
Map<String, String[]> parameterMap = request.getParameterMap(); //파라미터를 Map 으로 조회
String[] usernames = request.getParameterValues("username"); //복수 파라미터 조회
```

##### 복수 파라미터에서 단일 파라미터 조회

username=hello&username=kim 과 같이 파라미터 이름은 하나인데, 값이 중복이면<br>
`request.getParameterValues()` 를 사용해야 한다.<br>
중복일 때 request.getParameter() 를 사용하면 request.getParameterValues() 의 첫 번째 값을 반환한다.

#### HTTP 요청 데이터 - POST HTML Form

는 HTML의 Form을 사용해서 클라이언트에서 서버로 데이터를 전송하는방식이며<br>
주로 회원 가입, 상품 주문 등에서 사용하는 방식이다.

##### 특징

- content-type: application/x-www-form-urlencoded 로 들어온다
- 메시지 바디에 쿼리 파리미터 형식으로 데이터를 전달한다. username=hello&age=20

클라이언트(웹 브라우저) 입장에서는 GET, POST 방식에 차이가 있지만, 서버 입장에서는 둘의 형식이 동일하므로
`request.getParameter()` 로 편리하게 구분없이 조회할 수 있다

#### HTTP 요청 데이터 - API 메시지 바디 - 단순 텍스트

- HTTP message body에 데이터를 직접 담아서 요청
  - HTTP API에서 주로 사용, JSON, XML, TEXT
  - 데이터 형식은 주로 JSON 사용
  - POST, PUT, PATCH

##### RequestBodyStringServlet

```java

package hello.servlet.basic.request;
import org.springframework.util.StreamUtils;
import javax.servlet.ServletException;
import javax.servlet.ServletInputStream;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.nio.charset.StandardCharsets;

@WebServlet(name = "requestBodyStringServlet", urlPatterns = "/request-bodystring")
public class RequestBodyStringServlet extends HttpServlet {
    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response)
        throws ServletException, IOException {

            ServletInputStream inputStream = request.getInputStream();
            String messageBody = StreamUtils.copyToString(inputStream, StandardCharsets.UTF_8);
            System.out.println("messageBody = " + messageBody);
            response.getWriter().write("ok");
        }
}
```

##### 참고

inputStream은 byte 코드를 반환한다. byte 코드를 우리가 읽을 수 있는 문자(String)로 보려면 문자표
(Charset)를 지정해주어야 한다. 여기서는 UTF_8 Charset을 지정해주었다.

##### 문자 전송

- POST http://localhost:8080/request-body-string
- content-type: text/plain
- message body: hello
- 결과: messageBody = hello

#### HTTP 요청 데이터 - API 메시지 바디 - JSON

HTTP API에서 주로 사용하는 JSON 형식으로 데이터를 전달해본다.

##### JSON 형식 전송

- POST http://localhost:8080/request-body-json
- content-type: application/json
- message body: {"username": "hello", "age": 20}
- 결과: messageBody = {"username": "hello", "age": 20}

JSON 형식으로 파싱할 수 있게 객체를 하나 생성한다.

```java
import lombok.Setter;

@Getter @Setter
public class HelloData {
    private String username;
    private int age;
}
```

```java
package hello.servlet.basic.request;

import com.fasterxml.jackson.databind.ObjectMapper;
import hello.servlet.basic.HelloData;
import org.springframework.util.StreamUtils;

import javax.servlet.ServletException;
import javax.servlet.ServletInputStream;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.nio.charset.StandardCharsets;

@WebServlet(name = "requestBodyJsonServlet", urlPatterns = "/request-json-body")
public class RequestBodyJsonServlet extends HttpServlet {

    private ObjectMapper objectMapper = new ObjectMapper();

    @Override
    protected void service(HttpServletRequest req, HttpServletResponse res) throws ServletException, IOException {
        ServletInputStream inputStream = req.getInputStream();
        String messageBody = StreamUtils.copyToString(inputStream, StandardCharsets.UTF_8);

        System.out.println("messageBody = " + messageBody);

        HelloData helloData = objectMapper.readValue(messageBody, HelloData.class);

        System.out.println("helloData.getUsername = " + helloData.getUsername());
        System.out.println("helloData.getAge = " + helloData.getAge());

        res.getWriter().write("ok");

    }
}
```

Postman으로 실행해보자.

- POST http://localhost:8080/request-body-json
- content-type: application/json (Body raw, 가장 오른쪽에서 JSON 선택)
- message body: {"username": "hello", "age": 20}

##### 출력결과

```
messageBody={"username": "hello", "age": 20}
data.username=hello
data.age=20
```

##### 참고

JSON 결과를 파싱해서 사용할 수 있는 자바 객체로 변환하려면 Jackson, Gson 같은 JSON 변환<br>
라이브러리를 추가해서 사용해야 한다. 스프링 부트로 Spring MVC를 선택하면 기본으로 Jackson<br>
라이브러리( ObjectMapper )를 함께 제공한다.

### HttpServletResponse - 기본 사용법

#### HttpServletResponse 역할

##### HTTP 응답 메시지 생성

- HTTP 응답코드 지정
- 헤더 생성
- 바디 생성

#### HTTP 응답 데이터 - 단순 텍스트, HTML

HTTP 응답 메시지는 주로 다음 내용을 담아서 전달한다.

- 단순 텍스트 응답
- HTML 응답
- HTTP API - MessageBody JSON 응답

##### HttpServletResponse - HTML 응답

```java
package hello.servlet.basic.response;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;

@WebServlet(name = "responseHtmlServlet", urlPatterns = "/response-html")
public class ResponseHtmlServlet extends HttpServlet {

    @Override
    protected void service(HttpServletRequest req, HttpServletResponse res) throws ServletException, IOException {
        //Content-Type: text/html;charset=utf-8
        res.setContentType("text/html");
        res.setCharacterEncoding("utf-8");

        PrintWriter writer = res.getWriter();

        writer.println("<html>");

        writer.println("<body>");
        writer.println("<div>안녕</div>");
        writer.println("</body>");
        writer.println("</html>");
    }
}

```

HTTP 응답으로 HTML을 반환할 때는 content-type을 text/html 로 지정해야 한다.

##### HTTP 응답 데이터 - API JSON

```java
package hello.servlet.basic.response;

import com.fasterxml.jackson.databind.ObjectMapper;
import hello.servlet.basic.HelloData;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@WebServlet(name = "responseJsonServlet", urlPatterns = "/response-json")
public class ResponseJsonServlet extends HttpServlet {

    private ObjectMapper objectMapper = new ObjectMapper();

    @Override
    protected void service(HttpServletRequest req, HttpServletResponse res) throws ServletException, IOException {
        //Content-Type: application/json
        res.setContentType("application/json");
        res.setCharacterEncoding("utf-8");

        HelloData helloData = new HelloData();
        helloData.setUsername("kim");
        helloData.setAge(20);

        //{"username":"kim, "age":20}
        String result = objectMapper.writeValueAsString(helloData);
        res.getWriter().write(result);
    }
}
```

HTTP 응답으로 JSON을 반환할 때는 content-type을 application/json 로 지정해야 한다.
Jackson 라이브러리가 제공하는 objectMapper.writeValueAsString() 를 사용하면 객체를 JSON
문자로 변경할 수 있다
