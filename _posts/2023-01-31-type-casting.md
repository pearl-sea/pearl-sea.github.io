---
layout: post
title: "형변환"
categories: ["javascript"]
---

String, Number, Boolean 함수를 사용하면 데이터 타입을 바꿀 수 있다

#### String 함수로 타입 변경하기

```jsx
String(true); //'true'
String(1); //'1'
```

#### Number함수 쓸 때 주의할 점

```jsx
Number("text"); //NaN
Number("123"); //123
Number(false); //0
```

1. 숫자가 아닌 문자에 넘버 함수를 쓰면 ‘NaN’이 나온다.
2. 괄호안에 숫자가 든 스트링(예:’123’)의 경우 스트링이지만 넘버 함수를 쓰면 자동으로 넘버타입으로 변환해준다
3. 불린타입에 넘버 함수를 쓰면 0과 1로만 반환된다.

#### Boolean 함수 쓸 때 주의할 점

```jsx
Boolean(1); //ture
Boolean("1"); //ture

Boolean(""); //false
Boolean(0); //false
Boolean(NaN); //false
```

일반적으로 어떤 값을 Boolean형으로 변환하면 true가 나온다  
0과 ‘’(따옴표 안에 아무것도 없는 값), NaN을 넣으면 false가 나온다

#### 형변환 연산 시 주의점

```jsx
Number("1" + "2" + "3"); //123
//문자열의 덧셈 주의!!
//문자열에 + 연산자를 사용하면 계산이 되지 않고 문자열끼리 이어진다.

Number(123 + "4"); //1234
//문자열과 숫자열의 +연산도 문자열 연산과 같은 값이 나온다.

Number("7" - 2); //5
Number(10 / "5"); //2
Number(ture * "1"); //1
//덧셈을 제외하면 평범하게 형변환된후 연산된다.
```
