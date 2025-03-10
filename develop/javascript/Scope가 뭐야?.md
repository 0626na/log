# **Scope란?**

자바스크립트를 이해하기 위해 반드시 알아야 할 필수 개념 중 하나가 **Scope(스코프)**이다.  
Scope는 **변수나 함수가 어디에서 접근할 수 있는지를 결정하는 규칙**이다.  
이 개념을 제대로 이해하면, **변수가 어디서 선언되었고, 어디에서 접근 가능한지를 예측할 수 있으며, 코드의 실행 흐름을 더 깊이 이해할 수 있다.**

쉽게 말해, Scope는 **변수와 함수의 유효 범위를 정의하는 규칙**이며, 코드가 실행될 때 **어떤 변수가 어디에서 접근 가능한지를 결정한다.**

그러면 이 **유효 범위(Scope)는 언제 결정될까?**

---

## **자바스크립트의 컴파일 과정과 Scope**

Scope는 단순히 실행 중에 결정되는 것이 아니라, **자바스크립트 코드가 실행되기 전에 미리 결정된다.**  
자바스크립트 엔진이 코드를 실행하기 전, 대략적으로 다음과 같은 과정을 거친다.

1️⃣ **Tokenizing (토크나이징)** → 코드를 최소 단위(토큰)로 분리  
2️⃣ **Lexing (렉싱)** → 토큰 간의 문맥을 분석하여 의미를 부여  
3️⃣ **Parsing (파싱)** → AST(Abstract Syntax Tree, 추상 구문 트리) 생성  
4️⃣ **코드 실행** → 엔진이 바이트코드로 변환 후 실행

이 과정 중에서 **Scope는 Lexing 과정에서 결정된다.**  
Lexing 단계에서 **변수와 함수가 어디에서 선언되었는지를 기반으로, 해당 변수의 Scope가 어디에 속할지가 결정된다.**  
즉, 코드 실행 전에 이미 Scope가 결정되기 때문에, 이를 **Lexical Scope(렉시컬 스코프)** 라고 한다.

---

## **Lexical Scope**

Lexical Scope란, **"변수와 함수의 유효 범위가 코드가 실행되는 순간이 아니라, 코드가 작성된 위치에 따라 결정된다"**는 개념이다.  
즉, **자바스크립트는 실행 중에 Scope를 동적으로 변경하지 않으며, 변수의 유효 범위는 선언된 위치를 기준으로 정해진다.**

### ✅ **예제 코드**

```js
function outer() {
  let a = 10;
  function inner() {
    console.log(a); // ✅ outer()의 스코프에서 'a'를 참조 가능
  }
  inner();
}
outer();
```

✔ `inner()` 함수는 `outer()` 함수 내부에서 선언되었기 때문에, **렉싱 과정에서 이미 `a` 변수를 사용할 수 있도록 Scope가 결정된다.**  
✔ 즉, 실행될 때 변수를 찾는 것이 아니라, **코드를 작성한 위치에서 Scope가 결정된다.**

---

## **Environment Record & Lexical Environment**

Scope는 개념적인 설명을 위한 용어이고, **자바스크립트 엔진이 실제로 Scope를 구현하는 방식**은 다음과 같이 이루어진다.

### ✅ **실제 엔진 내부에서 Scope를 처리하는 방식**

✔ **Environment Record** → 변수와 함수 선언 정보를 저장하는 곳  
✔ **Lexical Environment** → 현재 Scope의 Environment Record + 외부 Scope의 Environment Record

즉, **Lexical Environment는 현재 실행 컨텍스트의 일부이며, 현재 코드에서 선언된 변수와 상위 Scope를 참조할 수 있도록 한다.**  
이 개념을 바탕으로 자바스크립트는 **Scope Chain**을 구성한다.

---

## **Scope Chain**

Scope Chain은 **어떤 변수가 사용될 때, 해당 변수가 어디에 선언되었는지를 찾아가는 과정**을 의미한다.  
자바스크립트는 **안쪽에서 바깥쪽으로 변수를 찾는다.**  
즉, 함수 내부에서 변수를 찾을 때, **현재 Scope에 없으면 상위 Scope로 계속해서 탐색하는 구조**이다.

### ✅ **예제 코드**

```js
function outer() {
  let a = 10;
  function inner() {
    let b = 20;
    console.log(a + b); // ✅ a는 outer()에서 찾고, b는 inner()에서 찾음
  }
  inner();
}
outer();
```

✔ `inner()` 내부에서 `a`를 찾으려고 할 때, **현재 Scope(inner)에서 못 찾으면 상위 Scope(outer)에서 찾음.**  
✔ **이렇게 변수를 찾는 과정을 "Scope Chain"이라고 한다.**

---

## **함수 선언 vs 함수 표현식**

자바스크립트에서 함수를 정의하는 방법은 크게 두 가지가 있다.

1️⃣ **함수 선언문 (Function Declaration)**

```js
function foo() {
  return "Hello";
}
console.log(foo()); // ✅ 정상 실행 (호이스팅됨)
```

✔ 함수 선언문은 **Lexing 과정에서 미리 Scope에 등록되므로, 선언 위치보다 위에서 호출해도 정상적으로 실행된다.**

2️⃣ **함수 표현식 (Function Expression)**

```js
console.log(bar()); // ❌ TypeError: bar is not a function

var bar = function () {
  return "Hi";
};
```

✔ 함수 표현식의 경우, **변수만 호이스팅되고 함수 자체는 값으로 할당되므로, 선언 이전에 호출하면 TypeError가 발생한다.**

💡 **즉, 함수 선언문은 호이스팅되지만, 함수 표현식은 변수를 undefined로 초기화하기 때문에 실행 전에 접근할 수 없다.**

---

## **var vs let vs const**

Scope를 이해하면 `var`, `let`, `const`의 차이를 명확히 알 수 있다.

| 키워드  | Scope 기준  | TDZ(일시적 사각지대) | 호이스팅 방식                              |
| ------- | ----------- | -------------------- | ------------------------------------------ |
| `var`   | 함수 스코프 | ❌ 없음              | 선언만 호이스팅, 값은 `undefined`로 초기화 |
| `let`   | 블록 스코프 | ✅ 있음              | 선언 전에 접근하면 `ReferenceError` 발생   |
| `const` | 블록 스코프 | ✅ 있음              | 선언 전에 접근하면 `ReferenceError` 발생   |

✔ `var`는 함수 스코프를 기준으로 동작하며, 블록 `{}`을 무시한다.  
✔ `let`과 `const`는 블록 스코프를 가지며, 선언 전에 접근하면 TDZ(일시적 사각지대)로 인해 ReferenceError가 발생한다.

---

## **📌 정리**

✅ Scope는 **변수와 함수가 어디에서 접근 가능한지를 결정하는 규칙**이다.  
✅ Scope는 **Lexing 과정에서 결정되며, 실행 중에 변경되지 않는다 (Lexical Scope).**  
✅ 자바스크립트 엔진에서는 Scope를 **Environment Record와 Lexical Environment**를 사용해 관리한다.  
✅ 변수의 유효 범위를 이해하면, **호이스팅과 TDZ(일시적 사각지대)를 포함한 변수 선언 방식의 차이**를 알 수 있다.

---

## **📌 Reference**

- 책: _You Don't Know JS - 스코프와 클로저_
