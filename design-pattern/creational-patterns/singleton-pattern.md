---
description: 싱글턴 패턴(Singleton pattern)
---

# Singleton Pattern

## 싱글턴 패턴\(Singleton pattern\)

싱글턴 패턴은 인스턴스를 하나만 생성하여 사용하는 패턴이다.

개인적으로 가장 쉬우면서 흔하게 볼 수 있는 패턴이다.



### 정의 및 예시

어떤 게임의 `설정` 클래스가 있다고 하자.

```java
public Config {
    private int volume = 10;
    private int theme = Config.DARK;
}
```

이 설정을 다른 클래스\(캐릭터, 키보드 관리자 등\)에서도 사용하고 싶다.

```java
public Character {
    private Config config;
}

public Keyboard {
    private Config config;
}
```

두 클래스 `Character`와 `Keyboard`는 같은 설정을 가지고 있을까? 아니다.

이 문제는, 아래처럼 해결할 수 있다.

```java
public Config {
    // static으로 메모리에 할당한다.
    private static Config instance;
    
    // 생성자를 숨겨서, 외부에서는 직접 생성할 수 없도록 한다.
    private Config() {
        // 실제 객체는 한번만 생성된다.
        instance = new Config();
    }
    
    // 외부에서는 가져다 쓰는 것만 가능하다.
    public getInstance() {
        return instance;
    }
}

// 이제 두 클래스는 같은 객체를 참조한다.
public Character {
    private Config config = Config.getInstance();
}

public Keyboard {
    private Config config = Config.getInstance();
}
```

`Config` 클래스의 생성자를 직접 호출하지 못하도록 하여, 인스턴스가 한번만 생성되도록 한다. 대신 `getInstace` 등의 함수로 인스턴스를 가져다 쓸 수만 있도록 제한한다.



### 문제점

메모리에 계속 떠있다보니, 싱글톤으로 만든 인스턴스가 무거우면 문제가 된다.

## Links

* [https://gmlwjd9405.github.io/2018/07/06/singleton-pattern.html](https://gmlwjd9405.github.io/2018/07/06/singleton-pattern.html)
* [https://jeong-pro.tistory.com/86](https://jeong-pro.tistory.com/86)

