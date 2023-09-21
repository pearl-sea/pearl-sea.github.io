---
layout: post
title: Context API
categories: ["React"]
---

회원 정보 수정 페이지를 구현하던 중 회원 정보 수정이 끝나면 정보 입력을 받은 폼 뿐만 아니라 같은 화면 안에 있으면서 회원 정보가 담겨 있는 다른 컴포넌트도 리렌더링이 필요했다. 생각해보니 **커스텀 훅으로 생성된 상태를 여러 컴포넌트에 적용했을때 그 상태는 컴포넌트마다 별개로 관리된다.**

연결된 컴포넌트에서는 상태 정보를 공유하고 싶어서 전역으로 컴포넌트를 관리할 수 있는 **Context API**를 사용해보았다. 컨텍스트를 이용하면 리액트에서 **프롭 체인을 만들지 않고 모든 컴포넌트에 바로 상태를 전달**할 수 있다. 이 방법으로 원하는 컴포넌트 구현에 성공했고 유저 정보를 관리하기 위해 만들어두었던 커스텀 훅에 컨텍스트를 어떻게 적용했는지 과정을 정리해 보았다.

#### createContext

컨텍스트 객체를 생성하는 역할을 한다. 생성된 컨텍스트는 꼭 export 해줘야 사용할 수 있다.

```jsx
import { createContext } from "react";

const UserinfoContext = createContext();
export default UserinfoContext;
```

#### Provider 컴포넌트 정의

생성한 컨텍스트에는 `Provider`컴포넌트가 있다.
컴포넌트 내에서 커스텀 훅을 호출하여 반환된 `user`와 `setUser`를 `Provider`의 값으로 전달하여
해당 컨텍스트를 구독하는 하위 컴포넌트들에게 값을 제공하는 역할을 한다.

```jsx
import useUserinfo from "../hooks/useUserinfo";

export const UserinfoProvider = ({ children }) => {
  const { user, setUser } = useUserinfo();
  return (
    <UserinfoContext.Provider value={% raw %}{{ user, setUser }}>{% endraw %}
      {children}
    </UserinfoContext.Provider>
  );
};
```

#### 최상위 컴포넌트에 Provider 적용

`Provider`로 `App`을 감싸면 모든 하위 컴포넌트가 해당 컨텍스트를 사용할 수 있게 된다.

```jsx
import React from "react";
import ReactDOM from "react-dom/client";
import { UserinfoProvider } from "./contexts/UserinfoContext";

import App from "./App";
import "./index.css";

const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(
  <React.StrictMode>
    <UserinfoProvider>
      <App />
    </UserinfoProvider>
  </React.StrictMode>
);
```

### 컨텍스트 구독

이제 모든 자식 컴포넌트들이 `useContext`를 통해 컨텍스트 값을 읽을 수 있고 값이 변경될때마다 컴포넌트를 업데이트 할 수 있다.

```jsx
import React, { useContext } from "react";
import UserinfoContext from "./path/to/UserinfoContext";

const ChildComponent = () => {
  const { user } = useContext(UserinfoContext);

  // ...
};
```

#### 결과

![회원정보 수정 컴포넌트](/assets/img/context-api-component.gif)
