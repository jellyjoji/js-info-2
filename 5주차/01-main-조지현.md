# ‘try…catch’ 문법

```jsx
try {
  // 코드...
} catch (err) {
  // 에러 핸들링
}
```

이렇게 try {…} 블록 안에서 에러가 발생해도 catch에서 에러를 처리하기 때문에 **스크립트는 죽지 않습니다.**

## ‘throw’ 연산자

`throw <error object>`
try 내부에 throw 를 던짐

```jsx
let json = '{ "age": 30 }'; // 불완전한 데이터

try {
  let user = JSON.parse(json); // <-- 에러 없음

  if (!user.name) {
    // try 내부에 throw 를 던짐
    throw new SyntaxError("불완전한 데이터: 이름 없음"); // (*)
  }

  alert(user.name);
} catch (e) {
  alert("JSON Error: " + e.message); // JSON Error: 불완전한 데이터: 이름 없음
}
```

## ‘다시 던지기’ 기술

에러를 다시 던져서 catch 블록에선 SyntaxError만 처리되도록 해보겠습니다.

```jsx
let json = '{ "age": 30 }'; // 불완전한 데이터
try {
  let user = JSON.parse(json);

  if (!user.name) {
    throw new SyntaxError("불완전한 데이터: 이름 없음");
  }

  blabla(); // 예상치 못한 에러

  alert(user.name);
} catch (e) {
  if (e instanceof SyntaxError) {
    alert("JSON Error: " + e.message);
  } else {
    // 에러 다시 던지기
    throw e;
  }
}
```

에러는 try..catch ‘밖으로 던져집니다’.

이때 바깥에 try..catch가 있다면 여기서 에러를 잡습니다.

```jsx
function readData() {
  let json = '{ "age": 30 }';

  try {
    // ...
    blabla(); // 에러!
  } catch (e) {
    // ...
    if (!(e instanceof SyntaxError)) {
      throw e; // 알 수 없는 에러 다시 던지기
    }
  }
}
// 다시 던져진 알 수 없는 에러

// 이제 try..catch를 하나 더 만들어,
// 다시 던져진 예상치 못한 에러를 처리해 보겠습니다.
try {
  readData();
} catch (e) {
  alert("External catch got: " + e); // 에러를 잡음
}
```

## try…catch…finally

try..catch는 finally라는 코드 절을 하나 더 가질 수 있습니다.
finally 는 항상 실행됩니다.
finally 절은 무언가를 실행하고, 실행 결과에 상관없이 실행을 완료하고 싶을 경우 사용됩니다.

- 에러가 없는 경우: try 실행이 끝난 후
- 에러가 있는 경우: catch 실행이 끝난 후

```jsx
try {
   ... 코드를 실행 ...
} catch(e) {
   ... 에러 핸들링 ...
} finally {
   ... 항상 실행 ...
}
```

예시

```jsx
try {
  result = fib(num);
} catch (e) {
  result = 0;
  <!-- finally 위치 -->
} finally {
  diff = Date.now() - start;
}
```

### 요약

```jsx
try {
  // 이곳의 코드를 실행
} catch (err) {
  // 에러가 발생하면, 여기부터 실행됨
  // err는 에러 객체
} finally {
  // 에러 발생 여부와 상관없이 try/catch 이후에 실행됨
}
```

# 커스텀 에러와 에러 확장

맨땅에서 커스텀 에러 객체를 만드는 것보다
HttpTimeoutError는 HttpError를 상속받는 식으로
Error를 상속받아 에러 객체를 만드는 것이 낫습니다.

### 에러 재던지기 throw

`throw err`
알려지지 않은 에러가 있을 때 이 에러는 재 던지기

### instanceof

instanceof를 사용하면 에러 종류를 판별할 수 있습니다.

## 예외 감싸기(wrapping exception)

종류를 알 수 있는 에러를 처리하고, 그렇지 않은 에러는 다시 던지기

```jsx
try {
  ...
  readUser()  // 잠재적 에러 발생처
  ...
} catch (err) {
  if (err instanceof ValidationError) {
    // validation 에러 처리
  } else if (err instanceof SyntaxError) {
    // 문법 에러 처리
  } else {
    throw err; // 알 수 없는 에러는 다시 던지기 함
  }
}
```

위에서 ValidationError 와 SyntaxError 에러 이외 에러는 다시 던지기 할수있지만 예외 감싸기로 바깥에서 일괄 처리를 해줄수도 있다.

