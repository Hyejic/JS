---
# try also 'default' to start simple
theme: purplin
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

# 42 ~ 44 장

비동기 프로그래밍, Ajax, REST API

<div class="pt-12">
  <span @click="$slidev.nav.next" class="px-2 py-1 rounded cursor-pointer" hover="bg-white bg-opacity-10">
    Press Space for next page <carbon:arrow-right class="inline"/>
  </span>
</div>

---

# **비동기 프로그래밍**

<style>
h1 {
  display: flex;
  justify-content: center;
  margin-top: 200px;
}
</style>

---

# **동기 처리와 비동기 처리**

- 자바스크립트 엔진은 **단 하나의 실행 컨텍스트 스택을** 갖는다.**(싱글 스레드)**
- 따라서 **동시에 2개 이상의 함수를 동시에 실행할 수 없다**.
- 처리에 시간이 걸리는 태스크를 실행하는 경우 **블로킹(작업 중단)이 발생**.

---

# **동기 처리와 비동기 처리**

<div grid="~ cols-2 gap-2" m="-t-2">

```ts
function sleep(func, delay) {
  const delayUntil = Date.now() + delay;
  while (Date.now() < delayUntil);
  func();
}
function foo() {
  console.log("foo");
}
function bar() {
  console.log("bar");
}

sleep(foo, 3 * 1000); // sleep 함수는 3초 이상 실행 된다.
bar(); // bar 함수는 sleep 함수의 실행이 종료된 이후에 호출되므로 3초 이상 블로킹된다.
// (3초 경과 후) foo 호출 -> bar 호출
```

```ts {all|9|all}
// 위 함수를 setTimeout으로 수정
function foo() {
  console.log("foo");
}
function bar() {
  console.log("bar");
}

setTimeout(foo, 3 * 100);
bar();
// bar 호출 -> (3초 경과 후) foo 호출
```

</div>

- 현재 실행 중인 태스크가 종료할 때까지 다음에 실행될 태스크가 대기하는 방식을 **동기처리**라고 한다.
- 현재 실행 중인 태스크가 종료되지 않은 상태라 해도 다음 태스크를 곧바로 실행하는 방식을 **비동기 처리**하고 한다.

---

# **동기 처리와 비동기 처리**

- 비동기 처리 방식으로는 **콜백 패턴**을 전통적으로 사용하지만 <br/>
  **가독성이 나쁘고(콜백 헬)**, <br/>
  비동기 처리 중 발생한 **에러의 예외 처리가 곤란**하고, <br/>
  **여러 개의 비동기 처리**를 한 번에 처리하는 데도 **한계**가 있다.
- **비동기 처리 방식**: 타이머함수(setTimeout, setInterval), HTTP 요청, 이벤트 핸들러
  | | **동기 처리** | **비동기 처리** |
  | --- | --- | --- |
  | **장점** | 태스크의 실행 순서가 보장된다. | 블로킹이 발생하지 않는다. |
  | **단점** | 순서에 따라 태스크들이 블로킹 된다. | 태스크의 실행 순서가 보장되지 않는다. |

---

# **이벤트 루프와 태스크 큐**

![이미지](/img/1.png)

<style>
img {
  margin: 0 auto;
  height: 400px;
}
</style>

---

# **이벤트 루프와 태스크 큐**

- **이벤트 루프**: 자바스크립트의 동시성을 지원하는 것.
- 구글의 V8 자바스크립트 엔진을 비롯한 대부분은 크게 2개의 영역으로 구분.
  <br/>**콜 스택**: 스택 자료구조인 실행 컨텍스트 스택.
  <br/>**힙**: 객체가 저장되는 메모리 공간. 실행 컨텍스트는 힙에 저장된 객체를 참조.
- 비동기 처리에서 소스코드의 평가와 실행을 제외한 모든 처리는 <br/>자바스크립트 엔진을 구동하는 환경인 **브라우저 또는 Node.js가 담당**.
- **태스크 큐**: 비동기 함수의 콜백 함수 또는 이벤트 핸들러가 일시적으로 보관되는 영역.
- **이벤트 루프**: 콜 스택에 현재 실행 중인 실행 컨텍스트가 있는지, 태스크 큐에 대기 중인 함수가 있는지 반복해서 확인 → 만약 콜 스택이 비어 있고, 태스크 큐에 대기 중인 함수가 있다면 → 순차적으로 태스크 큐에 대기 중인 함수를 콜 스택으로 이동시킴.
- 싱글 스레드 방식으로 동작하는 것은 브라우저가 아닌 브라우저에 내장된 자바스크립트 엔진. **자바스크립트 엔진은 싱글 스레드**로 동작하지만 **브라우저는 멀티 스레드로 동작**.

