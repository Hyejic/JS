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

<br/>

후속 처리 메서드 | 콜백 함수의 인수 | 후속 처리 메서드의 반환값
--|--|--
then | promiseGet 함수가 반환한 프로미스가 resolve한 값 | 콜백 함수가 반환한 프로미스
then | 첫 번째 then 메서드가 반환한 프로미스 resolve한 값 | 콜백 함수가 반환한 값(undefined)을 resolve한 값
catch(에러가 발생하지 않으면 호출되지 않는다) | promiseGet 함수 또는 앞선 후속 처리 메서드가 반환한 프로미스가 reject한 값 | 콜백 함수가 반환한 값(undefined)을 resolve한 프로미스

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
strong {
  font-weight: bold;
  color: #b5794a;
}
table th,
table td {
  font-size: 0.6em !important;
}
</style>

---
layout: center
---

# 프로미스의 정적 메서드 

- Promise.resolve
- Promise.reject
- Promise.all
- Promise.race
- Promise.allSettled

---

# Promise.resolve / Promise.reject

Promise.resolve와 Promise.reject 메서드는 이미 존재하는 값을 래핑하여 프로미스를 생성하기 위해 사용한다.  

<div grid="~ cols-2 gap-10" class="pt-2">
<div>

## Promise.resolve 

Promise.resolve 메서드는 인수로 전달받은 값을 resolve하는 프로미스를 생성한다.

```javascript
  // 배열을 resolve 하는 프로미스를 생성
  const resolvePromise = Promise.resolve([1, 2, 3]);
  resolvePromise.then(console.log); // [1, 2, 3]

  // 위와 동일하게 동작
  const resolvePromise = new Promise(resolve => resolve([1, 2, 3]));
  resolvedPromise.then(console.log); // [1, 2, 3]
```

</div>
<div>

## Promise.reject

Promise.reject 메서드는 인수로 전달받은 값을 reject하는 프로미스를 생성한다.
```javascript
  // 에러 객체를 reject하는 프로미스를 생성
  const rejectPromise = Promise.reject(new Error(`Error!`));
  rejectedPromise.catch(console.log); // Error: Error!

  // 위와 동일하게 동작
  const rejectedPromise = new Promise((_, reject) => reject(new Error(`Error!`)));
  rejectedPromise.catch(console.log); // Error: Error!
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
}
p {
  font-size: 0.8em !important;
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

# Promise.all

<strong>여러 개의 비동기 처리를 모두 병렬 처리</strong> 할 떄 사용.  

비동기 처리들이 서로 의존하지 않고 개별적으로 수행된다면 비동기 처리를 순차적으로 처리할 필요가 없기 떄문에 promise.all을 사용하여 병렬 처리를 한다.

```javascript
  const requestData1 = () => new Promise(resolve => setTimeout(() => resolve(1), 3000));
  
  const requestData2 = () => new Promise(resolve => setTimeout(() => resolve(2), 2000));
  
  const requestData3 = () => new Promise(resolve => setTimeout(() => resolve(3), 1000));

  // 세 개의 비동기 처리를 병렬로 처리
  Promise.all([requestData1(), requestData2(), requestData3()])
    .then(console.log) // [1, 2, 3] 약 3초 소요
    .catch(console.error);
```

- 인수로 전달받은 모든 프로미스가 모두 fulfilled 상태가 되면 종료한다.  
- 처리 순서가 보장
- 인수로 전달받은 배열의 프로미스가 하나라도 rejected 상태가 되면 즉시 종료


<style>
  h2 {
    color: #b39c36;
    font-size: 1.5em !important;
  }
  p {
    font-size: 0.8em !important;
  }
  li {
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

# Promise.all

```javascript
  Promise.all([
    new Promise((_, reject) => setTimeout(() => reject(new Error(`Error 1`)), 3000)),
    new Promise((_, reject) => setTimeout(() => reject(new Error(`Error 2`)), 2000)),
    new Promise((_, reject) => setTimeout(() => reject(new Error(`Error 3`)), 1000))
  ])
    .then(console.log)
    .catch(console.log) // Error: Error 3
