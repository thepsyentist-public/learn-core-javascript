# 01 📚 상황에 따라 달라지는 this

<br>

## 📝 읽은 내용

### this
#### 전역 공간에서의 this
- 전역 공간에서 this는 전역 객체를 가리킨다.

```js
// 브라우저 환경
console.log(this) // window { alert: f(), atob: f(), blur: f(), btoa: f(), ...}
console.log(window) // window { alert: f(), atob: f(), blur: f(), btoa: f(), ...}

// node.js환경
console.log(this) // global { process: {title: 'node', version: ...}
console.log(global) // global { process: {title: 'node', version: ...}
```

### 자바스크립트의 모든 변수는 사실 특정 객체의 프로퍼티

```js
var a = 1;
console.log(a); // 1
console.log(window.a); // 1
console.log(this.a); // 1
```

- 전역변수 a에 1을 할당했다.
- 전역변수는 자바스크립트 엔진으로 인해 전역객체의 프로퍼티로 할당된다.
- 전역 공간에서 this는 전역객체를 가리킨다.
- 따라서 `this.a`는 1이다.
- `a`를 입력해도 1이 나오는 이유는 스코프 체인에서 a를 검색하다가 가장 마지막에 도달하는 전역 스코프의 a를 발견하여 반환했기 때문

### 메서드로서 호출할 때의 this
- 메서드로 this는 호출한 객체를 참조한다.

```js
var obj = {
  method: function(x) { console.log(this, x); }
};
obj.method(1);  // { method: f } 1
obj['method'](2)  // { method: f } 2
```

### 메서드 내부에서의 this
- this에는 호출한 주제에 대한 정보가 담긴다.
- 점 표기법의 경우 마지막 점 앞에 명시된 객체가 곧 this가 된다.

```js
var obj = {
  methodA: function() { console.log(this); },
  inner: {
    methodB: function(){ console.log(this); }
  }
}

obj.inner.methodB();  // { methodB: f }
```

### 함수로서 호출할 때 그 함수 내부에서의 this
- 함수로서 호출할 경우에는 this가 지정되지 않는다. this에는 호출한 주제에 대한 정보가 담긴다.
- 실행 컨텍스트를 활성화할 당시에 this가 지정되지 않은 경우 this는 전역 객체를 바라본다.
- 따라서 함수에서의 this는 전역 객체를 가리킨다.
> 더글라스 크락포드는 이를 명백한 설계상의 오류라고 지적한다.

### 화살표 함수
- ES6에서는 함수 내부에서 this가 전역객체를 바라보는 문제를 보완하고자, this를 바인딩하지 않는 화살표 함수를 새로 도입했다.
- 화살표 함수는 실행 컨텍스트를 생성할 때 this 바인딩 과정 자체가 빠지게 되어, 상위 스코프의 this를 그대로 활용할 수 있다.

```js
var obj = {
  outer: function(){
    console.log(this);          // (1) { outer: f }
    var innerFunc = () => {
      console.log(this);        // (2) { outer: f }
    };
    innerFunc();
  }
}
obj.outer();
```

### 생성자 함수 내부에서의 this
- 생성자 함수로 생성된 인스턴스를 this로 바인딩 한다.

```js
var Cat = function(name, age){
  this.bark = '이동';
  this.name = name;
  this.age = age;
}
var choco = new Cat('초코', 7);
var nabi = new Cat('나비', 5);
console.log(choco, nabi);

// Cat { bark: '야옹', name: '초코', age: 7 }
// Cat { bark: '야옹', name: '나비', age: 5 }
```

## 💡 인사이트
- 어떤 함수를 객체의 프로퍼티에 할당한다고 해서 그 자체로서 무조건 메서드가 되는 것이 아니라 객체의 메서드로서 호출할 경우에만 메서드로 동작하고, 그렇지 않으면 함수로 동작한다.



```js
var func = function(x){
  console.log(this, x)
}

func(1); // Window { ... } 1
var obj = {
  method: func
};
obj.method(2); // { method: f } 2
```

- 메서드는 메서드로서 호출한 객체를 this로 바인딩한다. 메서드의 내부함수는 연관이없다.

```js
var obj1 = {
  outer: function(){
    console.log(this);             // (1)
    var innerFunc = function(){
      console.log(this)            // (2)  (3)
    }
    innerFunc();

    var obj2 = {
      innerMethod: innerFunc
    };
    obj.innerMethod();
  }
};
obj1.outer();
```

- (1): `obj1`, (2):전역객체(window), (3): `obj2`
- (1)은 메서드로서 호출 됨
- (2)은 함수로서 호출 됨
- (3)은 메서드로서 호출 됨
