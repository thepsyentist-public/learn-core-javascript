## 04 📚 const와 let

<br>

## 추가 내용

### const와 let의 호이스팅
지금까지 `var`로만 실행 컨텍스트를 통한 호이스팅을 설명했다. 그렇다면, const와 let에서는 호이스팅이 어떻게 이루어질까?

```js
console.log(a);    //undefined
var a;

console.log(b);   // ReferenceError: Cannot access 'b' before initialization
const b = 1;

console.log(c)  // ReferenceError: Cannot access 'c' before initialization
let c
```

var로 선언하는 것과 다른 결과물이 나온다. 그 이유는 TDZ에 있다.

### TDZ(Temporal Dead Zone)

![image](https://github.com/user-attachments/assets/74011049-7000-4cc0-b0e6-33e35e9e9ce8)

'일시적 사각 지대'라고 번역된다. var와 다르게 const와 let으로 선언된 변수들은 envorinmentRecord에 의해 수집되지 않는다.
즉, undefined로 초기화 되지 않고 선언문이 실행될떄까지 TDZ에 머무른다. 이 때, 변수를 참조하게 되면 ReferenceError를 발생시킨다.

> 오해하면 안 되는 점은 const와 let도 호이스팅이 발생한다는 것이다.
> 그 증거로, 선언문 이전에 변수를 참조하면 is not defined가 아닌 ReferenceError가 발생한다.
> 다만, 기존 var의 호이스팅으로 인해 발생하는 오류가 많았기 때문에,
> ECMAScript가 버전업되면서 이를 시스템적으로 방지하도록 설계된 것이다.

### const와 let의 전역객체 등록 방식
이전에 var로 변수를 선언하게 되면 전역 객체의 프로퍼티로 등록된다고 했다.

```js
var a = 1;
console.log(window.a) // 1;
```

그렇다면 const와 let은 다르게 동작할까?

```js
const a = 1;
console.log(window.a) // undefined

let b = 1;
console.log(window.b) // undefined
```

#### var는 함수 스코프
- var는 '함수 스코프'를 가지므로, 전역에서 선언하면 전역 객체의 프로퍼티로 등록된다.
- 함수 스코프란, 변수가 선언된 함수의 컨텍스트 내에서만 유효하다는 의미이다.

```js
if (true) {
    var x = 10;
}
console.log(x); // 10 (if 블록을 빠져나와도 접근 가능)
```

#### const, let은 블록 스코프
- const와 let은 블록 스코프이므로, 전역에서 선언된다 하여도 전역 객체의 프로퍼로 등록되지 않는다.
- 블록 스코프란, 변수가 선언된 블록 {} 내에서만 유효하다는 의미이다.
- 블록은 함수뿐만 아니라, 조건문과 반복문도 포함된다.

```js
if(true) {
  const a = 10;
}
console.log(a) // ReferenceError a is not defined
```


## 💡 인사이트
- const와 let의 호이스팅과정과 TDZ의 연관성을 이어서 설명할 수 있게 되었다.
- 블럭 스코프와 함수 스코프를 꺠우치고 둘을 전역 객체와 연관지어 설명할 수 있게 되었다.