---

# **이벤트 루프와 태스크 큐**

```ts
// 해당 함수 순서 알아보기
function foo() {
  console.log("foo");
}
function bar() {
  console.log("bar");
}

setTimeout(foo, 0); // 0초 후에 foo 함수가 호출.
bar();
```

**1.** 전역 코드가 평가되어 전역 실행 컨텍스트가 생성되고 **콜 스택에 푸시**.

**2.** setTimeout 함수 호출, setTimeout 함수의 함수 실행 컨택스트 생성되어 **콜 스택에 푸시**.
브라우저의 Web API인 타이머 함수도 실행 컨텍스트 생성.

**3.** setTimeout 함수 실행, 콜백 함수 호출 스케줄링 후 **콜 스택에서 팝**.
타이머 설정과 만료(호출 스케줄링)가 되면 콜백 함수를 태스크 큐에 푸시하는 것은 브라우저의 역활.

---

# **이벤트 루프와 태스크 큐**

<br/>

**4.** **브라우저 수행**(a)과 **자바스크립트 엔진 수행**(b)이 **병행 처리**됨.

- 타이머 설정 및 만료 진행 후 콜백 함수 **foo가 태스크 큐에 푸시**.
  = 0초(4ms) 후에 콜백 함수 foo가 태스크 큐에 푸시되어 대기.
- bar 함수 호출, 실행 컨텍스트 생성, **콜 스택에 푸시**. bar 함수 종료시 **콜 스택에서 팝**.
  = 이때 0초(4ms)가 경과했다면 foo함수는 아직 태스크 큐에서 대기 중.

**5.** 전역 코드 실행 종료 후, 전역 실행 컨텍스트가 **콜 스택에서 팝**.

**6.** 이벤트 루프에 의해 콜 스택이 비어있음이 감지되고 태스크 큐에서 대기 중인 콜백 함수 foo가 이벤트 루프에 의해 **콜 스택에 푸시**, foo 함수 실행 후 **콜 스택에서 팝**.

---

# **Ajax**

<style>
h1 {
  display: flex;
  justify-content: center;
  margin-top: 200px;
}
</style>

---

# **Ajax란?**

- 자바스크립트를 사용하여 브라우저가 서버에게 **비동기 방식으로 데이터를 요청**하고, 서버가 응답한 데이터를 수신하여 **웹페이지를 동적으로 갱신**하는 프로그래밍 방식.
- 브라우저에서 제공하는 Web API인 **XMTHttpRequest 객체를 기반으로 동작**.
- XMTHttpRequest는 HTTP 비동기 통신을 위한 **메서드와 프로퍼티를 제공**.

---

# **Ajax란?**

### 전통적인 웹페이지 생명 주기

- 화면 전환되면 서버로부터 새로운 HTML을 전송받아 **웹페이지 전체를 다시 렌더링**.

![이미지](/img/2.png)

- **단점**

**1.** 이전 웹페이지와 차이가 없어 **변경할 필요가 없는 부분까지 다시 전송 받음**.

**2.** 화면 전환이 일어나면 **화면**이 순간적으로 **깜박이는 현상이 발생**.

**3.** 클라이언트와 서버와의 통신이 동기 방식이기에 **블로킹 발생**.

<style>
img {
  margin: 0 auto;
  height: 200px;
}
</style>

---

# **Ajax란?**

### Ajax

![이미지](/img/3.png)

- **장점**

**1.** 변경 필요한 데이터만 전송받기에 **불필요한 데이터 통신이 발생하지 않음**.

**2.** **화면**이 순간적으로 **깜박이는 현상이 발생하지 않음**.

**3.** 클라이언트와 서버와의 통신이 비동기 방식이기에 **블로킹 발생하지 않음**.

<style>
img {
  margin: 0 auto;
  height: 200px;
}
</style>

---

# **JSON**

- **JSON**: 클라이언트와 서버간의 HTTP통신을 위한 **텍스트 데이터 포맷**.

