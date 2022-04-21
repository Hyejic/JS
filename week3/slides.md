---
# try also 'default' to start simple
theme: seriph
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://source.unsplash.com/collection/94734566/1920x1080
# apply any windi css classes to the current slide
class: "text-center"
# https://sli.dev/custom/highlighters.html
highlighter: shiki
# show line numbers in code blocks
lineNumbers: false
# some information about the slides, markdown enabled
info: |
  ## Slidev Starter Template
  Presentation slides for developers.

  Learn more at [Sli.dev](https://sli.dev)
# persist drawings in exports and build
drawings:
  persist: false
---

# function, scope

12~13장 함수, 스코프

<div class="abs-br m-6 flex gap-2">
  <a href="https://github.com/yeon-bo" target="_blank" alt="GitHub"
    class="text-xl icon-btn opacity-50 !border-none !hover:text-white">
    <carbon-logo-github />
  </a>
</div>

<style>
h1 {
  display: inline-block;
  background-color: rgba(0, 0, 0, .75);
  background-size: 100%;
  border-radius: 10px;
  border: 1px solid #555;
  padding: 50px 50px;
}
p {
  position: absolute;
  top: 30%;
  left: 24%;
  font-size: 12px;
}
</style>

---

# 함수

함수란?

- **프로그램의 함수** - 일련의 과정을 문으로 구현하고 코드 블록으로 감싸는 하나의 실행 단위
- **매개변수** - 함수 내부로 입력을 전달받는 변수
- **인수** - 입력 값
- **반환값** - 출력 값
  <br>
  <br>
  **함수 정의**로 통해 생성, 인수를 매개변수를 통해 함수에 전달, 함수 실행을 지시하는 것은 **함수 호출**.

![출처: https://velog.io/@dev_jazziron/12](https://velog.velcdn.com/images%2Fdev_jazziron%2Fpost%2Ffc7a2ae7-10bc-4f21-93b3-dad837c7f50d%2Fimage.png)

<style>
h1 {
  background-color: #2B90B6;
  background-size: 100%;
  -webkit-background-clip: text;
  -moz-background-clip: text;
  -webkit-text-fill-color: transparent;
  -moz-text-fill-color: transparent;
}
img {
  margin: 0 auto;
}
</style>

---

# 함수

함수를 사용하는 이유

- 코드의 재사용으로 유지보수의 **편의성을 높이고** 실수를 줄여 코드의 **신뢰성을 높인다.**
- 함수는 객체 타입의 값이라 식별자를 붙일 수 있어 코드의 **가독성을 향상시킨다.**

<style>
ul {
  padding-top: 100px;
}
</style>

---

# 함수

함수 리터럴

- 함수는 객체 타입의 값이기에 함수도 **함수 리터럴로 생성**할 수 있다.
- 함수의 구성: **function 키워드, 함수 이름, 매개변수 목록, 함수 몸체**

- 함수는 객체다. 그러나 일반 객체와는 다르다.
  - **일반 객체와 차이점**
  1. 함수는 호출 가능.
  1. 함수 객체만의 고유한 프로퍼티 가짐.

---

# 함수

함수 리터럴

- **함수 이름**

1. 식별자이기에 식별자 네이밍 규칙을 준수해야 함.
1. 함수 몸체 내에서만 참조할 수 있는 식별자다.
1. 이름이 있는 기명 함수와 이름이 없는 무명(익명)함수로 나눠진다.

![출처: https://velog.io/@dev_jazziron/12](https://velog.velcdn.com/images%2Fdev_jazziron%2Fpost%2Ffc7a2ae7-10bc-4f21-93b3-dad837c7f50d%2Fimage.png)

<style>
ul {
  padding-top: 50px;
}
img {
  margin: 0 auto;
}
</style>

---

# 함수

함수 리터럴

- **매개변수 목록**

1. 0개 이상의 매개변수를 소괄호로 감싸고 쉼표로 구분.
1. 함수 호출시 인수 순서대로 할당되기에 순서에 의미가 있음.
1. 함수 몸체 내에서 변수와 동일 취급하기에 식별자 네이밍 규칙을 준수해야 함.

![출처: https://velog.io/@dev_jazziron/12](https://velog.velcdn.com/images%2Fdev_jazziron%2Fpost%2Ffc7a2ae7-10bc-4f21-93b3-dad837c7f50d%2Fimage.png)

<style>
ul {
  padding-top: 50px;
}
img {
  margin: 0 auto;
}
</style>

---

# 함수

함수 리터럴

- **함수 몸체**

1. 함수 호출 시 일괄적으로 실행될 문들을 하나의 실행 단위로 정의한 코드 블록.
1. 함수 호출에 의해 실행.

![출처: https://velog.io/@dev_jazziron/12](https://velog.velcdn.com/images%2Fdev_jazziron%2Fpost%2Ffc7a2ae7-10bc-4f21-93b3-dad837c7f50d%2Fimage.png)

<style>
ul {
  padding-top: 50px;
}
img {
  margin: 0 auto;
}
</style>

---

# 함수

함수 정의

- 함수 정의: 함수를 호출하기 이전에 매개변수와 실행할 문들, 변환할 값을 지정하는 것.
- 자바스크립트의 함수는 **일급 객체(값의 성질을 갖는 객체)**.
- 함수 정의 방식: **함수 선언문, 함수 표현식, Function 생성자 함수, 화살표 함수**

<style>
ul {
  padding-top: 50px;
}
</style>

---

# 함수

함수 정의

- **함수 선언문(Function Declaration)**

```ts
function add(x, y) {
  return x + y;
}

console.log(add(2, 5)); // 7
```

<br>
<br>

- 함수 선언문은 **함수 이름을 생략할 수 없다.**
- 함수 선언문은 **표현식이 아닌 문**이다.

---

# 함수

함수 정의

- **함수 선언문(Function Declaration)**
- 아래 식처럼 하면 변수에 할당되는 것처럼 보이는데 문맥에 따라 함수 선언문 또는 함수 리터럴 표현식으로 해석이 달라진다.

```ts
let add = function add(x, y) {
  return x + y;
};

console.log(add(2, 5)); // 7
```

1. **{}을 블록문과 객체 리터럴**에서 무엇으로 해석하는가.
   → {}가 단독으로 존재한다면 **블록문**으로 해석, 값으로 평가되어야하는 문맥(할당 연산자의 우변)에서 피연산자로 사용되면 **객체 리터럴로 해석**
1. **기명 함수 리터럴과 형태가 동일**하기에 어떻게 해석하는가.
   → 함수 이름이 있는 함수 리터럴을 단독으로 사용(피연산자로 사용X)하면 **함수 선언문**으로, 함수 리터럴이 값으로 평가되어야하는 문맥(변수에 할당하거나 피연산자로 사용)이면 **함수 리터럴 표현식**으로 해석.

---

# 함수

함수 정의

- **함수 선언문(Function Declaration)**

```ts
// 기명 함수 리터럴을 단독으로 사용하면 함수 선언문으로 해석
function foo() {
  console.log("foo");
}
foo()(
// foo

// 함수 리터럴을 피연산자로 사용하면 함수 리터럴 표현식으로 해석
( function bar() {
    console.log("foo");
  }
);
bar(); // ReferenceError: bar is not defined
// 그룹 연산자의 피연산자는 값으로 평가될 수 있는 표현식이여하기에 문인 함수 선언문은 피연산자로 사용할 수 없다.
```

---

# 함수

함수 정의

- **함수 선언문(Function Declaration)**
- 함수 이름은 함수 몸체 내에서만 참조할 수 있는 식별자라는 의미.
  → 함수를 가리키는 식별자가 없다. → 식별자가 없어 호출 불가능.

![출처: https://velog.io/@yoonique_garage/%EB%AA%A8.%EC%9E%90-DeepDive-09-%ED%95%A8%EC%88%98](https://velog.velcdn.com/images%2Fyoonique_garage%2Fpost%2F4d118751-c077-4ac4-9cca-2521b8bca815%2Fft.png)

<style>
img {
  width: 70%;
  margin: 0 auto;
}
</style>

---

# 함수

함수 정의

- **함수 선언문(Function Declaration)**
- **함수 선언문일 때**, 자바스크립트 엔진은 생성된 함수를 호출하기 위해 함수 이름과 동일한 이름의 식별자를 암묵적으로 생성하고, 거기에 함수 객체를 할당 함.
  **→ 함수는 함수 이름으로 호출하는 것이 아닌 함수 객체를 가리키는 식별자로 호출.**
- 자바스크립트 엔진은 함수 선언문을 함수 표현식으로 변환해 함수 객체를 생성.
  → 단, 함수 선언문과 함수 표현식이 **동일하게 동작하는 것은 아님**.

---

# 함수

함수 정의

- **함수 표현식(Function Expression)**

```ts
let add = function (x, y) {
  return x + y;
};

console.log(add(2, 5)); // 7
```

<br>
<br>

- 함수 표현식: 함수 리터럴로 생성한 함수 객체를 변수에 할당하는 정의 방식.
- 함수 리터럴의 **함수 이름을 생략 가능**(익명 함수).
- 함수 표현식은 **표현식인 문**

---

# 함수

함수 정의

- 함수 호이스팅: 함수 선언문이 코드의 선두로 끌어 올려진 것처럼 동작하는 자바스크립트 고유의 특징

```ts
// 함수 참조
console.dir(add); // f add(x, y)
console.dir(sub); // undefined

// 함수 호출
console.log(add(2, 5)); // 7
console.log(sub(2, 5)); // TypeError: sub is not a function

// 함수 선언문
function add(x, y) {
  return x + y;
}
// 함수 표현식
const sub = function (x, y) {
  return x + y;
};
```

---

# 함수

함수 정의

- 함수 선언문과 함수 표현식으로 정의한 함수는 각각 **생성 시점이 다름**.
- **함수 선언문**으로 함수를 정의하면
  런타임 이전에 객체 생성 → 식별자를 암묵적으로 생성 → 객체 할당까지 완료.
  즉, **함수 호이스팅이 발생**.
- **함수 호이스팅과 변수 호이스팅 차이**
  변수 호이스팅은 선언문 이전에 호출 시 undefined로 초기화로 인해 undefinde 평가,
  함수 호이스팅은 선언문 이전에 호출 시 함수 객체로 초기화로 함수 호출이 가능.
- 변수 할당문의 값은 할당문이 실행되는 시점, 즉 런타임에 평가되므로 함수 표현식의 함수 리터럴도 할당문이 실행되는 시점에 평가되어 함수 객체가 됨.
  → **함수 표현식**으로 함수를 정의할 시, 함수 호이스팅이 아닌 **변수 호이스팅 발생**.

---

# 함수

함수 정의

- **Function 생성자 함수**

```ts
let add = new Function('x', 'y', 'return x + y' };

console.log(add(2, 5)); // 7
```

<br>
<br>

- 생성자 함수: 객체를 생성하는 함수 방식 중 하나.
- Function 생성자 함수를 사용하는 것은 **비추천**.
  이유: **클로저를 생성하지 않음**, 선언문이나 표현식과는 **다르게 동작함**

---

# 함수

함수 정의

- **화살표 함수(ES6)**

```ts
let add = (x, y) => x + y;

console.log(add(2, 5)); // 7
```

<br>
<br>

- 화살표 함수는 기존의 함수보다 **표현과 내부 동작이 간략화** 되어 있음.
- 화살표 함수는
  1. **생성자 함수**로 사용할 수 **없음**.
  1. 기존 함수와 **this 바인딩 방식이 다름**.
  1. **prototype 프로퍼티가 없음**.
  1. **arguments 객체**를 생성하지 **않음**.

---

# 함수

함수 호출

- **매개변수와 인수**

- **매개변수**: 함수 실행을 위해 외부의 인수를 함수 내부로 전달하는 요소.
- **함수를 정의할 때 선언**됨, 함수 내부에서 **변수와 동일하게 취급**(= 초기화 undefined)
- 내부에서만 참조 가능하다 = **스코프(유효 범위)는 함수 내부**.
- 매개변수와 인수의 개수를 체크하지 않아 에러가 발생하지 않음.
- 개수가 매개변수 > 인수 시: 인수가 할당되지 않는 매개변수 값은 **undefined**
- 개수가 매개변수 < 인수 시: 초과된 인수 **무시** + arguments 객체 프로퍼티로 **보관**

---

# 함수

함수 호출

- **인수 확인**
- 인수 부적절한 예시

```ts
function add(x, y) {
  return x + y;
}

console.log(add(2)); // NaN
console.log(add("a", "b")); // 'ab'
```

---

# 함수

함수 호출

- **인수 확인**
- ES6에 도입된 매개변수 기본값 할당 방법

```ts
function add(a = 0, b = 0, c = 0) {
  return a + b + c;
}

console.log(add(1, 2, 3)); // 6
console.log(add(1, 2)); // 3
console.log(add(1)); // 1
console.log(add()); // 0
```

---

# 함수

함수 호출

- **매개변수의 최대 개수**

- 매개 변수의 최대 개수는 명시적으로 제한하고 있지 않다.
- 매개변수는 순서에 의미가 있어 많아지면 **이해도** 힘들고 **실수 확률**도 높아진다. 또한 개수나 순서가 변경되면 코드 전체에 영향이 가 **유지보수성**이 나빠진다.
- 이상적인 함수 매개변수는 0개이며 가급적 작게, 최대 3개 이상 넘지 않는 것을 권장.
- 객체를 인수로 사용하는 경우 **프로퍼티 키**만 정확히 지정하면 매개변수의 순서는 신경쓰지 않아도 됨.
- **주의할 점**: 함수 외부에서 함수 내부로 전달한 객체를 함수 내부에서 변경하면 함수 외부의 객체가 변경되는 부수 효과 발생.

---

# 함수

함수 호출

- **반환문**
- return 키워드와 표현식(반환값)으로 이뤄져 실행 결과를 함수 외부로 반환.
- **반환문의 역활**

  1. 함수의 실행을 중단하고 함수 몸체를 빠져나감.
  2. return 키워드 뒤에 오는 표현식을 평가해 반환함. (없다면 undefined 반환)

- 반환문을 생략 가능하나 함수 마지막 문까지 실행 후 암묵적으로 undefined 반환
- return과 반환할 표현식 사이 줄바꿈이 있을 시 return에 자동 세미콜론이 추가되어 반환할 표현식은 무시됨. (undefined 출력)
- return을 함수가 아닌 전역에서 실행 시 문법에러(SyntaxError) 발생. (Node.js는 X)

---

# 함수

참조에 의한 전달과 외부 상태의 변경

- 원시 값은 값, 객체는 참조에 의한 전달 = 매개변수 타입에 따라 전달방식 같음.
- 함수 호출에서 매개변수에 값을 전달하는 방식을 값에 의한 호출, 참조에 의한 호출로 구별하나 전달은 위와 동일하다.
- 매개변수를 통해 전달받는 **원시 타입 인수**의 경우 값 자체가 복사되어 전달되기 때문에 원본이 훼손되지 않는다.
  **객체 타입** 경우에는 변경 가능한 값이므로 참조 값이 복사되어 전달되기 때문에 원본이 훼손된다.
- **함수가 외부 상태를 변경하면** 상태 변화를 추적하기 어려워 코드의 복잡성을 증가시키고 가독성을 해치는 원인이 됨
- 해결 방법 중 하나는 객체를 불변 객체로 만들어 사용하는 것. (깊은 복사를 사용)

---

# 함수

다양한 함수의 형태

- **즉시 실행 함수**
- 즉시 실행함수: 함수 정의와 동시에 즉시 호출되는 함수, 단 한 번만 호출.

```ts
(function () {
  var a = 3;
  var b = 5;
  return a * b;
})();
```

- **익명 함수를 사용**하는 것이 일반적.
- 그룹 연산자(()) 내의 기명 함수는 함수 선언문이 아닌 함수 리터럴로 평가되어 즉시 **실행 함수를 다시 호출할 수 없음**.
- 즉시 실행 함수는 반드시 **그룹 연산자(())로 감싸야 함**.(아닐 시 SyntaxError 발생)
- 그룹 연산자로 묶지 않을 시 에러 발생 이유
  익명 함수는 함수의 정의가 함수 선언문의 형식에 맞지 않기 때문.
  기명 함수는 선언문 뒤()가 함수 호출 연산자가 아닌 그룹 연산자로 해석되기 때문.
- 그룹 연산자로 함수를 묶은 이유: 함수 리터럴을 평가해 함수 객체를 생성하기 위해.

---

# 함수

다양한 함수의 형태

- **즉시 실행 함수**
- 즉시 실행 함수 사용 방식

```ts
(function () { ... }()); // 추천
(function () { ... })();
!function () { ... }();
+function () { ... }();
```

- 즉시 실행 함수 사용시 변수나 함수 이름의 충돌을 방지 가능.

---

# 함수

다양한 함수의 형태

- **재귀 함수**
- 함수가 자기 자신을 호출하는 것

```ts
function countdown(n) {
	for (let i = n; i >= 0; i--) console.log(i)
}

=

function countdown(n) {
	if (n < 0) return;
	console.log(n);
	countdown(n - 1); // 재귀 호출
}

countdown(10);
```

---

# 함수

다양한 함수의 형태

- **재귀 함수**
- 자기 자신을 호출할 때 사용한 식별자는 함수 이름.(함수 이름은 내부 사용 가능)
- 재귀 함수를 멈출 수 있는 **탈출 조건을 반드시 만들어야 한다**. (아닐 시 스택 오버플로 에러 발생)
- 반복문을 사용하는 것보다 재귀 함수를 사용하는 편이 더 직관적으로 이해하기 쉬울 때만 사용하는 것을 권장.

---

# 함수

다양한 함수의 형태

- **중첩 함수**
- **중첩 함수(내부 함수)**: 함수 내부에 정의된 함수 (→ **외부 함수**: 중첩을 포함한 함수)
- 중첩 함수는 외부 함수 내부에서만 호출 가능.
- 중첩함수는 외부 함수를 돕는 헬퍼 함수의 역활을 함.

```ts
function outer() {
  let x = 1;
  // 중첩 함수
  function inner() {
    let y = 2;
    console.log(x + y); //외부 함수의 변수를 참조 가능
  }
  inner();
}
outer();
```

- ES6부터 문이 위할 수 있는 문맥이라면 어디든지 함수 정의가 가능하기에 함수 내부 뿐만 아니라 for문이나 if문에서도 함수를 정의 가능. 하지만 호이스팅으로 인해 혼란이 발생할 수 있으므로 권장하지 않음.

---

# 함수

다양한 함수의 형태

- **콜백 함수**
- **콜백 함수**: 함수의 매개변수를 통해 다른 함수의 내부로 전달되는 함수.
- **고차 함수**: 매개변수를 통해 함수의 외부에서 콜백 함수를 전달받은 함수.
- 콜백 함수도 중첩 함수처럼 헬퍼 함수 의 역활을 함.

<div grid="~ cols-2 gap-2" m="-t-2">

```ts
function refeat1(n) {
  for (let i = 0; i < n; i++) console.log(i);
}
repeat1(5); // 0 1 2 3 4

function refeat1(n) {
  for (let i = 0; i < n; i++) {
    if (i % 2) console.log(i);
  }
}
repeat1(5); // 1 3
```

```ts
function refeat1(n, f) {
  // 고차함수
  for (let i = 0; i < n; i++) f(i);
}

let logAll = function (i) {
  // 콜백함수
  console.log(i);
};
repeat1(5, logAll); // 0 1 2 3 4

let logOdds = function (i) {
  // 콜백함수
  if (i % 2) console.log(i);
};
repeat1(5, logOdds); // 1 3
```

</div>

---

# 함수

다양한 함수의 형태

- **콜백 함수**
- **고차 함수**는 **콜백 함수**를 자신의 일부분으로 합성함.
- 콜백 함수는 고차 함수에 의해 호출되며 이때 고차 함수는 필요에 따라 콜백 함수에 인수를 전달 할 수 있다. → 콜백 함수를 전달할 때 함수 자체를 전달해야 함.(호출X)
- 고차 함수 내부에만 호출된다면 익명 함수 리터럴로 정의, 전달이 일반적

```ts
repeat(5, function (i) {
  // 콜백함수
  console.log(i);
}); // 1 3
```

---

# 함수

다양한 함수의 형태

- **콜백 함수**
- 콜백 함수는 함수형 프로그래밍 패러다임뿐만 아닌 비동기 처리(이벤트, Ajax, 타이머 함수 등), 배열 고차 함수에서도 활용.

<div grid="~ cols-2 gap-2" m="-t-2">

```ts
// 콜백 함수를 사용한 이벤트 처리
document.getElementById("myButton").addEventListener("click", function () {
  console.log("button clicked!");
});

// 콜백 함수를 사용한 비동기 처리
setTimeout(function () {
  console.log("1초 경과");
}, 1000);
```

```ts
// 콜백 함수를 사용하는 고차 함수 map
let res = [1, 2, 3].map(function (item) {
  return item * 2;
});
console.log(res); // [2, 4, 6]

// 콜백 함수를 사용하는 고차 함수 filter
let res = [1, 2, 3].filter(function (item) {
  return item * 2;
});
console.log(res); // [1, 3]

// 콜백 함수를 사용하는 고차 함수 reduce
let res = [1, 2, 3].reduce(function (acc, cur) {
  return acc + cur;
}, 0);
console.log(res); // [6]
```

</div>

---

# 함수

다양한 함수의 형태

- **순수 함수와 비순수 함수**
- **순수 함수**: 외부 상태에 의존하지도 않고 변경하지도 않는 부수 효과가 없는 함수.
- **비순수 함수**: 외부 상태에 외존하거나 외부 상태를 변경하는 부수 효과가 있는 함수.
- **순수 함수**는 동일한 인수가 전달되면 언제나 동일한 값을 반환. (= 상수)
- **비순수 함수**는 외부 상태를 변경하는 부수 효과가 있다.

<div grid="~ cols-2 gap-2" m="-t-2">

```ts
// 순수 함수는 동일한 인수면 항상 동일한 값을 반환
let count = 0;
function increace(n) {
  return ++n;
}

// 순수 함수가 반환한 결과값을 변수에 재할당해서 상태를 변경
count = increase(count);
console.log(count); // 1
count = increase(count);
console.log(count); // 2
```

```ts
// 비순수 함수, count가 increase 함수에 의해 변화
let count = 0;
function increace() {
  return ++count;
} //외부 상태에 의존하여 외부 상태를 변경

// 비순수 함수는 외부 상태(count)를 변경하므로 상태 변화를 추적하기 어려움
increase();
console.log(count); // 1
increase();
console.log(count); // 2
```

</div>

---

# 함수

다양한 함수의 형태

- **순수 함수와 비순수 함수**
- 함수 외부 상태의 변경을 지향하는 **순수 함수를 사용하는 것을 권장**.
- **함수형 프로그래밍**은 순수 함수와 보조 함수의 조합을 통해 외부 상태를 변경하는 부수 효과를 최소화해서 불변성을 지향하는 프로그래밍 패러다임.
- 로직 내 조건문과 반복문 제거로 복잡성을 해결, 변수 사용을 억제, 생명주기를 최소화 하여 상태 변경을 피해 오류를 최소화 하는것을 목표로 함.
- **함수형 프로그래밍**은 순수 함수를 통해 프로그램의 안정성을 높이려는 노력의 일환.

---

# 스코프

스코프란?

- 스코프(유효범위): 식별자가 유효한 범위, 식별자를 검색할 때 사용하는 규칙.
- 모든 식별자(변수, 함수, 클래스 등)는 자신이 선언된 위치에 의해 다른 코드가 식별자 자신을 참조할 수 있는 유효 범위가 결정됨.

```ts
let x = "global";

function foo() {
  let x = "local";
  console.log(x); // 'local'
}

foo();
console.log(x); // 'global'
```

---

# 스코프

스코프란?

- **식별자 결정**: 자바스크립트 엔진이 스코트를 통해 어떤 변수를 참조해야할 것인지 결정.
- **렉시컬 환경**: 코드가 어디서 실행되며 주변에 어떤 코드가 있는지(코드 문맥)
  → 이는 실행 컨택스트에서 평가되고 실행됨.
- 스코프 내에서 식별자는 유일해야 하지만 **다른 스코프에는 같은 이름의 식별자를 사용할 수 있음**. → 스코프는 네임스페이스
- var는 같은 스코프 내에서 중복 선언 가능하나 let 이나 const는 허용하지 않음.

```ts
function foo() {
  var x = 1;
  var x = 2;
  console.log(x); // 2
}

function bar() {
  let x = 1;
  let x = 2; // SyntaxError: Identifier 'x' has already been declared
}
```

---

# 스코프

스코프의 종류

- **전역**: 전역 스코프, 전역 변수, 코드의 가장 바깥 영역
- **지역**: 지역 스코츠, 지역 변수, 함수 몸체 내부
- **전역 변수**는 어디서든지 참조 가능.
- **지역 변수**는 자신의 지역 스코프와 하위 지역 스코프에서 유효.

---

# 스코프

스코프 체인

- **스코프는 함수**의 중첩에 의해 계층적 구조를 갖는다.
- **스코프 체인:** 스코프가 계층적으로 연결된 것.
- 변수를 참조할 때 스코프 체인을 통해 코드의 스코프에서 부터 상위 스코프 방향으로 이동하여 선언된 변수를 검색.
- **스코프 체인은 물리적인 실체로 존재.**
  코드를 실행전 렉시컬 환경을 생성 → 변수 선언 실행 후 변수 식별자가 키 등록 후 할당이 후 값 변경. (변수의 검색 또한 이 구조상에서 이뤄짐.)
- 스코프 체인은 실행 컨텍스트의 렉시컬 환경을 단방향으로 연결한 것(전역 렉시컬 환경은 코드가 로드되면 생성, 함수의 렉시컬 환경은 함수가 호출되면 생성.)
- 상위 스코프에서 유효한 변수는 하위 스코프에서 자유롭게 참조 할 수 있지만 하위 스코프에서 유효한 변수를 상위 스코프에서 참조할 수 없다.
- 함수또한 변수와 마찬가지기 때문에 **스코프는 식별자를 검색하는 규칙**.

---

# 스코프

함수 레벨 스코프

- **함수 레벨 스코프**: 코드 블록이 아닌 함수에 의해서만 지역 스코프가 생성됨. (var 키워드)
- **블록 레벨 스코프**: 모든 코드 블록이 지역 스코프를 만듬. (let, const 키워드)

```ts
var x = 1;
if (true) {
	var x = 10; // var는 함수레벨 스코프만 인정하기에 전역 변수가 된다.
}
console.log(x); // 10

===

var i = 10;
for (var i = 0; i < 5; i++) {
	console.log(i); // 0 1 2 3 4
}
console.log(i); // 5
```

---

# 스코프

렉시컬 스코프

- **동적 스코프**: 함수를 어디서 호출했는지에 따라 함수의 상위 스코프를 결정.
- **레시컬 스코프(정적 스코프)**: 함수를 어디서 정의했는지에 따라 상위 스코프를 결정.
  - **자바스크립트는 렉시컬 코프**를 따르므로 함수의 상위 스코프는 **자신이 정의된 스코프**.
- **함수의 상위 스코프**는 함수 정의가 실행될 때 정적으로 결정, 함수 정의가 실행, 생성된 함수는 이렇게 결정된 상위 스코프를 기억. (함수 호출마다 참조할 필요가 있기 때문.)

```ts
var x = 1;
function foo() {
  var x = 10;
  bar();
}
function bar() {
  console.log(x);
}
foo(); // 1
bar(); // 1
```
