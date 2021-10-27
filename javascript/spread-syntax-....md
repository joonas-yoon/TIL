---
description: '[...a] 와 같은 연산자에 대해서'
---

# Spread syntax (...)



{% embed url="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax" %}

ES6부터 생긴 문법으로, 코드를 작성할때나 어떤 솔루션들에서 종종 보인다.

처음 접했던 케이스는 가장 짧고도 간단한 array deep copy 였다. 자바스크립트는 객체를 대입할 때 copy-by-reference로 하기 때문에, 아래의 코드는 주석과 같은 결과가 나온다.

```javascript
let a = [1, 2, 3];
let b = a;
b[0] = 'x';
// a: ['x', 2, 3]
// b: ['x', 2, 3]
```

Spread syntax를 사용하면 아래와 같이 간단한 식으로 deep copy가 된다.

```javascript
let a = [1, 2, 3];
let b = [...a];
b[0] = 'x';
// a: [1, 2, 3]
// b: ['x', 2, 3]
```

ES9부터는 Object도 가능하다고 한다.

```javascript
let a = {x: 1, y: 2};
let b = {...a};
b['x'] = 9;
// a: {x: 1, y: 2}
// b: {x: 9, y: 2}
```
