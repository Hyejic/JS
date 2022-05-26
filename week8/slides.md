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

# 클래스의 인스턴스 생성 과정

1. 인스턴스 생성과 this 바인딩  
new 연산자와 함께 클래스를 호출하면 constructor의 내부 코드가 실행되기에 앞서 암묵적으로 빈 객체가 생성된다.  
  -> 클래스가 생성한 인스턴스  
인스턴스의 프로토타입으로 클래스의 prototype 프로퍼티가 가리키는 객체가 설정된다.  
빈객체, 즉 인스턴스는 this에 바인딩된다. 따라서 constructor 내부의 this는 클래스가 생성한 인스턴스를 가리킨다.
2. 인스턴스 초기화
constructor의 내부 코드가 실행되어 this에 바인딩되어 있는 인스턴스를 초기화한다.  
즉, this에 바인딩 되어 있는 인스턴스에 프로퍼티를 추가하고 constructor가 인수로 전달받은 초기값으로 인스턴스의 프로퍼티 값을 초기화한다. 만약 constructor가 생략 되었다면 이 과정도 생략된다.
3. 인스턴스의 반환
클래스의 모든처리가 끝나면 완성된 인스턴스가 바인딩된 this가 암묵적으로 반환된다.

<style>
ol {
  margin-top: 3rem !important;
}
ol li {
  font-size: 1em !important;
  margin-top: 1.3rem !important;
}
p {
  margin: 0.5em !important;
  font-size: 0.8em !important;
}
</style>

---

```javascript
  class Person {
    // 생성자
    constructor(name) {
      // 1. 암묵적으로 인스턴스가 생성되고 this에 바인딩된다.
      console.log(this); // Person {}
      console.log(Object.getPrototypeOf(this) === Person.prototype); // true

      // 2. this에 바인딩되어 있는 인스턴스를 초기화한다.
      this.name = name;

      // 3. 완선된 인스턴스가 바인딩된 this가 암묵적으로 반환된다. return문 반드시 생략
    }
  }
```

---

# 프로퍼티

## 인스턴스 프로퍼티
인스턴스 프로퍼티는 constructor 내부에서 정의해야 한다.

```javascript
  class Person {
    constructor(name) {
      // 인스턴스 프로퍼티
      this.name = name; // name 프로퍼티는 public하다.
    }
  }

  const me = new Person('Choi');
  console.log(me); // Person {name: "Choi"}
  // name은 public하다.
  console.log(me.name); // Choi
``` 
constructor 내부에서 this에 추가한 프로퍼티는 언제나 클래스가 생성한 인스턴스의 프로퍼티가 된다.


<style>
  h2 {
    color: #b39c36;
    font-size: 1.5em !important;
  }
  p, li {
    font-size: 0.8em !important;
  }
  .slidev-layout h1 + p {
    opacity: 1;
  }
</style>

---
 
## 접근자 프로퍼티
접근자 프로퍼티는 자체적으로는 값을 갖지 않고 다른 데이터 프로퍼티의 값을 읽거나 저장할 때 사용하는 접근자 함수로 구성된 프로퍼티다. 

<div grid="~ cols-2 gap-4">
<div>

```javascript
  class Person {
    constructor(firstName, lastName) {
      this.firstName = firstName;
      this.lastName = lastName;
    }
    // fullName은 접근자 함수로 구성된 접근자 프로퍼티다.
    // getter 함수
    get fullName() {
      return `${this.firstName} ${this.lastName}`;
    },
    // setter 함수
    set fullName(name) {
      // 배열 디스트럭처링 할당
      [this.firstName, this.lastName] = name.split(' ');
    }
  }
  
  const me = new Person('Hyeji', 'Choi');
``` 

</div>
<div>

```javascript
  // 데이터 프로퍼티를 통한 프로퍼티 값의 참조.
  console.log(`${person.firstName} ${person.lastName}`); 
  // Hyeji Choi

  // 접근자 프로퍼티를 통한 프로퍼티 값의 저장
  // 접근자 프로퍼티 fullName에 값을 저장하면 setter함수가 호출된다.
  person.fullName = `Hihi Choi`;
  console.log(person); 
  // {firstName: 'Hihi', lastName: 'Choi'}

  // 접근자 프로퍼티를 통한 프로퍼티 값의 참조
  // 접근자 프로퍼티 fullName에 접근하면 getter 함수가 호출된다.
  console.log(person.fullName); // Hihi Choi

  // fullName은 접근자 프로퍼티다.
  // 접근자 프로퍼티는 get, set, enumeravle, configurable 프로퍼티 어트리뷰트를 갖는다.
  console.log(Object.getOwnPropertyDescriptor(person, 'fullName')); 
  // {enumerable: true, configurable: true, get: ƒ, set: ƒ}
``` 

</div>
</div>

<style>
  h2 {
    color: #b39c36;
    font-size: 1.5em !important;
  }
  p, li {
    font-size: 0.8em !important;
  }
  .slidev-layout h1 + p {
    opacity: 1;
  }
</style>

---

접근자 프로퍼티는 getter 함수와 setter 함수로 구성되어있다.  

- getter  
`인스턴스 프로퍼티에 접근할 때마다` 프로퍼티 값을 조작하거나 별도의 행위가 필요할 때 사용.  
반드시 무언가를 반환해야한다.  
- setter  
`인스턴스 프로퍼티에 값을 할당할 때마다` 프로퍼티 값을 조작하거나 별도의 행위가 필요할 때 사용.  
할당해야 할 때 사용하므로 반드시 매개변수가 있어야 한다. 단 하나의 값만 할당받기 때문에 단 하나의 매개변수만 선언할 수 있다. 

---

