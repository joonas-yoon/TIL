---
description: Interface Segregation Principle (ISP; 인터페이스 분리 원칙)
---

# 4. ISP

## Interface Segregation Principle (ISP; 인터페이스 분리 원칙)

> 특정 클라이언트를 위한 인터페이스 여러 개가 범용 인터페이스 하나보다 낫다.

### 위반 사례

이 ATM기는 총 3개의 모듈을 가지고 있는데, 어떤 거래(transaction)에 대해서 입금(Deposit), 출금(Withdrawal), 송금(Transfer) 모듈을 각자 만들었다.

![](https://flylib.com/books/4/444/1/html/2/images/12\_5.jpg)

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

![](https://flylib.com/books/4/444/1/html/2/images/12\_6.jpg)

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

개발을 하다보면 클라이언트가 사용하지 않는 메소드를 추가하여, 하나의 인터페이스가 거대해지는 일이 잦다.\
쪼개려다보면 애매한 상황도 많고 파일이 많아지는게 또 피곤한 일이라, 굉장히 위배하기 쉬운 원칙이다.

### 전체 구조 및 코드

[https://repl.it/@joonasyoon/SOLID-ISP-ATM](https://repl.it/@joonasyoon/SOLID-ISP-ATM)

위 링크에서 실행 결과와 함께 더 자세히 확인해 볼 수 있다

## Links

* [https://ko.wikipedia.org/wiki/SOLID\_(객체\_지향\_설계)](https://ko.wikipedia.org/wiki/SOLID\_\(%EA%B0%9D%EC%B2%B4\_%EC%A7%80%ED%96%A5\_%EC%84%A4%EA%B3%84\))
* [http://stg-tud.github.io/sedc/Lecture/ws16-17/3.2-ISP.pdf](http://stg-tud.github.io/sedc/Lecture/ws16-17/3.2-ISP.pdf)
* [https://reflectoring.io/interface-segregation-principle/](https://reflectoring.io/interface-segregation-principle/)
* [The Interface Segregation Principle, Robert C. Martin, C++ Report, June 1996](https://docs.google.com/a/cleancoder.com/viewer?a=v\&pid=explorer\&chrome=true\&srcid=0BwhCYaYDn8EgOTViYjJhYzMtMzYxMC00MzFjLWJjMzYtOGJiMDc5N2JkYmJi\&hl=en)
* [https://flylib.com/books/en/4.444.1.79/1/](https://flylib.com/books/en/4.444.1.79/1/)