```jsx
  // 표기 방식
  {
  	"name": "Lee",
  	"age": 20,
  	"alive": true,
  	"hobby": ["traveling", "tennis"]
  }
  // 키는 반드시 큰따옴표로 묶어야 하며,
  // 값은 객체 리터럴과 같은 표기법을 사용할 수 있지만 문자열은 반드시 큰따옴표 사용.
```

---

# **JSON**

- **직렬화**: **JSON.stringify** 메서드는 객체와 배열을 JSON 포맷의 문자열로 변환.

```jsx {all|2-3|4|all|7-8|16-22|all|11-15|16-22|all}
  // 객체를 JSON 포맷의 문자열로 변환.
  const json= JSON.stringify(obj);
  console.log(typeof json, json);
  // string {"name": "Lee", "age": 20, "alive": true, "hobby": ["traveling", "tennis"]}

  // 객체를 JSON 포맷의 문자열로 변환하면서 들여쓰기.
  const prettyJson= JSON.stringify(obj, null, 2);
  console.log(typeof prettyJson, prettyJson);

  // replaacer 함수. 값의 타입이 Number이면 필터링되어 반환되지 않음.
  functin filter(key, value) {
  	return typeof value === 'number' ? undefined : value;
  }
  const strFilteredObject = JSON.stringify(obj, filter, 2);
  console.log(typeof strFilteredObject, strFilteredObject);
  /*
  string {
  	"name": "Lee",
  	"alive": true,
  	"hobby": ["traveling", "tennis"]
  }
  */
```

---

# **JSON**

- **역직렬화**: **JSON.parse** 메서드는 JSON 포맷의 문자열을 객체와 배열로 변환.

```jsx
// 객체를 JSON 포맷의 문자열로 변환.
const json = JSON.stringify(obj);

// JSON 포맷의 문자열을 객체로 변환.
const parsed = JSON.parse(json);
console.log(typeof parsed, parsed);
// object {name: "Lee", age: 20, alive: true, hobby: ["traveling", "tennis"]}
```

---

# **XMLHttpRequest**

- **XMLHttpRequest 객체**는 XMLHttpRequest 생성자 함수를 호출하여 생성.
- 브라우저에서 제공하는 Web API이므로 **브라우저 환경에서만 정상적으로 실행**.

```jsx
const xhr = new XMLHttpRequest();
```

---

# **XMLHttpRequest**

### XMLHttpRequest 객체의 다양한 프로퍼티와 메서드

- XMLHttpRequest 객체의 **프로토타입 프로퍼티**

| **프로토타입 프로퍼티** | **설명**                                                                  |
| ----------------------- | ------------------------------------------------------------------------- |
| readyState              | HTTP 요청의 현재 상태를 나타내는 정수. 객체의 정적 프로퍼티(아래 표 참조) |
| status                  | HTTP 요청에 대한 응답 상태(HTTP 상태 코드)를 나타내는 정수(ex.200)        |
| statusText              | HTTP 요청에 대한 응답 메시지를 나타내는 문자열 (ex. “OK”)                 |
| responseType            | HTTP 응답 타입 (ex. document, json, text, blob, arraybuffer)              |
| response                | HTTP 요청에 대한 응답 몸체. responseType에 따라 타입이 다름.              |
| reponseText             | 서버가 전송한 HTTP 요청에 대한 응답 문자열                                |

---

# **XMLHttpRequest**

### XMLHttpRequest 객체의 다양한 프로퍼티와 메서드

- XMLHttpRequest 객체의 **이벤트 핸들러 프로퍼티**

| **이벤트 핸들러 프로퍼티** | **설명**                                                     |
| -------------------------- | ------------------------------------------------------------ |
| onreadystatechange         | readyState 프로퍼티 값이 변경된 경우                         |
| onloadstart                | HTTP 요청에 대한 응답을 받기 시작한 경우                     |
| onprogress                 | HTTP 요청에 대한 응답을 받는 도중 주기적으로 발생            |
| onabort                    | abort 메서드에 의해 HTTP 요청이 중단된 경우                  |
| onerror                    | HTTP 요청에 에러가 발생한 경우                               |
| onload                     | HTTP 요청이 성공적으로 완료한 경우                           |
| ontimeout                  | HTTP 요청이 시간이 초과한 경우                               |
| onloadend                  | HTTP 요청이 완료한 경우, HTTP 요청이 성공 또는 실패하면 발생 |

