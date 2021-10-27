---
description: Liskov Substitution principle (LSP; 리스코프 치환 원칙)
---

# 3. LSP

## Liskov Substitution principle (LSP; 리스코프 치환 원칙)

> 프로그램의 객체는 프로그램의 정확성을 깨뜨리지 않으면서 하위 타입의 인스턴스로 바꿀 수 있어야 한다.

어떤 모듈 S가 모듈 T의 하위 모듈이라면, 속성의 변경없이 T를 S(상위)로 교체할 수 있어야 한다고 한다.\
즉, 부모 클래스와 자식 클래스가 일관되어야 한다는 뜻이다.

### 위반 사례

이를 위반하는 대표적인 사례로는 원-타원 문제 (또는 사각형-정사각형 문제)가 있다.

```cpp
class Rectangle {
  private int width;
  private int height;

  public void setWidth(int width){
    this.width = width;
  }

  public void setHeight(int height){
    this.height = height;
  }

  public int getArea() {
    return this.width * this.height;
  }
}

// Test
public void testArea(Rectangle rect) {
  rect.setWidth(5);
  rect.setHeight(4);
  System.out.println("area = " + rect.getArea()); // it must be 20.
}
```

사각형을 표현하는 `Rectangle` 라는 클래스가 있다.\
그리고 두 사각형의 넓이를 구하는 테스트 함수 `testArea()`가 있다.

그리고 하위 클래스로 정사각형을 표현하는 `Square` 라는 클래스를 만들었다.

```cpp
class Square extends Rectangle {
}

public void main(String[] args) {
  testArea(new Rectangle());
  testArea(new Square());
}
```

**그런데 뭔가 이상하다. 두 사각형의 넓이가 모두 20이 나온다.**

정사각형의 너비(width)와 높이(height)가 다르게 설정되어서이다. 그럼 정사각형이 아니지 않은가.\
그럼 `Square` 클래스를 이렇게 수정해보자.

```cpp
class Square extends Rectangle {
  @Override
  public void setWidth(int width) {
    super.setWidth(width);
    super.setHeight(width);
  }
  
  @Override
  public void setHeight(int height) {
    super.setWidth(height);
    super.setHeight(height);
  }
}
```

이제 정사각형의 너비나 넓이 중 무엇을 설정하여도, 두 값을 변경하여 정사각형으로 만들고 있다.

**... 그런데 이럴거면 `Rectangle` 클래스를 왜 상속한거지?**\
****정사각형에게 "너비 설정"과 "높이 설정"이 굳이 따로 있을 이유가 없다. 오히려 혼란스럽다.

즉, 동작에 있어서 상위 클래스인 `Rectangle` 과 `Square` 는 서로 일관되게 행동하지 않는다.

### 해결

위의 두 사각형 클래스는 포함 관계를 가지지 않으므로, 부모-자식이 아닌 형제(sibling)관계로 만들어 해결할 수 있다.

![Rectalges and Square - LSP compliant solution](<../../.gitbook/assets/image (1).png>)

상위에 `Shape` 라는 클래스를 두어 형제 관계로 만들 수도 있다.

## Links

* [https://ko.wikipedia.org/wiki/SOLID\_(객체\_지향\_설계)](https://ko.wikipedia.org/wiki/SOLID\_\(%EA%B0%9D%EC%B2%B4\_%EC%A7%80%ED%96%A5\_%EC%84%A4%EA%B3%84\))
* [https://dev-momo.tistory.com/entry/SOLID-원칙](https://dev-momo.tistory.com/entry/SOLID-%EC%9B%90%EC%B9%99)
* [https://dzone.com/articles/the-liskov-substitution-principle-with-examples](https://dzone.com/articles/the-liskov-substitution-principle-with-examples)
* [http://stg-tud.github.io/sedc/Lecture/ws13-14/3.3-LSP.html](http://stg-tud.github.io/sedc/Lecture/ws13-14/3.3-LSP.html)
