# TDZ \(Temporal Dead Zone\)

먼저 아래 코드를 살펴보자.

```javascript
new Car('red'); // Does it work? (no; ReferenceError)

class Car {
  constructor(color) {
    this.color = color;
  }
}
```

나는 당연히 클래스도 호이스팅되어 사용이 가능할 줄 알았으나, 아니었다. 참고로 함수는 가능하다.

```javascript
greet('World'); // Does it work? (yes)

function greet(who) {
  return `Hello, ${who}!`;
}
```

생각해보면 [ES6부터는 항상 strict 모드](https://262.ecma-international.org/6.0/#sec-strict-mode-code)로 해석되기 때문에 예상 가능한 결과였다.

`let`, `const`, `class` 키워드는 TDZ의 영향을 받으므로 주의해야겠다.

##  Links

{% embed url="https://dmitripavlutin.com/javascript-variables-and-temporal-dead-zone/" %}



