# Decorator Pattern

데코레이터 패턴이란,

> &#x20;주어진 상황 및 용도에 따라 어떤 객체에 책임을 덧붙이는 패턴으로,\
> 기능 확장이 필요할 때 서브클래싱 대신 쓸 수 있는 유연한 대안이 될 수 있다.
>
> \- 위키백과

## 앞서보기

이름대로 주변에 기능을 덧붙여서 사용할 수 있도록 설계된 패턴이다.

실제로 사용되는 사례로는 파이썬의 decorator가 있다.

함수를 사용하면 경고 로그를 남기는 `warning` 이라는 데코레이터가 있다고 치자. 그럼 파이썬에서는 아래처럼 코드를 작성할 수 있다.

```python
@warning
def myFunction():
    print('my function')
```

이제 myFunction() 함수는 데코레이터와 함께 동작한다.  (물론 파이썬의 문법에 의해 가능한 것이다. 모양새만 알고 가자.)

데코레이터가 함수 실행 전과 후에 로그를 남기도록 만들어졌다면, 결과는 아래와 같을 수 있다.

```python
warning: function 'myFunction()' called
my function
warning: function 'myFunction()' finished
```

이런 방식은 어떤 기능(메소드)을 추가하기 위해서 클래스를 상속받을 필요도 없고, 새로 생성할 필요도 없다.







