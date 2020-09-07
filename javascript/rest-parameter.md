# 나머지 매개변수 \(Rest parameter\)

C언어에서 가변인자와 같은 기능이다. 문법은 다음과 같다.

```text
function f(...args) {
  // ...
}
```

이것 역시 ES6 부터 추가되었으며, 사용을 권장하고 있다.

이전에도 `arguments` 라는 예약어가 있었으나, 이건 파라미터 전부에 접근한다. \(쉘에서 `$1` 의 표현을 떠올리면 이해하기 쉽다.\)

그럼 얘는 뭐가 다르냐?

`arguments`는 따로 정의된 특수한 객체지만, 나머지 매개변수는 `Array`의 인스턴스이다.

그리고 `...<name>` 에 해당하는 부분만 `Array`로 넘어간다.

```text
function f(tag, ...msg) {
   return tag +": " + msg.join('/');
}

f('alphabet', 'a', 'b', 'c');  // "alphabet: a/b/c"
f('primes', 2, 7, 9, 11);      // "primes: 2/7/9/11"
```

그렇다보니 `...arr` 로 넘기고 `arr.pop()` 과 같은 조작이 가능하다.



## Links

* [https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Functions/rest\_parameters](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Functions/rest_parameters)