```
세 번째 프로미스가 가장 먼저 rejected 상태가 되므로 세 번째 프로미스가 reject한 에러가 catch 메서드로 전달

Promise.all 메서드는 인수로 전달받은 이터러블의 요소가 프로미스가 아닌 경우 Promise.resolve 메서드를 통해 프로미스로 래핑

```javascript
  Promise.all([
      1, // ->  Promise.resolve(1)
      2, // ->  Promise.resolve(2)
      3 // ->  Promise.resolve(3)
    ])
    .then(console.log) // [1, 2, 3]
    .catch(console.log) 
```

<style>
  h2 {
    color: #b39c36;
    font-size: 1.5em !important;
  }
  p {
    font-size: 0.8em !important;
  }
  li {
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

# Promise.race

프로미스를 요소로 갖는 배열 등의 이터러블을 인수로 전달받는다. (promise.all과 동일)  
Promise.all 메서드와는 다르게 <strong>가장 먼저 fulfilled 상태가 된 프로미스의 처리 결과를 resolve하는 새로운 프로미스를 반환</strong>한다.

```javascript
  Promise.race([
    new Promise(resolve => setTimeout(() => resolve(1), 3000)), // 1
    new Promise(resolve => setTimeout(() => resolve(2), 2000)), // 2 
    new Promise(resolve => setTimeout(() => resolve(3), 1000)) // 3
  ])
    .then(console.log) // 3
    .catch(console.log);
```

전달된 프로미스가 하나라도 rejected 상태가 되면 에러를 reject하는 새로운 프로미스를 즉시 반환 (promise.all과 동일)
```javascript
  Promise.race([
    new Promise((_, reject) => setTimeout(() => reject(new Error('Error 1')), 3000)),
    new Promise((_, reject) => setTimeout(() => reject(new Error('Error 2')), 2000)),
    new Promise((_, reject) => setTimeout(() => reject(new Error('Error 3')), 1000)) 
  ])
    .then(console.log) 
    .catch(console.log); // Error: Error 3
```

<style>
  h2 {
    color: #b39c36;
    font-size: 1.5em !important;
  }
  p {
    font-size: 0.8em !important;
  }
  li {
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

# Promise.allSettled

프로미스를 요소로 갖는 배열 등의 이터러블을 인수로 전달받는다. (promise.all, Promise.race와 동일)  
<strong>전달받은 프로미스가 모두 settled 상태가 되면</strong> 처리 결과를 배열로 반환한다. (settled - fulfilled 또는 rejected 상태)

<span class="desc">ES11에 도입된 Promise.allSettled 메서드는 IE를 제외한 대부분의 모던 브라우저에서 지원</span>

```javascript
  Promise.allSettled([
    new Promise(resolve => setTimeout(() => resolve(1), 2000)),
    new Promise((_, reject) => setTimeout(() => reject(new Error('Error!')), 1000))
  ]).then(console.log);
```
<img width="350" alt="image" src="https://user-images.githubusercontent.com/44577555/174974616-9943d924-98b4-4f9e-b759-be6f43430113.png">

- 프로미스가 fulfilled 상태인 경우 비동기 처리 상태를 나타내는 status 프로퍼티와 처리 결과를 나타내는 value 프로퍼티를 갖는다.
- 프로미스가 rejected 상태인 경우 비동기 처리 상태를 나타내는 status 프로퍼티와 에러를 나타내는 reason 프로퍼티를 갖는다.

<style>
  h2 {
    color: #b39c36;
    font-size: 1.5em !important;
  }
  p {
    font-size: 0.8em !important;
  }
  ul {
    padding-top: 20px;
  }
  li {
    font-size: 0.6em !important;
  }
  img {
    padding-top: 20px;
  }
  .slidev-layout h1 + p {
    opacity: 1;
  }
  strong {
    font-weight: bold;
    color: #b5794a;
  }
  .desc {
    font-size: 0.8rem;
    opacity: 0.7;
  }
</style>

---

# 마이크로태스크 큐

```javascript
  setTimeout(() => console.log(1), 0);

  Promise.resolve()
  .then(() => console.log(2))
  .then(() => console.log(3));
