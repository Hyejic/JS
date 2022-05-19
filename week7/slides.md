---
# try also 'default' to start simple
theme: unicorn
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

# 22, 23장 this ~ 실행컨테스트

<div class="pt-12">
  <span @click="$slidev.nav.next" class="px-2 py-1 rounded cursor-pointer" hover="bg-white bg-opacity-10" style="color:#c078cf">
    next page <carbon:arrow-right class="inline"/>
  </span>
</div>
---

# this

<style>
h1 {
  display: flex;
  align-items: center;
  justify-content: center;
}
</style>

---

# this 키워드

필요한 이유, 상황

- **객체에서** 메서드가 자신이 속한 객체의 프로퍼티를 참조하려면 자신이 속한 객체를 가리키는 식별자를 참조하기 위해.
- **생성자 함수에서** 인스턴스를 생성하려면 먼저 생성자 함수가 존재해야 함.

<br>

<div grid="~ cols-2 gap-2" m="-t-2">

```ts
// 객체

const circle = {
  radius: 5,
  getDiameter() {
    return 2 * this.radius; // 메서드를 호출한 객처를 가리킴.
  },
};

console.log(circle.getDiameter()); //10
```

```ts
// 생성자 함수

function circle(radius) {
  this.radius = radius; // 생성자 함수가 생성할 인스턴스를 가리킴.
}
Circle.prototype.getDiameter = function () {
  return 2 * this.radius; // 생성자 함수가 생성할 인스턴스를 가리킴.
};

const circle = new Circle(5);
console.log(circle.getDiameter()); //10
```

</div>

---

# this 키워드

#### this

- 자신이 속한 객체 또는 자신이 생성할 인스턴스를 가리키는 **자기 참조 변수**.
- 자신이 속한 객체 또는 자신이 생성할 인스턴스의 프로퍼티나 메서드를 참조 가능
- this가 가리키는 값, 즉 this 바인딩은 **함수 호출 방식에 의해 동적으로 결정**됨.
- strict mode 역시 this 바인딩에 영향을 줌.
- **전역과 일반 함수 내부**에서는 this는 **window**를 가리키며, **strict mode**에서 this는 **undefined**가 바인딩.

<br>
<br>

- **this 바인딩**: 바인딩이란 식별자와 값을 연결하는 과정. this(식별자)와 this가 가리킬 객체(값)를 연결하는 것.

---

# 함수 호출 방식과 this 바인딩

- this 바인딩은 **함수 호출 방식에 따라 동적으로 결정**.
- 렉시컬 스코프는 함수 정의가 평가되어 함수 객체가 생성되는 시점에 상위 스코프를 결정하는 반면,
- **this 바인딩은 함수 호출 시점에 결정**된다.

---

# 함수 호출 방식과 this 바인딩

#### 일반 함수 호출

<div grid="~ cols-2 gap-2" m="-t-2">

```ts {all|2|4|all}
function foo() {
  console.log("foo's this:", this); // window
  function bar() {
    console.log("foo's this:", this); // window
  }
  bar();
}
foo();
```

```ts {all|5|7|all}
// strict mode에서 this는 undefined가 바인딩
function foo() {
  "use strict";

  console.log("foo's this:", this); // undefined
  function bar() {
    console.log("foo's this:", this); // undefined
  }
  bar();
}
foo();
```

</div>

- **기본적으로 this에는 전역 객체(window)가 바인딩**.
- **strict mode**에서 this는 **undefined가 바인딩**.

---

# 함수 호출 방식과 this 바인딩

#### 일반 함수 호출

<div grid="~ cols-2 gap-2" m="-t-2">

```ts {all|8|10|11|all}
// 중첩 함수
// var 전역 변수는 전역 객체의 프로퍼티 (const, let은 X)
var value = 1;

const obj = {
	value : 100,
	function foo() {
		console.log("foo's this:", this); // {value: 100, foo: f}
		function bar() {
			console.log("foo's this:", this); // window
			console.log("foo's this.value:", this.value); // 1
		}
		bar();
	}
};
obj.foo();
```

```ts {all|7|9|10|all}
// 콜백 함수
var value = 1;

const obj = {
  value: 100,
  foo() {
    console.log("foo's this:", this); // {value: 100, foo: f}
    setTimeout(function () {
      console.log("callback's this:", this); // window
      console.log("callback's this.value:", this.value); // 1
    }, 100);
  },
};
obj.foo();
```

</div>

- **중첩 함수**도 일반 함수로 호출되면 중첩 함수 내부의 this에는 전역 객체가 바인딩.

- **콜백 함수**가 일반 함수로 호출된다면 콜백 함수 내부의 this에도 전역 객체가 바인딩

- **일반 함수로 호출된 모든 함수**(중첩, 콜백 함수 포함) 내부 this는 전역 객체가 바인딩.

