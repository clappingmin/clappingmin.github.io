---
title: "[코어 자바스크립트] 03 this"
description: "코어 자바스크립트 03 실행 this를 읽고 내용을 요약했습니다."
author: "clappingmin"
date: 2023-04-01 20:00:00 +0800
categories: [Books, 코어 자바스크립트]
tags: [책, 코어 자바스크립트]
image:
  path: https://github.com/clappingmin/interactive-web/assets/50855726/ee184b74-b992-4e24-8f5c-c32f96dad310
  alt: 코어 자바스크립트.
---

# [코어 자바스크립트] 03 this

{: .mt-4 .mb-0 }

## 01 상황에 따라 달라지는 this

this는 실행 컨텍스트가 생성될 때(함수가 호출될 때) 결정된다

### 전역 공간에서의 this

- 전역 공간에서 this는 전역 객체를 가리킨다.
- 전역 객체는 런타임 환경에 따라 다른 이름과 정보를 가진다.
  - 브라우저 환경에서는 window
  - Node.js 환경에서는 global
- **자바스크립트의 모든 변수는 실은 특정 객체의 프로퍼티로서 동작**

### 메서드로서 호출할 때 그 메서드 내부에서의 this

#### 함수 vs 메서드

함수와 메서드의 차이 : 독립성

- 함수는 그 자체로 독립적인 기능을 수행
- 메서드는 자신을 호출한 객체에 관한 동작을 수행

```javascript
// 함수로서 호출
var func = function (x) {
  console.log(this, x);
};
func(1); // global, 1

// 메서드로서 호출
var obj = {
  method: func
};
obj.method(2); // { method: func }, 2

// 메서드 호출방법 2가지
obj.method(1);
obj["method"](1);
```

#### 메서드 내부에서의 this

```javascript
var obj = {
  methodA: function () {
    return this; // obj
  },

  inner: {
    methodB: function () {
      return this; // obj.inner
    }
  }
};

console.log(obj.methodA() === obj); // true
console.log(obj.inner.methodB() === obj.inner); // true
```

### 함수로서 호출할 때 그 함수 내부에서의 this

- 함수를 함수로서 호출한 경우에는 this가 지정되지 않는다.
- 실행 컨텍스트를 활성화할 때 this가 지정되지 않는 경우 this는 전역 객체를 바라본다.

```javascript
var obj1 = {
  outer: function () {
    console.log(this);

    var innerFunc = function () {
      console.log(this);
    };
    innerFunc(); // 전역 객체

    var obj2 = {
      innerMethod: innerFunc
    };
    obj2.innerMethod(); // obj2
  }
};
obj1.outer(); // obj1
```

- 함수로서 호출한 innerFuncsms this가 지정되지 않았으므로, 스코프체인의 최상위 객체인 전역객체가 바인딩된다.

#### 메서드의 내부 함수에서의 this를 우회하는 방법

- 호출 주체가 없을 때는 자동으로 전역 객체를 바인딩 하지 않고 호출 당시 주변의 this를 그대로 상속받아 사용하고 싶다!

```javascript
var obj = {
  outer: function () {
    console.log(this); // obj

    var innerFunc = function () {
      console.log(this);
    };
    innerFunc(); // 전역 객체

    var self = this; // obj
    var innerFunc2 = function () {
      console.log(self);
    };

    innerFunc2(); // obj
  }
};
obj.outer(); // obj
```

- innerFunc의 this는 전역객체를 가리킨다.
- outer 스코프의 self는 obj.outer()의 this가 출력된다.
  - 보통 self를 많이 쓰는데 _this, that, _ 등도 변수명으로 사용해도 무방하다.

#### this를 바인딩하지 않는 함수

- ES6에서 함수 내부에서 this가 전역객체를 바라보는 문제를 보완하고자 this를 바인딩하지 않는 화살표 함수를 도입
- 화살표 함수는 실행 컨텍스트를 생성할 때 this 바인딩 과정 자체가 빠지게 되어, 상위 스코프의 this를 그대로 사용할 수 있다
- 즉 우회하지 않아도 된다!

> 추가 학습
>
> - 화살표 함수도 실행 컨텍스트를 생성하나 일반적인 함수와 달리 Lexical Environment를 갖지 않는다.
> - 따라서 화살표 함수는 상위 스코프의 Lexical Environment를 공유하고 자신만의 Lexical Environtment를 생성하지 않는다.
> - 그래서 화살표 함수는 실행 컨텍스트를 생성할 때 this를 동적으로 바인딩하지 않고 상위 스코프의 this를 참조한다.

```javascript
var obj = {
  outer: function () {
    console.log(this); // obj

    var innerFunc = () => {
      console.log(this);
    };
    innerFunc(); // obj
  }
};
obj.outer(); // obj
```

### 콜백 함수 호출 시 그 함수 내부에서의 this

- 함수 A의 제어권을 다른 함수(또는 메서드) B에게 넘겨주는 경우 함수 A를 콜백 함수라고 한다.
  - 이때 함수 A는 함수 B의 내부 로직에 따라 실행
  - this 역시 함수 B 내부 로직에서 정한 규칙에 따라 결정된다.
  - 콜백 함수도 함수라서 기본적으로 this는 전역객체를 참조하는데, 제어권을 받은 함수에서 별도로 this를 지정하는 경우 그 대상을 참조한다.
- 대표적인 콜백함수 : setTimeout, 배열의 forEach문, eventListener

### 생성자 함수 내부에서의 this

- 생성자 함수 : 어떤 공통된 성질을 지니는 객체들을 생성하는데 사용하는 함수
- 구체적인 인스턴스를 만들기 위한 틀이라고 생각하자
- **this는 인스턴스** (ex. choco, nabi)

