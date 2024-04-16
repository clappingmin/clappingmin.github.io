---
title: "[코어 자바스크립트] 04 콜백 함수"
description: "코어 자바스크립트 04 콜백 함수를 읽고 내용을 요약했습니다."
author: "clappingmin"
date: 2024-04-08 21:33:00 +0800
categories: [Books, 코어 자바스크립트]
tags: [책, 코어 자바스크립트]
image:
  path: https://github.com/clappingmin/interactive-web/assets/50855726/ee184b74-b992-4e24-8f5c-c32f96dad310
  alt: 코어 자바스크립트.
---

# [코어 자바스크립트] 04 콜백 함수

{: .mt-4 .mb-0 }

## 01 콜백 함수란?

- 콜백 함수 : 다른 코드의 인자로 넘겨주는 함수
  - 다른 코드(함수 또는 메서드)에게 인자로 넘겨줌으로써 그 **제어권도 함께 위임한 함수**.
  - 코드는 자체적인 내부 로직에 의해 콜백 함수를 적절한 시점에 실행할 것

## 02 제어권

### 4-2-1 호출 시점

```javascript
var count = 0;
var cbFunc = function () {
  console.log(count);

  if (++count > 4) clearInterval(timer);
};

var timer = setInterval(cbFunc, 300);
```

- 위의 코드는 0.3초마다 count가 찍히고 4가 출력된 후 종료된다.
- setInterval이라고 하는 '다른 코드'에게 첫 번째 인자로 cbFunc 함수를 넘겨주면 제어권을 받은 setInterval이 스스로 판단에 따라 적절한 시점에(0.3초마다) 이 익명 함수를 시행했다.
- 이처럼 콜백 함수의 제어권을 넘겨받은 코드는 **콜백 함수 호출 시점에 대한 제어권을 가진다**.

### 4-2-2 인자

- map 메서드는 첫 번째 인자로 콜백 함수와 두 번째 인자로 생략가능한 this를 받아서 새로운 배열을 리턴한다.
- map 메서드의 규칙에는 전달 될 **인자들의 순서**도 포함되어 있다.
- 콜백 함수의 제어권을 넘겨받은 코드는 **콜백 함수를 호출할 때 인자에 어떤 순서로 넘길 것인지에 대한 제어권을 가진다**.

### 4-2-3 this

- 콜백 함수도 함수라서 기본적으로 this는 전역객체를 참조하는데, 제어권을 넘겨받을 코드에서 콜백 함수에 별도로 this가 될 대상을 지정한 경우에는 그 대상을 참조한다.

---

## 03 콜백 함수는 함수다

````javascript
var obj = {
  vals: [1, 2, 3],
  logvalues: function (v, i) {
    console.log(this, v, i);
  }
};

obj.logvalues(1, 2); // { vals: [ 1, 2, 3 ], logvalues: [Function: logvalues] } 1 2

[4, 5, 6].forEach(obj.logvalues); // window {...} 4 0```
````

- obj 객체의 logvalues는 객체이다.
- 메서드로서 호출할 때는 this는 obj 객체를 가리킨다.
- forEach의 콜백함수로 부른 경우 obj의 메서드로 호출된 게 아니라 함수만 전달된 것이라서 this는 전역 객체를 가리킨다.

---

## 04 콜백 함수 내부의 this에 다른 값 바인딩하기

- 객체의 메서드를 콜백 함수로 전달하면 해당 객체를 this로 바라볼 수 없다.
- 인자로 this를 받는 함수인 경우 원하는 this를 넘겨주면 되는데 없는 경우에는 어떻게 해야할까?

### 1. 전통적인 방법 : this를 다른 변수에 담아 콜백 함수로 활용할 함수에서는 this 대신 그 변수를 사용하게 함

```javascript
var obj1 = {
  name: "obj1",
  func: function () {
    var self = this;
    return function () {
      console.log(self.name);
    };
  }
};

var callback = obj1.func(); // 이 시점에서 this가 obj1이 된다.
setTimeout(callback, 1000);
```

