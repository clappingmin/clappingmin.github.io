---
title: "[코어 자바스크립트] 02 실행 컨텍스트"
description: "코어 자바스크립트 02 실행 컨텍스트를 읽고 내용을 요약했습니다."
author: "clappingmin"
date: 2023-03-25 20:00:00 +0800
categories: [Books, 코어 자바스크립트]
tags: [책, 코어 자바스크립트]
image:
  path: https://github.com/clappingmin/interactive-web/assets/50855726/ee184b74-b992-4e24-8f5c-c32f96dad310
  alt: 코어 자바스크립트.
---

# [코어 자바스크립트] 02 실행 컨텍스트

{: .mt-4 .mb-0 }

## 01 실행 컨텍스트란?

- 실행 컨텍스트: 실행할 코드에 제공할 환경 정보들을 모아놓은 객체
  - 자바스크립트는 실행 컨텍스트가 활성화되는 시점에 선언된 **변수를 위로 끌어올리고(호이스팅)**, **외부 환경 정보를 구성하고**, **this 값**을 설정한다.
  - 실행 컨텍스트를 콜 스택에 쌓아올렸다가, 가장 위에 쌓여있는 컨텍스트와 관련 있는 코드들을 실행하는 식으로 코드의 순서 보장
  - 실행 컨텍스트를 구성하는 방법 : 전역공간, eval(), 함수 실행

```javascript
//------------------ (1)
var a = 1;
function outer() {
  function inner() {
    console.log(a); // undefined
    var a = 3;
  }
  inner(); //------------------ (2)
  console.log(a); // 1;
}

outer(); //------------------ (3)
console.log(a); // 1
```

1. 처음 자바스크립트 코드를 실행하는 순간(1) 전역 콘텍스트가 콜 스택에 담김. **_별도의 실행 명령 없이도 브라우저에서 자동으로 실행함_**
2. (3)에서 outer 함수를 호출하면 자바스크립트 엔진은 outer에 대한 환경 정보를 수집해서 outer 실행 컨텍스트 생성 후, 콜 스택에 담음
3. 콜 스택 맨 위에는 outer 실행 컨텍스트라서 전역 컨텍스트와 관련된 실행들을 중단하고 outer 실행 컨텍스트와 관련된 코드(outer 함수 내부 코드)를 실행
4. (2)에서 inner 함수의 실행 컨텍스트가 가장 위에 담기면 outer 실행 컨텍스트와 관련된 실행을 중단. inner 함수 내부의 코드를 실행
5. inner 함수의 a에 3 할당하는 코드가 끝나면 inner 함수 실행이 종료되면서 inner 실행 컨텍스트가 제거됨
6. 그러면 outer 실행 컨텍스트가 콜 스택 가장 위에 오게되면서 중단했던 outer 실행 컨텍스트와 관련된 코드를 순차적으로 진행
7. outer 함수의 실행이 끝나면 outer 실행 컨텍스트가 제거되면서 전역 실행 컨텍스트가 콜 스택 가장 위에 옴
8. 그러면 전역 실행 컨텍스트에서 중단했던 다음줄 a를 출력하고 나면 실행 코드가 없게 됨
9. 콜 스택에서 전역 컨텍스트 제거. 콜 스택은 비어 있는 상태로 종료

**콜 스택에 실행 컨텍스트가 쌓이는 순간 = 현재 실행할 코드에 관여하게 되는 시점.**

### 실행 컨텍스트의 수집 정보

- _VariableEnvironment (변수 환경)_ : 현재 컨텍스트 내의 식별자들에 대한 정보 + 외부 환경 정보. 선언 시점의 LexicalEnvironment의 스냅샷으로 변경 사항을 반영되지 않음
- _LexicalEnvironment (렉시컬 환경)_: 처음에는 VariableEnvironment와 같지만 변경 사항이 실시간으로 반영됨
- _ThisBinding_ : this 식별자가 바라봐야 할 대상 객체

---

## 02 VariableEnvironment

- VariableEnvironment : 실행 컨텍스트를 생성할 때 VariableEnvironment에 정보를 먼저 담은 다음, 이를 복사해서 LexicalEnvironment를 만들고, 이후에는 LexicalEnvironment를 주로 활용함.
- **최초 실행 시 VariableEnvironment와 LexicalEnvironment은 동일한 내용을 가지지만 LexicalEnvironment은 동적으로 변하는 반면 VariableEnvironment은 최초의 스냅샷을 유지한다는 점이 다름**