```jsx
class ReadError extends Error {
  constructor(message, cause) {
    super(message);
    this.cause = cause;
    this.name = "ReadError";
  }
}

class ValidationError extends Error {
  /*...*/
}
class PropertyRequiredError extends ValidationError {
  /* ... */
}

function validateUser(user) {
  if (!user.age) {
    throw new PropertyRequiredError("age");
  }

  if (!user.name) {
    throw new PropertyRequiredError("name");
  }
}

function readUser(json) {
  let user;

  // SyntaxError 에러
  try {
    user = JSON.parse(json);
  } catch (err) {
    if (err instanceof SyntaxError) {
      throw new ReadError("Syntax Error", err);
    } else {
      throw err;
    }
  }

  // validateUser 에러
  try {
    validateUser(user);
  } catch (err) {
    if (err instanceof ValidationError) {
      throw new ReadError("Validation Error", err);
    } else {
      throw err;
    }
  }
}

// readUser 로 던지기 : ReadError에 하나로 모아(wrap) 처리하기 때문에 이제 바깥코드에서 readUser 하나만 확인하면 된다.
try {
  readUser("{잘못된 형식의 json}");
} catch (e) {
  if (e instanceof ReadError) {
    alert(e);
    // Original error: SyntaxError: Unexpected token b in JSON at position 1
    alert("Original error: " + e.cause);
  } else {
    throw e;
  }
}
```

이제 readUser는 위에서 설명한 것처럼 동작합니다. Syntax 에러나 Validation 에러가 발생한 경우 해당 에러 자체를 다시 던지기 하지 않고 ReadError를 던지게 됩니다.

이렇게 되면, readUser를 호출하는 바깥 코드에선 instanceof ReadError 딱 하나만 확인하면 됩니다. 에러 처리 분기문을 여러 개 만들 필요가 없어집니다.

ReadError에 하나로 모아(wrap) 처리하기 때문에 이제 바깥코드에서 readUser 하나만 확인하면 된다.

# 콜백

setTimeout 은 대표적인 비동기 asynchronous 스케줄링이다.
비동기적으로 실행되면 순차적으로 함수가 끝난후에 실행이 된다.

콜백 함수는 나중에 호출할 함수를 의미한다.

## 콜백 기반 비동기 프로그래밍

비동기적으로 수행하는 함수 내 동작이 모두 처리된 후 실행해야하는 함수가 들어갈 경우

```jsx
function loadScript(src, callback) {
  let script = document.createElement("script");
  script.src = src;

  script.onload = () => callback(script);

  document.head.append(script);
}
```

두번째 인수에 callback 함수를 추가하고
원하는 콜백함수를 호출하면 외부 스크립트 안의 함수를 사용할수있다.

```jsx
loadScript('/my/script.js', function() {
 // 콜백 함수는 스크립트 로드가 끝나면 실행됩니다.
 newFunction(); // 이제 함수 호출이 제대로 동작합니다.
 ...
});
```

여기서 callback 인수자리에 functioni(){원하는함수} 를 넣어주었다.

실제 적용 예시이다.

