# SOLID 원칙

분명 [로버트 C. 마틴의 클린코드](http://ebook.insightbook.co.kr/book/79) 책에서 봤었는데, 같은 사람이 소개한 규칙이었다. 그런 의미에서 클린 코드와 꽤 긴밀하다.

SOLID 원칙들은, 소프트웨어 작업에서 프로그래머가 소스 코드를 **읽기 쉽고 확장하기 쉽게** 될 때 까지 리팩토링하여 [**코드 냄새**](https://ko.wikipedia.org/wiki/%EC%BD%94%EB%93%9C_%EC%8A%A4%EB%A9%9C)**를 없애기 위해** 쓰기 좋은 지침이다.

## 1. Single Responsibility Principle \(SRP; 단일 책임 원칙\)

> 하나의 클래스나 모듈은 단 하나의 책임만 가져야 한다.

하나의 함수가 여러 개의 일을 한다는 뜻은 예측 불가능하다는 의미이다.

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

즉, 기능을 더 쪼갤 수 없을 만큼 아토믹\(atomic\)한 것이 좋다.

## 2. Open/Closed Principle \(OCP; 개방-폐쇄 원칙\)

> 소프트웨어 요소는 확장에는 열려 있으나, 변경에는 단혀 있어야 하다.

모듈 중 하나를 수정했는데, 그 모듈을 사용하는 모든 모듈의 코드를 수정하는 일이 있으면 안된다는 뜻이다.

조금만 떠올려봐도 얼마나 끔찍한 일인 지 알 수 있다.

### **확장에 대해 열려 있다.**

> 이것은 모듈의 동작을 확장할 수 있다는 것을 의미한다. 
>
> 애플리케이션의 요구 사항이 변경될 때, 이 변경에 맞게 새로운 동작을 추가해 모듈을 확장할 수 있다. 즉, 모듈이 하는 일을 변경할 수 있다.

### **수정에 대해 닫혀 있다.**

> 모듈의 소스 코드나 바이너리 코드를 수정하지 않아도 모듈의 기능을 확장하거나 변경할 수 있다.
>
> 그 모듈의 실행 가능한 바이너리 형태나 링크 가능한 라이브러리 \(예를 들어, Windows의 DLL, 자바의 jar ...\)를 건드릴 필요가 없다.

### 추상화

객체 지향 프로그래밍 언어에서는 "추상화"를 통해서 가능할 수 있다.

추상화 클래스나 인터페이스를 만들어, 상위 클래스는 **수정할 수 없도록 만들되** 서브 클래스에서는 기능을 추가하여 **확장할 수 있도록 만들기** 때문이다.

## 3. Liskov Substitution principle \(LSP; 리스코프 치환 원칙\)

> 프로그램의 객체는 프로그램의 정확성을 깨뜨리지 않으면서 하위 타입의 인스턴스로 바꿀 수 있어야 한다.

어떤 모듈 S가 모듈 T의 하위 모듈이라면, 속성의 변경없이 T를 S\(상위\)로 교체할 수 있어야 한다고 한다.  
즉, 부모 클래스와 자식 클래스가 일관되어야 한다는 뜻이다.

### 위반 사례

이를 위반하는 대표적인 사례로는 원-타원 문제 \(또는 사각형-정사각형 문제\)가 있다.

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

사각형을 표현하는 `Rectangle` 라는 클래스가 있다.  
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

정사각형의 너비\(width\)와 높이\(height\)가 다르게 설정되어서이다. 그럼 정사각형이 아니지 않은가.  
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

**... 그런데 이럴거면 `Rectangle` 클래스를 왜 상속한거지?**  
정사각형에게 "너비 설정"과 "높이 설정"이 굳이 따로 있을 이유가 없다. 오히려 혼란스럽다.

즉, 동작에 있어서 상위 클래스인 `Rectangle` 과 `Square` 는 서로 일관되게 행동하지 않는다.

### 해결

위의 두 사각형 클래스는 포함 관계를 가지지 않으므로, 부모-자식이 아닌 형제\(sibling\)관계로 만들어 해결할 수 있다.

![Rectalges and Square - LSP compliant solution](../.gitbook/assets/image%20%281%29.png)

상위에 `Shape` 라는 클래스를 두어 형제 관계로 만들 수도 있다.

## 4. Interface Segregation Principle \(ISP; 인터페이스 분리 원칙\)

> 특정 클라이언트를 위한 인터페이스 여러 개가 범용 인터페이스 하나보다 낫다.

## 5. Dependency Inversion Principle \(DIP; 의존관계 역전 원칙\)

> 프로그래머는 추상화에 의존해야지, 구체화에 의존하면 안된다.

## Links

* [https://ko.wikipedia.org/wiki/SOLID\_\(%EA%B0%9D%EC%B2%B4\_%EC%A7%80%ED%96%A5\_%EC%84%A4%EA%B3%84\)](https://ko.wikipedia.org/wiki/SOLID_%28%EA%B0%9D%EC%B2%B4_%EC%A7%80%ED%96%A5_%EC%84%A4%EA%B3%84%29)
* [https://dev-momo.tistory.com/entry/SOLID-%EC%9B%90%EC%B9%99](https://dev-momo.tistory.com/entry/SOLID-%EC%9B%90%EC%B9%99)
* [https://dzone.com/articles/the-liskov-substitution-principle-with-examples](https://dzone.com/articles/the-liskov-substitution-principle-with-examples)
* [http://stg-tud.github.io/sedc/Lecture/ws13-14/3.3-LSP.html](http://stg-tud.github.io/sedc/Lecture/ws13-14/3.3-LSP.html)