```

2 -> 3 -> 1의 순으로 출력  
프로미스의 후속 처리 메서드의 콜백 함수는 태스크 큐가 아니라 마이크로태스크 큐에 저장되기 때문.

## 마이크로태스크 큐, 태스크 큐는 별도의 큐
마이크로태스크 큐 - 프로미스의 후속처리 메서드의 콜백 함수가 일시 저장  
태스크 큐 - 비동기 함수의 콜백 함수나 이벤트 핸들러가 일시 저장된다.  

콜백 함수나 이벤트 핸들러를 일시 저장한다는 점에서 태스크 큐와 동일하지만 <strong>마이크로태스크 큐는 태스크 큐보다 우선순위가 높다.</strong>


<style>
  h2 {
    color: #b39c36;
    font-size: 1em !important;
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

# fetch

- XMLHttpRequest 객체와 마찬가지로<strong> HTTP 요청 전송 기능을 제공하는 클라이언트 사이드 Web API</strong>다. 
- XMLHttpRequest 객체보다 사용법이 간단하고 프로미스를 지원하기 때문에 비동기 처리를 위한 <strong>콜백 패턴의 단점에서 자유롭다.</strong>
- HTTP 요청을 전송할 URL과 HTTP 요청 메서드, HTTP 요청 헤더, 페이로드 등을 설정한 객체를 전달한다.


```javascript
  const promise = fetch(url, [, options])
```

fetch 함수는 HTTP 응답을 나타내는 Response 객체를 래핑한 Promise 객체를 반환한다.  
fetch 함수에 첫 번째 인수로 HTTP 요청을 전송할 URL만 전달하면 GET 요청을 전송한다.

```javascript
  fetch('https://jsonplaceholder.typicode.com/todos/1')
    .then(response => console.log(response));
```

<style>
ol {
  margin-top: 3rem !important;
}
ol li {
  font-size: 1em !important;
  margin-top: 1.3rem !important;
}
p, li {
  font-size: 0.8em !important;
  line-height: 2rem !important;
}
strong {
  font-weight: bold;
  color: #b5794a;
}
</style>

---


Response 객체는 HTTP 응답을 나타내는 다양한 프로퍼티를 제공  


Response.prototype.json 메서드는 Response 객체에서 HTTP 응답 몸체를 취득하여 역직렬화한다.
```javascript
  fetch('https://jsonplaceholder.typicode.com/todos/1')
    // response는 HTTP 응답을 나타내는 Response 객체다.
    // json 메서드를 사용하여 Response 객체에서 HTTP 응답 몸체를 취득하여 역직렬화한다.
    .then(response => response.json())
    // json은 역질렬화된 HTTP 응답 몸체다.
    .then(json => console.log(json));
    // {userId: 1, id:1, title: }
```


---

## fetch 함수를 사용할 떄는 에러 처리에 주의

```javascript
  const wrongUrl = 'https://jsonplaceholder.typicode.com/xxx/1';

  // 부적절한 URL이 지정되었기 떄문에 404 not found 에러가 발생한다.
  fetch(wrongUrl)
    .then(() => console.log('ok'))
    .catch(() => console.log('error'));
```

404 Not Found나 500 Internal Server Error와 같은 HTTP 에러가 발생 - 불리언 타입의 ok 상태를 false로 설정한 Response 객체를 resolve  
오프라인 등의 네트워크 장애나 CORS 에러에 의해 요청이 완료되지 못한 경우 - 프로미스를 reject

따라서 fetch 함수를 사용할 때는 fetch 함수가 반환한 프로미스가 resolve한 불리언 타입의 ok 상태를 확인해 명시적으로 에러를 처리할 필요가 있다.

```javascript
  const wrongUrl = 'https://jsonplaceholder.typicode.com/xxx/1';

  // 부적절한 URL이 지정되었기 떄문에 404 not found 에러가 발생한다.
  fetch(wrongUrl)
    .then(response => {
      if(! response.ok)  throw new Error(response.statusText);
      return response.json();
    })
    .then(todo => console.log(todo))
    .catch(err => console.log(err));
