# Template Literals

[Template Literals](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Template_literals)는 다른 언어에도 존재하여 익숙할 문자열 포맷팅 문법이다. 예를 들면, `printf("%s")` 나  `print("{} and {}")` 과 같다.

그 사용 방법은 아래와 같다.

```javascript
let datatime = (new Date()).toDateString();
console.log(`<div class="date">${datetime}</div>`);
```

문제는 마음껏 사용할 수 없는 문법이라는 점이다.

[ES6 호환성 표](https://kangax.github.io/compat-table/es6/)를 보면 Internet Explorer 11 버전에서조차 이것을 지원하지 않는다. 

babel 트랜스컴파일러에서는 이것을 변환하는 플러그인 [@babel/plugin-transform-template-literals](https://babeljs.io/docs/en/babel-plugin-transform-template-literals)을 지원한다.

