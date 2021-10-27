# 화살표 함수 표현 (arrow function expression)

## 화살표 함수 표현

자바스크립트로 서버사이드, 클라이언트사이드 모두 개발하면서 `() => {}` 이나 `() => x` 와 같은 문법을 많이 사용했었다.

그 이유는 그 많던 이름없는 `function` 키워드가 사라졌고, 코드가 짧아 읽기에도 편했기 때문이다.

하지만 그 문법과 동작을 자세히 살펴본 적이 없었다.



화살표 함수는 ES6에서 추가된 문법이다. 화살표 함수는 항상 이름이 없는 "익명"이다.

```javascript
(param1, param2, …, paramN) => { statements }
(param1, param2, …, paramN) => expression
// 다음과 동일함:  => { return expression; }

// 매개변수가 하나뿐인 경우 괄호는 선택사항:
(singleParam) => { statements }
singleParam => { statements }

// 매개변수가 없는 함수는 괄호가 필요:
() => { statements }
```

화살표(`=>`)다음의 괄호의 유무는 실행만 하는 지와 값을 반환하는 지 여부의 차이이다.

함수 정의를 어떻게 표현하는 지의 대한 문법이므로, 아래 실행 결과로 확인해봤다.

```javascript
> function f(){
  console.log(1);
  return 2;
}

> let y = () => { f() }
> let z = () => f()

> y()
// output: 1
// return: undefined

> z()
// output: 1
// return: 2
```

한가지 또 알게 된 사실은, [이 문서](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Functions/%EC%95%A0%EB%A1%9C%EC%9A%B0\_%ED%8E%91%EC%85%98)에 따르면 `this`을 바인딩이 되지 않는다고 말한다. 즉, `this` 가 없다.

그럼 이 익명 함수에서 사용하는 `this`는 대체 무엇을 가리킬까.

```javascript
> function A() {
  this.count = 0;
  setInterval(() => {this.count++;}, 1000);
}

> let a = new A()
// a = A {count: 1}
// a의 카운트는 1초마다 증가하고 있다. 이는 객체마다 독립적이다.
```

자신을 둘러싸고 있는 렉시컬 스코프(Lexical scope)의 `this`를 사용한다. 즉, 이 함수가 선언된 스코프의 `this` 를 사용한다.

위 코드는 `A` 라는 클래스를 만들어서 변수를 추가하였고, 화살표 함수로 count 변수를 1초마다 1씩 증가하게 한다.

(사실 자바스크립트의 모든 클래스 생성자는 function 이기 때문에...)





##

Links

* [https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Functions/%EC%95%A0%EB%A1%9C%EC%9A%B0\_%ED%8E%91%EC%85%98](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Functions/%EC%95%A0%EB%A1%9C%EC%9A%B0\_%ED%8E%91%EC%85%98)