---

# 함수 호출 방식과 this 바인딩

#### 일반 함수 호출

<br>

- **메서드 내부의 중첩 함수나 콜백 함수의 this 바인딩을 일치시키는 방법**
  <br>

<div grid="~ cols-3 gap-3" m="-t-2">

```ts {all|8|10|all}
// 1
var value = 1;

const obj = {
  value: 100,
  foo() {
    // this 바인딩을 변수 that에 할당.
    const that = this;
    setTimeout(function () {
      console.log(that.value); // 100
    }, 100);
  },
};
obj.foo();
```

```ts {all|11-12|all}
// 2
var value = 1;

// Function.prototype.apply, call, bind 메서드로 명시적으로 바인딩
const obj = {
  value: 100,
  foo() {
    // 콜백 함수에 명시적으로 this를 바인딩.
    setTimeout(
      function () {
        console.log(that.value); // 100
      }.bind(this),
      100
    );
  },
};
obj.foo();
```

```ts {all|8|all}
// 3
var value = 1;

const obj = {
  value: 100,
  foo() {
    // 화살표 함수 내부의 this는 상위 스코프의 this.
    setTimeout(() => console.log(that.value), 100); // 100
  },
};
obj.foo();
```

</div>

---

# 함수 호출 방식과 this 바인딩

#### 메서드 호출

<br>

- **메서드 내부의 this**에는 **메서드를 호출한 객체가 바인딩**.
- 객체의 **메서드의 프로퍼티**가 객체의 프로퍼티를 호출할 때, 메서드의 프로퍼티가 가리키는 함수 객체는 **독립적으로 존재하는 별도의 객체**.
- 따라서 메서드는 다른 객체의 프로퍼티에 할당하는 것으로 다른 객체의 메서드가 될 수도 있고 일반 변수에 할당하여 일반 함수로 호출될 수도 있음.

