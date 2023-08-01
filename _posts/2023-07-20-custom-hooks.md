---
layout: post
title: "Custom Hooks"
categories: ["react"]
---

#### React에서의 Hook

함수형 컴포넌트에서 class 문법을 사용하지 않고도 React의 기능을 사용할 수 있게 해주는 함수

#### Hook 쓸때 조심해야 하는 것

- **조건문, 반복문, 중첩 함수 내에서 Hook 실행 금지**  
  단 중첩함수이더라도 컴포넌트 내부의 **이벤트 핸들러에서는 실행 가능**하다.

- **컴포넌트 외부에서 실행 금지**  
  함수형 컴포넌트또는 커스텀 훅 내부에서만 호출해야한다.

---

#### Custom Hook을 사용하면 좋은 점

API 호출, form 상태 관리 등 여러 컴포넌트에서 중복되는 코드를 깔끔하고 재사용 가능하게 만들 수 있다.

#### Custom Hook 작성해보기

최근에 구현해본 북마크 기능을 커스텀 훅으로 리팩토링했다.  
`onChange`: 북마크한 상품을 `bookmark`배열에 저장하고, 북마크 추가/삭제 액션을 토스트UI로 보여주기 위해 `bookmarkActions`상태에 객체를 추가하는 함수이다.

```jsx
import { useState } from "react";

const useBookmark = () => {
  const [bookmark, setBookmark] = useState([]);
  const [bookmarkActions, setBookmarkActions] = useState([]);

  const onChange = (bookmarkData) => {
    if (!bookmark.some((list) => list.id === bookmarkData.id)) {
      setBookmark((prev) => [...prev, bookmarkData]);
      setBookmarkActions([{ type: TOAST_TYPE.add }]);
    } else {
      setBookmark((prev) => prev.filter((el) => el.id !== bookmarkData.id));
      setBookmarkActions([{ type: TOAST_TYPE.remove }]);
    }
  };

  return { bookmark, setBookmark, bookmarkActions, setBookmarkActions };
};

export default useBookmark;
```

- router  
  커스텀 훅을 이용하여 `prop`을 깔끔하게 작성할 수 있다.

```jsx
const bookmarkProps = useBookmark();

const router = createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    children: [
      {
        index: true,
        element: <Main {...bookmarkProps} />,
      },
      {
        path: "products/list",
        element: <Product {...bookmarkProps} />,
      },
      {
        path: "bookmark",
        element: <Bookmark {...bookmarkProps} />,
      },
    ],
  },
]);
```

- component  
  필요한 `prop`만 골라서 가져올 수 있다.

```jsx
const Product = ({ bookmark, onChange }) => {
  const onChangeHandler = (bookmarkData) => {
    onChange(bookmarkData);
  };
  //...생략
};
```

**사실은 prop으로 사용할 필요 없다.**  
컴포넌트에서 직접 `useBookmark`를 호출하면 커스텀 훅의 상태를 사용할 수 있다.

```jsx
import useBookmark from "../hooks/useBookmark";

const Product = () => {
  const { bookmark, onChange } = useBookmark();

  const onChangeHandler = (bookmarkData) => {
    onChange(bookmarkData);
  };
  //...생략
};
```
