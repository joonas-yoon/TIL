---
description: Dependency Inversion Principle (DIP; 의존관계 역전 원칙)
---

# 5. DIP

## Dependency Inversion Principle (DIP; 의존관계 역전 원칙)

> 프로그래머는 추상화에 의존해야지, 구체화에 의존하면 안된다.

더 자세히는 이렇게 말한다.

> 상위 계층(정책 결정)이 하위 계층(세부 사항)에 의존하는 전통적인 의존관계를 반전(역전)시킴으로써 상위 계층이 하위 계층의 구현으로부터 독립되게 할 수 있다.
>
> 1. 상위 모듈은 하위 모듈에 의존해서는 안된다. 상위 모듈과 하위 모듈 모두 추상화에 의존해야 한다.
> 2. 추상화는 세부 사항에 의존해서는 안된다. 세부사항이 추상화에 의존해야 한다.

의존 관계가 많을수록 코드의 변경이 잦아지는 것은 당연하다. 변경할 곳이 많다는 의미는 코드를 파악하고 수정하는 일이 무척 어렵다는 뜻이다. 의존 관계에 신경을 쓰는 이유는 이런 부분이다.

추상클래스나 인터페이스는 구현 클래스보다 덜 변화되기 때문에 추상화에 신경써서 의존도를 낮추라는 뜻이다.

### 위반 사례

여기 버튼(Button)과 전등(Lamp)이 있다.

**Button은 외부 환경을 감지하는 객체**이다. 버튼을 눌렀는지 여부만 감지할 뿐이다.

**Lamp는 외부 환경을 변화시키는 객체**이다. `turnOn` 메소드가 호출되면 전등을 켜서 불을 밝힌다.

Button을 눌러서 Lamp를 동작하게 하려면 어떤 구조여야 할까.

![](<../../.gitbook/assets/image (2).png>)

Lamp에 Button을 추가하면 해결될까?

```java
class Lamp {
  private Button button;
  public void turnOn();
  public void turnOff();
}
```

동작은 하겠지만 이 방법은 의존 관계 역전 규칙(DIP)을 위반한다. 구체적인 클래스인 `Lamp`에서 `Button`을 포함하기 때문이다.

지금의 `Button`은 `Lamp`의 구현에 완전히 묶여있다. `Button` 이 필요한 모든 클래스(`Computer`, `TV` 등..)에서 전부 선언할 수는 없는 노릇이다.

### 해결

![](<../../.gitbook/assets/image (3).png>)

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

![forked from C++ Report by Robert C. Martin](<../../.gitbook/assets/image (4).png>)

[Robert C. Martin의 레포트](https://web.archive.org/web/20110714224327/http://www.objectmentor.com/resources/articles/dip.pdf)에서는 `Button`도 추상화하였다.\
\
구현 클래스와 추상 클래스를 완전히 분리시킨 것이다.

{% hint style="info" %}
이 부분에는 어댑터 패턴(Adapter Pattern)이 사용되었다.
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

상태 변화만 다루고 있으며, `Lamp` 따위의 동작에는 어떤 개입도 하지 않는 것을 확인할 수 있다.

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

* [https://ko.wikipedia.org/wiki/SOLID\_(객체\_지향\_설계)](https://ko.wikipedia.org/wiki/SOLID\_\(%EA%B0%9D%EC%B2%B4\_%EC%A7%80%ED%96%A5\_%EC%84%A4%EA%B3%84\))
* [https://walbatrossw.github.io/oop/2018/07/27/06-solid-dip.html](https://walbatrossw.github.io/oop/2018/07/27/06-solid-dip.html)
* [The Dependency Inversion Principle, Robert C. Martin, C++ Report, May 1996](https://web.archive.org/web/20110714224327/http://www.objectmentor.com/resources/articles/dip.pdf)
* [https://blog.naver.com/jwyoon25/221776517812](https://blog.naver.com/jwyoon25/221776517812)
* [http://stg-tud.github.io/sedc/Lecture/ws13-14/3.5-DIP.html](http://stg-tud.github.io/sedc/Lecture/ws13-14/3.5-DIP.html)