---

## 03 LexicalEnvironment

### environment와 호이스팅

- environmentRecord는 현재 컨텍스트와 관련된 코드의 식별자 정보들이 저장된다.
- 컨텍스트를 구성하는 삼수에 지정된 매개변수 식별자, 선언한 함수가 있을 경우 그 함수 자체, var로 선언된 변수의 식별자 등
- 컨텍스트 내부 전체를 처음부터 끝까지 쭉 훑어나가며 순서대로 수집
- 실행 컨텍스트가 관여할 코드들이 실행 전에 변수 정보를 수집하는 과정을 모두 마침
  - 자바스크립트 엔진이 이미 해당 환경에 속한 코드의 변수명들을 모두 알고 있는 셈
  - 호이스팅 (hoisting) : 실행 컨텍스트가 생성되고 실행되기 전에 실행된다.
  - 마치 변수 선언이 코드의 맨 처음 부분으로 끌어올려진 것처럼 보임

#### 호이스팅 규칙

```javascript
function a(x) {
  // 수집 대상 1 (매개 변수)
  console.log(x); // 1
  var x; // 수집 대상 2 (변수 선언)
  console.log(x); // 1
  var x = 2; // 수집 대상 3 (변수 선언)
  console.log(x); // 2
}

a(1);
```

- environmentRecord는 현재 실행될 컨텍스트의 대상 코드의 식별자에만 관심이 있고 식별자에 어떤 값이 할당될지는 관심이 없음
- 변수 선언만 끌어올리고 할당 과정은 그대로 남겨져 있음 (매개 변수도 마찬가지)

```javascript
function a() {
  var x;
  var x;
  var x;

  x = 1;
  console.log(x); // 1
  console.log(x); // 1
  x = 2;
  console.log(x); // 2
}

a(1);
```

1. 변수 x 선언 : 메모리에 저장공간 확보 후 확보한 주솟값을 변수 x와 연결
2. 반복되는 변수 x 선언(var x;)은 이미 선언된 변수가 있으니 무시
3. x에 1 할당 : 메모리 공간에 1을 담고, 변수 x 메모리 공간에 1을 가리키는 메모리 주솟값을 입력
4. 1 출력
5. x에 2 할당 : 메모리 공간에 를 담고, 변수 x 메모리 공간으로 간다. 이미 3번에서 메모리 공간에 1을 가리키는 메모리 주솟값을 넣어줬는데 이걸 2의 주솟값으로 변경
6. 2 출력 후 a 함수 실행 컨텍스트 종료. 콜 스택에서 a 함수 실행 컨텍스트가 제거됨

다른 호이스팅 예제

```javascript
function a() {
  console.log(b);
  var b = "bbb"; // 수집 대상 1 (변수 선언)
  console.log(b);
  function b() {} // 수집 대상 2 (함수 선언)
  console.log(b);
}

a();
```

- **함수 선언은 변수와 달리 함수 전체를 끌어올린다.**

#### 함수 선언문과 함수 표현식

둘 다 함수를 정의할 때 쓰이는 방식

- 함수 선언문 : 함수 정의부만 존재하고 별도의 할당 명령이 없음. 반드시 함수명을 정의해야 함
- 함수 표현식 : 정의한 function을 별도의 변수에 할당하는 것. 함수명을 정의 안 해도 됨

- 함수명을 정의한 함수 표현식 : 기명 함수 표현식 (주의 : 외부에서 함수명으로 함수 호출할 수 없음, 함수 내부에서는 가능)
- 함수명을 정의하지 않은 표현식 : 익명 함수 표현식

```javascript
console.log(sum(1, 2));
console.log(multiply(3, 4));

function sum(a, b) {
  // 함수 선언문
  return a + b;
}

var multiply = function (a, b) {
  // 함수 표현식
  return a * b;
};
```

- sum 함수는 선언 전에 호출해도 아무 문제 없이 실행된다.
- multiply 함수는 선언 전에 사용하면 오류가 발생한다.

### 스코프, 스코프 체인, outerEnvironmentRefrence

- 스코프 : 식별자에 대한 유효범위
- 스코프 체인 : 식별자의 유효범위(스코프)를 안에서부터 바깥으로 차례로 검색해 나가는 것
- LexicalEnvironment 구조 : 첫 번째 인자 environmentRecord, 두 번째 인자 outerEnvironmentReference

