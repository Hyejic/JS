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

# 45장 프로미스

## week12
모던 자바스크립트 Deep Dive

---

# 비동기 처리를 위한 콜백 패턴의 단점
비동기 함수 내부의 비동기로 동작하는 코드는 비동기 함수가 종료된 이후에 완료.  
-> 비동기 함수 내부의 비동기로 동작하는 코드(onload)에서 처리 결과를 외부로 반환하거나 상위 스코프의 변수에 할당하지 못함.

<div grid="~ cols-2 gap-10">
<div>

```javascript {all|6-14,16-19|all}
  // GET 요청을 위한 비동기 함수
  const get = url => {
    const xhr = new XMLHttpRequest();
    xhr.open('GET', url);
    xhr.send();

    xhr.onload = () => {
      if (xhr.status === 200) {
        // 서버의 응답을 반환한다.
        return JSON.parse(xhr.response);
      } else {
        console.error(`${xhr.status} ${xhr.statusText}`);
      }
    };
  };

  // id가 1인 post를 취득
  const response = get('https://jsonplaceholder.typicode.com/posts/1');
  console.log(response); // undefined
```

</div>
<div>

```javascript {all|1,9-16,19-21}
  let todos;

  // GET 요청을 위한 비동기 함수
  const get = url => {
    const xhr = new XMLHttpRequest();
    xhr.open('GET', url);
    xhr.send();

    xhr.onload = () => {
      if (xhr.status === 200) {
      // 서버의 응답을 상위 스코프의 변수에 할당한다.
        todos = JSON.parse(xhr.response);
      } else {
        console.error(`${xhr.status} ${xhr.statusText}`);
      }
    };
  };

  // id가 1인 post를 취득
  get('https://jsonplaceholder.typicode.com/posts/1');
  console.log(todos); // undefined
```
</div>
</div>


<style>
  .slidev-layout h1 {
    font-size: 1.6rem;
  }
  .slidev-layout h1 + p {
    font-size: 1rem
  }
</style>

---
class: px-10
---

# 비동기 함수의 콜백

<div grid="~ cols-2 gap-10">
<div>
비동기 함수를 범용적으로 사용하기 위해 콜백 함수를 전달하는 것이 일반적.  

비동기 함수가 비동기 처리 결과를 가지고 또다시 비동기 함수를 호출해야 한다면 콜백 함수 호출이 중첩되어 복잡도가 높아지는 현상이 발생하는데, 
이를 <strong>콜백 헬</strong>이라 한다.

```javascript
  // 콜백 헬 전형적 사례
  get(`/step1`, a => {
    get(`/step2/${a}`, b => {
      get(`/step3/${b}`, c => {
        get(`/step2/${c}`, d => {
          console.log(d);
        });  
      });  
    });  
  });
```

</div>
<div>

```javascript
  const get = (url, callback) => {
    const xhr = new XMLHttpRequest();
    xhr.open('GET', url);
    xhr.send();
    xhr.onload = () => {
      if (xhr.status === 200) {
      // 서버의 응답을 상위 스코프의 변수에 할당한다.
        callback(JSON.parse(xhr.response));
      } else {
        console.error(`${xhr.status} ${xhr.statusText}`);
      }
    };
  };
  const url = 'https://jsonplaceholder.typicode.com';
  // id가 1인 post를 취득
  get(`${url}/posts/1`, ({userId}) => {
    console.log(userId); // 1
    // post의 userId를 사용하여 user 정보를 취득
    get(`${url}/users/${userId}`, userInfo => {
      console.log(userInfo); // {id: 1, name: 'Leanne Graham', username: 'Bret', email: 'Sincere@april.biz', address: {…}, …}
    });
  });
```

</div>
</div>


<style>
  .slidev-layout h1 {
    font-size: 1.6rem;
  }
  .slidev-layout p {
    font-size: 1rem
  }
  strong {
  font-weight: bold;
  color: #b5794a;
}
</style>

---
class: px-10
---

# 에러 처리의 한계
비동기 처리를 위한 콜백 패턴의 문제점 중에서 가장 심각한 것은 에러 처리가 곤란하다는 것이다.

<div grid="~ cols-2 gap-10">
<div>

```javascript
  try {
    setTimeout(() => {
      throw new Error('Error!');
    }, 1000)
  } catch (e) {
    // 에러를 캐치하지 못한다
    console.error('캐치한 에러', e);
  }
```

</div>
<div>

setTimeout 👉 비동기 함수  

