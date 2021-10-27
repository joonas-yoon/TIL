# async, defer 속성

## async

`<script async>...</script>` 는 자주 봤다. 이름에서 알 수 있듯이 비동기적으로 실행된다.

페이지 로드와 다른 스크립트의 로드를 기다리기 않고 바로 실행한다. 그렇기때문에 `DOMContentLoaded` 이벤트와 싱크가 안 맞을 수 있다.

## defer

defer 속성은 이름으로 추측해본다면, '미루다/지연하다'는 의미를 내포하고 있다.

이 속성을 가진 스크립트는 백그라운드에서 다운로드되고, 그 동안 HTML 파싱은 멈추지 않는다.\
그리고 `DOMContentLoaded` 이벤트는 DOM이 준비되어도, 이 `defer` 스크립트를 기다린다고 한다.

요약하자면, DOM이 준비되고, `defer` 스크립트가 (다운로드 완료와 상관없이) 적힌 순서대로 실행된 이후에, 나머지 스크립트들이 실행된다는 뜻이다. (`async`는 당연히 논외)



아래 페이지에서 자세한 설명과 실행 결과를 확인할볼 수 있다.

{% embed url="https://ko.javascript.info/script-async-defer" %}