---

# **XMLHttpRequest**

### XMLHttpRequest 객체의 다양한 프로퍼티와 메서드

- XMLHttpRequest 객체의 **객체의 메서드**

| **메서드**        | **설명**                                 |
| ----------------- | ---------------------------------------- |
| open              | HTTP 요청 초기화                         |
| send              | HTTP 요청 전송                           |
| abort             | 이미 전송된 HTTP 요청 중단               |
| setRequestHeader  | 특정 HTTP 요청 헤더의 값을 설정          |
| getResponseHeader | 특정 HTTP 요청 헤더의 값을 문자열로 반환 |

---

# **XMLHttpRequest**

### XMLHttpRequest 객체의 다양한 프로퍼티와 메서드

- XMLHttpRequest 객체의 **객체의 정적 프로퍼티**

| **정적 프로퍼티** | **값** | **설명**                              |
| ----------------- | ------ | ------------------------------------- |
| UNSENT            | 0      | open 메서드 호출 이전                 |
| OPENED            | 1      | open 메서드 호출 이후                 |
| HEADERS_RECEIVED  | 2      | send 메서드 호출 이후                 |
| LOADING           | 3      | 서버 응답 중(응답 데이터 미완성 상태) |
| DONE              | 4      | 서버 응답 완료                        |

---

# **XMLHttpRequest**

### HTTP 요청 순서

**1.** http://XMLHttpRequest.prototype.open 매서드로 HTTP **요청을 초기화.**

**2.** 필요에 따라 **XMLHttpRequest.prototype.setRequestHeader 매서드**로 특정 HTTP **요청의 헤더 값을 설정**.

**3.** **XMLHttpRequest.prototype.send 메서드**로 HTTP **요청을 전송**.

```jsx
// XMLHttpRequest 객체 생성
const xhr = new XMLHttpRequest();
// HTTP 요청 초기화
xhr.open("GET", "/users");
// HTTP 요청 헤더 설정
// 클라이언트가 서버로 전송할 데이터의 MINE 타입 지정: JSON
xhr.setRequestHeader("content-type", "application/json");
//HTTP 요청 전송
xhr.send();
```

---

# **XMLHttpRequest**

### HTTP 요청 순서

**http://XMLHttpRequest.prototype.open 메서드**

```jsx
  // 호출 방법
  xhr.open(method, url[, async])
```

| **매개변수** | **설명**                                                           |
| ------------ | ------------------------------------------------------------------ |
| method       | HTTP 요청 메서드(”GET”, “POST”, “PUT”, “DELETE” 등)                |
| url          | HTTP 요청을 전송할 URL                                             |
| async        | 비동기 요청 여부, 옵션으로 기본값은 true이며, 비동기 방식으로 동작 |

<style>
  p {
    text-decoration: none;
    }
</style>

---

# **XMLHttpRequest**

### HTTP 요청 순서

- http://XMLHttpRequest.prototype.open 메서드

| **HTTP 요청 메서드** | **종류**       | **목적**              | **페이로드** |
| -------------------- | -------------- | --------------------- | ------------ |
| GET                  | index/retrieve | 모든/특정 리소스 취득 | X            |
| POST                 | create         | 리소스 생성           | O            |
| PUT                  | replace        | 리소스의 전체 교체    | O            |
| PATCH                | modify         | 리소스의일부 수정     | O            |
| DELETE               | delete         | 모든/특정 리소스 삭제 | X            |

---

# **XMLHttpRequest**

### HTTP 요청 순서

- http://XMLHttpRequest.prototype.send 메서드

  - 서버로 전송하는 데이터는 GET, POST 요청 메서드에 따라 전송 방식 차이가 있다.
    - GET 요청 메서드는 데이터를 URL의 일부분인 쿼리 문자열로 서버에 전송
    - POST 요청 메서드는 데이터를 요청 몸체에 담아 전송

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/2861e275-4322-47e3-862f-c33bf648482e/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220615%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220615T174435Z&X-Amz-Expires=86400&X-Amz-Signature=9561a8d394f343981916b0ab2aa41fe51ee4c30013106dc9edbbe5429fe3d800&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

<style>

img {
  margin: 0 auto;
  height: 200px;
}
</style>

---

# **XMLHttpRequest**

### HTTP 요청 순서

