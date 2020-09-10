# SOLID 원칙

SOLID 원칙들은, 소프트웨어 작업에서 프로그래머가 소스 코드를 **읽기 쉽고 확장하기 쉽게** 될 때 까지 리팩토링하여 [**코드 냄새**](https://ko.wikipedia.org/wiki/%EC%BD%94%EB%93%9C_%EC%8A%A4%EB%A9%9C)**를 없애기 위해** 쓰기 좋은 지침이다.

## 1. Single Responsibility Principle \(SRP; 단일 책임 원칙\)

### 무엇인가?

> 하나의 클래스나 모듈은 단 하나의 책임만 가져야 한다.

분명 [로버트 C. 마틴의 클린코드](http://ebook.insightbook.co.kr/book/79) 책에서 봤었는데, 같은 사람이 소개한 규칙이었다. 나머지 규칙들도 그러하다.

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

### 무엇인가?

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

### 무엇인가?

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

### 무엇인가?

> 특정 클라이언트를 위한 인터페이스 여러 개가 범용 인터페이스 하나보다 낫다.

### 위반 사례

이 ATM기는 총 3개의 모듈을 가지고 있는데, 어떤 거래\(transaction\)에 대해서 입금\(Deposit\), 출금\(Withdrawal\), 송금\(Transfer\) 모듈을 각자 만들었다.

![](https://flylib.com/books/4/444/1/html/2/images/12_5.jpg)

각 모듈은 전문화된 기능을 가지기 위해 분리되었지만, 무언가 이상하다.

입금 거래 모듈은 입금만 하면 되는데, 위 구조와 같다면 코드가 이럴것이다.

{% tabs %}
{% tab title="DepositTransaction" %}
```java
public class DepositTransaction extends Transaction implements UI {
  @Override public void requestDepositAmount() {
    System.out.println("Request deposit");
  }

  @Override public void requestWithdrawalAmount() {
    throw new UnsupportedMethodException("Can not withdrawal at Deposit Transaction");
  }

  @Override public void requestTransferAmount() {
    throw new UnsupportedMethodException("Can not transfer at Deposit Transaction");
  }

  @Override public void execute() {
    requestDepositAmount();
  }
}
```
{% endtab %}

{% tab title="WithdrawalTransaction" %}
```java
public class WithdrawalTransaction extends Transaction implements UI {
  @Override public void requestDepositAmount() {
    throw new UnsupportedMethodException("Can not deposit at Withdrawal Transaction");
  }

  @Override public void requestWithdrawalAmount() {
    System.out.println("Request withdrawal");
  }

  @Override public void requestTransferAmount() {
    throw new UnsupportedMethodException("Can not transfer at Withdrawal Transaction");
  }

  @Override public void execute() {
    requestWithdrawalAmount();
  }
}
```
{% endtab %}

{% tab title="TransferTransaction" %}
```java
public class TransferTransaction extends Transaction implements UI {
  @Override public void requestDepositAmount() {
    throw new UnsupportedMethodException("Can not deposit at Transfer Transaction");
  }

  @Override public void requestWithdrawalAmount() {
    throw new UnsupportedMethodException("Can not withdrawal at Transfer Transaction");
  }

  @Override public void requestTransferAmount() {
    System.out.println("Request transfer");
  }

  @Override public void execute() {
    requestTransferAmount();
  }
}
```
{% endtab %}
{% endtabs %}

입금 거래 모듈을 개발하는 입장에서, 송금이 되지 않는다는 걸 작성하는 일은 **필요하지 않고 옳지 못하다.**

### 해결

각 모듈에 맞게 **인터페이스를 분리**하여 해결할 수 있다.

![](https://flylib.com/books/4/444/1/html/2/images/12_6.jpg)

`UI` 인터페이스를 모듈별로 분리하였다. 이제 각 모듈은 사용하는 기능들만 구현하면 된다.

먼저, `UI` 인터페이스를 각 모듈을 위해 쪼개자.

```java
// integrated interface for general
public interface UI extends DepositUI, WithdrawalUI, TransferUI {}

// segregated interface
public interface DepositUI {
    void requestDepositAmount();
}

public interface TransferUI {
    void requestTransferAmount();
}

public interface WithdrawalUI {
    void requestWithdrawalAmount();
}
```

그리고 각 모듈에서 필요한 인터페이스만 가져다가 구현한다.

{% tabs %}
{% tab title="DepositTransaction " %}
```java
public class DepositTransaction extends Transaction {
  private DepositUI depositUI;

  public DepositTransaction(DepositUI ui) {
    depositUI = ui;
  }

  @Override
  public void execute() {
    depositUI.requestDepositAmount();
  }
}
```
{% endtab %}

{% tab title="WithdrawalTransaction" %}
```java
public class WithdrawalTransaction extends Transaction {
  private WithdrawalUI withdrawalUI;

  public WithdrawalTransaction(WithdrawalUI ui) {
    withdrawalUI = ui;
  }

  @Override
  public void execute() {
    withdrawalUI.requestWithdrawalAmount();
  }
}
```
{% endtab %}

{% tab title="TransferTransaction" %}
```java
public class TransferTransaction extends Transaction {
  private TransferUI transferUI;

  public TransferTransaction(TransferUI ui) {
    transferUI = ui;
  }

  @Override
  public void execute() {
    transferUI.requestTransferAmount();
  }
}
```
{% endtab %}
{% endtabs %}

이제 필요한 기능만 구현하면 되는 인터페이스가 준비되었다. 그럼 아래와 같은 코딩이 가능하다.

```java
public void testFunction() {
  Transaction custom = new TransferTransaction(new TransferUI() {
    @Override
    public void requestTransferAmount() {
      System.out.println("Request transfer for me");
    }
  });
  custom.execute();
}
```

### 주의 사항

개발을 하다보면 클라이언트가 사용하지 않는 메소드를 추가하여, 하나의 인터페이스가 거대해지는 일이 잦다.  
쪼개려다보면 애매한 상황도 많고 파일이 많아지는게 또 피곤한 일이라, 굉장히 위배하기 쉬운 원칙이다.

### 전체 구조 및 코드

[https://repl.it/@joonasyoon/SOLID-ISP-ATM](https://repl.it/@joonasyoon/SOLID-ISP-ATM)

위 링크에서 실행 결과와 함께 더 자세히 확인해 볼 수 있다

## 5. Dependency Inversion Principle \(DIP; 의존관계 역전 원칙\)

### 무엇인가?

> 프로그래머는 추상화에 의존해야지, 구체화에 의존하면 안된다.

더 자세히는 이렇게 말한다.

> 상위 계층\(정책 결정\)이 하위 계층\(세부 사항\)에 의존하는 전통적인 의존관계를 반전\(역전\)시킴으로써 상위 계층이 하위 계층의 구현으로부터 독립되게 할 수 있다.
>
> 1. 상위 모듈은 하위 모듈에 의존해서는 안된다. 상위 모듈과 하위 모듈 모두 추상화에 의존해야 한다.
> 2. 추상화는 세부 사항에 의존해서는 안된다. 세부사항이 추상화에 의존해야 한다.

의존 관계가 많을수록 코드의 변경이 잦아지는 것은 당연하다. 변경할 곳이 많다는 의미는 코드를 파악하고 수정하는 일이 무척 어렵다는 뜻이다. 의존 관계에 신경을 쓰는 이유는 이런 부분이다.

추상클래스나 인터페이스는 구현 클래스보다 덜 변화되기 때문에 추상화에 신경써서 의존도를 낮추라는 뜻이다.

### 위반 사례

여기 버튼\(Button\)과 전등\(Lamp\)이 있다.

**Button은 외부 환경을 감지하는 객체**이다. 버튼을 눌렀는지 여부만 감지할 뿐이다.

**Lamp는 외부 환경을 변화시키는 객체**이다. `turnOn` 메소드가 호출되면 전등을 켜서 불을 밝힌다.

Button을 눌러서 Lamp를 동작하게 하려면 어떤 구조여야 할까.

![](../.gitbook/assets/image%20%282%29.png)

Lamp에 Button을 추가하면 해결될까?

```java
class Lamp {
  private Button button;
  public void turnOn();
  public void turnOff();
}
```

동작은 하겠지만 이 방법은 의존 관계 역전 규칙\(DIP\)을 위반한다. 구체적인 클래스인 `Lamp`에서 `Button`을 포함하기 때문이다.

지금의 `Button`은 `Lamp`의 구현에 완전히 묶여있다. `Button` 이 필요한 모든 클래스\(`Computer`, `TV` 등..\)에서 전부 선언할 수는 없는 노릇이다.

### 해결

![](../.gitbook/assets/image%20%283%29.png)

이제 `Button`와 `Lamp` 는 **추상화 된 모듈을 의존**한다. `Lamp`에서 코드가 바뀌더라도 서로 영향을 받지 않는다.

`Button`은 `Switchable` 인터페이스에 의존하고, `Lamp`가 `Switchable`의 인터페이스를 구현하게 되면 **`Lamp`가 `Button`에 의존하는 역전 관계가 되는 것**이다.

이렇게 되면, `Switchable`의 인터페이스를 조작하는 방법을 아는 객체는 `Lamp`를 켜고 끌 수 있다.

```java
interface Switchable {
  void activate();
  void deactivate();
}

class Lamp implements Switchable {
  @Override public void activate() {
    System.out.println("Lamp: turn on");
  }
  
  @Override public void deactivate() {
    System.out.println("Lamp: turn off");
  }
}
```

`Lamp`는 이제 환경에 상관없이 자신의 동작인 `activate`와 `deactivate` 구현에 집중할 수 있다.

![forked from C++ Report by Robert C. Martin](../.gitbook/assets/image%20%284%29.png)

[Robert C. Martin의 레포트](https://web.archive.org/web/20110714224327/http://www.objectmentor.com/resources/articles/dip.pdf)에서는 `Button`도 추상화하였다.  
  
구현 클래스와 추상 클래스를 완전히 분리시킨 것이다.

{% hint style="info" %}
이 부분에는 어댑터 패턴\(Adapter Pattern\)이 사용되었다.
{% endhint %}

```java
abstract class Button {
  private Switchable client;

  public Button(Switchable client) {
    this.client = client;
  }

  // detect its state and activate/deactivate  
  public void run() {
    boolean isButtonOn = getState();
    if (isButtonOn) {
      client.activate();
    } else {
      client.deactivate();
    }
  }

  abstract boolean getState();
}
```

`Button`도 **구현체에 상관없이 외부 환경을 감지**하여, 참조하는 객체에게 어떤 동작을 일으킬 수 있게 되었다.

```java
class ToggleButton extends Button {
  private boolean isActive = false;
  
  public ToggleButton(Switchable client) {
    super(client);
  }

  // Unique method only for ToggleButton
  public void toggle() {
    this.isActive = !this.isActive;
  }

  @Override public boolean getState() {
    return this.isActive;
  }
}
```

`Button`의 구현 중 하나인 `ToggleButton`은 `toggle()` 을 호출하면 버튼의 ON/OFF 상태가 반전된다.

태 변화만 다루고 있으며, `Lamp` 따위의 동작에는 어떤 개입도 하지 않는 것을 확인할 수 있다.

```java
class Main {
  public static void main(String[] args) {
    ToggleButton lampButton = new ToggleButton(new Lamp());
    lampButton.run();
    lampButton.toggle();
    lampButton.run();
  }
}
```

`ToggleButton`에 `Lamp`를 위처럼 연결하였다. `Button.run()` 을 호출하면 `Button` 의 상태에 따라 `Lamp` 를 동작시키고 있다.

같은 방식으로 객체 `Computer` 도 추가할 수 있다.

```java
class Computer implements Switchable {
  @Override public void activate() {
    System.out.println("Computer: turn on");
  }

  @Override public void deactivate() {
    System.out.println("Computer: turn off");
  }
}

class Main {
  public static void main(String[] args) {
    ToggleButton computerButton = new ToggleButton(new Computer());
    computerButton.run();
    computerButton.toggle();
    computerButton.run();
  }
}
```

### 전체 코드

{% embed url="https://repl.it/@joonasyoon/SOLID-DIP" %}

## Links

* [https://ko.wikipedia.org/wiki/SOLID\_\(객체\_지향\_설계\)](https://ko.wikipedia.org/wiki/SOLID_%28%EA%B0%9D%EC%B2%B4_%EC%A7%80%ED%96%A5_%EC%84%A4%EA%B3%84%29)
* [https://dev-momo.tistory.com/entry/SOLID-원칙](https://dev-momo.tistory.com/entry/SOLID-%EC%9B%90%EC%B9%99)
* [https://dzone.com/articles/the-liskov-substitution-principle-with-examples](https://dzone.com/articles/the-liskov-substitution-principle-with-examples)
* [http://stg-tud.github.io/sedc/Lecture/ws13-14/3.3-LSP.html](http://stg-tud.github.io/sedc/Lecture/ws13-14/3.3-LSP.html)
* [http://stg-tud.github.io/sedc/Lecture/ws16-17/3.2-ISP.pdf](http://stg-tud.github.io/sedc/Lecture/ws16-17/3.2-ISP.pdf)
* [https://reflectoring.io/interface-segregation-principle/](https://reflectoring.io/interface-segregation-principle/)
* [The Interface Segregation Principle, Robert C. Martin, C++ Report, June 1996](https://docs.google.com/a/cleancoder.com/viewer?a=v&pid=explorer&chrome=true&srcid=0BwhCYaYDn8EgOTViYjJhYzMtMzYxMC00MzFjLWJjMzYtOGJiMDc5N2JkYmJi&hl=en)
* [https://flylib.com/books/en/4.444.1.79/1/](https://flylib.com/books/en/4.444.1.79/1/)
* [https://walbatrossw.github.io/oop/2018/07/27/06-solid-dip.html](https://walbatrossw.github.io/oop/2018/07/27/06-solid-dip.html)
* [The Dependency Inversion Principle, Robert C. Martin, C++ Report, May 1996](https://web.archive.org/web/20110714224327/http://www.objectmentor.com/resources/articles/dip.pdf)
* [https://blog.naver.com/jwyoon25/221776517812](https://blog.naver.com/jwyoon25/221776517812)
* [http://stg-tud.github.io/sedc/Lecture/ws13-14/3.5-DIP.html](http://stg-tud.github.io/sedc/Lecture/ws13-14/3.5-DIP.html)

