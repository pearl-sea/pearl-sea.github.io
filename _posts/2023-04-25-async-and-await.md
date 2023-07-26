---
layout: post
title: "async, await"
categories: ["javascript"]
---

async, await은 Promise chaining의 가독성을 개선한 코드이다.

#### async, await을 사용하면 좋은 점

1. 리턴값이 변수에 담겨 있어 알아보기 쉽다
2. 코드가 복잡해질수록 중첩된 콜백함수의 깊이도 깊어지는데 이러한 문제를 예방할 수 있다.

#### async

함수 선언시 가장 앞에 붙이는 키워드이며 함수 안에 비동기적으로 실행할 부분이 있음을 명시한다.

### await

- 비동기적으로 실행할 부분에 await을 붙인다.
- await 키워드가 붙는 함수는 프로미스 객체를 리턴해야 한다.
- await 키워드는 async 안에서만 유효하다.
- await 키워드가 붙은 부분이 실행되면 Promise 객체가 fulfilled / rejected 상태가 될 때까지 async 함수를 정지시킨다.
- await이 여러개 붙을 경우 await이 붙은 코드는 순서대로 실행된다.

### Promise chaining 재작성하기

```jsx
fetch("https://jsonplaceholder.typicode.com/users")
  .then((response) => response.json())
  .then((users) => {
    const userName = users[0].name;
    console.log(userName);
  });
```

↓↓↓

```jsx
async function getUserName() {
  const response = await fetch("https://jsonplaceholder.typicode.com/users");
  const users = await response.json();
  const userName = users[0].name;
  console.log(userName);
}

getUserName();
```

### try, catch, finally 추가하기

```jsx
async function getUserName() {
  try {
    const response = await fetch("https://jsonplaceholder.typicode.com/users");
    const users = await response.json();
    const userName = users[0].name;
    console.log(userName);
  } catch (error) {
    console.log(error);
  } finally {
    console.log("끝");
  }
}

getUserName();
```
