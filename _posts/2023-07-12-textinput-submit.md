---
layout: post
title: "텍스트타입 인풋이 하나만 존재하면 submit되는 현상"
categories: ["HTML"]
---

```html
<form>
  <input type="text" />
</form>
```

프로젝트 진행을 위해 작업을 하다가 인풋태그를 테스트하기 위해 엔터를 눌렀는데 그대로 전송처리가 되었다.  
**폼태그 안에 텍스트 타입 인풋이 하나만 존재하면 엔터키로 전송이 된다**는데 내가 작성한 form 안에는 input과 textarea만 있었던 것이다.

```html
<!-- 아래의 케이스는 엔터를 누르면 제출이 된다.-->
<form>
  <input type="email" />
</form>

<form>
  <input type="password" />
</form>

<form>
  <input type="text" />
  <textarea></textarea>
</form>

<form>
  <input type="password" />
  <button></button>
</form>

<form>
  <input type="text" />
  <input type="checkbox" />
</form>

<!-- 아래의 케이스는 엔터를 누르면 제출이 안된다.-->
<form>
  <input type="email" />
  <input type="password" />
</form>

<form>
  <input type="text" />
  <input type="text" />
</form>
```

신기해서 이 현상에 대해 테스트를 해보니 인풋타입이 이메일이거나 패스워드여도 텍스트로 간주했다.  
이메일과 패스워드타입 역시 form 태그 안에 하나만 두면 텍스트 타입 처럼 엔터키로 제출이 되었다.

#### 해당 현상을 방지하는 방법

```html
<form>
  <input type="text" />
  <input type="text" style="display: none;" />
</form>
```

- 보이지 않는 텍스트 타입 인풋을 하나 더 추가한다.  
  가독성을 위해 예제에서는 인라인으로 스타일을 주었다. 실제로 이 방법을 사용 할 때는 클래스로 처리하자. <br><br>

```js
const form = document.getElementById("myForm");

form.addEventListener("submit", function (event) {
  event.preventDefault();
});
```

- 자바스크립트로 제출 이벤트를 막는다  
  `Event.preventDefault()`를 이용하여 기본 동작을 취소할 수 있다.
