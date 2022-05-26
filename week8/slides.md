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

## week8
25장 클래스

---
layout: center
class: text-left
---

# 클래스와 생성자 함수 차이점

. | 클래스 | 생성자 함수
--|--|--
new 연산자 | new 연산자 없이 호출하면 에러가 발생 | new 연산자 없이 호출하면 일반 함수로서 호출
extends, super | 상속을 지원하는 extends와 super 키워드를 제공 | 지원하지 않음
호이스팅 | 호이스팅이 발생하지 않는 것처럼 동작 | 함수 선언문으로 정의된 생성자 함수는 함수 호이스팅, 함수 표현식으로 정의한 생성자 함수는 변수 호이스팅 발생
strict mode | 클래스 내의 모든 코드에는 암묵적으로 strict mode가 저장되어 실행되며 strict mode를 해제할 수 없다. | 암묵적으로 strict mode가 지정되지 않는다.
[[Enumerable]]의 값 | constructor, 프로토타입 메서드, 정적 메서드는 모두 프로퍼티 어트리뷰트 [[Enumerable]]의 값이 false. 열거되지 않는다. | 


---
class: text-left
---


# 클래스의 정의  

class 키워드를 사용하여 정의. 파스칼 케이스를 사용.
```javascript
  // 클래스 선언문
  class Person {}
```
일반적이지는 않지만 함수와 마찬가지로 표현식으로 클래스를 정의할 수도 있다.
```javascript
  // 익명 클래스 표현식
  const Person = class {};

  // 기명 클래스 표현식
  const Person = class MyClass ();
```

<style>
  .slidev-layout h1 + p {
    opacity: 1;
  }
</style>

---
class: px-20
---

# 클래스의 특징  

클래스는 함수다.  
따라서 클래스는 값처럼 사용할 수 있는 일급 객체다.
- 무명의 리터럴로 생성할 수 있다. 즉, 런타임에 생성이 가능하다.
- 변수나 자료구조(객체, 배열 등)에 저장할 수 있다.
- 함수의 매개변수에게 전달할 수 있다.
- 함수의 반환값으로 사용할 수 있다. 


<style>
  .slidev-layout h1 + p {
    opacity: 1;
  }
</style>

---
class: px-40 text-center
---

# 클래스와 생성자 함수의 정의 방식 비교

<img alt="클래스와 생성자 함수의 정의 방식 비교" src="https://user-images.githubusercontent.com/44577555/169783238-5771091a-021b-42ec-b625-e00895874b23.png">


---
class: px-20
---

# 클래스 호이스팅

클래스는 함수로 평가된다.
```javascript
  // 클래스 선언문
  class Person {}
  console.log(typeof Person); // function
```

클래스 선언문으로 정의한 클래스는 함수 선언문과 같이 런타임 이전(소스코드 평가 과정)에 먼저 평가되어 함수 객체를 생성

함수 정의가 평가되어 함수 객체(constructor)를 생성하는 시점에 프로토타입도 생성 -> 프로토타입과 생성자 함수는 언제나 쌍으로 존재  

클래스 선언문은 let, const 키워드로 선언한 변수처럼 호이스팅 된다. -> 클래스 선언문 이전에 일시적 사각지대

```javascript
  const Person = '';

  {
    // 호이스팅이 발생하지 않는다면 ''이 출력되어야 한다.
    console.log(Person); // ReferenceError: Cannot access 'Person' before initialization
    // 클래스 선언문
    class Person {}
  }
```

<style>
  .slidev-layout h1 + p {
    opacity: 1;
  }
  p {
    font-size: 0.8em !important;
  }
</style>

--- 

# 인스턴스 생성

클래스는 생성자 함수이며 new 연산자와 함께 호출되어 인스턴스를 생성한다. 반드시 new 연산자와 함께 호출.  
<span class="desc">함수 - new 연산자 사용 여부에 따라 일반함수 또는 생성자 함수가 된다.</span>

