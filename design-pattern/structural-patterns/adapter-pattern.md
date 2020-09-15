---
description: 어댑터 패턴(Adapter Pattern)
---

# Adapter Pattern

## 어댑터 패턴\(Adapter Pattern\)

> **어댑터 패턴**\(Adapter pattern\)은 클래스의 인터페이스를 사용자가 기대하는 다른 인터페이스로 변환하는 패턴으로, 호환성이 없는 인터페이스 때문에 함께 동작할 수 없는 클래스들이 함께 작동하도록 해준다.

USB 어댑터를 떠올려보자.

어댑터만 있다면, USB A타입에 어댑터를 연결해서 USB-C 타입에 연결하여 정상적으로 사용할 수 있다.

이렇듯 호환되지 않던 인터페이스를 서로 동작할 수 있도록 해주는 패턴이다.

클래스를 감싸서 변환하는 모양 때문에 Wrapper 라고도 불리지만, 이건 다른 패턴과 혼동의 여지가 있다.

### 개요

어댑터 패턴이 해결할 수 있는 문제는 다음과 같다.

* 클라이언트에게는 필요하지만, 그런 인터페이스는 없는 클래스를 어떻게 재사용하지?
* 서로 호환되지 않는 인터페이스를 가진 클래스끼리 어떻게 동작시키지?
* 어떤 클래스를 위한 대체 인터페이스\(alternative interface\)를 어떻게 제공하지?

보통 재사용이 힘든 원인은, 기존의 클래스와 클라이언트 둘의 인터페이스가 다르기 때문이다.

이렇게 해결할 수 있다.

* `Adapter` 라는 별도의 클래스를 두어서, `Adaptee` 클래스의 인터페이스를 `Target` 클라이언트의 인터페이스로 변환하는 작업을 거친다.
* 필요한 인터페이스가 없는 클래스들은 `Adapter`를 통해서 동작한다.

아래 UML을 보면 이해가 빠르다.

### 구조

두 가지 구조로 설명하는데, 상속\(inheritance\) 관계를 사용했는 지 포함\(composition\) 관계를 사용했는 지가 가장 핵심적인 차이이다.

#### Object adapter pattern 

먼저, 객체를 감싼다면 어댑터 패턴은 아래와 같다.