#### 스코프 체인

outerEnvironmentReference는 현재 호출된 함수가 선언될 당시의 LexicalEnvironment를 참조한다.

```javascript
var a = 1;
var outer = function () {
  var inner = function () {
    console.log(a);
    var a = 3;
  };
  inner();
  console.log(a);
};
outer();
console.log(a);
```

1. 코드 실행 즉시 전역 컨텍스트 활성화. 전역 컨텍스트의 environmentRecord에 {a, outer} 식별자 저장. 전역 컨텍스트는 선언 시점이 없으므로 outerEnvironmentReference에 아무것도 없음
2. a에 1을 outer에 함수를 할당
3. outer 함수 호출 ouer 실행 컨텍스트 생성. 전역 실행 컨텍스트의 코드는 멈추고, outer 실행 컨텍스트가 활성화되면서 outer 함수 내부 코드가 실행
4. outer 실행 컨텍스트의 environmentRecord에 {inner} 식별자 저장. outerEnvironmentReference에는 outer 함수가 선언될 당시의 LexicalEnvironment가 담긴다. 즉 outer 함수는 전역 공간에서 선언되었으므로 전역 컨텍스트의 LexivalEnvironment가 outerEnvironmentReference에 담긴다.
5. outer 스코프 내부의 inner 변수에 함수 할당
6. inner 함수 호출. outer 실행 컨텍스트의 코드는 멈추고 inner 실행 컨텍스트가 생성되면서 활성화 되면서 inner 함수 내부 코드가 실행
7. inner 실행 컨텍스트의 environmentRecord에 {a} 식별자 저장. outerEnvironmentReference에는 outer 함수의 LexivalEnvironment가 담김
8. inner 함수 내부에서 a를 출력 : inner 컨텍스트의 environmentRecord에서 a를 검색 a가 있으나 a에 아직 할당된게 없으므로 undefined 출력
9. inner 함수 코드 종료 inner 실행 컨텍스트 콜 스택에서 제거되면서 바로 아래 outer 실행 컨텍스트 활성화
10. 식별자 a 접근 : 이때 자바스크립트 엔진은 활성화된 실행 컨텍스트의 LexicalEnvironment에 접근해서 environmentRecord에 a가 있는지 찾음. 없으면 outerEnvironmentReference에 있는 environmentRecod로 넘어가는 식으로 계속해서 검색함 전역 LexivalEnvironment에 a가 있으니 a에 저장된 1 출력
11. outer 함수 종료. outer 실행 컨텍스트가 종료되면서 콜 스택에서 제거 전역 실행 컨텍스트가 활성화
12. 식별자 a 접근 : 전역 실행 컨텍스트의 environmentRecord에서 a 검색.

✅요약

- 전역 컨텍스트 → outer 컨텍스트 → inner 컨텍스트 순으로 규모는 작아지지만, 스코프 체인을 타고 접근 가능한 변수의 수는 늘어남
- 식별자 a는 전역 공간에서도 선언했고 inner 한수 내부에서도 선언한 경우 inner 함수 내부에서 a에 접근하려고 하면 inner 스코프의 LexicalEnvironment부터 검색함. 즉 LexicalEnvironment에 존재하면 스코프체임 검색 중단 후 a 반환
- 이런 식으로 함수 내부에서 전역 변수의 이름과 동일하게 선언해서 전역 변수에 접근할 수 없는 것을 **변수 은닉화**라고 한다.

#### 전역변수와 지역변수

- 전역변수 : 전역 공간에서 선언한 변수
- 지역변수 : 함수 내부에서 선언한 변수

---

## 04 this

- 실행 컨텍스트의 thisBinding에는 this로 지정된 객체가 저장된다.
- 실행 컨텍스트 활성화 당시에 this가 지정되지 않은 경우 this에는 *전역 객체*가 저장된다.

---

## 05 정리

- 실행 컨텍스트 : 실행할 코드에 제공할 환경 정보들을 모아놓은 객체
- 실행 컨택스트 객체가 활성화되는 시점에 VariableEnvironment, LexicalEnvironment, thisBinding 세 가지 정보를 수집
- 호이스팅 : environmentRecord의 수집 과정을 추상화한 개념
- 스코프 : 변수의 유효 범위
- this : 실행 컨텍스트를 활성화하는 당시에 지정된 this가 저장됨. 지정하지 않을 경우 전역 객체가 저장됨