클래스 표현식으로 정의된 클래스의 경우 클래스를 가리키는 식별자를 사용해 인스턴스를 생성해야 한다.  
<span class="desc">기명 함수 표현식과 마찬가지로 클래스 표현식에서 사용한 클래스 이름은 외부 코드에서 접근 불가능하기 때문</span>

<div class="pt-10">

```javascript
  const Person = class MyClass {};

  // 함수 표현식과 마찬가지로 클래스를 가리키는 식별자로 인스턴스를 생성해야 한다.
  const me = new Person();

  // 클래스 이름 MyClass는 함수와 동일하게 클래스 몸체 내부에서만 유효한 식별자다.
  console.log(MyClass); // ReferenceError: MyClass is not defined

  const you = new MyClass(); // ReferenceError: MyClass is not defined
```

</div>

<div v-click="6" class="pt-4">
  <strong>undefined</strong>
  <p>foo 함수 내부에서 선언된 지역 변수 x는 ??? 시점에 이미 선언되었고 undefined 초기화되었다. <br>이처럼 호이스팅은 스코프 단위로 동작한다.</p>

  > 호이스팅이란? 변수 선언이 스코프의 선두로 끌어 올려진 것처럼 동작하는 자바스크립트 고유의 특징
</div>

<style>
.slidev-layout h1 + p {
  opacity: 1;
}
p {
  font-size: 0.8em !important;
}
strong {
  font-weight: bold;
  color: #b5794a;
}
.desc {
  opacity: 0.5;
}
</style>

---
layout: center
class: text-center
---

# 메서드
클래스 몸체에서 정의할 수 있는 메서드  
constructor(생성자), 프로토타입 메서드, 정적 메서드

<style>
.slidev-layout h1 + p {
  margin-top: 2em;
  opacity: 1;
}
</style>

---

# constructor
constructor는 인스턴스를 생성하고 초기화하기 위한 특수한 메서드

<div grid="~ cols-2 gap-10">
<div>

```javascript
  class Person {
    // 생성자
    constructor(name) {
      // 인스턴스 생성 및 초기화
      this.name = name;
    }
  }

  // 클래스는 함수다
  console.log(typeof Person); // function
  console.dir(Person); // class Person
```

</div>
<div>

<img width="250" alt="클래스와 생성자 함수의 정의 방식 비교" src="https://user-images.githubusercontent.com/44577555/169851947-d40a643c-b649-459d-a879-37567be392f9.png">

모든 함수 객체가 가지고 있는 프로토타입 객체의 constructor 프로퍼티는 클래스 자신을 가리키고 있다.  
이는 클래스가 인스턴스를 생성하는 생성자 함수라는 것을 의미한다.  
즉, <strong>new 연산자와 함께 클래스를 호출하면 클래스는 인스턴스를 생성</strong>한다.
</div>
</div>

<style>
.slidev-layout h1 + p {
  opacity: 1;
}
p {
  margin: 0.1em !important;
  font-size: 0.8em !important;
}
strong {
  font-weight: bold;
  color: #b5794a;
}
img {
  margin-top: 0.3em;
}
</style>

---

<div grid="~ cols-2 gap-10">
<div>

```javascript
  class Person {
    // 생성자
    constructor(name) {
      // 인스턴스 생성 및 초기화
      this.name = name;
    }
  }

  const me = new Person('Choi');
  console.log(me);
```

</div>
<div>

<img width="300" alt="클래스와 생성자 함수의 정의 방식 비교" src="https://user-images.githubusercontent.com/44577555/169854114-342a4a03-a7e5-4af1-b86a-85a2c3a13830.png">

Person 클래스의 constructor 내부에서 this에 추가한 name 프로퍼티가 클래스가 생성한 인스턴스의 프로퍼티로 추가된 것을 확인할 수 있다.
</div>
</div>

constructor 내부의 this는 클래스가 생성한 인스턴스를 가리킨다.  
클래스 몸체에 정의한 constructor는 단순한 메서드로 해석되는 것이 아니라 클래스가 평가되어 생성한 함수 객체 코드의 일부가 된다.  
다시 말해, 클래스 정의가 평가되면 constructor의 기술된 동작을 하는 함수 객체가 생성된다.

