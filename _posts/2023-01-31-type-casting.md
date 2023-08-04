---
layout: post
title: "형변환"
categories: ["javascript"]
---

String, Number, Boolean 함수를 사용하면 데이터 타입을 바꿀 수 있다.
이 함수들을 인자 없이 호출 했을 때는 빈 문자열(""), 숫자0, false가 반환된다.

```js
String(); //""
Number(); //0
Boolean(); //false
```

#### String 함수로 타입 변경

```js
String(true); //'true'
String(1); //'1'
String(undefined); //'undefined'
String(null); //'null'
```

#### Number 함수 사용시 주의할 점

- 숫자가 아닌 문자에 넘버 함수를 쓰면 NaN을 출력한다.
- 괄호안에 숫자가 든 문자열(예:’123’)을 인자로 넣으면 숫자를 반환한다.
- Boolean 타입을 인자로 넣으면 0과 1로만 반환된다.
- undefined를 인자로 넣으면 NaN, null을 인자로 넣으면 0을 출력한다.

```js
Number("text"); //NaN
Number("123"); //123
Number(false); //0
Number(undefined); //NaN
Number(null); //0
```

#### Boolean 함수 사용시 주의할 점

- 일반적으로 어떤 값을 Boolean형으로 변환하면 true를 출력한다.
- 0과 빈 문자열(""), NaN을 인자로 넣으면 false를 출력한다.

```js
Boolean(1); //ture
Boolean("1"); //ture

Boolean(""); //false
Boolean(0); //false
Boolean(NaN); //false
```

#### 형변환 연산 시 주의점

- 문자열에 + 연산자를 사용하면 문자열끼리 이어진다.
- 문자열과 숫자열의 +연산도 문자열 연산과 같은 결과가 나온다.
- 덧셈을 제외하면 자동으로 형변환된후 연산된다.

```js
Number("1" + "2" + "3"); //123

Number(123 + "4"); //1234

Number("7" - 2); //5
Number(10 / "5"); //2
Number(ture * "1"); //1
```
