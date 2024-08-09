# ‘try…catch’ 문법

```
try {

  // 코드...

} catch (err) {

  // 에러 핸들링

}
```

이렇게 try {…} 블록 안에서 에러가 발생해도 catch에서 에러를 처리하기 때문에 **스크립트는 죽지 않습니다.**

## ‘throw’ 연산자

try 내부에 throw 를 던짐

```
let json = '{ "age": 30 }'; // 불완전한 데이터

try {

  let user = JSON.parse(json); // <-- 에러 없음

  if (!user.name) {
    // try 내부에 throw 를 던짐
    throw new SyntaxError("불완전한 데이터: 이름 없음"); // (*)
  }

  alert( user.name );

} catch(e) {
  alert( "JSON Error: " + e.message ); // JSON Error: 불완전한 데이터: 이름 없음
}
```

## ‘다시 던지기’ 기술

에러를 다시 던져서 catch 블록에선 SyntaxError만 처리되도록 해보겠습니다.

```
let json = '{ "age": 30 }'; // 불완전한 데이터
try {

  let user = JSON.parse(json);

  if (!user.name) {
    throw new SyntaxError("불완전한 데이터: 이름 없음");
  }

  blabla(); // 예상치 못한 에러

  alert( user.name );

} catch(e) {

  if (e instanceof SyntaxError) {
    alert( "JSON Error: " + e.message );
  } else {
    // 에러 다시 던지기
    throw e;
  }

}
```

에러는 try..catch ‘밖으로 던져집니다’.

이때 바깥에 try..catch가 있다면 여기서 에러를 잡습니다.

```
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
  alert( "External catch got: " + e ); // 에러를 잡음
}
```

## try…catch…finally

try..catch는 finally라는 코드 절을 하나 더 가질 수 있습니다.
finally 는 항상 실행됩니다.
finally 절은 무언가를 실행하고, 실행 결과에 상관없이 실행을 완료하고 싶을 경우 사용됩니다.

- 에러가 없는 경우: try 실행이 끝난 후
- 에러가 있는 경우: catch 실행이 끝난 후

```
try {
   ... 코드를 실행 ...
} catch(e) {
   ... 에러 핸들링 ...
} finally {
   ... 항상 실행 ...
}
```

```
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

```
try {
  // 이곳의 코드를 실행
} catch(err) {
  // 에러가 발생하면, 여기부터 실행됨
  // err는 에러 객체
} finally {
  // 에러 발생 여부와 상관없이 try/catch 이후에 실행됨
}
```