![Object adapter pattern](https://www.cs.unc.edu/~stotts/GOF/hires/Pictures/adapt104.gif)

`Adapter` 클래스가 멤버 변수로 `Adaptee`를 가지고 있어서, 인터페이스에 맞게 동작시켜 호환하는 방법이다. \(포함 관계\)

즉, 내부적으로 `adaptee.specRequest()` 와 같이 적절하게 호출시키는 것이다. `Client`의 입장에서 `Adaptee` 객체는 내부를 볼 수 없는 블랙박스\(blackbox\)가 된다.

#### Class adapter pattern

클래스 어댑터 패턴은 상속\(inferitance\) 관계로 클래스를 감싼다.

![Class adapter pattern](https://www.cs.unc.edu/~stotts/GOF/hires/Pictures/adapt106.gif)

`Adapter`가 `Target`과  `Adaptee` 클래스를 모두 상속해서 호환되도록 구현하여 동작시키는 것이다.

그런데 이 방식은 다중 상속을 지원해야해서, JAVA\(JDK 1.8 이전\) 등에서는 불가능하다.

### 예시

{% hint style="warning" %}
언어 스펙 상 객체 어댑터 패턴으로 구현하였다.
{% endhint %}

라이트닝 케이블을 사용하는 아이폰과, 마이크로 USB를 사용하는 안드로이드폰이 있다.

```java
interface LightningPhone {
    void recharge();
    void useLightning();
}

interface MicroUsbPhone {
    void recharge();
    void useMicroUsb();
}

class Iphone implements LightningPhone {}
class Android implements MicroUsbPhone {}
```

두 스마트폰 모두 충전 기능을 가지고 있지만, 아이폰과 마이크로 USB 케이블밖에 없는 상황이라고 하자.

그래서 충전기\(클라이언트\)에 꽂는 케이블\(인터페이스\)이 달라 연결할 수 없는 상태이다.

* 아이폰 → 라이트닝 케이블 → 충전기
* 안드로이드폰 → 마이크로 USB → 충전기

{% tabs %}
{% tab title="Iphone" %}
```java
class Iphone implements LightningPhone {
    private boolean connector;

    @Override
    public void useLightning() {
        connector = true;
        System.out.println("Lightning connected");
    }

    @Override
    public void recharge() {
        if (connector) {
            System.out.println("Recharge started");
            System.out.println("Recharge finished");
        } else {
            System.out.println("Connect Lightning first");
        }
    }
}
```
{% endtab %}

{% tab title="Android" %}
```java
class Android implements MicroUsbPhone {
    private boolean connector;

    @Override
    public void useMicroUsb() {
        connector = true;
        System.out.println("MicroUsb connected");
    }

    @Override
    public void recharge() {
        if (connector) {
            System.out.println("Recharge started");
            System.out.println("Recharge finished");
        } else {
            System.out.println("Connect MicroUsb first");
        }
    }
}
```
{% endtab %}
{% endtabs %}

라이트닝 케이블로 마이크로 USB가 호환되도록 하는 어댑터은 다음과 같이 생겼다.

```java
class LightningToMicroUsbAdapter implements MicroUsbPhone {
    private final LightningPhone lightningPhone;

    public LightningToMicroUsbAdapter(LightningPhone lightningPhone) {
        this.lightningPhone = lightningPhone;
    }

    @Override
    public void useMicroUsb() {
        System.out.println("MicroUsb connected"); // MicroUsb
        /* ... any conversion for electric power ... */
        lightningPhone.useLightning();            // lightningPhone
    }

    @Override
    public void recharge() {
        lightningPhone.recharge();
    }
}
```

마이크로 USB를 사용하면, 적절한 처리와 함께 라이트닝 케이블이 동작하도록 것이다.

이제 아래처럼 사용할 수 있다.

* 아이폰 → 라이트닝 케이블 → \(어댑터\) → 마이크로 USB → 충전기

```java
public class AdapterDemo {
    static void rechargeMicroUsbPhone(MicroUsbPhone phone) {
        phone.useMicroUsb();
        phone.recharge();
    }

    static void rechargeLightningPhone(LightningPhone phone) {
        phone.useLightning();
        phone.recharge();
    }

    public static void main(String[] args) {
        Android android = new Android();
        Iphone iPhone = new Iphone();

        System.out.println("Recharging android with MicroUsb");
        rechargeMicroUsbPhone(android);

        System.out.println("Recharging iPhone with Lightning");
        rechargeLightningPhone(iPhone);

        System.out.println("Recharging iPhone with MicroUsb");
        rechargeMicroUsbPhone(new LightningToMicroUsbAdapter (iPhone));
    }
}
```

### 전체 코드 및 실행

[https://repl.it/@joonasyoon/DesignPattern-Adapter\#Main.java](https://repl.it/@joonasyoon/DesignPattern-Adapter#Main.java)

## Links

* [https://en.wikipedia.org/wiki/Adapter\_pattern](https://en.wikipedia.org/wiki/Adapter_pattern)
* [https://www.cs.unc.edu/~stotts/GOF/hires/pat4afso.htm](https://www.cs.unc.edu/~stotts/GOF/hires/pat4afso.htm)
* [https://yaboong.github.io/design-pattern/2018/10/15/adapter-pattern/](https://yaboong.github.io/design-pattern/2018/10/15/adapter-pattern/)
* [https://stackoverflow.com/questions/9978477/difference-between-object-adapter-pattern-and-class-adapter-pattern](https://stackoverflow.com/questions/9978477/difference-between-object-adapter-pattern-and-class-adapter-pattern)