- http://XMLHttpRequest.prototype.send 메서드

  - send 메서드에는 요청 몸체에 담아 전송할 데이터(페이로드)를 인수로 전달 가능.
  - HTTP 요청 메서드가 GET인 경우 send 메서드에 페이로드로 전달할 인수는 무시되고 요청 몸체는 null로 설정.

```jsx
xhr.send(JSON.stringify({ id: 1, content: "HTML", completed: false }));
```

---

# **XMLHttpRequest**

### HTTP 요청 순서

- http://XMLHttpRequest.prototype.opensetRequestHeader 메서드
  - 특정 HTTP 요청의 **헤더 값을 설정**.
  - 반드시 open 메서드를 호출한 이후에 호출
  - 자주 사용하는 HTTP 요청 헤더: **Content-type, Accept**

---

# **XMLHttpRequest**

### HTTP 요청 순서

- http://XMLHttpRequest.prototype.opensetRequestHeader 메서드

  - **Content-type**
    - 요청 몸체에 담아 전송할 데이터의 MIME 타입의 정보를 표현

<br/>

<div grid="~ cols-2 gap-2" m="-t-2">

| **MIME 타입** | **서브 타입**                                      |
| ------------- | -------------------------------------------------- |
| text          | text/plain, text/html, text/css, text/javascript   |
| application   | application/json, application/x-www-form-urlencode |
| multipart     | multipart/formed-data                              |

```jsx
const xhr = new XMLHttpRequest();
xhr.open("POST", "/users");
xhr.setRequestHeader("content-type", "application/json");
xhr.send(JSON.stringify({ id: 1, content: "HTML", completed: false }));
```

</div>

---

# **XMLHttpRequest**

### HTTP 요청 순서

- http://XMLHttpRequest.prototype.opensetRequestHeader 메서드

  - **Accept**

    - HTTP 클라이언트가 서버 요청 시 서버가 응답할 데이터의 MIME 타입을 Accept로 지정 가능
    - Accept 헤더를 미설정 시 send 메서드가 호출될 때 Accept 헤더가 \*/\* 으로 전송

    ```jsx
    xhr.setRequestHeader("accept", "application/json");
    ```

---

# **XMLHttpRequest**

### HTTP 응답 처리

- readystatechange 이벤트를 캐치하여 HTTP **응답을 처리**.
- XMLHttpRequest 객체는 브라우저에서 제공하는 Web API이므로 반드시 브라우저 환경에서 실행.

---

# **XMLHttpRequest**

### HTTP 응답 처리

```jsx {all|8|all}
const xhr = new XMLHttpRequest();
// https://jsonplaceholder.typicode.com은 Fake REST API를 제공하는 서비스
xhr.open('GET', 'https://jsonplaceholder.typicode.com/todos/1');
xhr.send();

// readystatechange 이벤트는 HTTP 요청의 현재 상태를 나타내는
// readyState 프로퍼티가 변경될 때마다 발생
xhr.onreadystatechange = () => {
	// readyState 프로퍼티 값이 4(XMLHttpRequest.DONE)이면 서버 응답 완료
	if xhr.readyState !== XMLHttpRequest.DONE) return;

	// status 프로퍼티는 응답 상태 코드를 나타내며 값이 200이면 응답된 상태
	// 정상적으로 응답된 상태라면 response 프로퍼티에서버의 응답 결과가 담겨 있다
	if (xhr.status === 200) {
		console.log(JSON.parse(xhr.response)); // {userID: 1, id: 1, title: ~~~ }
	} else {
		console.error('Error', xhr.status, xhr.statusText);
	}
};
```

---

# **XMLHttpRequest**

### HTTP 응답 처리

- **readystatechange 이벤트**는 readyState 프로퍼티가 변경될 때마다 발생.
- **onreadystatechange 이벤트 핸들러 프로퍼티에 할당한 이벤티 핸들러**는 xhr.readyState가 XMLHttpRequest.DONE인지 확인.
- 서버 응답 완료 후 **xhr.status가 200인지 확인**하여 **정상 처리와 에러 처리를 구분**.
- **정상 도착했다면**, 요청에 대한 응답 몸체를 나타내는 xhr.response에서 서버가 전송한 데이터를 취득. **에러가 발생했다면**, 에러 처리.

---

# **XMLHttpRequest**

### HTTP 응답 처리