## 클래스 필드 정의 제안
클래스 필드는 클래스 기반 객체지향 언어에서 클래스가 생성할 인스턴스의 프로퍼티를 가리키는 용어다.  
자바의 클래스 필드는 마치 클래스 내부에서 변수처럼 사용된다.  

```java
  // 자바의 클래스 정의
  public class Person {
    // 1. 클래스 필드 정의
    // 클래스 필드는 클래스 몸체에 this 없이 선언해야 한다.
    private String firstName = "";
    private String lastName = "";

    // 생성자
    Person(String firstName, String lastName) {
      // 3. this는 언제나 클래스가 생성할 인스턴스를 가리킨다.
      this.firstName = firstName;
      this.lastName = lastName;
    }

    public String getFullName() {
      // 2. 클래스 필드 참조
      // this 없이도 클래스 필드를 참조할 수 있다.
      return firstName + " " + lastName;
    }
  }
``` 

<style>
  h2 {
    color: #b39c36;
    font-size: 1.5em !important;
  }
  p, li {
    font-size: 0.8em !important;
  }
  .slidev-layout h1 + p {
    opacity: 1;
  }
</style>

---

자바스크립트의 클래스 몸체에는 메서드만 선언할 수 있다.  
따라서 클래스 몸체에서 자바와 유사하게 클래스 필드를 선언하면 문법에러가 발생한다.  
하지만 최신 브라우저 또는 최신 Node.js에서 실행하면 문법 에러가 발생하지 않고 정상 동작한다.  
<span class="desc">"Class field declarations"가 2021년 1월 TC39 프로세스의 stage3에 제안</span>  
또는 Babel과 같은 build 시스템을 사용한다면 기능을 사용할 수 있다.

```javascript
  class Person {
    // 클래스 필드 정의
    name = 'Choi';
  }
  const me = new Person('Choi');
  console.log(me); // Person {name: 'Choi'}
```

<style>
ol {
  margin-top: 3rem !important;
}
ol li {
  font-size: 1.3em !important;
  margin-top: 1.3rem !important;
}
.desc {
  opacity: 0.5;
  font-size: 0.8em;
}
</style>

---

## 클래스 필드 정의 정의 방법

- this에 클래스 필드를 바인딩해서는 안 된다. this는 클래스의 constructor와 메서드 내에서만 유효하다.
  ```javascript
    class Person {
      // this에 클래스 필드를 바인딩해서는 안 된다.
      this.name = ''; // Uncaught SyntaxError: Unexpected token '.'
    }
  ``` 
- 클래스 필드를 참조하는 경우 자바스크립트에서는 this를 반드시 사용해야 한다.
  ```javascript
    class Person {
      // 클래스 필드
      name = 'Choi';
      
      constructor() {
        console.log(name); // 에러 발생 안됨. console 값 출력 안됨.
      }
    }

    new Person();
  ``` 

<style>
  h2 {
    color: #b39c36;
    font-size: 1.5em !important;
  }
  ul {
    margin-top: 1em;
  }
  p, li {
    font-size: 0.8em !important;
  }
  .slidev-layout h1 + p {
    opacity: 1;
  }
</style>

---

- 클래스 필드에 초기값을 할당하지 않으면 undefined를 갖는다.
  ```javascript
    class Person {
      // 클래스 필드 초기화 하지 않으면 undefined를 갖는다.
      name;
    }

    const me = new Person();
    console.log(me); // Person {name: undefined}
  ``` 
- 인스턴스를 생성할 때 외부의 초기값으로 클래스 필드를 초기화해야 할 필요가 있다면 constructor에서 클래스 필드를 초기화해야 한다.
  ```javascript
    class Person {
      name;

      constructor(name) {
        // 클래스 필드 초기화
        this.name = name;
      }
    }
    
    const me = new Person('Choi');
    console.log(me); // Person {name: 'Choi'}
  ``` 

<style>
  h2 {
    color: #b39c36;
    font-size: 1.5em !important;
  }
  ul {
    margin-top: 1em;
  }
  p, li {
    font-size: 0.8em !important;
  }
  .slidev-layout h1 + p {
    opacity: 1;
  }
</style>

---

- 함수는 일급 객체 이므로 함수를 클래스 필드에 할당할 수 있다. 따라서 클래스 필드를 통해 메서드를 정의할 수도 있다.
  ```javascript
    class Person {
      // 클래스 필드에 문자열을 할당
      name = 'Choi';

      // 클래스 필드에 함수 할당
      getName = function(){
        return this.name;
      }
      // 화살표 함수도 가능
      // getName = () => this.name;
    }
    
    const me = new Person();
    console.log(me); // Person {name: 'Choi', getName: ƒ}
    console.log(me.getName()); // Choi
  ``` 
  이처럼 클래스 필드에 함수를 할당하는 경우, 이 함수는 프로토타입 메서드가 아닌 인스턴스 메서드가 된다. 클래스 필드에 함수를 할당하는 것은 권장하지 않는다.

  ### 클래스 필드 정의 제안으로 인스턴스 프로퍼티 정의하는 방법 두 가지
  1. 외부 초기값으로 클래스 필드를 <strong>초기화할 필요가 있다면</strong> - constructor에서 인스턴스 프로퍼티를 정의하는 기존 방식 사용.
  2. 외부 초기값으로 클래스 필드를 <strong>초기화할 필요가 없다면</strong> - 기존의 constructor에서 인스턴스 프로퍼티를 정의하는 방식과 클래스 필드 정의 제안 모두 사용 가능.