> `클래스의 constructor 메서드와 프로토타입의 constructor 프로퍼티`  
클래스의 constructor 메서드와 프로토타입의 constructor 프로퍼티는 이름이 같아 혼동하기 쉽지만 직접적인 관련이 없다.  
프로토타입의 constructor 프로퍼티는 모든 프로토타입이 가지고 있는 프로퍼티이며, 생성자 함수를 가리킨다.  

<style>
h2 {
  color: #b39c36;
  font-size: 1em !important;
  margin-top: 1.3rem !important;
}
p {
  margin: 0.1em !important;
  font-size: 0.8em !important;
}
strong {
  font-weight: bold;
  color: #b5794a;
}
img {
  margin-top: 0.3em;
}
</style>

---

## 클래스의 constructor
생성자 함수와 유사하지만 몇가지 차이점이 있다.

- constructor는 클래스 내에 최대 한 개만 존재
- constructor는 생략할 수 있다. 생략하면 클래스에 빈 consructor가 암묵적으로 정의된다.  
  따라서 constructor를 생략한 클래스는 빈 constructor에 의해서 빈 객체를 생성한다.
- 프로퍼티가 추가되어 초기화된 인스턴스를 생성하려면 constructor 내부에서 this에 인스턴스 프로퍼티를 추가한다.
- 외부에서 인스턴스 프로퍼티의 초기값을 전달하려면 constructor에 매개변수를 선언하고 인스턴스를 생성할 때 초기값을 전달한다.  
  이때 초기값은 constructor의 매개변수에게 전달된다.
  ```javascript
    class Person {
      constructor(name, address) {
        this.name = name;
        this.address = address;
      }
    }

    // 인스턴스 프로퍼티가 추가된다.
    const me = new Person('Choi', 'Seoul');
    console.log(me); // Person {name: "Choi", address: "Seoul"}
  ```

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
li {
  margin: 0.5em !important;
  font-size: 0.8em !important;
}
</style>

---

- new 연산자와 함께 클래스가 호출되면 생성자함수와 동일하게 암묵적으로 this, 즉 인스턴스를 반환한다.
  constructor 내부에서 명시적으로 this가 아닌 다른 값을 반환하는 것은 클래스의 기본 동작을 훼손하는 것이므로 <strong>constructor 내부에서 return 문은 반드시 생략</strong>해야 한다.
  ```javascript
    class Person {
      constructor(name) {
        this.name = name;

        // return {}; // 명시적으로 객체를 반환하면 암묵적인 this 반환이 무시된다.
        //return 100; // 명시적으로 원시값을 반환하면 원시값 반환은 무시되고 암묵적으로 this가 반환된다.
      }
    }

    // constructor에서 명시적으로 반환한 빈 객체가 반환된다.
    const me = new Person('Choi');
    console.log(me); // Person {name: 'Choi'}
  ```

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
li {
  margin: 0.5em !important;
  font-size: 0.8em !important;
}
strong {
  font-weight: bold;
  color: #b5794a;
}
</style>

---

# 프로토타입 메서드

<div grid="~ cols-2 gap-10">
<div>

셍성자 함수를 사용하여 인스턴스를 생성하는 경우 프로토타입 메서드를 생성하기 위해서는 다음과 같이 명시적으로 프로토타입에 메서드를 추가해야 한다.
```javascript
  // 생성자 함수
  function Person(name) {
    this.name = name;
  }

  // ⭐️ 생성자 함수 프로토타입 메서드
  Person.prototype.sayHi = function() {
    console.log(`Hi my name is ${this.name}`);
  }

  const me = new Peroson('Choi');
  me.sayHi(); // Hi my name is Choi
```   

</div>
<div>

클래스 몸체에서 정의한 메서드는 클래스의 prototype 프로퍼티에 메서드를 추가하지 않아도 기본적으로 프로토타입 메서드가 된다.
```javascript
  class Person {
    // 생성자
    constructor(name) {
      // 인스턴스 생성 및 초기화
      this.name = name;
    }
  }

  // ⭐️ 클래스 프로토타입 메서드
  sayHi() {
    console.log(`Hi my name is ${this.name}`);
  }

  const me = new Peroson('Choi');
  me.sayHi(); // Hi my name is Choi
``` 

