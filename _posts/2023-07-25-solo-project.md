
---
layout: post
title: "무한스크롤 / 북마크 / 토스트 구현하기"
categories: ["Study"]
---

리액트 솔로프로젝트에서 구현한 주요 기능들을 정리하였다.
이전에 작업했던 코드내용을 빠르게 기억하기 위한 포스팅이기 때문에
컴포넌트를 포함한 전체코드는 생략되어있다.

#### 무한스크롤

`renderNextPage`  
실질적인 무한스크롤이 일어나는 함수  
`IntersectionObserver`의 콜백함수에서 조건을 만족하면 실행된다.

`IntersectionObserver`  
뷰포트와 요소를 관찰하여 스크롤해서 요소가 화면안에 들어왔는지 관찰하는 api

```jsx
const renderNextPage = () => {
  if (isLoading) return;
  setIsLoading(true);

  setScrollData((prev) => [...prev, ...data.slice(PAGE_SIZE * (page - 1), PAGE_SIZE * page)]);
  setPage(page + 1);

  setIsLoading(false);
};

useEffect(() => {
  const observer = new IntersectionObserver(
    (entries) => {
      if (entries[0].isIntersecting) {
        renderNextPage();
      }
    },
    { root: null, rootMargin: "0px", threshold: 0.5 }
  );

  observer.observe(loadMoreRef.current);
  return () => observer.disconnect();
}, [isLoading, page]);
```

코드를 잘 보면 observer가 `useRef` 를 참조하고 있다는 것을 알 수 있다.

```jsx
 observer.observe(loadMoreRef.current);
```

컴포넌트를 간략하게 표현했을때 useref가 참조하는 엘리먼트는 스크롤로 로드되는 리스트의 아래에 있다.

```jsx
<PageContent>
      <List/>
      <div ref={loadMoreRef}></div>
</PageContent>
```

리액트가 아닌 순수 자바스크립트 환경이었다면 직접적으로 DOM을 참조했겠지만 리액트 환경이기 때문에 일반적으로 `useRef` 와 같이 사용한다.

```jsx
const targetElement = document.getElementById("loadMore"); 

if (targetElement) {
  observer.observe(targetElement);
}
```

---

#### 북마크

bookmarkData 라는 배열 안에 체크박스로 체크된 데이터가 들어가게 했다.
북마크 추가/취소 여부에 따라 타입이라는 속성에 값을 다르게 주어 객체 상태로 관리했다.

```jsx
const onChangeHandler = (bookmarkData) => {
    if (!bookmark.some((list) => list.id === bookmarkData.id)) {
      setBookMark((prev) => [...prev, bookmarkData]);
      setAction([{ type: "add" }]);
    } else {
      setBookMark((prev) => prev.filter((el) => el.id !== bookmarkData.id));
      setAction([{ type: "remove" }]);
    }
  };
```

---

#### 토스트 메세지

첫번째 `useEffect`  
toastAction과 toastQueue 상태를 기반으로 토스트 메시지를 생성한다.

두번째 `useEffect`  
toastQueue 배열이 변경될때마다 훅이 실행된다.

`checkTime`  
토스트 메시지에 애니메이션 효과를 부여하는 함수이다.  
애니메이션이 끝나면 큐에서 요소가 빠져나간다.

`requestAnimationFrame`  
`setInterval`, `setTimeout` 보다 더 나은 성능의 부드러운 애니메이션을 만들 수 있는 내장함수이다. 애니메이션 프레임마다 `checkTime` 을 실행시키는 역할을 한다.

useEffect를 실행하면 컴포넌트가 더이상 필요하지 않을때 컴포넌트를 언마운트 하는데 실행이 다 끝나 필요 없는 `requestAnimationFrame`를 정리하기 위해 `useEffect`의 리턴 함수에 `cancelAnimationFrame`를 넣어 애니메이션을 중지시켜야 한다.

```jsx
useEffect(() => {
  const currentTime = new Date().getTime();
  if (toastAction.length > 0) {
    setToastQueue((prev) => [
      ...prev,
      {
        type: toastAction[toastAction.length - 1].type,
        createAt: currentTime,
        onSlide: false,
        expiration: currentTime + 3500,
      },
    ]);
  }
}, [toastAction]);

useEffect(() => {
  const currentTime = new Date().getTime();
  animationRef.current = requestAnimationFrame(checkTime);

  function checkTime() {
    //생성되고 0.1초 지나서 왼쪽으로 슬라이드
    setToastQueue((prev) =>
      prev.map((el) => {
        if (currentTime >= el.expiration - 3400 && !el.onSlide) {
          el = { ...el, onSlide: true };
        }
        return el;
      })
    );

    //생성되고 2초 지나서 오른쪽으로 슬라이드
    setToastQueue((prev) =>
      prev.map((el) => {
        if (currentTime >= el.expiration - 1500 && el.onSlide) {
          el = { ...el, onSlide: false };
        }
        return el;
      })
    );

    if (toastQueue.some((el) => currentTime >= el.expiration)) {
      setToastQueue((prev) => prev.slice(1));
    }
  }

  animationRef.current = requestAnimationFrame(checkTime);
  return () => {
    cancelAnimationFrame(animationRef.current);
  };
}, [toastQueue]);
```