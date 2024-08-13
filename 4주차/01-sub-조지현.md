# 클로저

클로저는 태어난 환경을 기억한다.
클러저는 태어남 변수를 기억한다...

함수는 특별한 프로퍼티 [[Environment]]에 저장된 정보를 이용해 자기 자신이 태어난 곳을 기억합니다. [[Environment]]는 함수가 만들어진 렉시컬 환경을 참조합니다.

# 폴리필

구식 브라우저를 사용할때
모던 자스 지원 기능을 직접 구현해서 사용

# new function

사실 쓰이는지는 모르겠다.

```
let func = new Function ([arg1, arg2, ...argN], functionBody);
```

# setTimeout

setTimeout(실행코드,지연시간)

```
function sayHi() {
  alert('안녕하세요.');
}

setTimeout(sayHi, 1000);
```

## clearTimeout으로 스케줄링 취소하기

```
let timerId = setTimeout(...);
clearTimeout(timerId);

```

# setInterval

setInterval(실행함수,주기시간)

주기적으로 실행

## clearInterval(timerId)으로 중단하기

```
let timerId = setTimeout(...);
clearInterval(timerId);
```

# func.call func.apply 데코레이터

함수가 얼마나 많이 호출되었는지 세거나 호출 시 얼마나 많은 시간이 소모되었는지 등의 정보를 래퍼의 프로퍼티에 저장할 수 있습니다.

`func.call(context, arg1, arg2, ...)`

```
func.call(context, ...args); // 전개 구문을 사용해 인수가 담긴 배열을 전달하는 것과
func.apply(context, args);   // call을 사용하는 것은 동일합니다.
```

func.call(context, arg1, arg2…) – 주어진 컨텍스트와 인수를 사용해 func를 호출합니다.
func.apply(context, args) – this에 context가 할당되고, 유사 배열 args가 인수로 전달되어 func이 호출됩니다.

## 콜 포워딩(call forwarding)

컨텍스트와 함께 인수 전체를 다른 함수에 전달하는 것

# 함수 바인딩

`func.bind(context);`

# 화살표 함수

- 화살표 함수엔 'arguments’가 없습니다
- 화살표 함수에는 'this’가 없습니다

# 프로퍼티 getter와 setter

```
let obj = {
  get propName() {
    // getter, obj.propName을 실행할 때 실행되는 코드
  },

  set propName(value) {
    // setter, obj.propName = value를 실행할 때 실행되는 코드
  }
};
```

# [[Prototype]]

자바스크립트의 객체는 명세서에서 명명한 [[Prototype]]이라는 숨김 프로퍼티를 갖습니다.

프로토타입은 프로퍼티를 읽을 때만 사용합니다.

- Object.create(proto, [descriptors]) – [[Prototype]]이 proto를 참조하는 빈 객체를 만듭니다. 이때 프로퍼티 설명자를 추가로 넘길 수 있습니다.
- Object.getPrototypeOf(obj) – obj의 [[Prototype]]을 반환합니다.
- Object.setPrototypeOf(obj, proto) – obj의 [[Prototype]]이 proto가 되도록 설정합니다.
