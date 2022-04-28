---
# try also 'default' to start simple
theme: seriph
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://source.unsplash.com/collection/94734566/1920x1080
# apply any windi css classes to the current slide
class: 'text-center'
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

# 모던 자바스크립트 Deep Dive

## week4 
14장 ~ 15장

<div class="pt-12">
  <span @click="$slidev.nav.next" class="px-2 py-1 rounded cursor-pointer" hover="bg-white bg-opacity-10">
    Press Space for next page <carbon:arrow-right class="inline"/>
  </span>
</div>

<!--
The last comment block of each slide will be treated as slide notes. It will be visible and editable in Presenter Mode along with the slide. [Read more in the docs](https://sli.dev/guide/syntax.html#notes)
-->

---
layout: center
class: text-center
---

# 14장
## 전역 변수의 문제점

---

# 변수의 생명주기(life cycle)
<div grid="~ cols-2 gap-10">
<div class="mt-5">

## 지역 변수의 생명 주기
var 키워드로 선언한 지역 변수는 함수의 생명 주기와 일치.  
하지만 누군가가 지역 변수의 메모리 공간을 참조하고 있으면 해제되지 않고 확보된 상태로 남아있게 되어서 함수의 생명주기와 다를 수 있다.
<img
  class="pt-6"
  src="https://user-images.githubusercontent.com/44577555/165106439-35cda26f-b0b2-4f36-97ae-e730d2ab183f.png"
/>

</div>
<div class="mt-5">

## 전역 변수의 생명 주기
var 키워드로 선언한 전역 변수는 전역 객체의 프로퍼티가 된다.  
즉, 전역 변수의 생명 주기가 전역 객체의 생명 주기와 일치한다.  
<img
  class="pt-6"
  src="https://user-images.githubusercontent.com/44577555/165118277-b36b8668-068f-42d8-b60d-8ef109348f5b.png"
/>

</div>
</div>

<style>
h2 {
  color: #b39c36;
  font-size: 1.5em !important;
}
p {
  font-size: 0.8em !important;
}
strong {
  font-weight: bold;
  color: #b5794a;
}
.slidev-layout p {
  margin: 0.6em 0;
}
</style>

---
class: px-20
---

### 🤔 지역 변수 생명주기 예제  

<div class="pt-10">

#### 주석 ??? 부분 결과 예측하기

```javascript  {all|1|3-6|1,10|4|all}
  var x = 'global';

  function foo() {
    console.log(x);  // ???
    var x = 'local';
  }

  foo();

  console.log(x); // global  
```

</div>

<div v-click="6" class="pt-4">
  <strong>undefined</strong>
  <p>foo 함수 내부에서 선언된 지역 변수 x는 ??? 시점에 이미 선언되었고 undefined 초기화되었다. <br>이처럼 호이스팅은 스코프 단위로 동작한다.</p>

  > 호이스팅이란? 변수 선언이 스코프의 선두로 끌어 올려진 것처럼 동작하는 자바스크립트 고유의 특징
</div>

<style>
h3 {
  color: #809e41;
  font-size: 1.5em !important;
}
h4 {  
  margin-bottom: 10px;
  font-size: 1em !important;
}
p {
  font-size: 0.8em !important;
}
strong {
  font-weight: bold;
  color: #b5794a;
}
</style>

---

# 전역 변수의 문제점

## 암묵적 결합 
전역 변수를 만든 의도는 어디서든 참조하고 할당할 수 있는 변수를 사용하겠다는 것.  
<strong>모든 코드가 전역 변수를 참조하고 변경</strong>할 수 있는 암묵적 결합을 허용하는 것이다.

## 긴 생명 주기  
전역 변수는 생명 주기가 길어서 메모리 리소스도 오랜 기간 소비한다.  
var 키워드는 중복 선언을 허용하므로 전역 변수로 선언했을때 변수 이름이 중복될 가능성이 있고 의도치 않은 재할당이 이뤄질 수 있다.

<div grid="~ cols-2 gap-4">
<div>

```javascript 
  var x = 1;
  
  // ...

  // 변수의 중복 선언. 기존 변수에 값을 재할당한다.
  var x = 100;
  console.log(x);  //100
```

</div>
<div>

```javascript 
  // **전역 함수의 경우

  function add(x,y) { return x + y; }

  add(1,2); // 1 + 2 = 3 -> 중복 함수 호이스팅

  // ...
  function add(x,y) { return `${x} + ${y} = ${x + y}`; }
```

</div>
</div>

<style>
h2 {
  color: #b39c36;
  font-size: 1em !important;
  margin-top: 1.3rem !important;
}
p {
  margin: 0.5em !important;
  font-size: 0.8em !important;
}
strong {
  font-weight: bold;
  color: #b5794a;
}
</style>

---

## 스코프 체인 상에서 종점에 존재
변수를 검색할 때 전역 변수가 가장 마지막에 검색되어서 검색 속도가 가장 느리다.  
검색 시 지역 스코프에서 전역 스코프로 올라가기 때문

<br>

## 네임스페이스 오염
자바스크립트는 파일이 분리되어 있다 해도 전역 스코프를 공유한다.  
따라서 동일한 이름을 가진 변수나 함수가 같은 스코프에 존재할 수 있으며 예상치 못한 결과를 가져올 수 있다.

<style>
h2 {
  color: #b39c36;
  font-size: 1em !important;
  margin-top: 1.3rem !important;
}
p {
  margin: 0.5em !important;
  font-size: 0.8em !important;
}
strong {
  font-weight: bold;
  color: #b5794a;
}
</style>

---

# 전역 변수의 사용을 억제하는 방법

1. 즉시 실행 함수
2. 네임스페이스 객체
3. 모듈 패턴
4. ES6 모듈


<p class="absolute bottom-23 left-10">⭐️ 전역 변수를 반드시 사용해야 할 이유를 찾지 못한다면 지역 변수를 사용해야 한다. 변수의 스코프는 좁을수록 좋다.</p>

<style>
ol {
  margin-top: 3rem !important;
}
ol li {
  font-size: 1.3em !important;
  margin-top: 1.3rem !important;
}
p {
  margin: 0.5em !important;
  font-size: 0.9em !important;
}
</style>

---

# 즉시 실행 함수
즉시 실행 함수는 단 한 번만 호출된다.  
모든 코드를 즉시 실행 함수로 감싸면 모든 변수는 즉시 실행 함수의 지역 변수가 된다.

```javascript

  (function () {
    var foo = 10;
  }());
  
  console.log(foo); // ReferenceError: foo is not defined

```

<style>
  .slidev-layout h1 + p {
    opacity: 1;
  }
</style>

---

# 네임스페이스 객체
전역에 네임스페이스 역할을 담당할 객체를 생성하고 전역 변수처럼 사용하고 싶은 변수를 프로퍼티로 추가하는 방법이다.  
네임스페이스를 분리해서 식별자 충돌을 방지하는 효과는 있으나 네임스페이스 객체 자체가 전역 변수에 할당되므로 유용하지 않다.

```javascript
  var MYAPP = {};

  MYAPP.name = "Choi";

  console.log(MYAPP.name); // Choi  

```

```javascript
  // 계층적 구조 가능
  var MYAPP = {};
  
  MYAPP.person = {
    name: 'Choi',
    address: 'Seoul' 
  };

  console.log(MYAPP.person.name) // Choi

```

<style>
  .slidev-layout h1 + p {
    opacity: 1;
  }
</style>

---

# 모듈 패턴
모듈 패턴은 자바스크립트의 강력한 기능인 <strong>클로저를 기반으로 동작</strong>하고 전역 변수의 억제는 물론 캡슐화까지 구현할 수 있다.  
모듈 패턴은 전역 네임스페이스의 오염을 막는 기능은 한정적이지만 <strong>정보 은닉을 구현하기 위해 사용</strong>한다.


<div class="absolute bottom-9 left-10">

<br>

> '캡슐화'는 객체의 상태를 나타내는 프로퍼티와 프로퍼티를 참조하고 조작할 수 있는 동작인 메서드를 하나로 묶는것을 말한다.  
캡슐화는 객체의 특정 프로퍼티나 메서드를 감출 목적으로 사용하기도 하는데 이를 정보 은닉이라 한다.

<br>

> 자바스크립트는 public, private, protected 등의 접근 제한자를 제공하지 않는다.
</div>

<style>
  h2 {
    color: #b39c36;
    font-size: 1.5em !important;
  }
  .slidev-layout h1 + p {
    opacity: 1;
  }
  strong {
    font-weight: bold;
    color: #b5794a;
  }
</style>

---
class: px-20
---

<div grid="~ cols-2 gap-4">
<div>

### 🤔 모듈 패턴 예제  

<div class="pt-5">

#### 자바스크립트에서 퍼블릭, 프라이빗 맴버 만들기

<div v-click="4">
  <p><strong>클로저</strong>는 반환된 내부함수가 자신이 선언됐을 때의 환경(Lexical environment)인 스코프를 기억하여 자신이 선언됐을 때의 환경(스코프) 밖에서 호출되어도 그 환경(스코프)에 접근할 수 있는 함수이다.<br>클로저는 자신이 생성될 때의 환경을 기억하는 함수.</p>
  <p>
  외부에 노출하고 싶은 변수나 함수를 객체에 담아 프로퍼티를 퍼블릭 맴버로 만들었다. <br>
  외부에 노출하고 싶지 않은 변수나 함수는 반환하는 객체에 추가하지 않으면 외부에서 접근할 수 없는 프라이빗 맴버가 된다.</p>
</div>

</div>
</div>

<div>

```javascript  {all|2-16|3-4,18-19|all}

  var Counter = (function () {
    // private 변수
    var num = 0;

    // 외부로 공개할 데이터나 메서드를 프로퍼티로 추가한 객체를 반환
    // public member
    return {
      increase() {
        return ++num;
      },
      decrease() {
        return --num;
      }
    }
  }());

  // private 변수는 외부로 노출되지 않는다
  console.log(Counter.num) // undefined
  
  console.log(Counter.increase()); // 1
  console.log(Counter.increase()); // 2
  console.log(Counter.decrease()); // 1
  console.log(Counter.decrease()); // 0


```
</div>
</div>

<style>
  h3 {
    color: #809e41;
    font-size: 1.5em !important;
  }
  h4 {  
    margin-bottom: 10px;
    font-size: 1em !important;
  }
  p {
    font-size: 0.8em !important;
  }
  strong {
    font-weight: bold;
    color: #b5794a;
  }
</style>

---

# ES6 모듈
ES6 모듈은 파일 자체의 독자적인 모듈 스코프를 제공한다. script 테그에 type=”module” 어트리뷰트를 추가하면 로드된 자바스크립트 파일은 모듈로서 동작한다.

- mjs 확장자 권장.
- 구형 브라우저에서는 동작하지 않으며 브라우저의 ES6모듈 기능을 사용하더라도 트랜스파일링이나 번들링이 필요하다.  


<style>
  .slidev-layout h1 + p {
    opacity: 1;
  }

</style>
---
layout: center
class: text-center
---

# 15장
## let, const 키워드와 블록 레벨 스코프

---

# var 키워드로 선언한 변수의 문제점

1. 변수 중복 선언 허용
2. 함수 레벨 스코프
3. 변수 호이스팅
4. ES6 모듈

<style>
ol {
  margin-top: 3rem !important;
}
ol li {
  font-size: 1.3em !important;
  margin-top: 1.3rem !important;
}
p {
  margin: 0.5em !important;
  font-size: 0.8em !important;
}
</style>

---

# 변수 중복 선언 허용

var 키워드로 선언한 변수는 중복 선언이 가능

```javascript {all|2-3|5-6|7-8|all}

  var x = 1;
  var y = 1;

  // var 키워드 변수에 초기화 문이 있는 경우 자바스크립트 엔진에 의해 var 키워드가 없는 것처럼 동작. 재선언.
  var x = 100;
  // 초기화문이 없는 변수 선언문은 무시된다.
  var y; 

  console.log(x); // 100
  console.log(y); // 1

```

<p v-click="5">동일한 이름의 변수를 중복 선언하고 값을 할당하면 의도치 않게 먼저 선언된 변수 값이 변경되는 부작용 발생</p>

<style>
  .slidev-layout h1 + p {
    opacity: 1;
  }
</style>
---

# 함수 레벨 스코프

var 키워드로 선언한 변수는 오로지 함수의 코드 블록만을 지역 스코프로 인정한다.

```javascript {all|4-7|all}

  var x = 1;

  if (true) {
    // var 키워드로 선언된 변수는 함수의 코드 블록만을 지역스코프로 인정하기 때문에 x 변수가 중복 선언된다.
    var x = 10;
  }

  console.log(x) // 10

```

<p v-click="2">동일한 이름의 변수를 중복 선언하고 값을 할당하면 의도치 않게 먼저 선언된 변수 값이 변경되는 부작용 발생</p>

<style>
  .slidev-layout h1 + p {
    opacity: 1;
  }
</style>

---

# 변수 호이스팅

var 키워드로 선언한 변수는 변수 선언문 이전에 참조할 수 있다.  
단, 할당문 이전에 변수를 참조하면 언제나 undefined를 반환한다.

```javascript {all|2,11-12|3-4|6-9|all}

  // 이 시점에 호이스팅에 의해 foo변수가 선언
  // 변수 foo는 undefined로 초기화
  console.log(foo); // undefined

  // 변수에 값을 할당
  foo = 123;

  console.log(foo) // 123

  // 변수 선언은 런타임 이전에 자바스크립트 엔진에 의해 암묵적으로 실행된다.
  var foo;

```

<p v-click="4">변수 선언문 이전에 변수를 참조할 수 있지만 프로그램의 흐름상 맞지 않고 가독성을 떨어뜨리며 오류를 발생시킬 여지를 남긴다.</p>

<style>
  .slidev-layout h1 + p {
    opacity: 1;
  }
</style>

---

# let 키워드의 특징
ES6에서 도입.

1. 변수 중복 선언 금지
2. 블록 레벨 스코프
3. 변수 호이스팅
4. 전역 객체와 let

<style>
ol {
  margin-top: 3rem !important;
}
ol li {
  font-size: 1.3em !important;
  margin-top: 1.3rem !important;
}
</style>

---

# 변수 중복 선언 금지
이름이 같은 변수를 중복 선언하면 문법에러 발생

```javascript

  let bar = 123;
  
  let bar = 456; // SyntaxError: Identifier 'bar' has already been declared  


```

<style>
  .slidev-layout h1 + p {
    opacity: 1;
  }
</style>

---

# 블록 레벨 스코프
모든 코드 블록을 지역 스코프로 인정하는 블록 레벨 스코프를 따른다.

```javascript {all|1|2-5|all}
  let foo = 1; // 전역변수
  {
    let foo = 2; // 지역변수
    let bar = 3; // 지역변수
  }
  console.log(foo); // 1
  console.log(bar); // ReferenceError: bar is not defined -> 전역에서 지역변수를 참조할 수 없다.
```

<style>
  .slidev-layout h1 + p {
    opacity: 1;
  }
</style>

---

# 변수 호이스팅
 
let 키워드 변수는 var 키워드 변수와 다르게 선언 단계와 초기화 단계가 분리되어 진행된다.  
스코프의 시작 지점부터 초기화 시작 지점까지 변수를 참조할 수 없는 구간을 일시적 사각지대 라고 한다.  

<div class="mt-10">

var 키워드 변수 생명주기 | let 키워드 변수 생명주기
:--:|:--:|
<img width="300" alt="var 키워드 변수 생명주기" src="https://user-images.githubusercontent.com/44577555/165249667-c6cebe00-cd26-406e-9d35-750d03cd8715.png"> | <img width="300" alt="let 키워드 변수 생명주기" src="https://user-images.githubusercontent.com/44577555/165249653-d750aacb-9dd8-4280-bd1c-3038170aa82e.png">

</div>

<style>
  img {
    margin: 0 auto;
  }
  .slidev-layout h1 + p {
    opacity: 1;
  }
</style>

---

# let 키워드 변수 호이스팅
호이스팅이 발생하지 않는 것처럼 동작 -> 선언문이 실행되기 전에 참조하면 참조 에러 발생  
<br>
초기화 단계는 변수 선언문에 도달했을 때 실행된다.  

```javascript {all|1-2|4-5|7-8|all}
  // 초기화 이전의 일시적 사각지대에서는 참조할 수 없다. 
  // 크롬 브라우저에서 전역 변수로 선언했을때 initialization에러가 아닌 defined에러가 뜬다.
  console.log(foo); // ReferenceError: foo is not defined 
  
  let foo; // 변수 선언문에서 초기화 단계가 실행
  console.log(foo); // undefined

  foo = 1; // 할당문에서 할당 단계 실행
  console.log(foo); // 1
```
```javascript {all|1-2|4-5|7-8|all}
  // ** 블록 레벨에서는 오류가 다르게 출력된다.
  {
    console.log(foo); // ReferenceError: Cannot access 'foo' before initialization
    let foo = 2;
  }
```


<style>
  .slidev-layout h1 + p {
    opacity: 1;
  }
</style>

---

# 전역 객체와 let
let 키워드로 선언한 전역 변수는 전역 객체의 프로퍼티가 아니다.  
let 전역 변수는 보이지 않는 개념적인 블록 내에 존재하게 된다.

```javascript
  let x = 1;
  
  console.log(window.x); // undefined -> let, const로 선언한 변수는 전역 객체 window의 프로퍼티가 아니다.
  console.log(x); // 1

```

<style>
  .slidev-layout h1 + p {
    opacity: 1;
  }
</style>

---

# const 키워드
ES6에서 도입.  
const 키워드는 let 키워드와 대부분 동일하다.

1. 선언과 초기화
2. 재할당 금지
3. 상수
4. const 키워드와 객체

<style>
  ol {
    margin-top: 3rem !important;
  }
  ol li {
    font-size: 1.3em !important;
    margin-top: 1.3rem !important;
  }
  .slidev-layout h1 + p {
    opacity: 1;
  }
</style>

---

# 선언과 초기화
const 키워드로 선언한 변수는 반드시 선언과 동시에 초기화해야 한다.

```javascript {all|1-2|3-7|3-9}
  // 선언과 동시에 초기화를 하지 않으면 에러 발생
  const foo; // SyntaxError: Missing initializer in const declaration
  {
    console.log(foo); // ReferenceError: Cannot access 'foo' before initialization
    const foo = 1;
    console.log(foo); // 1
  }

  console.log(foo); // ReferenceError: foo is not defined
```

<style>
  .slidev-layout h1 + p {
    opacity: 1;
  }
</style>

---

# 재할당 금지
var, let 키워드로 선언한 변수는 재할당이 자유롭지만 const 키워드로 선언한 변수는 재할당이 금지된다.

<style>
  .slidev-layout h1 + p {
    opacity: 1;
  }
</style>

---

# 상수
상수는 재할당이 금지된 변수를 말한다.  
const 키워드로 선언한 변수에 원시 값을 할당한 경우 변수 값을 변경할 수 없기때문에 상수를 표현하는 데 사용하기도 한다.

## 상수의 특징
- 변수의 상태 유지
- 가독성이 좋음
- 유지보수의 편의 상승  

적극적으로 사용 권장.  
일반적으로 상수의 이름은 대문자로 사용하고 여러 단어로 이뤄진 경우에는 스테이크 케이스로 표현한다.

<style>
  .slidev-layout h1 + p {
    opacity: 1;
  }
</style>

---
class: px-20
---

### 상수 적용 예제  

<div class="pt-10">

#### 고정값으로 사용될 변수를 상수로 적용하기 🤔

```javascript  {all|1-2|2,6|all}
  // 세율을 의미하는 0.1은 변경할수 없는 상수로서 사용될 값
  const TAX_RATE = 0.1;

  let perTaxPrice = 100;

  let afterTaxPrice = perTaxPrice + (perTaxPrice * TAX_RATE);

  console.log(afterTaxPrice); // 110
```

</div>

<div v-click="6" >
  <p>상수는 유지보수의 편의를 위해 변경되지 않는 변수의 값을 할당할 때 적극 권장한다.</p>
</div>

<style>
h3 {
  color: #9C5170;
  font-size: 1.5em !important;
}
h4 {  
  margin-bottom: 10px;
  font-size: 1em !important;
}
p {
  font-size: 0.8em !important;
}
</style>

---

# const 키워드와 객체
const 키워드로 선언된 변수에 객체를 할당한 경우 값을 변경할 수 있다. 이때 객체가 변경되더라도 변수에 할당된 참조 값은 변경되지 않는다.
const 키워드는 재할당을 금지할 뿐 “불변”을 의미하지는 않는다.

```javascript
  const person = {
    name: 'Choi'
  };

  person.name = 'Kim';

  console.log(person); // {name: "Kim"}
```


<style>
  .slidev-layout h1 + p {
    opacity: 1;
  }
</style>

---

# var vs. let vs. const
변수 선언에는 기본적으로 const를 사용하고 let은 재할당이 필요한 경우에 한정해 사용하는 것이 좋다.

- ES6를 사용한다면 var 키워드를 사용하지 않는다.
- 재할당이 필요한 경우에 한정해 let 키워드를 사용한다. 이때 변수의 스코프는 최대한 좁게 만든다.
- 변경이 발생하지 않고 읽기 전용으로 사용하는 원시 값과 객체에는 const 키워드를 사용한다. const 키워드는 재할당을 금지하므로 var, let 키워드보다 안전하다.


---

# 변수 키워드 비교  

<table class="mt-20">
  <thead>
    <tr>
      <th style="width:10%" class="text-center">키워드</th>
      <th style="width:10%" class="text-center">중복선언</th>
      <th style="width:10%" class="text-center">재할당</th>
      <th style="width:30%">스코프</th>
      <th style="width:40%">호이스팅</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td class="text-center">var</td>
      <td class="text-center">o</td>
      <td class="text-center">o</td>
      <td>함수의 코드 블록</td>
      <td>변수 선언문 이전에 참조 가능하고 할당문 이전에 참조하면 undefined 반환.</td>
    </tr>
    <tr>
      <td class="text-center">let</td>
      <td class="text-center">x</td>
      <td class="text-center">o</td>
      <td>모든 코드 블록(함수, if문, for문, while 문, try/catch문 등)</td>
      <td>호이스팅이 발생하지 않는 것처럼 동작한다.<br/>선언단계와 초기화 단계가 분리되어 진행. <br/>변수 선언 이전에 참조하면 참조 에러발생</td>
    </tr>
    <tr>
      <td class="text-center">const</td>
      <td class="text-center">x</td>
      <td class="text-center">x</td>
      <td>모든 코드 블록(함수, if문, for문, while 문, try/catch문 등)</td>
      <td>호이스팅이 발생하지 않는 것처럼 동작한다</td>
    </tr>
  </tbody>
</table>

<style>
  .text-center {
    text-align:center !important;
  }
</style>


---
layout: center
class: text-center
---

# Thanks!