![이미지](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/d07866a1-d542-4c9d-9048-034f47150a27/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220518%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220518T205313Z&X-Amz-Expires=86400&X-Amz-Signature=452f9e88dcbff0bc24739e589b929d99510c7f26f8cdbbab5d7fed92f5dc4a60&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

<style>
img {
  margin: 0 auto;
  width: 600px;
}
</style>

---

# 함수 호출 방식과 this 바인딩

#### 메서드 호출

```ts {all|1-7|10-14|13|3-5|10-12|14|17-18|all}
const person = {
  name: "Lee",
  getName() {
    return this.name;
  },
};
console.log(person.getName()); // Lee

// 다른 객체의 프로퍼티에 할당
const anotherPerson = {
  name: "Kim",
};
anoterPerson.getName = person.getName;
console.log(anoterPerson.getName()); // Kim

// 일반 변수에 할당
const getName = person.getName;
console.log(getName()); // ''
// 위의 this.name은 window.name(기본값: '', Node.js에서는 undefined)과 같음
```

---

# 함수 호출 방식과 this 바인딩

#### 메서드 호출

<br>

- **프로토타입 메서드 내부에서 사용된 this도 일반 메서드와 같음.**

```ts {all|1-7|9-11|13-15|all}
function Person(name) {
  this.name = name;
}

Person.prototype.getName = function () {
  return this.name;
};

const me = new Person("Lee");
// getName 메서드를 호출한 객체는 me
console.log(me.getName()); // Lee

Person.prototype.name = "Kim";
// getName 메서드를 호출한 객체는 Person.prototype
console.log(Person.prototype.getName()); // Kim
```

---

# 함수 호출 방식과 this 바인딩

#### 메서드 호출

![이미지](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/40627c4e-3d12-4f04-9209-7065e2abb25e/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220518%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220518T205551Z&X-Amz-Expires=86400&X-Amz-Signature=bb622728ee4286512edcf7b8a6123c8e1393bc774894cc045ae5c76fc471e9b5&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

---

# 함수 호출 방식과 this 바인딩

#### 생성자 함수 호출

- **생성자 함수 내부의 this**에는 생성자 함수가 (미래에) **생성할 인스턴스가 바인딩**.
- new 연산자와 함께 호출하지 않으면 생성자 함수가 아닌 일반 함수로 동작.

```ts {all|1-7|9-10|12-14|16-17|all}
function Circle(radius) {
  // 생성자 함수 내부의 this는 생성자 함수가 생성할 인스턴스를 가리킴
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}

const circle1 = new Circle(5);
console.log(circle1.getDiameter()); //10

// new 연산자와 함께 호출하지 않으면 일반 함수로 동작
const circle2 = Circle(15);
console.log(circle2.getDiameter()); // 반환문이 없어 암묵적으로 undefined 반환, typeError..?

// 일반 함수로 호출된 Circle 내부의 this는 전역 객체를 가리킴
console.log(radius); // 15 <-?
```

---

# 함수 호출 방식과 this 바인딩

#### Function.prototype.apply/call/bind 메서드에 의한 간접 호출

<br>

- **apply, call, bind 메서드**는 Function.prototype의 메서드이기에 모든 함수가 상속받아 사용 가능.
- **apply와 call 메서드**는 this로 **사용할 객체와 인수 리스트를 인수로 전달받아 함수를 호출**함.

<br>

```ts
function getThisBinding() {
  return this;
}
const thisArg = { a: 1 };
console.log(getThisBinding()); // window
console.log(getThisBinding.apply(thisArg)); // {a: 1}
console.log(getThisBinding.call(thisArg)); // {a: 1}
```

---

# 함수 호출 방식과 this 바인딩

#### Function.prototype.apply/call/bind 메서드에 의한 간접 호출

<br>

<div grid="~ cols-2 gap-2" m="-t-2">

```ts
// 주어진 this 바인딩과 인수 리스트 배열을 사용하여 함수를 호출
// @param thisArg - this로 사용할 객체
// @param argsArray - 함수에게 전달할 인수 리스트의 배열 또는 유사 배열 객체
// @returns 호출된 함수의 반환값
// Function.prototype.apply(thisArg[, argsArray])

function getThisBinding() {
  console.log(arguments);
  return this;
}
const thisArg = { a: 1 };

console.log(getThisBinding.apply(thisArg, [1, 2, 3]));
// arguments(3) [1, 2, 3, callee: f, symbol(symbol.iterator): f]
// {a: 1}
```

```ts
// 주어진 this 바인딩과 ,로 구분된 인수 리스트를 사용하여 함수를 호출
// @param thisArg - this로 사용할 객체
// @param arg1, arg2, ... - 함수에게 전달할 인수 리스트
// @returns 호출된 함수의 반환값
// Function.prototype.call(thisArg[, arg1[, arg2[, ...]]])

function getThisBinding() {
  console.log(arguments);
  return this;
}

console.log(getThisBinding.call(thisArg, 1, 2, 3));
// arguments(3) [1, 2, 3, callee: f, symbol(symbol.iterator): f]
// {a: 1}
```

</div>

---

# 함수 호출 방식과 this 바인딩

#### Function.prototype.apply/call/bind 메서드에 의한 간접 호출

<br>

```ts
// arguments 객체는 배열이 아니기에 Array.prototype.slice를 사용할 수 없지만
// apply와 call 메서드를 이용하면 가능
function convertArgsToArray() {
  console.log(arguments);
  const arr = Array.prototype.slice.call(arguments); // = apply
  console.log(arr);
  return arr;
}
convertArgsToArray(1, 2, 3); // [1, 2, 3]
// 사실 잘 모르겠다 27열 다시 알아본다니까 그때보자.
```

---

# 함수 호출 방식과 this 바인딩

#### Function.prototype.apply/call/bind 메서드에 의한 간접 호출

- **bind**는 **첫 번째 인수로 전달한 값으로 this 바인딩이 교체된 함수를 새로 생성 반환**.
- bind 메서드는 **메서드의 this와 메서드 내부의 중첩 함수 또는 콜백 함수의 this가 불일치하는 문제를 해결하기 위해 사용**.

<br>

<div grid="~ cols-2 gap-2" m="-t-2">

```ts
function getThisBinding() {
  return this;
}
const thisArg = { a: 1 };

// 첫 번째 인수로 전달한 thisArg로 this 바인딩이 교체된
// getThisBinding 함수를 새롭게 생성해 반환.
console.log(getThisBinding.bind(thisArg)); // getThisBinding
// bind 메서드는 함수를 호출하지 않으므로 명시적으로 호출해야 함.
console.log(getThisBinding.bind(thisArg)()); // {a: 1}
```

```ts
const person = {
	name: 'Lee'.
	foo(callback) { setTimeout(callback, 100); }
};
person.foo(function () {
	console.log('Hi! my name is ${this.name}.'); // Hi! my name is .
	// 일반 함수로 호출된 콜백 함수 내부의 this.name은 window.name과 같음.
})

const person = {
	name: 'Lee'.
	foo(callback) { setTimeout(callback.bind(this), 100); }
};
person.foo(function () {
	console.log('Hi! my name is ${this.name}.'); // Hi! my name is Lee.
})
```

</div>

---

# 함수 호출 방식과 this 바인딩

| **함수 호출 방식**                             | **this 바인딩**                          |
| ---------------------------------------------- | ---------------------------------------- |
| 일반 함수 호출                                 | 전역 객체                                |
| 메서드 호출                                    | 메서드를 호출한 객체                     |
| 생성자 함수 호출                               | 생성자 함수가 (미래에)생성할 인스턴스    |
| Function.prototype.apply/call/bind의 간접 호출 | 해당 메서드에 첫 번째 인수로 전달한 객체 |

---

# 실행컨테스트

<style>
h1 {
  display: flex;
  align-items: center;
  justify-content: center;
}
</style>

---

# 소스코드의 타입

| **소스코드의 타입** | **설명**                                                                  |
| ------------------- | ------------------------------------------------------------------------- |
| 전역 코드           | 전역에 존재하는 소스코드. 전역 정의된 함수, 클래스 등의 내부 코드는 포함X |
| 함수 코드           | 함수 내부에 존재하는 소스코드. 함수 내부 중첩 함수, 클래스 등은 포함X     |
| eval 코드           | 빌트인 전역 함수인 eval 함수에 인수로 전달되어 실행되는 소스코드.         |
| 모듈 코드           | 모듈 내부에 존재하는 소스코드. 모듈 내부 함수, 클래스 등은 포함X          |

---

# 소스코드의 타입

- 소스코드를 구분하는 이유: 소스코드의 타입에 따라 실행 컨텍스트를 생성하는 과정과 관리 내용이 다르기 때문.
  <br>

| **소스코드의 타입** | **설명**                                                                                                                                                     |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 전역 코드           | 전역 스코프 생성. <br> 전역 객체와 연결을 위해 전역 코드가 평가 되면 전역 실행 컨텍스트가 생성.                                                              |
| 함수 코드           | 지역 스코프 생성. <br> 지역 변수, 매개변수, arguments 객체 관리. + 전역 스코프와 스코프 체인 연결 <br> 함수 코드가 평가되면 함수 실행 컨텍스트가 생성. 포함X |
| eval 코드           | strict mode에서 자신만의 독자적인 스코프 생성. <br> eval 코드가 평가되면 eval 실행 컨텍스트가 생성.                                                          |
| 모듈 코드           | 모듈별로 독립적인 모듈 스코프 생성. <br> 모듈 코드가 평가되면 모듈 실행 컨텍스트 생성.                                                                       |

---

# 소스코드의 평가와 실행

- 자바스크립트 엔진은 소스코드를 **소스코드의 평가**와 **소스코드의 실행**으로 나눠 처리.
- **평가 과정**
  - 실행 컨텍스트를 생성하고 변수, 함수 등의 선언문만 먼저 실행
  - 생성된 변수나 함수 식별자를 키로 실행 컨텍스트가 관리하는 스코프에 등록
- **실행 과정**
  - 선언문을 제외한 소스코드가 순차적으로 실행. (=런타임 시작)
  - 소스코드 실행에 필요한 정보를 실행 컨텍스트가 관리하는 스코프에서 검색해 취득.
  - 변수 값의 변경 등 실행 결과는 다시 실행 컨텍스트가 관리하는 스코프에 등록.

![이미지](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/01d5cff2-8b90-4215-a5b5-79cb5e7b9577/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220518%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220518T213046Z&X-Amz-Expires=86400&X-Amz-Signature=feefad5f6ac00f04dff4a41783b1561d433c357462aa13bc0a3a57f0bd22a3ab&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

<style>
img {
  margin: 0 auto;
  width: 500px;
}
</style>

---

# 실행 컨텍스트의 역활

- 전역코드와 함수코드로 구성되어 있는 코드 실행 과정
  1. **전역 코드 평가**: 변수 선언문 + 함수 선언문 실행 → 전역 스코프에 등록
  2. **전역 코드 실행**: 런타임 시작. 전역 변수 값 할당 + 함수 호출.
  3. **함수 코드 평가**: 매개 변수 + 지역 변수 선언문 실행 → 지역 스코프에 등록
  4. **함수 코드 실행**: 런타임 시작. 함수 코드 실행 + 매개변수, 지역 변수 값 할당

---

# 실행 컨텍스트의 역활

- **코드가 실행되려면 스코프, 식별자, 코드 실행 순서 등의 관리가 필요**.
  - 선언에 의해 생성된 모든 식별자(변수, 함수, 클래스 등)를 스코프를 등록하고 상태 변화를 지속적으로 관리해야 함.
  - 스코프는 중첩 관계에 의해 스코프 체인 형성 → 스코프 체인을 통해 상위 스코프로 이용하여 식별자 검색 가능해야 함.
  - 현재 실행 중인 코드의 실행 순서를 변경할 수 있고 다시 되돌아갈 수도 있어야 함.

---

# 실행 컨텍스트의 역활

- **실행 컨텍스트**: 소스코드를 실행하는 데 필요한 환경을 제공하고 코드의 실행 결과를 실제로 관리하는 영역.
- **모든 코드는 실행 컨텍스트를 통해 실행되고 관리된다.**
- 식별자와 스코프는 실행 컨텍스트의 **렉시컬 환경**으로 관리,
  코드 실행 순서는 실행 컨텍스트 **스택**으로 관리.

---

# 실행 컨텍스트 스택

![이미지](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/582f256d-00d5-4c9f-b12c-e743120264a0/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220518%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220518T213400Z&X-Amz-Expires=86400&X-Amz-Signature=24dea1fc1ce3f2fe608514eb18a53d3cf7245b12d460ea6dc9346bf7d0ea962b&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

- 실행 컨텍스트 스택은 **코드의 실행 순서를 관리**.
- 실행 컨텍스트 스택의 최상위에 존재하는 실행 컨텍스트는 언제나 현재 실행 중인 코드의 실행 컨텍스트

→ **실행 중인 실행 컨텍스트**라 부름.

---

# 실행 컨텍스트 스택

전역 코드와 함수 코드, 중첩 함수 코드의 실행 컨텍스트 실행 과정

1. **전역 코드의 평가와 실행**

- 전역 코드 평가로 전역 실행 컨텍스트 생성 후 실행 컨텍스트에 푸시.

2. **함수 코드의 평가와 실행**

- 전역 코드 실행 중단, 함수 내부 코드 평가로 함수 실행 컨텍스트 생성 후 실행 컨텍스트에 푸시.

3. **중첩 함수 코드의 평가와 실행**

- 함수 코드 실행 중단, 중첩 함수 내부 코드 평가로 함수 실행 컨텍스트 생성 후 실행 컨텍스트에 푸시. 중첩 함수 코드 실행(실행 컨텍스트 스택에서 제거).

4. **함수 코드로 복귀**

- 중첩 함수 코드 종료 후 함수 코드 실행(실행 컨텍스트 스택에서 제거).

5. **전역 코드로 복귀**

- 함수 코드 종료 후 전역 코드 실행(실행 컨텍스트 스택에서 제거).
  → 이후 실행 컨텍스트 스택에는 아무것도 없으므로 코드 종료.

---

# 렉시컬 환경

- **렉시컬 환경**: 식별자와 식별자에 바인딩된 값, 상위 스코프에 대한 참조를 기록하는 자료구조로 실행 컨텍스트를 구성하는 컴포넌트.
- **렉시컬 환경**은 객체형태의 스코프를 생성하여 식별자를 키로 등록하고 식별자에 바인딩된 값을 관리 → **저장소 역활**을 하는 렉시컬 스코프의 실체.
- 실행 컨텍스트는 LexicalEnvironment 컴포넌트와 VariableEnvironment 컴포넌트로 구성.
- 위 두 컴포넌트를 렉시컬 환경으로 통일해 간략히 설명하자면
  렉시컬 환경은 EnvironmentRecord 컴포넌트와 OuterLexicalEnvironmentReference 컴포넌트로 구성 된다.
- **환경 레코드(Environment Record)**
- 스코프에 포함된 식별자를 등록하고 바인딩된 값을 관리하는 저장소.
- **외부 렉시컬 환경에 대한 참조(Outer Lexical Environment Reference)**
- 외부 렉시컬 환경에 대한 참조는 상위 스코프(실행 컨텍스트를 생성한 소스코드를 포함하는 상위 코드의 렉시컬 환경)를 가리킨다. → 이를 통해 스코프 체인을 구현.

---

# 실행 컨텍스트의 생성과 식별자 검색 과정

```jsx
var x = 1;
const y = 2;

function foo(a) {
  var x = 3;
  const y = 4;

  function bar(b) {
    const z = 5;
    console.log(a + b + x + y + z);
  }
  bar(10);
}

foo(20); // 42
```

<br>

**전역 객체 생성** -> **전역 코드 평가** -> **전역 코드 실행** -> **foo 함수 코드 평가** -> **foo 함수 코드 실행** -> **bar 함수 코드 평가** -> **bar 함수 코드 실행** -> **bar 함수 코드 실행 종료** -> **foo 함수 코드 실행 종료** -> **전역 코드 실행 종료**

---

# 실행 컨텍스트의 생성과 식별자 검색 과정

#### **1. 전역 객체 생성**

- **전역 객체는 전역 코드가 평가되기 이전에 생성.**
- 빌트인 전역 프로퍼티와 빌트인 전역 함수, 표준 빌트인 객체가 추가되며, 동작 환경에 따라 Web API 또는 특정 환경을 위한 호스트 객체를 포함.
- 전역 객체도 Object.prototype을 상속 받음. → **전역 객체도 프로토타입 체인의 일원**.

```jsx
// Object.prototype.toString
window.toString(); // -> "[object Window]"
window.__proto__.__proto__.__proto__.__proto__ === Object.prototype; // true
```

---

# 실행 컨텍스트의 생성과 식별자 검색 과정

#### **2. 전역 코드 평가**

- 전역 코드 평가는 아래와 같은 순서, 전역 실행 컨텍스트와 렉시컬 환경 그림 참고.

![이미지](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/fd821bdd-344d-4f0b-a794-eaa691c7744b/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220519%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220519T050315Z&X-Amz-Expires=86400&X-Amz-Signature=a9ca4640d8aa9f263eab8e1d8b07f2788bded2de85fb842ef2b33ff14e32ba06&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

---

# 실행 컨텍스트의 생성과 식별자 검색 과정

#### **2. 전역 코드 평가**

**1. 전역 실행 컨텍스트 생성**

- 비어있는 전역 실행 컨텍스트 생성 후 실행 컨텍스트 스택에 푸시.

![이미지](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/fbaa6611-b233-436b-b32f-748cb9cea514/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220519%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220519T112439Z&X-Amz-Expires=86400&X-Amz-Signature=09826643cbf4ee90835d6dc05d9b5ad5db3ac3e597f0e5169c39e15e274432ed&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

<style>
img {
  margin: 0 auto;
  width: 300px;
}
</style>

---

# 실행 컨텍스트의 생성과 식별자 검색 과정

#### **2. 전역 코드 평가**

**2. 전역 렉시컬 환경 생성**

- 전역 렉시컬 환경을 생성 후 전역 실행 컨텍스트에 바인딩.

![이미지](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/067f1ab6-6da8-470f-b7d9-b8f02dd45271/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220519%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220519T112507Z&X-Amz-Expires=86400&X-Amz-Signature=ec880126aa58bf714360365f8703dce55358b7208139394807bdcc33f90a4261&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

<style>
img {
  margin: 0 auto;
  width: 500px;
}
</style>

---

# 실행 컨텍스트의 생성과 식별자 검색 과정

#### **2. 전역 코드 평가**

**2. 전역 렉시컬 환경 생성**
<br>

**2.1 전역 환경 레코드 생성**

- **전역 환경 레코드**는 전역 스코프, 전역 프로퍼티, 전역 함수, 객체를 제공.
- **전역 환경 레코드**는 **객체 환경 레코드**와 **선언적 환경 레코드**로 구성.
  이유: 변수 키워드마다 선언한 전역 변수를 구분하여 관리하기 위해.
- **객체 환경 레코드**: var 키워드로 선언한 전역 변수, 함수 선언문으로 정의한 전역 함수, 빌트인 전역 프로퍼티, 빌트인 전역 함수, 표준 빌트인 객체 관리.
- **선언적 환경 레코드**: let, const 키워드로 선언한 전역 변수.

---

# 실행 컨텍스트의 생성과 식별자 검색 과정

#### **2. 전역 코드 평가**

**2. 전역 렉시컬 환경 생성**
<br>

**2.1 전역 환경 레코드 생성** - **2.1.1 객체 환경 레코드 생성**

- 객체 환경 레코드는 **BindingObject라고 부르는 객체**와 연결.
- var 키워드로 선언한 전역 변수와 함수 선언문으로 정의된 전역 함수는 전역 환경 레코드의 객체 환경 레코드에 연결된 BindingObject를 통해 **전역 객체의 프로퍼티와 메서드가 됨**.
- 이때 선언 단계와 초기화 단계가 동시 진행됨.(undefined)

![이미지](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/57e45d83-3f7b-4266-9607-e0d4d2e1abaf/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220519%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220519T113809Z&X-Amz-Expires=86400&X-Amz-Signature=4729085afbb6fc89ca34db06e29199eb11e7cd29e5a622112874cd6c817833df&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

<style>
img {
  margin: 0 auto;
  width: 500px;
}
</style>

---

# 실행 컨텍스트의 생성과 식별자 검색 과정

#### **2. 전역 코드 평가**

**2. 전역 렉시컬 환경 생성**
<br>

**2.1 전역 환경 레코드 생성** - **2.1.2 선언적 환경 레코드 생성**

- let, const 키워드로 선언한 전역 변수가 **선언적 환경 레코드에 등록, 관리**.
- 이때 선언 단계와 초기화 단계가 분리되는 일시적 사각지대이다.

![이미지](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/65bcaf4c-6928-460b-ab04-76457d35f142/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220519%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220519T114201Z&X-Amz-Expires=86400&X-Amz-Signature=b72dca841af7f8fc1a609808b9d565184edf5379ef335482b77e29967e61fa56&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

<style>
img {
  margin: 0 auto;
  width: 600px;
}
</style>

---

# 실행 컨텍스트의 생성과 식별자 검색 과정

#### **2. 전역 코드 평가**

**2. 전역 렉시컬 환경 생성**
<br>

**2.2 this 바인딩**

- 전역 환경 레코드의 **[[GlobalThisValue]] 내부 슬롯에 this가 바인딩**.
- 이때 객체 환경 레코드와 선언적 환경 레코드에는 this 바인딩이 없음.

->this 바인딩은 전역 환경 레코드와 함수 환경 레코드에만 존재.

![이미지](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/34c2f026-9a9a-4488-b62e-1fb1de5db2ed/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220519%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220519T114830Z&X-Amz-Expires=86400&X-Amz-Signature=03202932ddb435df10eecef2891af83eefbe26e1629c3f113d40c5bf4a27f7a3&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

<style>
img {
  margin: 0 auto;
  width: 600px;
}
</style>

---

# 실행 컨텍스트의 생성과 식별자 검색 과정

#### **2. 전역 코드 평가**

**2. 전역 렉시컬 환경 생성**
<br>

**2.3 외부 렉시컬 환경에 대한 참조 결정**

- **외부 렉시컬 환경**에 대한 참조는 상위 스코프를 가리킨다.
- 현재 평가 중인 소스코드는 전역 코드, 상위 스코프가 없기에 null이 할당됨.

→ 전역 렉시컬 환경이 스코프 체인의 종점에 존재함을 의미.

![이미지](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/847dfea3-4479-4657-95d1-0f3800358dee/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220519%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220519T114928Z&X-Amz-Expires=86400&X-Amz-Signature=a0b5197c1a547bc56c936e27593c3d86b2d2c5b5ea89e6fe13295eabac9e4f3e&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

<style>
img {
  margin: 0 auto;
  width: 600px;
}
</style>

---

# 실행 컨텍스트의 생성과 식별자 검색 과정

#### **3. 전역 코드 실행**

- 전역 코드가 순차적으로 실행되어 변수 x, y에 값이 할당 되고 foo 함수가 호출 됨.
- **식별자 결정**: 어느 스코프의 식별자를 참조하면 되는지 결정.
- 식별자 결정을 위해 실행 중인 실행 컨텍스트에서 식별자를 검색 시작.
- 실행중인 컨텍스트의 렉시컬 환경에서 식별자를 검색할 수 없으면 상위 스코프로 이동하여 식별자를 검색.
- 전역 렉시컬 환경에서 검색할 수 없는 식별자는 참조 에러를 발생.

---

# 실행 컨텍스트의 생성과 식별자 검색 과정

#### **4. foo 함수 코드 평가**

![이미지](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/9e59e489-46db-4ca8-a015-4381480423e5/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220519%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220519T115119Z&X-Amz-Expires=86400&X-Amz-Signature=ee1eaf5784314f4309b7ccd1fac24bc8115ebd20bfc6861504e224a7793e2f79&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

<style>
img {
  margin: 0 auto;
  width: 600px;
}
</style>

---

# 실행 컨텍스트의 생성과 식별자 검색 과정

#### **4. foo 함수 코드 평가**

**1. 함수 실행 컨텍스트 생성**

- foo함수 실행 컨텍스트 생성. 함수 렉시컬 환경 완성 후 실행 컨텍스트 스택 푸시.

---

# 실행 컨텍스트의 생성과 식별자 검색 과정

#### **4. foo 함수 코드 평가**

**2. 함수 렉시컬 환경 생성**

- foo 함수 렉시컬 환경(함수 환경 레코드, 외부 렉시컬 환경으로 구성)을 생성, foo 함수 실행 컨텍스트에 바인딩.

---

# 실행 컨텍스트의 생성과 식별자 검색 과정

#### **4. foo 함수 코드 평가**

**2. 함수 렉시컬 환경 생성**

- **2.1. 함수 환경 레코드 생성**
  - 함수 환경 레코드는 매개변수, arguments 객체, 함수 내부에서 선언한 지역 변수와 중첩 함수를 등록, 관리.
- **2.2 this 바인딩**
  - 함수 관경 레코드의 [[ThisValue]] 내부 슬롯에 this가 바인딩.
    이는 전역 객체를 가리킴.
- **2.3 외부 렉시컬 환경에 대한 참조 결정**
  - 외부 렉시컬 환경에 대한 참조에 foo 함수 정의가 평가된 시점에 실행 중인 실행 컨텍스트의 렉시컬 환경의 참조가 할당.
    → foo 함수 정의는 전역 코드 평가 시점에 평가.
    → 외부 렉시컬 환경에 대한 참조에는 전역 렉시컬 환경의 참조가 할당.
  - 함수의 상위 스코프를 함수 객체의 내부 슬롯 [[Environment]]에 저장.
  - 함수 객체의 내부 슬롯[[Environment]]가 렉시컬 스코프 구현 메커니즘.

---

# 실행 컨텍스트의 생성과 식별자 검색 과정

#### **5. foo 함수 코드 실행**

![이미지](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/116cc515-4e99-42c1-b72f-074801998960/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220519%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220519T115448Z&X-Amz-Expires=86400&X-Amz-Signature=d57119be6cf8a01cbb8d74e5c8921eec06acc4ce9de56f8d66d08ef254b74629&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

- **런타임 시작**으로 foo 함수의 소스코드가 순차적으로 실행.
- 매개변수에 인수가 할당, 변수 할당문이 실행, 함수 bar가 호출.
- **식별자 결정**을 위해 실행 중인 실행 컨텍스트의 렉시컬 환경에서 식별자 검색 시작.

-> foo 함수 렉시컬 환경에서 검색 후 검색할 수 없다면 상위 스코프로 이동.

<style>
img {
  margin: 0 auto;
  width: 600px;
}
</style>

---

# 실행 컨텍스트의 생성과 식별자 검색 과정

#### **6. bar 함수 코드 평가**

![이미지](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/1956ee51-6e95-437c-b7fa-78b154cf93bd/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220519%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220519T115539Z&X-Amz-Expires=86400&X-Amz-Signature=c17aa0fb2eb980f14bae3fc18fee088a84bf7e046e030348a2e062405a68a6f3&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

- bar 함수 호출 시 bar 함수 내부로 코드의 제어권이 이동, bar 함수 코드 평가 시작.
- 실행 컨텍스트와 렉시컬 환경 싱성 과정은 **foo 함수 코드 평가와 동일**.

<style>
img {
  margin: 0 auto;
  width: 500px;
}
</style>

---

# 실행 컨텍스트의 생성과 식별자 검색 과정

#### **7. bar 함수 코드 실행**

- **런타임 시작**으로 bar 함수의 소스 코드가 순차적으로 실행.
- 매개변수에 인수가 할당, 변수 할당문이 실행, console.log(a+b+x+y+z) 실행.
- **bar 함수 코드 실행 순서**
  1. **console 식별자 검색**
     : console 식별자를 bar 함수 렉시컬 환경에서 검색, console 식별자 없음.
     → 상위 스코프로 이동, foo 함수 렉시컬 환경에서 검색, 식별자 없음.
     → 상위 스코프로 이동, 전역 렉시컬 환경에서 검색, 객체 환경 레코드의 BindingObject를 통해 전역 객체에서 찾음.
  2. **log 메서드 검색**
     : console 객체에서 log 메서드를 검색
     → log 메서드는 상속된 프로퍼티가 아닌 console 객체가 직접 소유한 프로퍼티.
  3. **표현식 a + b + x + y + z의 평가**
     : a, b, x, y, z 식별자 검색, 현재 실행 중인 컨텍스트의렉시컬 환경에서 시작하여 외부 렉시컬 환경에 대한 참조로 이어지는 렉시컬 환경의 연속에서 검색.
  4. **console.log 메서드 호출**
     : 표현식 a + b + x + y + z가 평가되어 생성한 값을 console.log 메서드에 전달.

---

# 실행 컨텍스트의 생성과 식별자 검색 과정

#### **bar 함수 코드 실행 종료**

- 더 이상 실행할 코드가 없으므로 **bar 함수 코드의 실행 종료**.
- **실행 컨텍스트 스택에서** bar 함수 실행 컨텍스트가 팝되어 **제거**.
- bar 함수 실행 컨텍스트가 소멸되더라도 bar 함수 렉시컬 환경을 누군가 참조하고 있다면 bar 함수 렉시컬 환경은 소멸하지 않음.

#### **9. foo 함수 코드 실행 종료**

- 더 이상 실행할 코드가 없으므로 **foo 함수 코드의 실행 종료**.
- **실행 컨텍스트 스택에서** foo 함수 실행 컨텍스트가 팝되어 **제거**.

#### **10. 전역 코드 실행 종료**

- 더 이상 실행할 코드가 없으므로 **전역 코드의 실행 종료**.
- **실행 컨텍스트 스택에서** 전역 실행 컨텍스트가 팝되어 **제거**.

→ 실행 컨텍스트 스택에는 아무것도 남아있지 않게 됨.

---

# 실행 컨텍스트와 블록 레벨 스코프

```jsx
let x = 1;

if (true) {
  let x = 10;
  console.log(x); // 10
}

console.log(x); // 1
```

- if문의 코드 블록을 위한 **외부 렉시컬 환경에 대한 참조는 전역 렉시컬 환경을 가리킴**.
- if문뿐 아닌 **블록 레벨 스코프를 생성하는 모든 블록문에 적용.**

---

# 실행 컨텍스트와 블록 레벨 스코프

![이미지](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/c871efc1-a4de-44f9-b672-6d7550e80213/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220519%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220519T045354Z&X-Amz-Expires=86400&X-Amz-Signature=91d3952d90d58889c196f8bd3b745c66d621896f6dd83bdd823b5c151a6431ee&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

<style>
img {
  margin: 0 auto;
  height: 450px;
}
</style>
