---
title: "개인 Study Project(Home Server)"
search: false
categories:
  - study
toc: true
tags:
  - springBoot
  - vue
author: 이승효
---

# Intro
안녕하세요? 이승효 사원입니다.<br>
Home서버를 구축하며 알게된 점을 공유합니다.


# 1. Home서버 구축

## 공유기
현재 집에서는 공유기를 사용해 인터넷 통신을 하고 있습니다.<br>
공유기는 공유기에 연결되는 모든 기기들이 인터넷을 사용할 수 있게 해주며 공인 아이피를 하나 할당받습니다.

* 인터넷을 사용하기 위해서는 공인 아이피가 필요합니다.
* 공유기는 연결된 모든 기기에 사설 아이피 할당함
* 공유기에 연결된 모든 기기는 인터넷에 접근하기 위해서 반드시 공유기를 거쳐야 함

![router](https://cdn.lamanus.kr/wp-content/uploads/2019/05/29004625/59-2-2048x1152.png)


## 포트 포워딩
이제, 컴퓨터 1에 웹서버를 80번 포트로 열었다고 생각해 봅시다. 
인터넷을 통해 다른 사람이 컴퓨터 1의 웹서버에 접근하기 위해서는 사설 아이피가 아닌 공인 아이피가 필요합니다.
그렇다고 http://12.34.56.78:80 공유기 공인 아이피로 직접 접근을 하게 되면 연결은 공유기에 도달하면 끝나됩니다.
컴퓨터 1, 2, 3 중 어느 곳으로 보내줘야하는지 판단을 하지 못하기 때문인데
그렇기 때문에 공인 아이피로 들어오는 80번 포트의 연결을 컴퓨터 1의 80번 포트로 연결되게 설정하는 것을 포트 포워딩이라고 부릅니다.


![portforwarding](https://cdn.lamanus.kr/wp-content/uploads/2019/05/29004638/59-3-2048x1152.png)


## 포트 포워딩 설정
포트포워딩 설정은 대부분 공유기 설정에서 찾아볼 수 있으며 공유기마다 다르기 때문에
쓰던 공유기를 확인 후 메뉴얼대로 진행해야 합니다.

kt 공유기의 경우
http://homehub.olleh.com 접속 로그인 후


### 아이피 주소 확인
시작-실행 혹은 시작 버튼 옆의 windows 검색을 누른 후 "cmd"라고 입력
ipconfig 입력 후 엔터를 치면 IPv4 주소가 나오는 데 이 주소를 기억해두기


### 포트 포워딩 설정
장치 설정 - 트래픽 관리
기억해둔 IPv4주소 입력 후
내부 포트는 서버를 열은 포트, 외부 포트는 외부에서 접근할 때 이용할 포트를 적습니다.
보통은 내부, 외부 포트를 같게 설정합니다.
![portforwarding](https://mblogthumb-phinf.pstatic.net/MjAxODAzMDlfMTE4/MDAxNTIwNjA0ODUzMzMx.mYbxoy69pkqDSc4EektvipPfCfCBHeryTA-sstHwbEgg.epx03zmdDbaJS8f0Fi7Fxvb9EGqNYQzfkO8VhOvCbvYg.JPEG.zetezz/K-001.jpg?type=w800)


## DDNS?
여기까지 설정 후 외부에서 접속을 조금 더 편하게 하기 위해 도메인 네임을 설정하려고 했는데
찾아보니 유동 아이피는 도메인 네임에 아이피를 할당해도 아아피가 변경되면 사용 할 수 없게 된다고 한다.
집에서 사용하는 대부분 회선은 유동 아이피로 사용되기 때문에 홈 서버를 편하게 사용하기 위해선 고정 아이피를 할당받아야 한다.

그래서 찾아보던 중 고정 아이피를 할당해줘도 되지만 유동 아이피로도 사용할 수 있는 DDNS(Dynamic Domain Name Service)를 알게 되었고 적용해 보았다.
DuckDns라는 DDNS 서비스를 사용해 보았다. 
![duckdns](/assets/images/homeServerPost/duckdns.PNG)

### 도메인 세팅
회원 가입 후 도메인 네임을 입력 후 add domain 버튼을 누르면 도메인이 생성되고 current ip 란에 아이피 주소를 입력 후 update ip를 누르면 셋팅이 된다.

### 유동 아이피 세팅
DuckDns를 install해서 적용을 시켜줘야 하는데 OS마다 적용법이 다르니 메뉴얼을 참고하자.
OS는 윈도우-gui방식으로 설치해서 셋팅해 주었고,
도메인은과 토큰은 DuckDns로그인 시 보여지는 값을 입력하면 된다.
![ducksetting](/assets/images/homeServerPost/ducksetting.PNG)
이렇게 입력만하면 아이피가 변경되어도 변경된 아이피를 추적해 자동으로 업데이트를 시켜준다.

---

<br>

# 2. Study Project

## 사용 기술
* java
* javascript
* mariadb
* vue
* jpa
* springboot
<br>

## 로그인
springSecurity + jwt 방식으로 구현 했습니다.

<b>Spring Security</b><br>
Spring Security 는 사용자 정보 (ID/PW) 검증 및 유저 정보 관리 등을 쉽게 사용할 수 있도록 제공합니다. <br>
<b>jwt</b><br>
JWT(Json Web Token)은 웹표준(RFC7519)로서 일반적으로 클라이언트와 서버, 서비스와 서비스 사이 통신 시 권한 인가(Authorization)을 위해 사용하는 토큰이다.<br>


```java
@PostMapping("/signin")
	public ResponseEntity<?> authenticateUser(@Valid @RequestBody SignInVO signInReq) {

		Authentication authentication = authenticationManager.authenticate(
				new UsernamePasswordAuthenticationToken(signInReq.getMemberEmail(), signInReq.getMemberPwd())); // 아이디 패스워드로 인증

		SecurityContextHolder.getContext().setAuthentication(authentication); // 인증정보보관 
        
		String token = jwtUtils.generateJwtToken(authentication); // 토큰 발급
		String refreshToken = jwtUtils.generateRefreshJwtToken(token); // 리프레시 토큰 발급

		UserDetailsImpl boUserDetails = (UserDetailsImpl) authentication.getPrincipal(); // 인증정보매핑
        List<String> roles = boUserDetails.getAuthorities().stream()
				.map(item -> item.getAuthority())
				.collect(Collectors.toList());

		return getResponseEntity(new JwtResponse(
				boUserDetails.getUserSeq(),
				boUserDetails.getUsername(),
				boUserDetails.getUserRealName(),
				boUserDetails.getAddr(),
			    boUserDetails.getUserNickName(),
                roles,
                token, refreshToken));
	}
```

인증이 성공하면 유저정보, 토큰, 권한정보가 넘어가고 main 화면으로 이동
실패하면 오류 메시지를 던져줘 화면에서 처리하도록 했습니다.

```javascript
// mutations
const mutations = {
    signinSuccess(state, res) {
        if (res.resultCode === '0000') {
            const data = res.data;

            state.user = new User(data.id ,data.email, data.userNm, data.roles[0], data.addr, data.nick); 
            state.token = data.token;
            state.refreshToken = data.refreshToken;
            state.type = data.type; // Bearer
            
            setTimeout(() => {
                router.push({ name: "Main" });
            }, 100);

            Vue.notify({
                group: 'loggedIn',
                type: 'success',
                text: '로그인에 성공했습니다.'
            });

        } else { 
            if (res.resultMessage == 'ID/PW를 확인해 주세요.') {
                Vue.notify({
                    group: 'loggedIn',
                    type: 'error',
                    text: '계정 정보를 확인해주세요!!'
                });
            } else {
                alert(res.resultMessage);
            }
        }
    }
}
```


### 로그인 체크
페이지를 이동 할 때 마다 프론트에서 라우터 가드로 토큰 유/무 체크

```javascript
router.beforeEach((to, from, next) => {
	
	// 로그인 페이지 접근
	if (to.fullPath == "/") {
		// 토큰 확인 후 메인으로 이동
		if (store.getters['auth/getToken']) {
			next({ name: "Main" });
		}
	// 로그인 페이지 외 접근
	} else {
		// 권한 필요 페이지 체크
		if (to.matched.some(record => record.meta.requiresAuth)) {
			// 토큰 확인 후 정상 동작
			if (store.getters["auth/getToken"]) {
				next();
			// 토큰 없으면 로그인 페이지로 튕김
			} else {
				alert("다시 로그인 해주세요.");
                next({
					path: '/',
					query: { redirect: '' }
				})
			}
		// 권한 필요 페이지 아니면 정상 동작
		} else {
			next();
		}
	}
	
})

```

### 토큰 체크

서버 통신을 할때 마다
인터셉터로 토큰을 체크 후 header에 토큰 정보를 넣어서 보내줍니다.

```javascript
this.axios.interceptors.request.use(
    config => {
        if (config.url == '/user/auth/refreshtoken') {
            const refreshToken = store.getters['auth/getRefreshToken'];
            if (refreshToken != null) {
                config.headers['Authorization'] = 'Bearer ' + refreshToken;
            }
        } else {
            const token = store.getters['auth/getToken'];
            if (token != null) {
                config.headers['Authorization'] = 'Bearer ' + token;
            }
        }
        return config;
    },
    error => {
        Promise.reject(error)
    });
```


401 에러가 떨어지면 refreshToken을 발급하는 로직을 태워 재발급을 실행합니다.

```javascript
this.axios.interceptors.response.use((response) => {
    return response;
}, async (error) => {
    const originalRequest = error.config;
    const errorMessage = error.response.data.message;
    if (error.response.status === 401 && errorMessage != "ID/PW를 확인해 주세요.") {
        originalRequest._retry = true;
        await store.dispatch('auth/refreshToken');
        originalRequest.headers['Authorization'] = 'Bearer ' + store.getters['auth/getToken'];
        return axios(originalRequest);
    }
    if (error.response.status === 403) {
        alert('접근권한이 없습니다');
        router.push({
            path: '/',
            query: { redirect: '' }
        });
    }
    return Promise.reject(error);
});
```


리프레시 토큰 유효성 검증 후 분기 처리를 해줍니다.

```java
@PostMapping(value = "/refreshtoken")
	public ResponseEntity<?> refreshToken(HttpServletRequest request) {
		String headerAuth = request.getHeader("Authorization");
		if (StringUtils.isNotEmpty(headerAuth) && headerAuth.startsWith("Bearer ")) { // 토큰 체크
			String refreshToken = headerAuth.substring(7);
			try {
				if(jwtUtils.validateJwtToken(refreshToken)) { // 유효성 검증
					String newToken = jwtUtils.generateJwtToken(refreshToken); // 새로운 토큰 생성
					String newRefreshToken = jwtUtils.generateRefreshJwtToken(newToken); // 새로운 리프레시 토큰 생성
					HashMap<String, String> tokens = new HashMap<>();
					tokens.put("token", newToken);
					tokens.put("refreshToken", newRefreshToken);
					return getResponseEntity(tokens);
				} else {
					log.error("리플레시 토큰 유효기간 만료!!");
					return getErrorEntity(new HashMap<String,String>(){{put("message","토큰이 유효하지 않습니다.");}});
				}
			} catch (Exception e) {
				log.error("refreshToken Exception", e);
			}

		}

		return null;
	}

```

<br>

## 새로고침 시 로그인 풀림현상
프로젝트를 진행 하던 중 새로고침 시 로그인이 풀리는 버그가 있었는데
새로고침을 하면 store에 들어있는 데이터(토큰, 유저정보)등이 날아가서 라우터가드에 의하여
메인페이지로 튕겨져 나갔다. 구글링 중 vuex-persistedstate라는 라이브러리가 store와 localstorage 값을 동기화 시켜준다는 걸 알게되었고
vuex-persistedstate플러그인을 적용해 이문제를 해결 하였다.

```javascript
import createPersistedState from "vuex-persistedstate";

export const store = new Vuex.Store({
  modules: {
    auth,
    notice,
    video
  },
  plugins: [createPersistedState()] // 적용
})

```