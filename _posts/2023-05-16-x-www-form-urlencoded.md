---
layout: post
title: application/x-www-form-urlencoded
categories: ["study"]
---

#### 며칠전부터 로그인이 안되던 오류 발생

회원가입할때는 문제없는데, 로그인에서 422에러가 발생했고 백엔드 개발자님에게 이 문제를 알렸다.
그렇게 접속기록을 뜯어보며 문제를 파악한 결과 백엔드에서는 로그인, 회원가입 리퀘스트 헤더가 `application/x-www-form-urlencoded`, `application/json`이었는데
클라이언트에서는 둘 다 `application/json`였다. 헤더가 달랐던 이유는 백엔드님이 로그인을 구현하기 위해 사용한 라이브러리의 문제였다.
`application/x-www-form-urlencoded` 헤더를 쓸 이유도 딱히 없었기 때문에 나중에 헤더를 통일해달라고 요청했다.

#### application/x-www-form-urlencoded

key=value 쌍으로된 쿼리스트링과 유사한 문자열을 전송하는 형식이다.  
헤더의 Content-Type이 `application/x-www-form-urlencoded`이면  
form 데이터를 보낼때 username=myUsername&password=myPassword 이런 형식으로 코딩해서 전송해야 한다고 한다.  
그래서 `stringify`를 사용하지 않고 `URLSearchParams`를 사용한다.

```jsx
const response = await fetch("/api/user/create", {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
  },
  body: JSON.stringify(authData),
});
```

- json으로 전송할 때와 코드 비교

```jsx
const response = await fetch("/api/user/login", {
  method: "POST",
  headers: {
    "Content-Type": "application/x-www-form-urlencoded",
  },
  body: new URLSearchParams(authData),
});
```