</div>
</div>


<style>
p {
  margin: 0.5em !important;
  font-size: 0.9em !important;
}
</style>

---

클래스가 생성한 인스턴스는 프로토타입 체인의 일원이 된다.
```javascript
  class Person {
    constructor(name) {
      this.name = name;
    }
  }

  sayHi() {
    console.log(`Hi my name is ${this.name}`);
  }

  const me = new Peroson('Choi');
  me.sayHi(); // Hi my name is Choi

  // me 객체의 프로토타입은 Person.prototype이다.
  Object.getPrototypeOf(me) === Person.prototype; // true
  me instanceof Person; // true

  // Person.prototype의 프로토타입은 Object.prototype이다.
  Object.getPrototypeOf(Person.prototype) === Object.prototype; // true
  me instanceof Obejct; // true

  // me 객체의 constructor는 Person 클래스다.
  me.constructor === Person; // true
``` 

<style>
  .slidev-layout h1 + p {
    opacity: 1;
  }
  p {
    margin: 0.5em !important;
    font-size: 0.9em !important;
  }
</style>

---

# 정적 메서드
정적 메서드는 인스턴스를 생성하지 않아도 호출할 수 있는 메서드를 말한다.
<div grid="~ cols-2 gap-10">
<div>

## 생성자 함수 정적 메서드
```javascript
  // 생성자 함수
  function Person(name) {
    this.name = name;
  }

  // 정적 메서드 
  Person.sayHi = function () {
    console.log('Hi!');
  }

  // 정적 메서드 호출
  Person.sayHi(); // Hi!
```  
 

</div>
<div>

## 클래스 정적 메서드 
```javascript
  class Person {
    // 생성자
    constructor(name) {
      // 인스턴스 생성 및 초기화
      this.name = name;
    }

    // 정적 메서드
    static sayHi() {
      console.log('Hi');
    }
  }
``` 
<div class="desc">클래스에서는 메서드에 <strong>static</strong> 키워드를 붙이면 정적 메서드가 된다.</div>
</div>
</div>

<style>
.slidev-layout h1 + p {
  opacity: 1;
}
h2 {
  color: #b39c36;
  font-size: 1em !important;
  margin-top: 1.3rem !important;
}
p {
  font-size: 0.9em !important;
}
.desc {
  font-size: 0.8em !important;
  margin-top: 1rem !important;
}
strong {
  font-weight: bold;
  color: #b5794a;
}
</style>

---

## 클래스 정적 메서드
정적 메서드는 클래스에 바인딩된 메서드가 된다.  
클래스는 함수 객체로 평가되므로 프로퍼티나/메서드를 소유할 수 있고 클래스 정의 이후 인스턴스를 생성하지 않아도 호출할 수 있다.  

정적 메서드는 프로토타입 메서드처럼 인스턴스로 호출하지 않고 클래스로 호출한다.
```javascript
  // 정적 메서드는 클래스로 호출한다.
  // 정적 메서드는 인스턴스 없이도 호출할 수 있다.
  Person.sayHi(); // Hi
``` 
정적 메서드는 인스턴스로 호출할 수 없다.  
인스턴스의 프로토타입 체인 상에는 클래스가 존재하지 않기 때문에 인스턴스로 클래스의 메서드를 상속받을 수 없다.
```javascript
  // 인스턴스 생성
  const me = new Person('Lee');
  me.sayHi(); // TypeError: me.sayHi is not a function
``` 

