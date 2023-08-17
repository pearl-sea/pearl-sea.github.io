---
layout: post
title: 해시(Hash)를 이용한 비밀번호 암호화
categories: ["study"]
---

로그인을 하고 나서 브라우저 창에서 F12키를 눌러 개발자 도구로 진입한 뒤 네트워크 탭에서 로그인 기록의 payload 부분을 확인했을때 비밀번호 부분이 평문으로 백엔드에 전달되는 경우는 거의 없을 것이다. 대부분의 개발자는 비밀번호에 해싱 처리를 한 후 작업한다.

---

해싱(Hashing)은 해시 함수를 이용한 암호화 기법이다.

* **해시 함수의 특징**
  * 매번 다른 글자수를 입력해도 항상 같은 길이의 문자열을 반환한다.
  * 서로 다른 문자열을 입력하면 항상 다른 결과를 반환한다.
  * 동일한 문자열을 입력하면 항상 같은 결과를 반환한다.

#### 비밀번호는 단방향 암호화로 저장
해싱은 **복호화가 불가능**한 암호화 방식이다. 이러한 방식을 사용하는 이유는 서비스 운영자가 사용자의 비밀번호를 알 필요도 없고 알아서도 안되기 때문이다. 해싱을 통한 암호화의 핵심은 사용자가 어떤 값을 입력했느냐가 아닌 사용자가 **회원가입때 입력한 비밀번호와 동일한 값을 입력했느냐**이다 그렇기 때문에 암호화된 비밀번호를 원래값으로 복호화할 필요가 없다.

#### 프론트엔드에서 비밀번호 해싱해서 백엔드로 전달하기
CryptoJS를 이용하여 비밀번호 해싱을 구현하였다. 백엔드에서 SHA-3 방식으로 해싱해달라고 요청하였기 떄문에 SHA-3으로 해싱하였다.  
아래의 예제는 salt가 없다.

```jsx
import CryptoJS from "crypto-js";

///...생략

function hashPassword(password) {
  const hashedPassword = CryptoJS.SHA3(password).toString();
  return hashedPassword;
}

const requestBody = {
  username: username,
  password: hashPassword(password),
};
```

#### Salt 
해시 함수를 사용하여 만들어낼 수 있는 값을 대량으로 저장한 레인보우 테이블이라는 물건도 있다. 레인보우 테이블로 비밀번호를 유추하기 어렵게 만들기 위해 해싱된 비밀번호에 소금을 치는 것 처럼 Salt라는 랜덤한 문자열을 추가한다. 솔트는 바이트 데이터로 생성되어 16진수 문자열로 변환되어 저장된다. 바이트 단위로 생성하는 이유는 데이터가 컴퓨터에서 처리되는 기본 단위가 바이트이기 때문이고 16진수로 변환하는 이유는 바이너리 형태의 데이터의 길이가 너무 길기 때문이다.


#### Salt를 적용한 예제
salt를 백엔드로 보낼때는 회원가입때 1번만 보내면 된다. 백엔드에서 해당 솔트와 해싱된 비밀번호를 저장하면 사용자가 로그인을 할때 전송한 해싱된 비밀번호와 데이터베이스에 저장된 솔트를 비교해 인증을 수행한다. 

```jsx
import CryptoJS from "crypto-js";

///...생략

const salt = CryptoJS.lib.WordArray.random(128/8).toString(CryptoJS.enc.Hex);

function hashPassword(password, salt) {
  const saltedPassword = salt + password;
  const hashedPassword = CryptoJS.SHA3(saltedPassword).toString();
  return hashedPassword;
}

const requestbody = {
  username: username,
  password: hashPassword(password, salt),
  salt: salt,
};
```