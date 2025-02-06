# 02 📚 명시적으로 this를 바인딩하는 방법

<br>

## 📝 읽은 내용

### call메서드
- call 메서드는 메서드의 호출 주체인 함수를 즉시 실행하도록 하는 명령
- 이때 call메서드의 첫 번째 인자를 this로 바인딩하고, 이후의 인자들을 호출할 함수의 매개변수로 한다.

```js
var func = function(a, b, c){
  console.log(this, a, b, c);
}

func(1, 2, 3)
func.call({ x: 1 }, 4, 5, 6};
```

### apply 메서드
- 두 번째 인자를 배열로 받아 그 배열의 요소들을 호출할 함수의 매개변수로 저장한다
- 기능적으로는 call메서드와 완전히 동일하다.

```js
var func = function(a, b, c){
  console.log(this, a, b, c);
}
func.apply({x: 1}, [4, 5, 6]);   // { x: 1 } 4 5 6

var obj = {
  a: 1,
  method: function(x, y){
    console.log(this.a, x, y);
  }
};
obj.method.apply({ a: 4 }, [5, 6]); // 4 5 6
```

### bind 메서드
- ES5에서 추가된 기능으로, call과 비슷하지만 즉시 호출하지는 않고 넘겨 받은 this 및 인수들을 바탕으로 새로운 함수를 반환하기만 하는 메서드이다.
- 다시 새로운 함수를 호출할 때 인수를 넘기면 그 인수들은 기존 bind 메서드를 호출할 때 전달했던 인수들의 뒤에 이어서 등록된다.

```js
var func = function(a, b, c, d){
  console.log(this, a, b, c, d);
}
func(1, 2, 3, 4);     // Window{ ... } 1 2 3 4

var bindFunc1 = func.bind({ x: 1});
bindFunc1(5, 6, 7, 8);  // { x: 1 } 5 6 7 8

var bindFunc2 = func.bind({ x: 1 }, 4, 5);
bindFunc2(6, 7);      // { x: 1 } 4 5 6 7
bindFunc2(8, 9);      // { x: 1 } 4 5 8 9
```

#### name 프로퍼티
- bind 메서드를 적용해서 새로 만든 함수는 한 가지 독특한 성질이 있는데 바로 name 프로퍼티에 동사 bind의 수동태인 'bound'라는 접두어가 붙는다.
- 기존의 call이나 apply보다 코드를 추적하기에 더 수월해진 면이 있다.

```js
var func = function(a, b, c, d){
  console.log(this, a, b, c);
};
var bindFunc = func.bind({ x: 1 }, 4, 5);
console.log(func.name);            // func
console.log(bindFunc.name);        // bound func
```


## 💡 인사이트
- call, apply, bind메서드의 차이를 알 수 있었고, bind메서드가 좀 더 명시적이라는 느낌이 들었다.