<style>
  h2 {
    color: #b39c36;
    font-size: 1.5em !important;
  }
  p {
    font-size: 0.9em !important;
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

## 정적 메서드와 프로토타입 메서드의 차이

1. 정적 메서드와 프로토타입 메서드는 자신이 속해 있는 프로토타입 체인이 다르다.
2. 정적 메서드는 클래스로 호출하고 프로토타입 메서드는 인스턴스로 호출한다.
3. 정적 메서드는 인스턴스 프로퍼티를 참조할 수 없지만 프로토타입 메서드는 인스턴스 프로퍼티를 참조할 수 있다.  
  따라서 인스턴스 프로퍼티를 참조해야 한다면 정적 메서드 대신 프로토타입 메서드를 사용해야 한다.
<div grid="~ cols-2 gap-4">
<div>

```javascript
  class Square {
    // 정적 메서드
    static area(width, height) {
      return width * height;
    }
  }

  console.log(Square.area(10, 10)); // 100
``` 

</div>
<div>
 
```javascript
  class Square {
    // 인스턴스
    constructor(width, height) {
      // 인스턴스 프로퍼티 초기화
      this.width = width;
      this.height = height;
    }

    // 프로토타입 메서드
    area() {
      return this.width * this.height;
    }
  }
  const square = new Square(10, 10);
  console.log(square.area()); // 100
``` 

</div>
</div>


<style>
  h2 {
    color: #b39c36;
    font-size: 1.5em !important;
  }
  h3 {
    color: #809e41;
    font-size: 1.5em !important;
  }
  h4 {  
    margin-bottom: 10px;
    font-size: 1em !important;
  }  
  p, li {
    font-size: 0.8em !important;
  }
  strong {
    font-weight: bold;
    color: #b5794a;
  }
</style>

---

## 프로토타입 메서드와 정적 메서드 내부의 this 바인딩

<strong>프로토타입 메서드 내부의 this는</strong> 메서드를 소유한 객체가 아니라 메서드를 호출한 객체, 즉 메서드 이름 앞의 마침표 연산자 앞에 기술한 객체에 바인딩 된다.  

<strong>정적 메서드</strong>는 클래스로 호출해야 하므로 정적 메서드 내부의 <strong>this는</strong> 인스턴스가 아닌 <strong>클래스</strong>를 가리킨다.  

즉, 프로토타입 메서드와 정적 메서드 내부의 this 바인딩이 다르다.   
따라서 메서드 내부에서 인스턴스 프로퍼티를 참조하려면 this를 사용해야 하며, 이러한 경우 프로토타입 메서드로 정의해야 한다.  


```javascript
  class Square {
    // 인스턴스
    constructor(width, height) {
      this.width = width;
      this.height = height;
    }

    // 프로토타입 메서드
    area() {
      return this.width * this.height;
    }
  }
  const square = new Square(10, 10);
  console.log(square.area()); // 100
``` 

<style>
  h2 {
    color: #b39c36;
    font-size: 1.5em !important;
  }
  p, li {
    font-size: 0.8em !important;
  }
  strong {
    font-weight: bold;
    color: #b5794a;
  }
  .slidev-layout h1 + p {
    opacity: 1;
  }
</style>

---

## 클래스에서 정의한 메서드의 특징

1. function 키워드를 생략한 메서드 축약 표현을 사용한다.
2. 객체 리터럴과는 다르게 클래스에서 메서드를 정의할 때는 콤마(,)가 필요 없다.
3. 암묵적으로 strict mode로 실행된다.
4. for ...in 문이나 Object.keys 메서드 등으로 열거할 수 없다.  
  즉, 프로퍼티 어트리뷰트[[Enumerable]]의 값이 false다.
5. 내부 메서드 [[Construct]]를 갖지 않는 non-constructor다. 따라서 new 연산자와 함께 호출할 수 없다.

<style>
  h2 {
    color: #b39c36;
    font-size: 1.5em !important;
  }
  ol {
    margin-top: 2em;
  }
  strong {
    font-weight: bold;
    color: #b5794a;
  }
  .slidev-layout h1 + p {
    opacity: 1;
  }
</style>

---

# var 키워드로 선언한 변수의 문제점

1. 변수 중복 선언 허용
2. 함수 레벨 스코프
3. 변수 호이스팅  

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

```javascript 
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
일반적으로 상수의 이름은 대문자로 사용하고 여러 단어로 이뤄진 경우에는 스네이크 케이스로 표현한다.

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
