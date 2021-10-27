---
description: Single Responsibility Principle (SRP; 단일 책임 원칙)
---

# 1. SRP

## Single Responsibility Principle (SRP; 단일 책임 원칙)

> 하나의 클래스나 모듈은 단 하나의 책임만 가져야 한다.

하나의 함수가 여러 개의 일을 한다는 뜻은 예측 불가능하다는 의미이다.

### 예시

아주 단적인 예시로, 끝 원소를 제거를 하는 함수가 삭제되는 원소를 반환할 이유는 없다. 제거만 하면 된다.

```cpp
Element pop() {
    if (size < 1) throw "Empty";
    size = size - 1;
    return array[size + 1]; // ?
}
```

삭제되는 값을 얻고 싶다면, `pop` 하기 전에 끝자리를 `get` 하는 것이 옳다. 삭제는 삭제만, 조회는 조회만 하는 것이 예측할 수 없는 사이드 이펙트를 줄일 수 있기 때문이다.

```cpp
void pop() {
    size = size - 1;
}

Element get() {
    return array[size - 1];
}
```

즉, 기능을 더 쪼갤 수 없을 만큼 아토믹(atomic)한 것이 좋다.

## Links

* [https://ko.wikipedia.org/wiki/SOLID\_(객체\_지향\_설계)](https://ko.wikipedia.org/wiki/SOLID\_\(%EA%B0%9D%EC%B2%B4\_%EC%A7%80%ED%96%A5\_%EC%84%A4%EA%B3%84\))
* [https://ko.wikipedia.org/wiki/단일\_책임\_원칙](https://ko.wikipedia.org/wiki/%EB%8B%A8%EC%9D%BC\_%EC%B1%85%EC%9E%84\_%EC%9B%90%EC%B9%99)