```jsx {all|5|all}
const xhr = new XMLHttpRequest();
xhr.open("GET", "https://jsonplaceholder.typicode.com/todos/1");
xhr.send();

chr.onload = () => {
  if (xhr.status === 200) {
    console.log(JSON.parse(xhr.response)); // {userID: 1, id: 1, title: ~~~ }
  } else {
    console.error("Error", xhr.status, xhr.statusTest);
  }
};
```

- readystatechange 이벤트 대신 **load 이벤트를 캐치**해도 ok.
- load 이벤트는 HTTP 요청이 성공적으로 완료한 경우 발생하기에 **DONE확인 미필요**.

---

# **REST API**

<style>
h1 {
  display: flex;
  justify-content: center;
  margin-top: 200px;
}
</style>

---

# **REST API**

- **REST**: HTTP를 기반으로 클라이언트가 서버의 리소스에 접근하는 방식을 규정한 아키텍처.
- **REST API**: REST를 기반으로 서비스 API를 구현한 것.

---

# **REST API의 구성**

| **구성요소** | **내용**                       | **표현 방법**    |
| ------------ | ------------------------------ | ---------------- |
| 자원         | 자원                           | URI(엔드포인트)  |
| 행위         | 자원에 대한 행원               | HTTP 요청 메서드 |
| 표현         | 자원에 대한 행위의 구체적 내용 | 페이로드         |

---

# **REST API의 설계 원칙**

- **REST에서 중요한 기본 원칙** = RESTful API를 설계하는 중심 규칙

<br/>

<div grid="~ cols-2 gap-2" m="-t-2">

- **URI는 리소스를 표현**

```jsx
// 리소스를 식별할 수 있는 이름은 동사보다는 명사를 사용
# bad
GET /getTodos/1
GET /todos/show/1

# good
GET /todos/1
```

- **행위에 대한 정의는 HTTP 요청 메서드로 표현**

```jsx
// 리소스에 대한 행위는 HTTP 요청 메서드를 통해 표현하며
// URI에 표현하지 않음
# bad
GET /todos/delete/1

# good
DELETE /todos/1
```

</div>

---

# **JSON Server를 이용한 REST API 실습**

### **1.** JSON Server 설치

<br/>

- npm에서 JSON Server 설치

```jsx
$ mkdir json-server-exam && cd json-server-exam
$ npm init -y
$ npm install json-server --save-dev
```

---

# **JSON Server를 이용한 REST API 실습**

### **2.** db.json 파일 생성

<br/>

- 프로젝트 루트 폴터(/json-server-exam)에서 db.json 파일 생성

```jsx
{
	"todos": [
		{
			"id": 1,
			"content": "HTML",
			"completed": true
		},
		{
			"id": 2,
			"content": "CSS",
			"completed": false
		},
		{
			"id": 3,
			"content": "Javascript",
			"completed": true
		}
	]
}
```

---

# **JSON Server를 이용한 REST API 실습**

### **3.** JSON Sever 실행

<br/>

```jsx
// watch 옵션을 추가하여 db.json 파일의 변경을 감지한다.
## 기본 포트(3000) 사용 / watch 옵션 적용
$ json-server --watch db.json

## 포트 변경 / watch 옵션 적용
$ json-server --watch db.json --port 5000
// 위 방법이 되지 않아 npm i -g json-server 설치

// 명령어 대신 package.json 파일의 scripts 수정으로 실행 가능
{
	"name": "json-server-exam",
	"version": "1.0.0",
	"scripts": {
		"start": "json-server --watch db.json"
	},
	"devDependencies": {
		"json-server": "^0.16.1"
	}
}
```

---

# **JSON Server를 이용한 REST API 실습**

### **4.** GET 요청

<br/>

<div grid="~ cols-2 gap-2" m="-t-2">

- todos 리소스에서 모든 todo를 취득.
- JSON Server의 루트 폴더에 public 폴더를 생성하고 JSON Server를 중단 후 재 실행
- public 폴더에 get_index.html을 추가하고 http://localhost:3000/get_index.html 접속

```html
<!DOCTYPE html>
<html>
  <body>
    <pre></pre>
    <script>
      const xhr = new XMLHttpRequest();
      // todos 리소스에서 id를 사용하여 특정 todo를 취득(retrieve)
      xhr.open("GET", "/todos/1");
      xhr.send();

      xhr.onload = () => {
        if (xhr.status === 200) {
          document.querySelector("pre").textContent = xhr.response;
        } else {
          console.error("Error", xhr.status, xhr.statusText);
        }
      };
    </script>
  </body>
</html>

{ "id": 1, "content": "HTML", "completed": true }
```

