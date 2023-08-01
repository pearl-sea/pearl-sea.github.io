---
layout: post
title: 객체와 프로퍼티
categories: ["javascript"]
---

객체는 key와 value로 이루어진 property의 집합이다.  
자바스크립트의 객체는 중괄호`{}` 안에 만들 수 있다.

```js
const obj = {
  key1: value1,
  key2: value2,
};
```

#### 기본 문법

- 콜론`:` 왼쪽의 값은 `key`, 콜론 오른쪽의 값은 `value`라고 부르고 key, value 한쌍을 property라고 부른다
- 프로퍼티와 프로퍼티의 사이는 쉼표`,`를 찍어 구분한다.
- `key`는 property name, `value`는 property value라고 부른다.
- property name은 따옴표를 생략하고 작성한다.
- property name의 첫번째 글자는 반드시 문자, 언더바`_`, 달러기호`$`로 시작해야한다. (css 클래스처럼 숫자로 시작할 수 없다.)
- property name에 띄어쓰기나 하이픈`-`을 쓸 수도 있지만 반드시 따옴표로 감싸줘야한다.
- property value에는 어떤 자료형이던 다 넣을 수 있다.

```js
const obj = {
  my key: 'value1', //틀린 문법
  my-key: value2, //틀린 문법
  '1st': 1234,
  'my key': false,
}
```

#### 객체의 타입

객체를 `typeof`연산자로 콘솔에 출력해보면 `object`라고 출력된다.

```js
console.log(typeof {
  key1: value1;
  key2: value2;
}) //object
```

#### 객체의 값 조회하기

```js
const user1 = {
  name: "pearl",
  level: 10,
  hp: 5500,
};
```

- **Dot notation**  
  값을 조회하는 가장 간단한 방법이다.  
  object.key 형식으로 표기하면 해당 키의 값을 가져올 수 있다.

```js
user1.name;
```

- **Bracket notation**  
  key값을 대괄호에 적어서 값을 조회하는 방법이다.  
  대괄호 안에 백틱 또는 따옴표가 없으면 틀린 문법으로 간주한다.

```js
user1[`name`]; //O
user1["level"]; //O
user1["hp"]; //O

user1[name]; //X
```

마지막의 `user1[name]` 으로 값을 조회하려면  
새로운 변수를 생성하고 **객체의 key에 해당하는 문자열**을 할당해야 한다.

```js
let name = "name";
console.log(user1[name]);
```

#### 객체 안에 값이 있는지 확인하기

객체 안에 해당 값이 있는지 in 연산자를 활용해 확인할 수 있다

```js
let animal = {
  "animal name": "cat",
  legs: 4,
};

if ("animal name" in animal) {
  console.log(`${animal["animal name"]}의 다리는${animal.legs}개다`);
}

//cat의다리는4개다
```

### 객체의 값 편집하기

객체의 값은 추가, 수정, 삭제가 가능하다.  
존재하지 않는 프로퍼티에 접근 하려고 하면 undefined가 출력된다.

```js
let animal = {
  "animal name": "cat",
  legs: 4,
};

//----추가
animal.features = "귀엽다";
console.log(animal); //{animal name: 'cat', legs: 4, features: '귀엽다'}

//----수정
console.log(animal.legs); //4
animal.legs = 8;
console.log(animal.legs); //8

//-----삭제
delete animal.legs;
console.log(animal.legs); //undefined
```

#### 객체 프로퍼티의 개수 구하기

`Object.keys()` ,`Object.entries()`를 통해 쉽게 구할 수 있다.

```js
Object.keys(user1).length;
Object.entries(user1).length;
```

#### 서로 다른 객체 합치기

두 객체에 동일한 속성이 있다면 가장 마지막에 합쳐지는 객체의 속성이 우선순위를 가진다.

```js
let obj1 = {
  id: 1,
  name: "쿠키",
  price: "2천원",
};

let obj2 = {
  price: "1억원",
  taste: "버터맛",
};

function extend(obj1, obj2) {
  return { ...obj1, ...obj2 };
}

extend(obj1, obj2); //쿠키가 1억원이 된다.
extend(obj2, obj1); //쿠키가 2천원이 된다.
```
