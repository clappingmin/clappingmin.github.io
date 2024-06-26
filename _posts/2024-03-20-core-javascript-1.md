---
title: "[코어 자바스크립트] 01 데이터 타입"
description: "코어 자바스크립트 01 데이터 타입을 읽고 내용을 요약했습니다."
author: "clappingmin"
date: 2024-03-20 21:33:00 +0800
categories: [Books, 코어 자바스크립트]
tags: [책, 코어 자바스크립트]
image:
  path: https://github.com/clappingmin/interactive-web/assets/50855726/ee184b74-b992-4e24-8f5c-c32f96dad310
  alt: 코어 자바스크립트.
---

# [코어 자바스크립트] 01 데이터 타입

{: .mt-4 .mb-0 }

## 01 데이터 타입의 종류

- 기본형
  - number
  - string
  - boolean
  - null
  - undefined
  - Symbol (ES6에서 추가됨)
- 참조형
  - object
  - Array
  - Function
  - Date
  - 정규표현식 (RegExp)

---

## 02 데이터 타입에 관한 배경지식

- 비트 : 0 또는 1만 표현할 수 있는 하나의 메모리 조각
- 바이트 : 8개의 비트로 구성. 256(2^8)개의 값을 표현할 수 있다

자바스크립트에서 숫자의 경우 정수형인지 부동소수형인지를 구분하지 않고 **64비트**, 즉 **8바이트를** 확보한다. 모든 데이터는 바이트 단위의 식별자, 즉 **메모리 주솟값**을 통해 서로 구분하고 연결할 수 있다.

- 변수 : 변할 수 있는 무언가. (무언가 = 데이터)
- 식별자 : 어떤 데이터를 식별하는 데 사용하는 이름. **변수명**

---

## 03 변수 선언과 데이터 할당

### 변수 선언

- 변수 : 변경 가능한 데이터가 담길 수 있는 공간 또는 그릇

### 데이터 할당

```javascript
var a = "abc";
```

