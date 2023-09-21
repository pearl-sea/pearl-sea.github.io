---
layout: post
title: Type Alias
categories: ["TypeScript"]
---

타입 별칭(Type Alias)는 타입에 이름을 부여하는 타입스크립트 문법이다.  
타입 별칭은 앞에서 정리했던 인터페이스와 유사하게 작동한다. 그래서 인터페이스와의 차이점을 위주로 정리해 보려고 한다.

아래의 코드는 **타입 별칭**으로 작성하였다.

```ts
type Data = {
  id: number;
  title: string;
};

const data: Data[] = [];
```

아래의 코드는 **인터페이스**로 작성하였다.

```ts
interface Data {
  id: number;
  title: string;
}

const data: Data[] = [];
```

두 코드는 쓰임새와 문법이 그렇게 차이가 날 것 같아 보이지 않는다.  
타입 별칭은 원시값을 직접 할당하여 타입을 정의할 수 있다.
인터페이스는 불가능하다.

```ts
type Name = "pearl";
type Dice = 1 | 2 | 3 | 4 | 5 | 6;
type Islogin = true | false;
```

또 동일한 타입을 두번 선언하면 에러가 난다. 인터페이스는 에러가 나지 않는다.

```ts
type Data = {
  id: number;
  title: string;
};

type Data = {
  //식별자가 중복되었습니다.
  content: string;
};
```

타입 별칭은 인터페이스와 다르게 기존에 선언된 타입을 확장할 수 없다. 그렇기 때문에 extends를 사용할 수 없다.  
인터페이스는 인터페이스와 타입 모두 상속 가능하다.

### 타입 별칭과 인터페이스 둘 중에 어떤걸 사용해야 하는가

상황에 따라 다르지만 원시값 또는 튜플 타입을 타입 선언 해야 한다면 타입 별칭을 사용하고 상속을 통한 확장이 필요하다면 인터페이스를 사용하는것이 좋다.
어느 한쪽을 선택했다고 반드시 나머지 한쪽을 코드에서 배제해야 하는것은 아니며 타입 별칭과 인터페이스를 조합하면 더 안정적인 타입을 작성할 수 있다.