```javascript
var Cat = function (name, age) {
  this.bark = "야옹";
  this.name = name;
  this.age = age;
  this.test = function () {
    return this;
  };
};

var choco = new Cat("초코", 7);
var nabi = new Cat("나비", 3);
console.log(choco, nabi);

console.log(nabi.test() === nabi); // true

/**
 * Cat { bark: '야옹', name: '초코', age: 7 }
 * Cat { bark: '야옹', name: '나비', age: 3 }
 */
```

---

## 명시적으로 this를 바인딩하는 방법

### call 메서드

- 첫 번째 인자 : this 바인딩할 대상
- 그 뒤의 인자 : 함수의 인자

```javascript
var func = function (a, b, c) {
  console.log(this, a, b, c);
};

func(1, 2, 3); // global, 1, 2, 3
func.call({ a: 1 }, 4, 5, 6); // { a: 1}, 4, 5, 6
```

### apply 메서드

- 기능적으로 call 메서드와 동일
- 함수의 인자를 배열로 받음

```javascript
var func = function (a, b, c) {
  console.log(this, a, b, c);
};

func(1, 2, 3); // global, 1, 2, 3
func.apply({ a: 1 }, [4, 5, 6]); // { a: 1}, 4, 5, 6

var obj = {
  a: 1,
  method: function (x, y) {
    console.log(this.a, x, y);
  }
};

obj.method(2, 3); // 1, 2, 3
obj.method.apply({ a: 0 }, [0, 0]); // 0, 0, 0
```

### call / apply 메서드의 활용

#### 활용 예시 1) 유사배열객체에서 배열 메서드 적용

```javascript
var obj = {
  0: "a",
  1: "b",
  2: "c",
  length: 3
};

Array.prototype.push.call(obj, "d");

var arr = Array.prototype.slice.call(obj);
console.log(arr); // [ 'a', 'b', 'c', 'd' ]
```

#### 활용 예시 2) 생성자 내부에서 다른 생성자를 호출

- 다른 생성자와 공통된 내용이 있을 경우 call 또는 apply를 이용해 다른 생성자를 호출하면 반복을 줄일 수 있다.

```javascript
function Person(name, gender) {
  this.name = name;
  this.gender = gender;
}

function Student(name, gender, school) {
  Person.call(this, name, gender);
  this.school = school;
}

function Employee(name, gender, company) {
  Person.apply(this, [name, gender]);
  this.company = company;
}

var by = new Student("보영", "female", "단국대");
var ja = new Employee("재민", "male", "구글");
```

#### 활용 예시 3) 여러 인수를 묶어 하나의 배열로 전달하고 싶을 때 - apply 사용

```javascript
var numbers = [10, 20, 3, 16, 45];
var max = Math.max.apply(null, numbers);
var min = Math.min.apply(null, numbers);
console.log(max, min); // 45,3
```

### bind 메서드

- call/apply 메서드처럼 바로 호출하지 않고 넘겨받은 this와 인수들을 바탕으로 새로운 함수를 반환하는 메서드
- bind 메서드를 사용해서 만든 메서드는 name 프로퍼티에 bound가 앞에 붙는다.

```javascript
var func = function (a, b, c, d) {
  console.log(this, a, b, c, d);
};

var bindFunc1 = func.bind({ a: 1 }, 5, 6, 7, 8);
bindFunc1(); // { a: 1 } 5 6 7 8

var bindFunc2 = func.bind({ a: 1 });
bindFunc2(0, 0, 0, 0); // { a: 1 } 0 0 0 0

var bindFunc3 = func.bind({ a: 1 }, 9, 9);
bindFunc3(0, 0); // { a: 1 } 9 9 0 0

var func = function (a, b, c, d) {
  console.log(this, a, b, c, d);
};

var bindFunc = func.bind({ a: 1 }, 4, 5);
console.log(func.name); // func
console.log(bindFunc.name); // bound func
```

#### 상위 컨텍스트의 this를 내부함수나 콜백 함수에 전달하기

- 함수로 호출한 경우 this는 전역
- bind로 상위 함수의 this를 전달

```javascript
var obj = {
  outer: function () {
    console.log(this);

    var innerFunction = function () {
      console.log(this);
    }.bind(this);

    innerFunction(); // obj
  }
};

obj.outer(); // obj
```

### 화살표 함수의 예외사항

- 화살표 함수는 함수의 실행 컨텍스트를 생성할 때 this를 동적으로 바인딩하지 않고 스코프상 가장 가까운 this를 정적으로 참조한다
- call/apply/bind를 굳이 사용하지 않아도 된다.

### 별도의 인자로 this를 받는 경우(콜백 함수 내에서의 this)

- ex) forEach, map, filter, find...

---

## 정리

- 전역 공간에서 this는 전역객체이다.
- 함수를 메서드로 호출한 경우 this는 호출 주체(메서드명 앞의 객체)이다.
- 함수를 함수로 호출한 경우 this는 전역 객체이다.
- 콜백 함수 내에서의 this는 콜백 함수의 제어권을 넘겨받은 함수가 정의한 바에 따르며, 정의하지 않은 경우 전역 객체이다.
- 생성자 함수에서 this는 생성된 인스턴스이다.
- call, apply는 this를 명시적으로 지정하면서 함수 또는 메서드를 호출한다.
- bind 메서드는 this 및 인자를 일부 지정해서 새로운 함수를 만든다.
- 요소를 순환하면서 콜백 함수를 반복 호출하는 일부 메서드는 별도의 인자를 this를 받기도 한다.