에러는 호출자 방향으로 전파된다.  

즉, 콜 스택의 아래 방향(실행 중인 실행 컨텍스트가 푸기되기 직전에 푸시된 실행 컨텍스트 방향)으로 전파된다.  

하지만 앞에서 살펴본 바와 같이 setTimeout 함수의 콜백 함수를 호출한 것은 setTimeout 함수가 아니다.  
👉 이벤트 루프에 의해 콜 스택으로 푸시

따라서 setTimeout 함수의 콜백 함수가 발생시킨 에러는 catch 블록에서 캐치되지 않는다.


</div>
</div>

<style>
  .slidev-layout h1 {
    font-size: 1.6rem;
  }
  .slidev-layout p {
    font-size: 1rem
  }
  strong {
  font-weight: bold;
  color: #b5794a;
}
</style>

---
layout: center
class: text-center
---

비동기 처리를 위한 콜백 패턴은 콜백 헬이나 에러 처리가 곤란하다는 문제가 있다.   

<strong>이를 극복하기위해 ES6에서 프로미스가 도입되었다.</strong>

<style>
  strong {
    font-weight: bold;
    color: #b5794a;
  }
</style>

---
class: px-20 
---

# 프로미스의 생성

- new 연산자와 함께 호출
- resolve와 reject 함수를 인수로 전달받음


```javascript
  // 프로미스 생성
  const promise = new Promise((resolve, reject) => {
    // promise 함수의 콜백 함수 내부에서 비동기 처리를 수행한다.
    if ( /* 비동기 처리 성공 */) {
      resolve('result');
    } else { /* 비동기 처리 실패 */
      reject('failure reason')
    }
  });
```
 
---
class: px-20
---

# 프로미스로 구현


<div grid="~ cols-2 gap-10">
<div>

```javascript {all|3,9-15}
  // GET 요청을 위한 비동기 함수
  const promiseGet = url => {
    return new Promise((resolve, reject) => {
      const xhr = new XMLHttpRequest();
      xhr.open('GET', url);
      xhr.send();

      xhr.onload = () => {
        if (xhr.status === 200) {
          // 성공적으로 응답을 전달받으면 resolve
          resolve(JSON.parse(xhr.response));
        } else {
          // 에러 처리를 위해 reject 함수를 호출한다.
          reject(new Error(xhr.status));
        }
      }
    })
  }

  promiseGet('https://jsonplaceholder.typicode.com/posts/1');
```
</div>
<div>

  함수 내부에서 프로미스를 생성하고 반환  

  Promise 생성자 함수가 인수로 전달받은 콜백 함수 내부에서 수행

  비동기 처리가 성공하면 비동기 처리 결과를 resolve 함수에 인수로 전달하면서 호출
  
  비동기 처리가 실패하면 에러를 reject 함수에 인수로 전달하면서 호출

</div>
</div>

<style>
  .slidev-layout h1 + p {
    opacity: 1;
  }
  p {
    font-size: 0.8em !important;
  }
</style>

--- 

# 프로미스의 상태 정보


프로미스의 상태 정보 | 의미 | 상태 변경 조건
--|--|--
pending | 비동기 처리가 아직 수행되지 않은 상태 | 프로미스가 생성된 직후 기본 상태
fulfilled | 비동기 처리가 수행된 상태(성공) | resolve 함수 호출
rejected | 비동기 처리가 수행된 상태(실패) | reject 함수 호출


<div grid="~ cols-2 gap-10" class="pt-2">
<div>

<img width="540" alt="image" src="https://user-images.githubusercontent.com/44577555/174729452-08a8b70a-a7fc-4928-8c41-fe524b866728.png">

</div>
<div>

  fulfilled 또는 rejected 상태를 <strong>settled</strong> 상태라고 한다.  

  settled 상태는 fulfilled 또는 rejected 상태와 상관없이  
  <strong>pending이 아닌 상태로 비동기 처리가 수행된 상태</strong>를 말한다.  
  
  settled 상태가 되면 더는 다른 상태로 변화할 수 없다.

</div>
</div>

<style>
.slidev-layout h1 + p {
  opacity: 1;
}
p {
  font-size: 0.8em !important;
}
.pt-2 {
  padding-top: 2rem;
}
strong {
  font-weight: bold;
  color: #b5794a;
}
</style>

---
layout: center
---

# 프로미스의 후속 처리 메서드

- Promise.prototype.then
- Promise.prototype.catch
- Promise.prototype.finally

---