</div>

---

# **JSON Server를 이용한 REST API 실습**

### **5.** POST 요청

<br/>

<div grid="~ cols-2 gap-2" m="-t-2">

- 새로운 todo를 생성.
- POST 요청 시 setRequestHeader 메서드를 요청 몸체에 담아 서버로 전송할 페이로드의 MIME 타입을 지정.
- public 폴더에 post.html을 추가하고 http://localhost:3000/post.html 접속

```html
<!DOCTYPE html>
<html>
  <body>
    <pre></pre>
    <script>
      const xhr = new XMLHttpRequest();
      xhr.open("POST", "/todos");
      xhr.setRequestHeader("content-type", "application/json");
      xhr.send(JSON.stringify({ id: 4, content: "Angular", completed: false }));
      chr.onload = () => {
        // 200 = OK, 201 = Created
        if (xhr.status === 200 || xhr.status === 201) {
          document.querySelector("pre").textContent = xhr.response;
        } else {
          console.error("Error", xhr.status, xhr.statusText);
        }
      };
    </script>
  </body>
</html>

{ "id": 4, "content": "Angular", "completed": false }
```

</div>

---

# **JSON Server를 이용한 REST API 실습**

### **6.** PUT 요청

<br/>

<div grid="~ cols-2 gap-2" m="-t-2">

- PUT은 특정 리소스 전체를 교체할 때 사용.
- PUT 요청 시 setRequestHeader 메서드를 요청 몸체에 담아 서버로 전송할 페이로드의 MIME 타입을 지정.
- public 폴더에 put.html을 추가하고 http://localhost:3000/put.html 접속

```html
<!DOCTYPE html>
<html>
  <body>
    <pre></pre>
    <script>
      const xhr = new XMLHttpRequest();
      xhr.open("PUT", "/todos/4");
      xhr.setRequestHeader("content-type", "application/json");
      xhr.send(JSON.stringify({ id: 4, content: "React", completed: true }));

      chr.onload = () => {
        if (xhr.status === 200) {
          document.querySelector("pre").textContent = xhr.response;
        } else {
          console.error("Error", xhr.status, xhr.statusText);
        }
      };
    </script>
  </body>
</html>

{ "id": 4, "content": "React", "completed": true }
```

</div>

---

# **JSON Server를 이용한 REST API 실습**

### **7.** PATCH 요청

<br/>

<div grid="~ cols-2 gap-2" m="-t-2">

- PATCH는 특정 리소스의 일부를 수정할 때 사용.
- PATCH 요청 시 setRequestHeader 메서드를 요청 몸체에 담아 서버로 전송할 페이로드의 MIME 타입을 지정.
- public 폴더에 patch.html을 추가하고 http://localhost:3000/patch.html 접속

```html
<!DOCTYPE html>
<html>
  <body>
    <pre></pre>
    <script>
      const xhr = new XMLHttpRequest();
      xhr.open("PATCH", "/todos/4");
      xhr.setRequestHeader("content-type", "application/json");
      xhr.send(JSON.stringify({ completed: false }));

      chr.onload = () => {
        if (xhr.status === 200) {
          document.querySelector("pre").textContent = xhr.response;
        } else {
          console.error("Error", xhr.status, xhr.statusText);
        }
      };
    </script>
  </body>
</html>

{ "id": 4, "content": "React", "completed": false }
```

</div>

---

# **JSON Server를 이용한 REST API 실습**

### **8.** DELETE 요청

<br/>

<div grid="~ cols-2 gap-2" m="-t-2">

- todo 리소스에서 id를 사용하여 todo를 삭제.
- public 폴더에 delete.html을 추가하고 http://localhost:3000/delete.html 접속

```html
<!DOCTYPE html>
<html>
  <body>
    <pre></pre>
    <script>
      const xhr = new XMLHttpRequest();
      xhr.open("PATCH", "/todos/4");
      xhr.send();

      chr.onload = () => {
        if (xhr.status === 200) {
          document.querySelector("pre").textContent = xhr.response;
        } else {
          console.error("Error", xhr.status, xhr.statusText);
        }
      };
    </script>
  </body>
</html>

{}
```

</div>
