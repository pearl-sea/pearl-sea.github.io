---
layout: post
title: SHA-3으로 비밀번호 해싱
categories: ["study"]
---

#### 비밀번호는 단방향 암호화로 저장

로그인 리퀘스트를 보내고 개발자도구 -> network -> payload를 확인했을때 비밀번호 문자가 별도의 처리 없이 화면에 노출되고 백엔드에 전송되는 문제가 있었다.
보안을 위해 비밀번호는 hash라는 암호화 기법을 사용하여 저장된다. 암호화는 되고 복호화는 불가능한 방법이라 웹서비스 만든사람도 운영하는 사람도 사용자 비밀번호는 모른다.
해시를 적용하기 위해 CryptoJS를 사용해 보기로 했다.

```jsx
import CryptoJS from "crypto-js";

///...생략

function hashPassword(password) {
  const hashedPassword = CryptoJS.SHA3(password).toString();
  return hashedPassword;
}

const data = await request.formData();
const authData = {
  username: data.get("email"),
  password: hashPassword(data.get("password")),
};
```

이제 비밀번호를 제출하면 해싱 처리가 되어 백엔드로 전송이 되는데 서버에서 해싱된 패스워드를 틀린 비밀번호로 인식하고있었다. 알고보니 해싱처리 이전에 생성된 계정은 로그인이 안되고 이후에 생성되는 계정은 정상적으로 로그인이 되는 거였다. 평문 → 백엔드 전달 → 해싱 → 저장 의 과정에서 프론트 1차 해싱 → 백엔드 전달 → 백엔드 2차 해싱 → 저장의 과정이 되어 기존에 생성된 계정은 값이 달라져 로그인이 불가능하다고 한다.
