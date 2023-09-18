---
layout: post
title: 기술 발표 준비
categories: ["study"]
---

저는 프로젝트에서 기본 CRUD와 실시간 경매 기능, 이미지 업로드 기능을 담당했습니다.
데이터 조회 코드 작성을 위해 리액트 쿼리를 사용하면서 경험한 일을 짧게 소개드리겠습니다.

리액트 쿼리를 사용하면 데이터 페칭 코드를 조금 더 간결하게 작성할 수 있고
리액트 쿼리의 캐싱 기능을 이용하여 불필요한 호출 횟수를 줄이거나 데이터가 만료되었을때 자동으로 재요청할 수 있습니다.

이렇게 편리한 라이브러리지만 처음 사용하다보니 캐싱과 쿼리키에 대한 이해가 부족하여 잘못된 코드를 작성했습니다.

#### 쿼리키을 동적으로 작성하지 않은 문제
아래는 문제가 된 코드입니다. 쿼리키에 useLocation의 리턴값인 location을 넣어 브라우저 주소창이 변경될때마다 새로운 데이터를 받아오고 싶었습니다. 처음 호출할때는 문제가 없었으나 한번이라도 접근했던 카테고리로 다시 이동했을경우 api요청이 이루어지지 않아 useEffect를 추가로 사용했습니다. 

#### staleTime을 infinity로 한 이유
브라우저 포커싱을 벗어났다 다시 돌아왔을때 데이터가 낡았다고 판단하고 리페칭하는것이 리액트 쿼리의 기본 설정입니다. 그러나 저는 포커싱 여부와 상관없이 url의 변화가 있을때만 리페칭을 하고 싶었습니다.

```jsx
  const location = useLocation();
  const itemNumber = location.pathname.split("/");
  const queryClient = useQueryClient();
  const { isLoading, error, data } = useQuery(
    ["productData", location],
    async () => {
      const response = await authInstance.get(
        API_PATHS.products.default(itemNumber[itemNumber.length - 1]),
      );
      return response.data;
    },
    {
      staleTime: Infinity,
    },
  );

  //컴포넌트가 마운트 될 때마다 쿼리 다시 실행
  useEffect(() => {
    queryClient.invalidateQueries("productData");
  }, []);

   //페이지넘버, url, 검색데이터가 변할때마다 데이터 갱신
  useEffect(() => {
    refetch();
  }, [location.pathname, location.search, currentPage]);
```

쿼리 키를 다음과 같이 작성했을때 한번이라도 접근했던 경로는 다시 접근하더라도 리페칭 되지 않았습니다.

```jsx

  //첫번째 시도
  [
    "productList", location.pathname, location.search
  ],

  //두번째 시도
  const searchParams = new URLSearchParams(location.search);
  const categoryId = getCategoryId(location.pathname);
  const page = Number(searchParams.get("page")) - 1;
  const type = searchParams.get("type");
  const keyword = searchParams.get("keyword");

  ["productList", categoryId, page, keyword, type],

```

쿼리 키를 아래처럼 작성했을 때 의도한대로 리페칭이 되었습니다.

```jsx
  [
    "productList", location
  ],
```

### 다시 작성한 코드
여러가지 시행착오를 거쳐 쿼리키를 동적으로 작성하려고 할때 location.pathname은 사용하면 안된다는것을 알았습니다. location 객체 자체의 변화는 감지하고 있어서 이것을 이용해 구현에 성공할 수 있었습니다.

useEffect로 작성할 수 있는 코드는 `onSuccess` 안에 작성할 수 있기 때문에 useEffect를 지웠습니다. `queryClient.invalidateQueries`는 데이터에 변화가 있을때 쿼리키의 유효성을 제거하기 위해 사용되어 데이터 조회 목적과 맞지 않기 때문에 적지 않았습니다.

```jsx
const location = useLocation();
const searchParams = new URLSearchParams(location.search);
const categoryId = getCategoryId(location.pathname);
const path = getAPIPath(categoryId);
const page = Number(searchParams.get("page")) - 1;
const type = searchParams.get("type");
const keyword = searchParams.get("keyword");

const { isLoading, error, data } = useQuery<Data>(
  [
    "productList", location
  ],
  getData,
  {
    onSuccess: (data) => {
      if (data.totalPages !== undefined) {
        setTotalPages(data.totalPages);
      }
      setCurrentPage(page);
    },
    staleTime: Infinity,
  },
);
```

리액트쿼리에서 queryFn의 역할을 하는 getData함수입니다. 검색결과, 필터링결과, 카테고리 조회 api의 pathname이 전부 달라서 복잡한 형태가 되었습니다. pathname으로 구분하지 않고 쿼리스트링으로 구분했다면 더 간단하게 작성할 수 있었을 것 같아요.

```jsx

const getData = async () => {
    const params = { page: page, size: ITEMS_PER_VIEW };
    if (type) {
      const response = await axios.get(`/products/available`, {
        params: { ...params, type: type },
      });

      return response.data;
    }

    const response = keyword
      ? await axios.get(`/products/search`, {
          params: { ...params, keyword: keyword },
        })
      : await axios.get(path, {
          params: params,
        });

    return response.data;
  };

```

쿼리키를 URL 변화에 따라 동적으로 가져오려면

1. staleTime에 infinity를 적지 않는다.
2. staleTime에 infinity를 적었다면 쿼리키 배열 안의 값으로 location을 이용한다.(location.pathname, location.search는 불가능)

이렇게 해야 한다는 사실을 알았습니다. 쿼리키를 구체적으로 구조화하는 방식도 있었는데 저의 경우에는 staleTime때문에 그렇게 하는 게 도움이 되지 않았습니다. 경매가 실시간성을 띄고 있기 때문에 staleTime에 infinity를 줄 필요도 없었던 것 같습니다. 그리고 url를 꼼꼼하게 가공을 잘 해서 api호출과 미스매칭이 일어나지 않게 신경을 더 써야겠다고 생각했습니다.
 