```

<style>
  h2 {
    color: #b39c36;
    font-size: 1em !important;
  }
  p, li {
    font-size: 0.7em !important;
  }
  .slidev-layout h1 + p {
    opacity: 1;
  }
</style>

---

## fetch 함수를 통해 HTTP 요청을 전송

<div grid="~ cols-2 gap-10" class="pt-2">
<div>

첫번째 인수로 HTTP 요청을 전송할 URL  
두번째 인수로 HTTP 요청 메서드, HTTP 요청 헤더, 페이로드 등을 설정한 객체를 전달

### GET 
- 단순히 원격 API에 있는 데이터를 가져올 때 쓰이는 GET 방식의 HTTP 통신

### POST 
- 원격 API에서 관리하고 있는 데이터를 생성해야 한다면 요청 전문을 포함할 수 있는 POST 방식의 HTTP 통신이 필요

### PATCH 
- 원격 API에서 관리하고 있는 데이터를 갱신할 때 자주 쓰임

### DELETE 
- 원격 API에서 관리하는 데이터의 수정과 삭제를 위해서 DELETE 방식의 HTTP 호출을 해야할 때 필요

</div>
<div>

```javascript
  const request = {
    get(url) {
      return fetch(url);
    },
    post(url, payload) {
      return fetch(url, {
        method: 'POST',
        headers: {'content-Type': 'application/json'},
        body: JSON.stringify(payload)
      });
    },
    patch(url, payload) {
      return fetch(url, {
        method: 'PATCH',
        headers: {'content-Type': 'application/json'},
        body: JSON.stringify(payload)
      });
    },
    delete(url) {
      return fetch(url, {method: 'DELETE'});
    }
  };

```

</div>
</div>

<style>
  h2 {
    color: #b39c36;
    font-size: 1em !important;
  }
  h3 {
    color: limegreen;
    font-size: 0.8em !important;
    opacity: 0.8;
  }
  p, li {
    font-size: 0.7em !important;
  }
  .slidev-layout h1 + p {
    opacity: 1;
  }
</style>

---
class: px-10
---


<div grid="~ cols-2 gap-10" class="pt-2">
<div>

```javascript
  // 1. GET 요청
  request.get('https://jsonplaceholder.typicode.com/todos/1')
    .then(response => {
      if(!response.ok) throw new Error(response.statusText);
      return response.json();
    })
    .then(todos => console.log(todos))
    .catch(err => console.error(err));

  // {userId: 1, id: 1, title: 'delectus aut autem', completed: false}


  // 2. POST 요청
  request.post('https://jsonplaceholder.typicode.com/todos', {
    userId: 1,
    title: 'JavaScript',
    complated: false
  }).then(response => {
      if(!response.ok) throw new Error(response.statusText);
      return response.json();
    })
    .then(todos => console.log(todos))
    .catch(err => console.error(err));

  // {userId: 1, title: 'JavaScript', complated: false, id: 201}
```

</div>
<div>

```javascript
  //3. PATCH 요청
  request.patch('https://jsonplaceholder.typicode.com/todos/1', {
    complated: true
  }).then(response => {
      if(!response.ok) throw new Error(response.statusText);
      return response.json();
    })
    .then(todos => console.log(todos))
    .catch(err => console.error(err));

  //{userId: 1, id: 1, title: 'delectus aut autem', completed: false, complated: true}



  // 4. DELETE 요청
  request.delete('https://jsonplaceholder.typicode.com/todos/1')
    .then(response => {
      if(!response.ok) throw new Error(response.statusText);
      return response.json();
    })
    .then(todos => console.log(todos))
    .catch(err => console.error(err));
  
  // {}
```

</div>
</div>

---
layout: center
class: text-center
---

# Thanks!
12주 동안 고생 많으셨습니다!