# Promise.prototype.then


then 함수는 <strong>두 개의 콜백 함수를 인수</strong>로 받는다. 

<ul class="list">
  <li>첫 번째 콜백 함수 - 프로미스가 fulfilled 상태(resolve 함수가 호출된 상태)가 되면 호출. 이때 콜백 함수는 프로미스의 비동기 처리 결과를 인수로 전달받는다.</li>
  <li>두 번째 콜백 함수 - 프로미스가 rejected 상태(reject 함수가 호출된 상태)가 되면 호출. 이때 콜백 함수는 프로미스의 에러를 인수로 전달받는다.</li>
</ul>

<br/>

```javascript
  //fulfilled
  new Promise(resolve => resolve(`fulfilled`))
    .then(v => console.log(v), e => console.error(e)); // fulfilled

  // rejected
  new Promise((_, reject) => reject(new Error('rejected')))
    .then(v => console.log(v), e => console.error(e)); // Error: rejected
```
<br/>

then 메서드는 언제나 프로미스를 반환한다.  

콜백 함수가 프로미스가 아닌 값을 반환하면 그 값을 암묵적으로 resolve 또는 reject하여 프로미스를 생성해 반환한다.

<style>
.slidev-layout h1 + p {
  opacity: 1;
}
p, li {
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
.list {
  padding-top: 2rem;
}
</style>

---

# Promise.prototype.catch

catch 메서드는 <strong>한 개의 콜백 함수를 인수</strong>로 전달 받는다.  
프로미스가 rejected 상태인 경우만 호출된다.

<br/>

```javascript
  // rejected
  new Promise((_, reject) => reject(new Error('rejected')))
    .catch(e => console.error(e)); // Error: rejected
```
<br/>

then 메서드와 마찬가지로 언제나 프로미스를 반환한다.

<style>
.slidev-layout h1 + p {
  opacity: 1;
}
p, li {
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
.list {
  padding-top: 2rem;
}
</style>

---

# Promise.prototype.finally

finally 메서드는 <strong>한 개의 콜백 함수를 인수</strong>로 전달받는다.  

프로미스의 성공 또는 실패와 상관없이 무조건 한 번 호출된다.  

<strong>프로미스의 상태와 상관없이 공통적으로 수행해야 할 처리 내용이 있을 때 유용하다.</strong>  
 
then/catch 메서드와 마찬가지로 언제나 프로미스를 반환한다.

```javascript
  new Promise((_, reject) => reject(new Error('rejected')))
    .finally(() => console.log('finally')); // finally
```


<style>
.slidev-layout h1 + p {
  opacity: 1;
}
p, li {
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
.list {
  padding-top: 2rem;
}
</style>


---

## 프로미스로 구현한 비동기 함수 get을 사용해 후속 처리를 구현

<div grid="~ cols-2 gap-10" class="pt-2">
<div>

```javascript
  const promiseGet = url => {
    return new Promise((resolve, reject) => {
      const xhr = new XMLHttpRequest();
      xhr.open('GET', url);
      xhr.send();

      xhr.onload = () => {
        if(xhr.status === 200) {
          // 성공적으로 응답을 전달 받으면 resolve 함수를 호출
          resolve(JSON.parse(xhr.response));
        } else {
          // 에러 처리를 위해 reject 함수를 호출한다.
          reject(new Error(xhr.status));
        }
      };
    });
  };

  // promiseGet 함수는 프로미스를 반환한다.
  promiseGet('https://jsonplaceholder.typicode.com/posts/1')
    .then(res => console.log(res))
    .catch(err => console.log(err))
    .finally(() => console.log('Bye!'));
```

</div>
<div>

<img width="350" alt="image" src="https://user-images.githubusercontent.com/44577555/174737987-94725ae5-134e-499c-8302-42156ee26cfc.png">

</div> 
</div> 

<style>
h2 {
color: #b39c36;
font-size: 1em !important;
}
p {
  margin: 0.5em !important;
  font-size: 0.8em !important;
}
li {
  margin: 0.5em !important;
  font-size: 0.8em !important;
}
img {padding-top: 0.5rem;}
</style>

---

# 프로미스의 에러 처리

비동기 처리에서 발생한 에러는 <strong>then 메서드의 두 번째 콜백 함수</strong> 또는 <strong>catch</strong>를 사용하여 처리할 수 있다.

<div grid="~ cols-2 gap-10">
<div>

## then
```javascript
  const wrongUrl = 'https://jsonplaceholder.typicode.com/XXX/1'

  // 부적절한 url이 지정되었기 때문에 에러가 발생
  promiseGet(wrongUrl)
    .then(
      res => console.log(res),
      err => console.error(err) // 에러 처리 Error: 404
    );
```

</div>
<div>

## catch
```javascript
  const wrongUrl = 'https://jsonplaceholder.typicode.com/xxx/1'

  // 부적절한 url이 지정되었기 때문에 에러가 발생
  promiseGet(wrongUrl)
  .then(res => console.log(res))
  .catch(err => console.error(err)); // Error: 404
```

catch 메서드를 모든 then 메서드를 호출한 이후에 호출하면 then 메서드 내부에서 발생한 에러까지 모두 캐치할 수 있다. 
```javascript
  // catch
  promiseGet('https://jsonplaceholder.typicode.com/todos/1')
  .then(res => console.xxx(res))
  .catch(err => console.error(err)); // TypeError: console.xxx is not a function
```
⭐️ 에러 처리는 가독성이 좋고 명확한 <strong>catch 메서드에서 하는 것을 권장</strong>

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

# 프로미스 체이닝

then, catch, finally 후속 처리 메서드는 언제나 프로미스를 반환하므로 연속적으로 호출할 수 있다. 👉  프로미스 체이닝

```javascript
  const url = 'https://jsonplaceholder.typicode.com';

  // id가 1인 post의 userId로 user 정보를 취득
  promiseGet(`${url}/post/1`)
  .then(({userId}) => promiseGet(`${url}/user/${userId}`))
  .then(userInfo => console.log(userInfo))
  .catch(err => console.error(err));
```


후속 처리 메서드 | 콜백 함수의 인수 | 후속 처리 메서드의 반환값
--|--|--
then | promiseGet 함수가 반환한 프로미스가 resolve한 값 | 콜백 함수가 반환한 프로미스
then | 첫 번째 then 메서드가 반환한 프로미스 resolve한 값 | 콜백 함수가 반환한 값(undefined)을 resolve한 값
catch(에러가 발생하지 않으면 호출되지 않는다) | promiseGet 함수 또는 앞선 후속 처리 메서드가 반환한 프로미스가 reject한 값 | 콜백 함수가 반환한 값(undefined)을 resolve한 프로미스

<div grid="~ cols-2 gap-10">
<div>

생성자 함수를 사용하여 인스턴스를 생성하는 경우 프로토타입 메서드를 생성하기 위해서는 다음과 같이 명시적으로 프로토타입에 메서드를 추가해야 한다.
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
.slidev-layout h1 + p {
  opacity: 1;
}
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

      // 3. 완성된 인스턴스가 바인딩된 this가 암묵적으로 반환된다. return문 반드시 생략
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
접근자 프로퍼티는 자체적으로는 값을 갖지 않고 <strong>다른 데이터 프로퍼티의 값을 읽거나 저장할 때 사용</strong>하는 접근자 함수로 구성된 프로퍼티다. 

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
  // 접근자 프로퍼티는 get, set, enumerable, configurable 프로퍼티 어트리뷰트를 갖는다.
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
  strong {
    font-weight: bold;
    color: #b5794a;
  }
</style>

---

접근자 프로퍼티는 getter 함수와 setter 함수로 구성되어있다.  

- getter  
<strong>인스턴스 프로퍼티에 접근할 때마다</strong> 프로퍼티 값을 조작하거나 별도의 행위가 필요할 때 사용.  
반드시 무언가를 반환해야한다.  
- setter  
<strong>인스턴스 프로퍼티에 값을 할당할 때마다</strong> 프로퍼티 값을 조작하거나 별도의 행위가 필요할 때 사용.  
할당해야 할 때 사용하므로 반드시 매개변수가 있어야 한다. 단 하나의 값만 할당받기 때문에 단 하나의 매개변수만 선언할 수 있다. 

<style>
  strong {
    font-weight: bold;
    color: #b5794a;
  }
</style>

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

## 클래스 필드 정의 방법

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
  ul {
    margin-top: -1em;
  }
  p, li {
    font-size: 0.8rem !important;
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
클래스에는 static 키워드를 사용하여 정적 메서드를 정의 할 수 있지만 정적 필드를 정의할 수는 없었다.  
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
  console.log(derived instanceof Base2); // false
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
  console.log(myArray.filter(v => v % 2) instanceof MyArray); // true
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
  console.log(myArray.average() instanceof Array); // true

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
