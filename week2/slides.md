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

# Javascript Deep Dive

Ch 10 ~ 11 객체 리터럴, 그리고 원시 값과의 비교

<div class="pt-12">
  <span @click="$slidev.nav.next" class="px-2 py-1 rounded cursor-pointer" hover="bg-white bg-opacity-10">
    Press Space for next page <carbon:arrow-right class="inline"/>
  </span>
</div>

<div class="abs-br m-6 flex gap-2">
  <button @click="$slidev.nav.openInEditor()" title="Open in Editor" class="text-xl icon-btn opacity-50 !border-none !hover:text-white">
    <carbon:edit />
  </button>
  <a href="https://github.com/choiwook940" target="_blank" alt="GitHub"
    class="text-xl icon-btn opacity-50 !border-none !hover:text-white">
    <carbon-logo-github />
  </a>
</div>

<!--
The last comment block of each slide will be treated as slide notes. It will be visible and editable in Presenter Mode along with the slide. [Read more in the docs](https://sli.dev/guide/syntax.html#notes)
-->

---



# Contents

Javascript는 객체기반의 프로그래밍 언어입니다

<br>
<br>

- **객체란?**  
- **왜 객체인가?**
- **객체지향 프로그래밍** 
- **객체의 생성**
- **객체의 특징** 
<br>
<br>

<!-- Read more about [Why Slidev?](https://sli.dev/guide/why) -->

<!--
You can have `style` tag in markdown to override the style for the current page.
Learn more: https://sli.dev/guide/syntax#embedded-styles
-->

<style>
h1 {
  background-color: #2B90B6;
  background-image: linear-gradient(45deg, #4EC5D4 10%, #146b8c 20%);
  background-size: 100%;
  -webkit-background-clip: text;
  -moz-background-clip: text;
  -webkit-text-fill-color: transparent;
  -moz-text-fill-color: transparent;
}

</style>

---

# 객체란?

Javascript의 객체는 순서가 없는 키-값 쌍(프로퍼티)의 모음입니다.

<div grid="~ cols-2 gap-2" m="-t-2">

```

프로퍼티는: 키와 값으로 구성되어있다

```

```yaml

값이 함수인 프로퍼티를: 메소드라고 한다


```
</div>

<img border="rounded" src="/images/object.png">

[출처: W3Schools](https://www.w3schools.com/js/js_objects.asp)

<style>
  div {
    font-size: 14px;
  }
</style>



---

# 왜 객체인가?

```yaml
객체의 상태를 나타내는 프로퍼티와 동작인 메서드를 하나의 단위로 구조화할 수 있어서 유용하다.
```
```yaml
각 아이템들을 하나 하나 개별 전송하는 것보다, 하나의 객체를 전송하는 것이 훨씬 효율적이다.
```
<br>
<div grid="~ cols-2 gap-2" m="-t-2">

<img border="rounded" src="/images/절차적프로그래밍.png">
<img border="rounded" src="/images/객체적프로그래밍.png">

</div>

[출처: 네이버블로그 메이커멘토](https://m.post.naver.com/viewer/postView.nhn?volumeNo=29654748&memberNo=6255089&vType=VERTICAL)

<style>
  div {
    font-size: 14px;
  }
</style>

---

# 객체지향 프로그래밍

객체지향 프로그래밍이란 객체의 집합으로 프로그램을 표현하려는 패러다임입니다
<br>


<div grid="~ cols-1 gap-2" m="-t-2">

|     |  클래스 기반 객체지향 프로그래밍   | 프로토타입 기반 객체지향 프로그래밍 |
| --- | --- | --- |
| 언어 | C++, C#, JAVA, Python, … | Javascript | 
| 객체 정의 방식 | 클래스로 정의 |  |
| 객체 생성 방식 | 생성자로 호출 | 클래스 없이 객체 생성. 리터럴, Object, 생성자 함수, Object.create, 클래스(문법적 설탕)  |
| 특징 | 이미 생성된 인스턴스의 자료구조와 기능을 동적으로 변경할 수 없다 | 이미 생성된 인스턴스의 자료구조와 기능을 동적으로 변경할 수 있다 |
|      | 정확성, 안정성, 예측성 측면에서 장점 | 객체지향의 상속을 구현하기 위해 “프로토타입”이라는 객체의 프로퍼티와 메소드를 상속받을 수 있음 |

</div>


<style>
  div {
    font-size: 14px;
  }
</style>

---
layout: image-right
image: https://source.unsplash.com/collection/94734566/1920x1080
---


# 객체의 생성
<div>
Javascript의 객체를 생성할 때 클래스는 필요 없습니다
</div>
<br>
1. 리터럴

```ts
const emptyObject = {};
console.log(typeof emptyObject); // object

const person = {
  name: 'Choi',
  gender: 'male',
  sayHello: function () {
    console.log('Hi! My name is ' + this.name);
  }
};

console.log(typeof person); // object
console.log(person); 
// {name: "Choi", gender: "male", sayHello: ƒ}
person.sayHello(); // Hi! My name is Choi
```

<style>
div {
  font-size: 14px;
  color: gray;
}
</style>

---
layout: image-left
image: https://source.unsplash.com/collection/94734567/1920x1080
---

# 
<div>
Javascript의 객체를 생성할 때 클래스는 필요 없습니다
</div>
<br>
<br>
<br>
2. Object 생성자 함수

```ts
// 빈 객체의 생성
const person = new Object();
// 프로퍼티 추가
person.name = 'Choi';
person.gender = 'male';
person.sayHello = function () {
  console.log('Hi! My name is ' + this.name);
};

console.log(typeof person); // object
console.log(person); 
// {name: "Choi", gender: "male", sayHello: ƒ}
person.sayHello(); // Hi! My name is Choi
```

<br>
<br>

<div>
이 방식으로 객체를 생성하는 것은 유용해보이지 않습니다...
</div>

<style>
div {
  font-size: 14px;
  color: gray;
}
</style>

---

# 
<div>
Javascript의 객체를 생성할 때 클래스는 필요 없습니다
</div>

<br>
<br>

3. 생성자 함수 : 단 하나의 객체만 생성하는 1, 2 의 방식과 달리 프로퍼티가 동일한 객체 여러 개를 간편하게 생성할 수 있습니다

<br>
<div grid="~ cols-2 gap-2" m="-t-2">


  ```ts
    const person1 = {
      name: 'Lee',
      gender: 'male',
      sayHello: function () {
        console.log('Hi! My name is ' + this.name);
      }
    };
    const person2 = {
      name: 'Kim',
      gender: 'female',
      sayHello: function () {
        console.log('Hi! My name is ' + this.name);
      }
    };
    const person3 = {
      name: 'Choi',
      gender: 'male',
      sayHello: function () {
        console.log('Hi! My name is ' + this.name);
      }
    };
  ```
  ```ts
    // 생성자 함수 - 첫 글자 대문자 사용으로 일반함수와 구별
    // (일반함수도 new를 붙이면 생성자 함수로 동작합니다)
    function Person(name, gender) {
      this.name = name;
      this.gender = gender;
      this.sayHello = function(){
        console.log('Hi! My name is ' + this.name);
      };
    }

    // 인스턴스의 생성
    const person1 = new Person('Lee', 'male');
    const person2 = new Person('Kim', 'female');
    const person2 = new Person('Choi', 'male');

  ```
  <br>
  <br>
</div>


<style>
div {
  font-size: 14px;
  color: gray;
}
</style>

---

# 객체의 특징

객체는 변경 가능한 값입니다

<div grid="~ cols-2 gap-2" m="-t-2">

```yaml
원시 값 : 값에 의한 전달이 일어나며
원본과 복사본은 각각 다른 메모리에 있는 값을 가지고 있습니다
``` 
```yaml
객체: 참조에 의한 전달
원본과 복사본은 동일한 메모리에 있는 값을 참조하는 참조 값을 
각각 다른 메모리에 가지고 있습니다
```

<img border="rounded" src="/images/byValue.png">

<img border="rounded" src="/images/byRef.png">

</div>

왜 객체는 참조 값을 복사하도록 설계된 것일까요? <br>
객체는 원시 값과 다르게 확보해야 할 메모리 공간의 크기를 사전에 정할 수 없기 때문입니다. <br>
이것은 합리적인 설계지만 동시에 여러 곳에서 하나의 객체를 공유하게 되어 코드의 안정성이 떨어지는 허점을 가집니다. => 얕은 복사

<style>
  div {
    font-size: 14px;
  }
</style>