<style>
  h3 {
    color: #b39c36;
    font-size: 1.2em !important;
  }
  p, li {
    font-size: 0.8em !important;
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

## private 필드 정의 제안
자바스크립트는 캡슐화를 완전하게 지원하지 않는다.  
ES6의 클래스도 생성자 함수와 마찬가지로 private, public, protected 키워드와 같은 접근 제한자를 지원하지 않는다.  

따라서 인스턴스 프로퍼티는 인스턴스를 통해 클래스 외부에서 언제나 참조할 수 있는 pubilc이다.  
클래스 필드 정의 제안을 사용하더라도 클래스 필드는 public하기 때문에 외부에 그대로 노출된다.

<div grid="~ cols-2 gap-4">
<div>

  ```javascript
    class Person {
      constructor(name) {
        // 인스턴스 프로퍼티는 기본적으로 public하다.
        this.name = name; 
      }
    }

    const me = new Person('Choi');
    console.log(me.name); // Choi
  ``` 

</div>
<div>

```javascript
  class Person {
    // 클래스 필드도 기본적으로 public하다.
    name = 'Choi'; 
  }

  // 인스턴스 생성
  const me = new Person();
  console.log(me.name); // Choi
``` 

</div>
</div>


다행히도 TC39 프로세서의 stage 3에는 private 필드를 정의할 수 있는 새로운 표준 사양이 제안되어 있다. 표준 사양으로 승급이 확실시되는 이 제안도 최신 브라우저와 최신 Node.js에 이미 구현되어 있다.  

<style>
  h2 {
    color: #b39c36;
    font-size: 1.2em !important;
  }
  p, li {
    font-size: 0.8em !important;
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

## private 필드 정의 방법
 
private 필드의 선두에는 #을 붙여준다.  
private 필드를 참조할 때도 #을 붙여주어야 한다.  

```javascript
  class Person {
    // private 필드 정의
    #name = '';
    constructor(name) {
      // private 필드 참조
      this.#name = name;
    }
  }

  const me = new Person('Choi');

  // private 필드 #name은 클래스 외부에서 참조할 수 없다.
  console.log(me.#name);
  // Uncaught SyntaxError: Private field '#name' must be declared in an enclosing class
``` 
<style>
  h2 {
    color: #b39c36;
    font-size: 1.2em !important;
  }
  p, li {
    font-size: 0.8em !important;
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


<div grid="~ cols-2 gap-4">
<div style="width:400px">

public 필드는 어디서든 참조할 수 있지만  
private 필드는 클래스 내부에서만 참조할 수 있다.
<table class="mt-5">
  <thead>
    <tr>
      <th style="width:40%" class="text-center">접근 가능성</th>
      <th style="width:30%" class="text-center">public</th>
      <th style="width:30%" class="text-center">private</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td class="text-center">클래스 내부</td>
      <td class="text-center">o</td>
      <td class="text-center">o</td>
    </tr>
    <tr>
      <td class="text-center">자식 클래스 내부</td>
      <td class="text-center">o</td>
      <td class="text-center">x</td>
    </tr>
    <tr>
      <td class="text-center">클래스 인스턴스를 통한 접근</td>
      <td class="text-center">o</td>
      <td class="text-center">x</td>
    </tr>
  </tbody>
</table>
</div>
<div>

외부에서 private 필드에 직접 접근할 수 있는 방법은 없다.  
다만 접근자 프로퍼티를 통해 간접적으로 접근하는 방법은 유효하다.

```javascript
  class Person {
    #name = ''; // private 필드 정의
    constructor(name) {
      this.#name = name;
    }
    //name은 접근자 프로퍼티다.
    get name() {
      return this.#name.trim(); // private 필드를 참조하여 trim한 다음 반환한다.
    }
  }
```

private 필드는 반드시 클래스 몸체에 정의해야 한다.
```javascript
  class Person {
    constructor(name) {
      // private 필드는 클래스 몸체에서 정의해야 한다.
      this.#name = name;
      // Uncaught SyntaxError: Private field '#name' must be declared in an enclosing class
    }
  }
```
</div>
</div>

<style>
  h2 {
    color: #b39c36;
    font-size: 1.2em !important;
  }
  p, li {
    font-size: 0.8em !important;
  }
  .slidev-layout h1 + p {
    opacity: 1;
  }
  strong {
    font-weight: bold;
    color: #b5794a;
  }
  th, td {
    text-align: center !important;
    font-size: 0.6em !important;
  }
</style>

---

## static 필드 정의 제안
클래스애는 static 키워드를 사용하여 정적 메서드를 정의 할 수 있지만 정적 필드를 정의할 수는 없었다.  
하지만 static public 필드, static private 필드, static private 메서드를 정의할 수 있는 새로운 표준 사양이 제안되어있다.  
<span class="desc">"Class field declarations"가 2021년 1월 TC39 프로세스의 stage3에 제안</span>  

```javascript
  class MyMath {
    // static public 필드 정의
    static PI = 22 / 7;

    // static private 필드 정의
    static #num = 10;

    // static 메서드
    static increment() {
      return ++MyMath.#num;
    }
  }

  console.log(MyMath.PI); // 3.142857142857143
  console.log(MyMath.increment()); // 11
``` 

<style>
  h2 {
    color: #b39c36;
    font-size: 1.2em !important;
  }
  p, li {
    font-size: 0.8em !important;
  }
  .slidev-layout h1 + p {
    opacity: 1;
  }
  strong {
    font-weight: bold;
    color: #b5794a;
  }
  .desc {
    opacity: 0.5;
    font-size: 0.8em;
  }
</style>

---

# 클래스 상속과 생성자 함수 상속

상속에 의한 클래스 확장은 프로토타입 기반 상속과는 다른 개념으로 기존 클래스를 상속받아 새로운 클래스를 확장하여 정의하는 것이다.  
<strong>코드 재사용 관점에서 매우 유용</strong>

<div grid="~ cols-2 gap-4">
<div>

```javascript
  class Animal {
    constructor(age, weight) {
      this.age = age;
      this.weight = weight;
    }
    eat() { return 'eat'; }
    move() { return 'move'; }
  }

  // 상속을 통해 Animal 클래스를 확장한 Bird 클래스
  class Bird extends Animal {
    fly() { return 'fly'; }
  }

  const bird = new Bird(1, 5);
``` 

</div>
<div>

```javascript
  console.log(bird); // Bird {age: 1, weight: 5}
  console.log(bird instanceof Bird); // true
  console.log(bird instanceof Animal); // true
  console.log(bird.eat()); // eat
  console.log(bird.move()); // move
  console.log(bird.fly()); // fly
``` 
</div>
</div>

클래스는 상속을 통해 다른 클래스를 확장할 수 있는 문법인 extends 키워드가 기본적으로 제공된다. 
<style>
  h2 {
    color: #b39c36;
    font-size: 1.2em !important;
  }
  p, li {
    font-size: 0.8em !important;
  }
  .slidev-layout h1 + p {
    opacity: 1;
  }
  strong {
    font-weight: bold;
    color: #b5794a;
  }
  .desc {
    opacity: 0.5;
    font-size: 0.8em;
  }
</style>

---

## extends 키워드

상속을 통해 클래스를 확장하려면 extends 키워드를 사용하여 상속받을 클래스를 정의한다.  
- 상속을 통해 확장된 클래스 -> 서브클래스/파생클래스/자식클래스
- 서브클래스에게 상속된 클래스 -> 수퍼클래스/베이스클래스/부모클래스  

```javascript
  // 수퍼(베이스/부모) 클래스
  class Base {}

  // 서브(파생/자식) 클래스
  class Derived extends Base {}
``` 

<div grid="~ cols-2 gap-6">
<div>

extends 키워드의 역할은 수퍼클래스와 서브클래스 간의 상속 관계를 설정하는 것이다.  

수퍼클래스와 서브클래스는 인스턴스의 프로토타입 체인뿐 아니라 클래스 간의 프로토타입 체인도 생성한다.  

이를 통해 프로토타입 메서드, 정적 메서드 모두 상속이 가능하다.

</div>
<div>
  <img  width="350" alt="클래스와 생성자 함수의 정의 방식 비교" src="https://user-images.githubusercontent.com/44577555/170218055-7c74d5ea-fd82-48a2-883e-984fae36d693.png">
</div>
</div>

<style>
  h2 {
    color: #b39c36;
    font-size: 1.2em !important;
  }
  p, li {
    font-size: 0.8em !important;
  }
  .slidev-layout h1 + p {
    opacity: 1;
  }
  strong {
    font-weight: bold;
    color: #b5794a;
  }
  .desc {
    opacity: 0.5;
    font-size: 0.8em;
  }
</style>

---

## 동적 상속

extends 키워드 다음에는 클래스뿐만이 아니라 [[Construct]] 내부 메서드를 갖는 함수 객체로 평가될 수 있는 모든 표현식을 사용할 수 있다.  
이를 통해 동적으로 상속받을 대상을 결정할 수 있다. 
단, extends 키워드 앞에는 반드시 클래스가 와야 한다. 

```javascript
  function Base1() {}

  class Base2 {}

  let condition = true;

  // 조건에 따라 동적으로 상속 대상을 결정하는 서브클래스
  class Derived extends (condition ? Base1 : Base2) {}

  const derived = new Derived();
  console.log(derived); // Derived {}
  console.log(derived instanceof Base1); // true
  console.log(derived instanceof Base2); // false -> 클래스는 프로퍼티 어트리뷰트[[Enumerable]]의 값이 false
``` 

<style>
  h2 {
    color: #b39c36;
    font-size: 1.2em !important;
  }
  p, li {
    font-size: 0.8em !important;
  }
  .slidev-layout h1 + p {
    opacity: 1;
  }
  strong {
    font-weight: bold;
    color: #b5794a;
  }
  .desc {
    opacity: 0.5;
    font-size: 0.8em;
  }
</style>

---

## 서브클래스의 construtor

<div grid="~ cols-2 gap-6">
<div>

  클래스에서 constructor를 생략하면 클래스에 다음과 같이 비어있는 constructor가 암묵적으로 정의된다.

  ```javascript
    constructor() {}
  ``` 
</div>
<div>

  서브 클래스에서 constructor를 생략하면 클래스에 다음과 같은 constructor가 암묵적으로 정의된다.  
  args는 new 연산자와 함께 클래스를 호출할 때 전달한 인수의 리스트다.

  ```javascript
    constructor(...args) { super(...args); }  
  ``` 
</div>
</div>

super()는 수퍼클래스의 constructor를 호출하여 인스턴스를 생성한다.
```javascript
  // 수퍼클래스
  class Base {} 
  /** constructor 생략시 암묵적으로 constructor가 정의된다.
   *  constructor() {}
  */
  // 서브클래스
  class Derived extends Base {}
  /** constructor 생략시 암묵적으로 constructor가 정의된다.
   *  constructor(...args) { super(...args); }  
  */
  const derived = new Derived();
  console.log(derived); // Derived {}
```

<style>
  h2 {
    color: #b39c36;
    font-size: 1.2em !important;
  }
  p, li {
    font-size: 0.8em !important;
  }
  .slidev-layout h1 + p {
    opacity: 1;
  }
  strong {
    font-weight: bold;
    color: #b5794a;
  }
  .desc {
    opacity: 0.5;
    font-size: 0.8em;
  }
</style>

---

## super 키워드

super 키워드는 함수처럼 호출할 수도 있고 this와 같이 식별자처럼 참조할 수 있는 특수한 키워드다.  

### super 동작
- super를 호출하면 수퍼클래스의 constructor를 호출한다.
- super를 참조하면 수퍼클래스의 메서드를 호출할 수 있다.  

### super 호출
super를 호출하면 수퍼클래스의 constructor를 호출한다.  
new 연산자와 함께 서브클래스를 호출하면서 수퍼클래스의 constructor에 전달할 필요가 있는 인수는 서브클래스의 constructor에서 호출하는 super를 통해 전달한다.

<style>
  h2 {
    color: #b39c36;
    font-size: 1.2em !important;
  }
  h3 {
    margin-top: 1.2em;
    color: #9C5170;
    font-size: 1em !important;
  }
  p, li {
    font-size: 0.8em !important;
  }
  .slidev-layout h1 + p {
    opacity: 1;
  }
  strong {
    font-weight: bold;
    color: #b5794a;
  }
  .desc {
    opacity: 0.5;
    font-size: 0.8em;
  }
</style>

---

<div grid="~ cols-2 gap-6">
<div>

```javascript {all|11,17|11-12|3-5}
  // 수퍼클래스
  class Base {
    constructor(a, b) { // 4
      this.a = a;
      this.b = b;
    }
  }

  // 서브클래스 
  class Derived extends Base {
    constructor(a, b, c) { // 2
      super(a,b); // 3
      this.c = c;
    }
  }

  const derived = new Derived(1, 2, 3); // 1
  console.log(derived); // Derived {a: 1, b: 2, c: 3}
```

</div>
<div>

  1 -> Derived 클래스를 호출하면서 전달한 인수 1, 2, 3은 Derived 클래스의 constructor에 전달

  2 -> 전달받은 인수 1, 2, 3

  3 -> super 호출을 통해 1, 2 전달  

  4 -> 1, 2 전달받음

</div>
</div>


<style>
  h2 {
    color: #b39c36;
    font-size: 1.2em !important;
  }
  h3 {
    margin-top: 1.2em;
    color: #9C5170;
    font-size: 1em !important;
  }
  p, li {
    font-size: 0.8em !important;
  }
  .slidev-layout h1 + p {
    opacity: 1;
  }
  strong {
    font-weight: bold;
    color: #b5794a;
  }
  .desc {
    opacity: 0.5;
    font-size: 0.8em;
  }
</style>

---

### super를 호출할 때 주의사항  
1. 서브 클래스에서 constructor를 생략하지 않는 경우 서브클래스의 constructor에서는 반드시 super를 호출해야 한다.
2. 서브 클래스의 constructor에서 super를 호출하기 전에는 this를 참조할 수 없다.  
    ```javascript
      class Base {}

      class Derived extends Base {
        constructor() {
          // Uncaught ReferenceError: Must call super constructor in derived class before accessing 'this' or returning from derived constructor
          this.a = 1;
          super();
        }
      }

      const derived = new Derived();
    ``` 
3. super는 반드시 <strong>서브클래스의 constructor에서만 호출</strong>한다. 서브클래스가 아닌 클래스의 constructor나 함수에서 super를 호출하면 에러가 발생한다.
    ```javascript
      class Base {
        constructor() {
          super(); // SyntaxError: 'super' keyword unexpected here
        }
      }
    ``` 
<style>
  h2 {
    color: #b39c36;
    font-size: 1.2em !important;
  }
  h3 {
    margin-top: 1.2em;
    color: #9C5170;
    font-size: 1em !important;
  }
  p, li {
    font-size: 0.8em !important;
  }
  .slidev-layout h1 + p {
    opacity: 1;
  }
  strong {
    font-weight: bold;
    color: #b5794a;
  }
  .desc {
    opacity: 0.5;
    font-size: 0.8em;
  }
</style>

---

### super 참조
매서드 내에서 super를 참조하면 수퍼클래스의 메서드를 호출할 수 있다.

1. 서브클래스의 프로토타입 메서드 내에서 super.sayHi는 수퍼클래스의 프로토타입 메서드 sayHi를 가리킨다.  
    ```javascript
      // 수퍼클래스
      class Base {
        constructor(name) {
          this.name = name;
        }
        sayHi() {
          return `Hi ${this.name}`;
        }
      }
      // 서브클래스
      class Derived extends Base {
        sayHi() {
          // super.sayHi는 수퍼클래스의 프로토타입 메서드를 가리킨다.
          return `${super.sayHi()}. how are you doing?`;
        }
      }

      const derived = new Derived('Choi');
      console.log(derived.sayHi());  // Hi Choi. how are you doing?
    ``` 
    
<style>
  h2 {
    color: #b39c36;
    font-size: 1.2em !important;
  }
  h3 {
    margin-top: 1.2em;
    color: #9C5170;
    font-size: 1em !important;
  }
  p, li {
    font-size: 0.8em !important;
  }
  .slidev-layout h1 + p {
    opacity: 1;
  }
  strong {
    font-weight: bold;
    color: #b5794a;
  }
  .desc {
    opacity: 0.5;
    font-size: 0.8em;
  }
</style>

---

super를 참조하고 있는 메서드가 바인딩되어 있는 객체의 프로토타입을 찾기 위해 메서드는 <strong>내부 슬롯 [[HomeObject]]</strong>를 가지며,  
<strong>자신을 바인딩하고 있는 객체를 가리킨다.</strong>  
주의할 것은 ES6의 메서드 축약 표현으로 정의된 함수만이 [[HomeObject]]를 갖는다는 것이다.  
<strong>super 참조</strong>는 수퍼클래스의 메서드를 참조하기 위해 사용하므로 <strong>서브클래스의 메서드에서 사용</strong>해야 한다.

```javascript
  // 수퍼클래스
  class Base {
    constructor(name) {
      this.name = name;
    }
    sayHi() {
      return `Hi ${this.name}`;
    }
  }
  // 서브클래스
  class Derived extends Base {
    sayHi() {
      const __super = Object.getPrototypeOf(Derived.prototype); // __super는 Base.prototype을 가리킨다.
      return `${__super.sayHi.call(this)} how are you doing?`; // Base.prototype.sayHi를 호출할 때 call 매서드를 사용해 this를 전달해야 한다.
    }
  }

  const derived = new Derived('Choi');
  console.log(derived.sayHi());  // Hi Choi. how are you doing?
``` 

<style>
  h2 {
    color: #b39c36;
    font-size: 1.2em !important;
  }
  h3 {
    margin-top: 1.2em;
    color: #9C5170;
    font-size: 1em !important;
  }
  p, li {
    font-size: 0.8em !important;
  }
  .slidev-layout h1 + p {
    opacity: 1;
  }
  strong {
    font-weight: bold;
    color: #b5794a;
  }
  .desc {
    opacity: 0.5;
    font-size: 0.8em;
  }
</style>

---

2. 서브 클래스의 정적 메서드 내에서 super.sayHi는 수퍼클래스의 정적 메서드 sayHi를 가리킨다.

```javascript
  // 수퍼클래스
  class Base {
    static sayHi() {
      return `Hi`;
    }
  }

  // 서브클래스
  class Derived extends Base {
    static sayHi() {
      // super.sayHi는 수퍼클래스의 정적 메서드를 가리킨다.
      return `${super.sayHi()}. how are you doing?`;
    }
  }
  console.log(derived.sayHi());  // Hi. how are you doing?   
``` 


<style>
  h2 {
    color: #b39c36;
    font-size: 1.2em !important;
  }
  h3 {
    margin-top: 1.2em;
    color: #9C5170;
    font-size: 1em !important;
  }
  p, li {
    font-size: 0.8em !important;
  }
  .slidev-layout h1 + p {
    opacity: 1;
  }
  strong {
    font-weight: bold;
    color: #b5794a;
  }
  .desc {
    opacity: 0.5;
    font-size: 0.8em;
  }
</style>

---

## 상속 클래스의 인스턴스 생성 과정


<div grid="~ cols-2 gap-6">
<div>

```javascript
  // 수퍼클래스
  class Rectangle {
    constructor(width, height) {
      this.width = width;
      this.height = height;
    }
    getArea() {
      return this.width * this.height;
    }
    toString() {
      return `width = ${this.width}, height = ${this.height}`;
    }
  }
  // 서브 클래스
  class ColorRectangle extends Rectangle {
    constructor(width, height, color) {
      super(width, height);
      this.color = color;
    }
    toString() {
      return super.toString() + `, color = ${this.color}`;
    }
  }
  const colorRectangle = new ColorRectangle(2, 4, 'red');
``` 

</div>
<div>

1. 서브클래스의 super 호출  
2. 수퍼클래스의 인스턴스 생성과 this 바인딩
3. 수퍼클래스의 인스턴스 초기화
4. 서브클래스 constructor로의 복귀와 this 바인딩
5. 서브클래스의 인스턴스 초기화
6. 인스턴스 반환

</div>
</div>

<style>
  h2 {
    color: #b39c36;
    font-size: 1.2em !important;
  }
  h3 {
    margin-top: 1.2em;
    color: #9C5170;
    font-size: 1em !important;
  }
  p, li {
    font-size: 0.8em !important;
  }
  .slidev-layout h1 + p {
    opacity: 1;
  }
  strong {
    font-weight: bold;
    color: #b5794a;
  }
  .desc {
    opacity: 0.5;
    font-size: 0.8em;
  }
</style>

---

## 1. 서브클래스의 super 호출  
클래스를 평가할 때 수퍼 클래스와 서브클래스를 구분하기 위해 "base" 또는 "derived"를 값으로 갖는 내부 슬롯[[ConstructorKind]]를 갖는다.  

<strong>다른 클래스를 상속받지 않는 클래스 -> "base"</strong>  
<strong>다른 클래스를 상속받는 서브 클래스 -> "derived"</strong>  

이를 통해 수퍼클래스와 서브클래스는 new 연산자와 함께 호출되었을 때의 동작이 구분된다.  

서브클래스가 new 연산자와 함께 호출되면 서브클래스 constructor 내부의 super 키워드가 함수처럼 호출된다.  
super가 호출되면 수퍼클래스의 constructor가 호출된다.

<style>
  h2 {
    color: #b39c36;
    font-size: 1.2em !important;
  }
  h3 {
    margin-top: 1.2em;
    color: #9C5170;
    font-size: 1em !important;
  }
  p, li {
    font-size: 0.8em !important;
  }
  .slidev-layout h1 + p {
    opacity: 1;
  }
  strong {
    font-weight: bold;
    color: #b5794a;
  }
  .desc {
    opacity: 0.5;
    font-size: 0.8em;
  }
</style>

---

## 2. 수퍼클래스의 인스턴스 생성과 this 바인딩
수퍼클래스의 constructor 내부의 코드가 실행되기 이전에 암묵적으로 빈 객체를 생성  
암묵적으로 생성된 빈 객체, 즉 인스턴스는 this에 바인딩  

new 연산자와 함께 호출된 함수를 가리키는 new.target은 서브클래스를 가리킨다.  
따라서 인스턴스는 new.target이 가리키는 서브클래스가 생성한 것으로 처리된다.
```javascript {all|4-7}
  // 수퍼클래스
  class Rectangle {
    constructor(width, height) {
      // 암묵적으로 빈 객체, 즉 인스턴스가 생성되고 this에 바인딩된다.
      console.log(this); // ColorRectangle {}
      // new 연산자와 함께 호출된 함수, 즉 new.target은 ColorRectangle이다.
      console.log(new.target); // ColorRectangle

      // 생성된 인스턴스의 프로토타입으로 ColorRectangle.prototype이 설정된다.
      console.log(Object.getPrototypeOf(this) === ColorRectangle.prototype); // true
      console.log(this instanceof ColorRectangle); // true
      console.log(this instanceof Rectangle); // true
  ...
``` 

<style>
  h2 {
    color: #b39c36;
    font-size: 1.2em !important;
  }
  h3 {
    margin-top: 1.2em;
    color: #9C5170;
    font-size: 1em !important;
  }
  p, li {
    font-size: 0.8em !important;
  }
  .slidev-layout h1 + p {
    opacity: 1;
  }
  strong {
    font-weight: bold;
    color: #b5794a;
  }
  .desc {
    opacity: 0.5;
    font-size: 0.8em;
  }
</style>

---

## 3. 수퍼클래스의 인스턴스 초기화
this에 바인딩되어 있는 인스턴스에 프로퍼티를 추가하고 constructor가 인수로 전달받은 초기값으로 인스턴스의 프로퍼티를 초기화 한다.  
```javascript {all|13-18}
  // 수퍼클래스
  class Rectangle {
    constructor(width, height) {
      // 암묵적으로 빈 객체, 즉 인스턴스가 생성되고 this에 바인딩된다.
      console.log(this); // ColorRectangle {}
      // new 연산자와 함께 호출된 함수, 즉 new.target은 ColorRectangle이다.
      console.log(new.target); // ColorRectangle

      // 생성된 인스턴스의 프로토타입으로 ColorRectangle.prototype이 설정된다.
      console.log(Object.getPrototypeOf(this) === ColorRectangle.prototype); // true
      console.log(this instanceof ColorRectangle); // true
      console.log(this instanceof Rectangle); // true

      // 인스턴스 초기화
      this.width = width;
      this.height = height;

      console.log(this); // ColorRectangle {width: 2, height: 4}
    }
  ...
```

<style>
  h2 {
    color: #b39c36;
    font-size: 1.2em !important;
  }
  h3 {
    margin-top: 1.2em;
    color: #9C5170;
    font-size: 1em !important;
  }
  p, li {
    font-size: 0.8em !important;
  }
  .slidev-layout h1 + p {
    opacity: 1;
  }
  strong {
    font-weight: bold;
    color: #b5794a;
  }
  .desc {
    opacity: 0.5;
    font-size: 0.8em;
  }
</style>


---

## 4. 서브클래스 constructor로의 복귀와 this 바인딩
super의 호출이 종료되고 제어 흐름이 서브클래스 constructor로 돌아온다.  
이때 super가 반환한 인스턴스가 this에 바인딩된다.  
서브클래스는 별도의 인스턴스를 생성하지 않고 super가 반환한 인스턴스를 this에 바인딩하여 그대로 사용한다.  
```javascript
  // 서브 클래스
  class ColorRectangle extends Rectangle {
    constructor(width, height, color) {
      super(width, height);

      // super가 반환한 인스턴스가 this에 바인딩된다.
      console.log(this); // ColorRectangle {width: 2, height: 4}
    }
  ...
``` 
이처럼 super가 호출되지 않으면 인스턴스가 생성되지 않으며, this 바인딩도 할 수 없다.  
서브클래스의 constructor에서 super를 호출하기 전에는 this를 참조할 수 없는 이유가 바로 이 때문이다.  
따라서 <strong>서브클래스 constructor 내부의 인스턴스 초기화는 반드시 super 호출 이후에 처리</strong>되어야 한다.

<style>
  h2 {
    color: #b39c36;
    font-size: 1.2em !important;
  }
  h3 {
    margin-top: 1.2em;
    color: #9C5170;
    font-size: 1em !important;
  }
  p, li {
    font-size: 0.8em !important;
  }
  .slidev-layout h1 + p {
    opacity: 1;
  }
  strong {
    font-weight: bold;
    color: #b5794a;
  }
  .desc {
    opacity: 0.5;
    font-size: 0.8em;
  }
</style>

---

## 5. 서브클래스의 인스턴스 초기화
  super 호출 이후, 서브클래스의 constructor에 기술되어 있는 인스턴스 초기화가 실행된다.  
  즉, this에 바인딩되어 있는 인스턴스에 프로퍼티를 추가하고 constructor가 인수로 전달받은 초기값으로 인스턴스의 프로퍼티를 초기화한다.

## 6. 인스턴스 반환
  클래스의 모든 처리가 끝나면 완성된 인스턴스가 바인딩된 this가 암묵적으로 반환된다.

  ```javascript
    // 서브 클래스
    class ColorRectangle extends Rectangle {
      constructor(width, height, color) {
        super(width, height);
        // super가 반환한 인스턴스가 this에 바인딩된다.
        console.log(this); // ColorRectangle {width: 2, height: 4}
        // 인스턴스 초기화
        this.color = color;
      }
      // 완성된 인스턴스가 바인딩된 this가 암묵적으로 반환된다.
      console.log(this); // ColorRectangle {width: 2, height: 4, color: "red"}
    }
  ```

<style>
  h2 {
    color: #b39c36;
    font-size: 1.2em !important;
  }
  h3 {
    margin-top: 1.2em;
    color: #9C5170;
    font-size: 1em !important;
  }
  p, li {
    font-size: 0.8em !important;
  }
  .slidev-layout h1 + p {
    opacity: 1;
  }
  strong {
    font-weight: bold;
    color: #b5794a;
  }
  .desc {
    opacity: 0.5;
    font-size: 0.8em;
  }
</style>

---

## 표준 빌트인 생성자 함수 확장
extends 키워드 다음에는 클래스뿐만이 아니라 [[Construct]] 내부 메서드를 갖는 함수 객체로 평가될 수 있는 모든 표현식을 사용할 수 있다.  
String, Number, Array 같은 표준 빌트인 객체도 [[Construct]] 내부 메서드를 갖는 생성자 함수이므로 extends 키워드를 사용하여 확장할 수 있다. 
```javascript
  // Array 생성자 함수를 상속받아 확장한 MyArray
  class MyArray extends Array {
    // 중복된 배열 요소를 제거하고 반환한다: [1, 1, 2, 3] => [1, 2, 3]
    uniq() {
      return this.filter((v, i, self) => self.indexOf(v) === i);
    }
    // 모든 배열 요소의 평균을 구한다: [1, 2, 3] => 2
    average() {
      return this.reduce((pre, cur) => pre + cur, 0) / this.length;
    }
  }

  const myArray = new MyArray(1, 1, 2, 3);
  console.log(myArray); // MyArray(4) [1, 1, 2, 3]

  // MyArray.prototype.uniq 호출
  console.log(myArray.uniq()); // MyArray(3) [1, 2, 3]
  // MyArray.prototype.average 호출
  console.log(myArray.average()); // 1.75
``` 

<div style="position:absolute; bottom:50px; right: 60px; width: 400px; font-size: 0.7em; line-height: 1.5; word-break: keep-all;">
  Array 생성자 함수를 상속받아 확장한 MyArray 클래스가 생성한 인스턴스는 Array.prototype과 MyArray.prototype의 모든 메서드를 사용할 수 있다.
</div>

<style>
  h2 {
    color: #b39c36;
    font-size: 1.2em !important;
  }
  h3 {
    margin-top: 1.2em;
    color: #9C5170;
    font-size: 1em !important;
  }
  p, li {
    font-size: 0.8em !important;
  }
  .slidev-layout h1 + p {
    opacity: 1;
  }
  strong {
    font-weight: bold;
    color: #b5794a;
  }
  .desc {
    opacity: 0.5;
    font-size: 0.8em;
  }
</style>

---

🖐❕ Array.prototype의 메서드 중에서 map, filter와 같이 새로운 배열을 반환하는 메서드가 MyArray 클래스의 인스턴스를 반환한다는 것에 주의하자.

```javascript
  console.log(myArray.filter(v => v % 2) instanceof myArray); // true
``` 

만약 새로운 배열을 반환하는 메서드가 MyArray 클래스의 인스턴스를 반환하지 않고 Array의 인스턴스를 반환하면 MyArray 클래스의 메서드와 메서드 체이닝이 불가능하다.
```javascript
  // 메서드 체이닝
  // [1, 1, 2, 3] => [1, 1, 3] => [1, 3] => 2
  console.log(myArray.filter(v => v % 2).uniq().averge()); // 2 
```

myArray.filter가 반환하는 인스턴스는 MyArray 클래스가 생성한 인스턴스, 즉 MyArray 타입이다.  
따라서 myArray.filter가 반환하는 인스턴스로 uniq 메서드를 연이어 호출할 수 있다.

<style>
  h2 {
    color: #b39c36;
    font-size: 1.2em !important;
  }
  h3 {
    margin-top: 1.2em;
    color: #9C5170;
    font-size: 1em !important;
  }
  p, li {
    font-size: 0.8em !important;
  }
  .slidev-layout h1 + p {
    opacity: 1;
  }
  strong {
    font-weight: bold;
    color: #b5794a;
  }
  .desc {
    opacity: 0.5;
    font-size: 0.8em;
  }
</style>

---

만약 MyArray 클래스의 uniq 메서드가 MyArray 클래스가 생성한 인스턴스가 아닌 Array가 생성한 인스턴스를 반환하게 하려면 다음과 같이 <strong>Symbol.species</strong>를 사용하여 정적 접근자 프로퍼티를 추가한다.
```javascript
  // Array 생성자 함수를 상속받아 확장한 MyArray
  class MyArray extends Array {
    // 모든 메서드가 Array 타입의 인스턴스를 반환하도록 한다.
    static get [Symbol.species]() {return Array;}
    uniq() {
      return this.filter((v, i, self) => self.indexOf(v) === i);
    }
    average() {
      return this.reduce((pre, cur) => pre + cur, 0) / this.length;
    }
  }

  const myArray = new MyArray(1, 1, 2, 3);

  console.log(myArray.uniq() instanceof MyArray); //false
  console.log(myArray.average() instanceof MyArray); // true

  // 메서드 체이닝
  // uniq 메서드는 Array 인스턴스를 반환하므로 average 메서드를 호출할 수 없다.
  console.log(myArray.uniq().average());
  // TypeError: myArray.uniq(...).average is not a function
```

<style>
  h2 {
    color: #b39c36;
    font-size: 1.2em !important;
  }
  h3 {
    margin-top: 1.2em;
    color: #9C5170;
    font-size: 1em !important;
  }
  p, li {
    font-size: 0.8em !important;
  }
  .slidev-layout h1 + p {
    opacity: 1;
  }
  strong {
    font-weight: bold;
    color: #b5794a;
  }
  .desc {
    opacity: 0.5;
    font-size: 0.8em;
  }
</style>

---
layout: center
class: text-center
---

# Thanks!