```jsx
function loadScript(src, callback) {
  let script = document.createElement("script");
  script.src = src;
  script.onload = () => callback(script);
  document.head.append(script);
}

// 두번째 callback 인수 자리에 `script => { alert(`${script.src}가 로드되었습니다.`)` 를 콜백함수로 넣어주었다.
loadScript(
  "https://cdnjs.cloudflare.com/ajax/libs/lodash.js/3.2.0/lodash.js",
  (script) => {
    alert(`${script.src}가 로드되었습니다.`);
    alert(_); // 스크립트에 정의된 함수
  }
);
```

# 콜백 속 콜백 (콜백지옥)

콜백 속에 중첩으로 콜백을 호출할수있지만 가시성이 좋지않다.

```jsx
loadScript("/my/script.js", function (script) {
  loadScript("/my/script2.js", function (script) {
    loadScript("/my/script3.js", function (script) {
      // 세 스크립트 로딩이 끝난 후 실행됨
    });
  });
});
```

# 에러 핸들링

## 오류 우선 콜백(error-first callback) 패턴

성공하면 callback(null, script)을, 실패하면 callback(error)을 호출

```jsx
loadScript("/my/script.js", function (error, script) {
  if (error) {
    // 에러 처리
  } else {
    // 스크립트 로딩이 성공적으로 끝남
  }
});
```

### 멸망의 피라미드

아래와 같이 중첩에 중첩이 길어지면 콜백지옥이 만들어진다.

```jsx
loadScript("1.js", function (error, script) {
  if (error) {
    handleError(error);
  } else {
    // ...
    loadScript("2.js", function (error, script) {
      if (error) {
        handleError(error);
      } else {
        // ...
        loadScript("3.js", function (error, script) {
          if (error) {
            handleError(error);
          } else {
            // 모든 스크립트가 로딩된 후, 실행 흐름이 이어집니다. (*)
          }
        });
      }
    });
  }
});
```

=> 이를 보완하기 위해 프라미스(promise) 가 등장한다.

# 프라미스

resolve와 reject는 자바스크립트에서 자체 제공하는 콜백이다.

- resolve(value) — 일이 성공적으로 끝난 경우 그 결과를 나타내는 value와 함께 호출
- reject(error) — 에러 발생 시 에러 객체를 나타내는 error와 함께 호출

```jsx
let promise = new Promise(function (resolve, reject) {
  // executor (제작 코드, '가수')
});
```

executor는 자동으로 실행되는데 여기서 원하는 일이 처리됩니다. 처리가 끝나면 executor는 처리 성공 여부에 따라 resolve나 reject를 호출한다.

- resolve 예시

```jsx
let promise = new Promise(function (resolve, reject) {
  // 프라미스가 만들어지면 executor 함수는 자동으로 실행됩니다.

  // 1초 뒤에 일이 성공적으로 끝났다는 신호가 전달되면서 result는 '완료'가 됩니다.
  setTimeout(() => resolve("완료"), 1000);
});
```

- reject 예시

```jsx
let promise = new Promise(function (resolve, reject) {
  // 1초 뒤에 에러와 함께 실행이 종료되었다는 신호를 보냅니다.
  setTimeout(() => reject(new Error("에러 발생!")), 1000);
});
```

# then, catch, finally

- then(): Promise가 성공적으로 완료되었을 때 실행될 함수를 등록합니다.
  성공적인 결과 값을 인자로 받아 처리할 수 있습니다.
- catch(): Promise가 실패했을 때 실행될 함수를 등록합니다.
  에러 객체를 인자로 받아 처리할 수 있습니다.
- finally(): Promise가 성공하든 실패하든 항상 실행될 함수를 등록합니다.

```jsx
function fetchData() {
  return new Promise((resolve, reject) => {
    // 데이터를 가져오는 비동기 작업 (예: 서버에서 데이터 가져오기)
    setTimeout(() => {
      if (Math.random() > 0.5) {
        resolve("데이터 가져오기 성공");
      } else {
        reject(new Error("데이터 가져오기 실패"));
      }
    }, 1000);
  });
}

fetchData()
  .then((data) => {
    console.log(data); // 데이터 가져오기 성공 시 실행
  })
  .catch((error) => {
    console.error(error); // 데이터 가져오기 실패 시 실행
  })
  .finally(() => {
    console.log("데이터 가져오기 작업 완료"); // 항상 실행
  });
```

then 메서드는 데이터 가져오기가 성공했을 때 실행될 함수를 등록합니다.
catch 메서드는 데이터 가져오기가 실패했을 때 실행될 함수를 등록합니다.
finally 메서드는 성공하든 실패하든 항상 실행될 함수를 등록합니다.

# 프라미스 체이닝

result가 .then 핸들러의 체인(사슬)을 통해 전달.
.then 을 연속해서 사용.

```jsx
new Promise(function (resolve, reject) {
  setTimeout(() => resolve(1), 1000); // (*)
})
  .then(function (result) {
    // (**)

    alert(result); // 1
    return result * 2;
  })
  .then(function (result) {
    // (***)

    alert(result); // 2
    return result * 2;
  })
  .then(function (result) {
    alert(result); // 4
    return result * 2;
  });
```

# fetch와 체이닝 함께 응용하기

```jsx
function loadJson(url) {
  return fetch(url)
    .then(response => response.json());
}

// data fetch 해오기
function loadGithubUser(name) {
  return fetch(`https://api.github.com/users/${name}`)
    .then(response => response.json());
}

function showAvatar(githubUser) {
  return new Promise(function(resolve, reject) {
    let img = document.createElement('img');
    img.src = githubUser.avatar_url;
    img.className = "promise-avatar-example";
    document.body.append(img);

    setTimeout(() => {
      img.remove();
      resolve(githubUser);
    }, 3000);
  });
}

=> .then 체이닝으로 간편하게 fetch

// 함수를 이용하여 다시 동일 작업 수행
loadJson('/article/promise-chaining/user.json')
  .then(user => loadGithubUser(user.name))
  .then(showAvatar)
  .then(githubUser => alert(`Finished showing ${githubUser.name}`));
  // ...
```

# 프라미스와 에러 핸들링 .catch

체인 끝에 .catch를 붙이기

```jsx
fetch("https://no-such-server.blabla") // 거부
  .then((response) => response.json())
  .catch((err) => alert(err)); // TypeError: failed to fetch (출력되는 내용은 다를 수 있음)
```

## throw를 사용해 에러를 던지기

```jsx
new Promise((resolve, reject) => {
  resolve("OK");
})
  .then((result) => {
    throw new Error("에러 발생!"); // 프라미스가 거부됨
  })
  .catch(alert); // Error: 에러 발생!
```

.catch는 이렇게 명시적인 거부뿐만 아니라 핸들러 위쪽에서 발생한 비정상 에러 또한 잡는다.

# async와 await

async await 을 사용하면 promise 보다 비동기를 쉽게 작성할수있다.
async await 을 promise 를 기반하고 있다.

await는 promise.then보다 좀 더 세련되게 프라미스의 result 값을 얻을 수 있도록 해주는 문법입니다.

async 룰 사용하면 함수는 언제나 프라미스를 감싸안고 await 을 사용해야한다.
await 키워드가 붙으면 프라미스가 처리될때까지 대기하고
에러가 발생하면 에러를 생성하고
성공하면 result 값을 반환한다.
