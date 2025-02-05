# 02 📚 VariableEnvironment

<br>

## 📝 읽은 내용

### VariableEnvironment
- VariableEnvironment에 담기는 내용은 LexicalEnvironment와 같지만 최초 실행 시의 스냅샷을 유지한다는 점이 다르다.
- 실행 컨텍스트를 생성할 때 VariableEnvironment에 정보를 먼저 담은 다음, 이를 그대로 복사해서 LexicalEnvironment를 만들고, 이후에는 LexicalEnvironment를 주로 활용하게 된다.
- 즉 VariableEnvironment는 스냅샷으로, 변경 사항을 반영하지 않지만, LexicalEnvironment은 처음에 스냅샷과 똑같이 저장하지만 이후에 변경 사항을 실시간으로 반영한다.

### LexicalEnvironment
- 흔히, '어휘적 환경', '정적 환경'이라는 단어로 소개가 된다.
- 하지만, 책에서는 '사전적 환경'이라고 변역하는데 좀 더 이해하기 수월할 수 있다.

#### environmentRecord와 호이스팅
- environmentRecord에는 현재 컨텍스트와 관련된 코드의 식별자 정보들이 저장된다.
- 변수 정보를 수집하는 과정을 모두 마쳤더라도 아직 실행 컨텍스트가 관여할 코드들은 실행되기 전의 상태이다.
- 코드가 실행되기 전임에도 불구하고 자바스크립트 엔진은 이미 해당 환경에 속한 코드의 변수명들을 모두 알고 있다.
- 이때 코드가 실행되면서 호이스팅이 발생하는 것이다.

#### 호이스팅이 일하는 과정 예시

```js
function a(x){
  console.log(x);
  var x;
  console.log(x);
  var x = 2;
  console.log(x);
}
a(1)
```

1. 전역 컨텍스트의 envrionmentRecord가 함수, 변수의 식별자들을 저장한다.
2. 컨텍스트가 실행되면서 코드를 읽어나가다가 a함수를 실행시키면서 a함수 컨텍스트가 콜스택에 쌓인다.
3. 콜 스택에 쌓인 a함수의 environmentRecord가 변수의 식별자들을 저장한다.
4. a함수의 로직을 실행한다.
5. 실행 결과는 순서대로 1, 1, 2를 출력한다.
6. 첫 번째 출력이 1이 나온 이유는 매개변수의 x를 바라보았기 때문에 1이 출력된다.
7. 두 번째 출력이 1 나온 이유는 `var x;`는 이미 존재하는 변수를 재선언만 할 뿐, 초기화는 하지 않는다.
8. 세 번째 출력이 2가 나온 이유는 x의 값이 할당되었기 떄문이다.

- 이해를 돕기 위한 호이스팅 코드 (실제 이렇게 동작하지는 않지만 이해하기에는 무관)

```js
function a(){
  var x;
  var x;
  var x;

  x = 1;
  console.log(x);
  console.log(x);
  x = 2;
  console.log(x);
}
a(1)
```

- 이렇게 동작하는 이유는 environmentRecord는 현재 실행될 컨텍스트의 대상 코드 내에 어떤 식별자들이 있는지에만 관심이 있고, 각 식별자에 어떤 값이 할당될 지는 관심이 없기 때문이다.
- 따라서 변수 호이스팅할 때 변수명만 끌어올리고 할당 과정은 원래 자리에 그대로 남겨둔다.
- 매개 변수도 마찬가지이다.
> 예상 결과는 1, undefined, 2였다 ㅠㅠ


### 변수에서 함수로 바뀐다면?

```js
function a(){
  console.log(b);
  var b = 'bbb';
  console.log(b);
  function b(){ }
  console.log(b);
}
a();
```

- 예상 동작 코드 예시

```js
function a(){
  var b;
  function b() { }
  console.log(b); // function b
  b = 'bbb';
  console.log(b); // 'bbb'
  console.log(b); // 'bbb'
}
a();
```

#### outerEnvironmentReference

#### 스코프란?
- 식별자에 대한 유효범위
- 어떤 경계 A의 외부에서 선언한 변수는 A의 외부뿐 아니라 A의 내부에서도 접근할 수 있다.
- A의 내부에서 선언한 변수는 오직 A의 내부에서만 접근할 수 있다.
- 이러한 범위를 지정하는 것을 스코프라고 하며 자바스크립트는 전역 공간을 제외하면 오직 함수에 의해서만 스코프가 생성된다.

#### 스코프체인
- outerEnvironmentReference는 현재 호출된 함수가 선언될 당시의 LexicalEnvironment를 참조한다.
- 예를 들어, A 함수 내부에 B 함수를 선언하고 다시 B 함수 내부에 C 함수를 선언한 경우, 함수 C의 outerEnvironmnetRefernece는 함수 B의 LexicalEnvironemnt를 참조한다. 같은 방식으로 함수 B는 A를 참조한다.
- 이런식으로 참조의 연결리스트가 생기는데, 선언 시점의 LexicalEnvironment를 계속 찾아 올라가면 마지막에 전역 컨텍스트의 LexicalEnvironment가 있다.
- 또한 각 outerEnvironmentReference는 오직 자신이 선언된 시점의 LexicalEnvironment만 참조하고 있으므로 가장 가까운 요소부터 차례대로만 접근할 수 있다.

```js
var a = 1;
var outer = function(){
  var inner = function(){
    console.log(a); //(1)
    var a = 3;
  };
  inner();
  console.log(a); //(2)
};
outer();
console.log(a); //(3)
```

1. `(1)`의 출력 결과는 undefined이다. 그 이유는, `inner함수`가 선언될 당시에 함수 스코프에 변수 a가 선언되어 environmentRecord에 의해 수집되어 호이스팅이 발생했기 때문이다. 즉, 스코프체인을 타지 않고 가장 가까운 a의 변수를 찾은 결과이다.
2. `(2)`의 출력 결과는 1이다. 그 이유는, `outer함수`가 선언될 당시에 LexicalEnvironment를 참조하는데, 선언될 당시에 변수 a가 선언되지 않았기 때문에, 스코프 체인을 통해 변수 a를 찾는다. 결국 전역 스코프까지가서 변수 a를 찾고 1을 출력한다.
3. `(3)`의 출력 결과는 1이다. 그 이유는, `(3)`은 전역 스코프에서 출력되었고, 전역에는 변수 a가 선언되고 할당되어 전역 변수 a를 참조한다.


## 💡 인사이트
- VariableEnvironment는 스냅샷으로 실행 컨텍스트의 선언된 변수와 함수들을 초기화하여 정보들을 담는다. 그 이후 LexicalEnvironment가 그 정보들을 그대로 담고 이후 변경된 값을 감지한다.
- EnvironmentRecord는 선언된 식별자들을 수집한다. 수집된 식별자들은 OuterReference를 통해 상위 스코프와 체인을 형성하면서 스코프체인을 형성한다.
- 로직이 실행되면서 LexicalEnvironment가 변수들의 값을 할당한다.