- callback 변수에 넣어줄 때 this가 obj1이 됨. 함수를 호출한 결과로 내부 함수가 리턴. self에는 obj1이 들어가 있음
  내부 함수 (클로저)를 리턴하는 방법
- 이 방법은 번거롭고 차라리 this를 안쓰는 편이 나음

### 2. 콜백 함수 내부에서 this를 사용하지 않은 경우

```javascript
var obj1 = {
  name: "obj1",
  func: function () {
    console.log(obj1.name);
  }
};

setTimeout(obj1.func, 1000);
```

- 이 같은 경우 func 내부에 obj1을 고정해서 다른 객체를 바라보게 할 수 없음

### 3. 1번 방법의 함수 재활용

```javascript
// 1번 방식 (클로저) 코드 ...

var obj2 = {
  name: "obj2",
  func: obj1.func
};

var callback2 = obj2.func();
setTimeout(callback2, 300);

var obj3 = { name: "obj3" };
var callback3 = obj1.func.call(obj3);
setTimeout(callback3, 300);
```

- callback2는 obj2의 func (obj1.func을 복사한 함수)를 실행한 결과를 담아 콜백으로 사용
- callback3는 call 메서드를 사용해서 obj1.func이 실행될 때 this를 obj3로 지정

### 4. 전통적인 방식의 아쉬움을 보완하는 방법 : bind 메서드

```javascript
var obj1 = {
  name: "obj1",
  func: function () {
    console.log(this.name);
  }
};

setTimeout(obj1.func.bind(obj1), 1000);

var obj2 = { name: "obj2" };
setTimeout(obj1.func.bind(obj2), 1000);
```

---

## 05 콜백 지옥과 비동기 제어

- 콜백 지옥 : 콜백 함수를 익명 함수로 전달하는 과정이 반복되어 코드의 들여쓰기 수준이 깊어지는 현상
  - 주로 이벤트 처리나 서버 통신과 같이 비동기적인 작업을 수행하기 위해 이런 형태가 자주 등장
- 비동기 : 동기의 반대말. 현재 실행 중인 코드의 완료 여부와 무관하게 즉시 다음 코드로 넘어감

- 동기 : CPU에 의해 즉시 처리가 가능한 대부분의 코드
- 비동기 : 별도의 요청, 실행 대기, 보류와 관련된 코드

### 콜백 함수의 해결법

#### 1. 익명 함수를 기명함수로 변환

- 가독성을 높일 수 있다.
- 하지만 일회성 함수를 전부 변수에 할당하는 것이 만족스럽지 않다.

#### 2. ES6의 Promise 사용

- Promise의 인자로 넘겨주는 콜백 함수는 호출할 때 바로 실행되지만 내부의 resolve 또는 reject 함수를 호출하는 구문이 있을 경우 둘 중 하나가 실행되기 전까지는 다음(then)으로 넘어가지 않는다.

#### 3. ES2017 async/await

- 비동기 작업을 수행할 함수에 async를 앞에 붙이고 함수 내부에 비동기 작업이 필요한 위치마다 await를 표기한다.

---

## 06 정리

- 콜백 함수는 다른 코드에 인자로 넘겨주면서 제어권도 함께 위임한 함수
- 위임 받은 제어권
  - 콜백함수를 호출하는 시점
  - 콜백 함수를 호출할 때 인자로 넘겨줄 값들 및 순서가 정해져 있다
  - this가 정해져 있는 경우도 있으나, 그렇지 않은 경우 콜백 함수도 함수라서 this로 전역 객체를 가진다. this를 지정하고 싶으면 bind 메서드를 사용하자
- 어떤 함수에 인자로 메서드를 전달해도 메서드가 아닌 함수로 실행한다.
- 비동기 제어를 위해 함수를 사용할 때 콜백 지옥에 빠지기 쉬운데 Promise나 async/await를 사용해서 적절히 제어하자.
