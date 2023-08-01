---
layout: post
title: Interface
categories: ["typescript"]
---

타입스크립트에서 interface는 객체의 타입을 지정할때 사용되는 문법이다.

#### interface를 안쓴다면

인터페이스 없이 객체에 타입 지정은 가능하나 변수 옆의 중괄호의 길이가 점점 늘어나게 되고 가독성에서 손해를 본다.  
또 재사용이 안된다는 단점이 있다. 새로운 변수를 선언할 때마다 타입 지정을 다시 해줘야 한다.

```ts
const player :{name: string, level: number} = { //프로퍼티가 추가될때마다 이 부분이 점점 길어진다.
  name: "user"
  level: 99
}
```

#### interface 문법

- **optianal**  
  인터페이스 안에 작성된 어떤 프로퍼티가 항상 필요한 경우가 아니라면 `?`연산자를 사용하려 선택적 프로퍼티를 작성할 수 있다.

```ts
interface Friend {
  name: string;
  level: number;
}

interface Player {
  name: string;
  level: number;
  friend?: Friend[];
}

const player :Player = {
  name: "뉴비"
  level: 1,
}
```

- **readonly**  
  참조는 가능하지만 수정 불가능한 값의 타입을 지정할 때 사용한다.

```ts
interface Player {
  readonly name: string;
  level: number;
}
```

- **extends**  
  새로운 인터페이스를 만들고 기존의 인터페이스와 상속 관계를 만들 수 있다.
  하위 인터페이스는 상위 인터페이스의 모든 프로퍼티를 가지고 있어야 한다.
  예제의 하위 인터페이스에 해당하는 `Warrior`인터페이스는 `name`, `level`타입 모두 가지고 있어야 한다.

```ts
interface Player {
  name: string;
  level: number;
}

interface Warrior extends Player {
  jobskill: string;
}

const player: Player = {
  name: "초보자",
  level: 10,
};

const warrior: Warrior = {
  name: "전사",
  level: 20,
  jobskill: "Sword Mastery",
};
```

#### interface를 함수에서 사용하기

인터페이스는 객체 외에도 함수의 매개변수와 반환 타입도 지정할 수 있다.

```ts
interface Player {
  name: string;
  level: number;
}

interface Chatting {
  (player: Player, message: string): string;
}

const chat: Chatting = (player, message) => {
  return `${player} : ${message}`;
};

const player : Player = {
  name: "토끼공듀"
  level: 99
}

const message = chat(player, "ㅋㅋ");
console.log(message) //"토끼공듀: ㅋㅋ"
```

#### 자주 쓸 것 같은 패턴 적용하기

API호출을 하는 코드를 적어보고 인터페이스를 적용해 보았다.

```ts
interface Data {
  id: number;
  title: string;
  created_at: string;
  username: string;
}

interface FetchData {
  (url: string): Promise<Data[]>;
}

const fetchData: FetchData = async (url) => {
  try {
    const response = await axios.get<Data[]>(url);
    return response.data;
  } catch (error) {
    console.error("Error:", error.message);
    return [];
  }
};
```