![원시값의 데이터 할당](https://github.com/clappingmin/interactive-web/assets/50855726/1d62e0a5-5fa0-473b-b2c4-aafaf844c49d)

1. 변수 영역에서 빈 공간(@1003)을 확보한다.
2. 확보한 공간의 식별자를 a로 지정한다.
3. 데이터 영역의 빈 공간(@5004)에 문자열 'abc'를 지정한다.
4. 변수 영역에서 a라는 식별자를 검색한다. (@1003)
5. 앞서 저장한 문자열의 주소(@5004)를 @1003의 공간에 대입한다.

이렇게 값을 직접 대입하지 않고 굳이 번거롭게 한 단계를 거치는 이유

- 메모리를 효율적으로 관리하기 위해서. 변수 a에 재할당할 문자열이 기존의 메모리 보다 클 경우 확보된 메모리 공간을 늘리는 작업이 필요하다. 그런 불필요한 작업 대신 별도의 공간에 저장하고 그 주소를 변수 공간과 연결하는 것이 효율적이기 때문

---

## 04 기본형 데이터와 참조형 데이터

### 불변값

- 변수 : 변경 가능
- 상수 : 변경 불가능 > 둘의 차이는 변경 가능성

- 변수와 상수를 구분할 때는 **변수 영역 메모리** (데이터를 재할당 할 수 있는지의 여부)
- 불변성 여부를 구분할 때의 가능성의 대상은 **데이터 영역 메모리**
- **기본형 데이터는 모두 불변값**

```javascript
var a = "abc";
a = a + "def";

var b = 5;
var c = 5;
b = 7;
```

- a + 'def'의 경우 기존에 저장되어 있던 'abc'를 변경하는 것이 아닌 새로운 문자열 'abcdef'를 만들어 그 주소를 a에 저장한다.
- b = 5; 의 경우 컴퓨터가 데이터 영역에서 5를 찾고 5가 없으면 데이터 공간에 5를 저장한다. 그 후 5가 저장된 주소를 b에 저장한다.
- c = 5; 의 경우 컴퓨터가 데이터 영역에서 5를 찾는데 이미 만들어놓은 값이 있으니 그 주소를 재활용한다.
- b = 7; 의 경우 기존의 5 자체를 7로 변경하는 것이 아니라, 데이터 영역에서 7을 찾고 7이 없으면 7을 저장 후 b에 저장한다.
- 이처럼 문자열 값도, 숫자도 한 번 만든 값을 변경하는 것이 아니라 다른 값을 새로 만드는 방식이다. (**불변성**)

### 가변값

참조형 데이터는 기본적으로는 가변값인 경우가 많지만 설정에 따라 변경 불가능한 경우도 있고, 아예 불변값으로 활용하는 방법도 있다.

```javascript
var obj1 = { a: 1, b: "bbb" };
```

![가변값의할당](https://github.com/clappingmin/interactive-web/assets/50855726/a0518c8c-019c-4b83-85d0-6e31cbf7bc5e)

1. 변수 영역의 빈공간(@1002) 확보, 주소 이름은 obj1
2. 임의의 데이터 공간(@5001)에 데이터를 저장하려고 보니 데이터 그룹이다. 그룹의 내부 프로퍼티들을 저장하기 위해 변수 영역의 공간에 프로퍼티들 저장. 임의의 데이터 공간에는 내부 프로퍼티가 저장된 변수 영역의 주소 저장
3. @7103, @7104에 각 a,b 프로퍼티 이름 지정
4. 데이터 영역에서 1 검색, 없으니 데이터 공간(@5003)에 1 저장하고 @7103에 이 주소(@5003) 저장, b도 마찬가지로 'bbb'를 데이터 공간에서 검색 후 없으니 @5004에 저장후 이 주소를 @7104에 저장

{: .mt-4 .mb-4 }

```javascript
obj1.a = 2;
```

![참조값의 데이터 변경](https://github.com/clappingmin/interactive-web/assets/50855726/75452c88-67a7-41d7-8402-a3a5c33d4e3f)

1. 데이터 영역에서 2를 검색, 없으니 @5005에 2를 저장
2. @7103이 @5005를 저장
3. **변수 obj1이 가진 메모리 영역 주소 @5001은 변하지 않음. 즉 새로운 객체가 만들어진 것이 아닌 기존의 객체의 내부 값이 변경된 것**

중첩된 경우도 살펴보자

![참조값이 중첩된 경우](https://github.com/clappingmin/interactive-web/assets/50855726/2e5e18c5-8d2b-457d-b548-c4515fbdbe27)

- 재할당 명령시 @5003을 참조하는 변수가 하나도 없게 된다. 참조 카운트가 0인 메모리 주소는 가비지 컬렉터의 수거 대상이 된다. 가비지 컬렉터는 특정 시점이나 메모리 사용량이 포화 상태로 임박할 때마다 수거 대상들을 수거한다.

### 변수 복사 비교

```javascript
// 변수 복사 과정
var a = 10;
var b = a;

var obj1 = { c: 10, d: "ddd" };
var obj2 = obj1;

// 변수 복사 이후 값 변경
b = 15;
obj2.c = 20;
```

![변수 복사 비교](https://github.com/clappingmin/interactive-web/assets/50855726/ffd65175-cb8d-4e9a-a893-713036e0f3b4)

기본형과 참조형 데이터의 가장 큰 차이점

- 기본형 데이터의 경우 복사한 변수의 데이터를 바꿨을 때 값이 달라짐
- 참조형 데이터는 복사한 변수의 프로퍼티를 변경해도 값이 달라지지 않음

근데 위의 참조형 예제의 경우는 객체의 내부 프로퍼티를 변경하는 예제이다. 만약 기본형처럼 아예 새로운 객체를 할당하면 어떻게 될까?

```javascript
obj2 = { c: 20, d: "ddd" };
```

- 새로운 주소값을 가진다. 즉 obj1과 데이터 값이 달라진다.
- **즉 참조형 데이터가 '가변값'이라고 설명할 때는 데이터 자체를 변경할 경우가 아니라 내부 프로퍼티를 변경할 때만 성립**

---

## 05 불변 객체

### 얕은 복사와 깊은 복사

- 얕은 복사 : 바로 아래 단계의 값만 복사하는 방법
- 깊은 복사 : 내부의 모든 값들을 하나하나 찾아서 전부 복사하는 방법

```javascript
const a = { a: 1, b: 2, c: { a: 1, b: 2 } };
const b = { ...a }; // 얕은 복사

b.a = 0;
b.c.a = 0; // 얕은 복사라서 원본도 변경됨

console.log(a.a === b.a); // false
console.log(a.c === b.c); // true
```

- 즉 얕은 복사에서 객체에 직접 속한 프로퍼티는 완전 새로운 데이터가 만들어진 반면, **한 단계 더 들어간 프로퍼티(c)는 기존 데이터를 그대로 참조**
- 그러니 객체의 모든 값을 복사해서 완전히 새로운 데이터를 만들고자 할 때(깊은 복사), 기본형 데이터일 경우에는 그대로 복사하면 되지만 **참조형 데이터는 다시 그 내부의 프로퍼티들을 복사해야 한다.**

---

## 06 undefined와 null

- 두 가지 다 "없음"을 나타냄
  **undefined는 사용자가 명시적으로 지정할 수도 있지만 자바스크립트 엔진이 특정한 경우에 부여함**

1. 값에 대입하지 않은 변수. 즉 데이터 영역의 메모리 주소를 지정하지 않은 식별자에 접근할 때 (빈 객체와 undefind는 다름)
2. 객체 내부의 존재하지 않는 프로퍼티에 접근하려고 할 때
3. return 문이 없거나 호출되지 않는 함수의 실행 결과

- 사용자가 명시적으로 부여한 undefined는 그 자체로 값으로 동작함.
- 자바스크립트 엔진이 반환해준 undefined는 해당 프로퍼티 또는 배열의 키값(인덱스)가 존재하지 않음을 의미한다. 전자는 데이터이고 후자는 말 그대로 데이터 없음을 의미한다.

**undefind는 사용자의 통제를 벗어난 경우, 즉 자바스크립트 엔진이 반환하는 경우에만 해당되게 하고 값이 없음을 할당해주고 싶으면 null을 사용하자.**

```javascript
console.log(typeof null); // object
```
