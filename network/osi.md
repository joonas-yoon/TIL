---
description: 7 layers of the OSI Model
---

# OSI 7 계층 모델

이 모델은 **프로토콜을 기능별로 나눈 것**이다. 각 레이어(Layer)는 하위 레이어의 기능을 사용할 수 있다.

레이어를 나눈 이유는 **통신의 과정을 단계별로 확인할 수 있기 때문**이다.

인터넷 연결이 안될 때 단계별로 어디가 문제인지 파악하는 모습을 떠올리면 된다.

![](https://www.coengoedegebure.com/content/images/2018/09/osimodel-3.gif)

상위 4개 레이어는 _Host Layer_ 라고 불리고, 하위 3개 레이어는 _Media Layer _라고 불린다.

## 7. **Application**

> High-level API's, including resource sharing, remote file access

컴퓨터에서 실행되는 소프트웨어와 네트워크 프로토콜 사이의 인터페이스를 제공한다.

원격 파일 접근, 원격 프린트, 가상 터미널 등 사용자와 가장 닿아있는 영역이다. 응용 서비스나 프로세스가 이 레이어에서 동작한다.

이 레이어에서 사용하는 프로토콜은 [HTTP](https://en.wikipedia.org/wiki/Hypertext\_Transfer\_Protocol), [FTP](https://en.wikipedia.org/wiki/File\_Transfer\_Protocol), [SMTP](https://en.wikipedia.org/wiki/Simple\_Mail\_Transfer\_Protocol), [DNS](https://en.wikipedia.org/wiki/Domain\_Name\_System), [Telnet](https://en.wikipedia.org/wiki/Telnet), [SSH](https://en.wikipedia.org/wiki/Secure\_Shell), [IMAP](https://en.wikipedia.org/wiki/Internet\_Message\_Access\_Protocol), [POP](https://en.wikipedia.org/wiki/Post\_Office\_Protocol), [SNMP](https://en.wikipedia.org/wiki/Simple\_Network\_Management\_Protocol) 등이 있다.

## 6. **Presentation**

Translation of data between a networking service and an application. This is the layer where character encoding, data compression and encryption takes place.

## 5. **Session**

Managing communication sessions.

## 4. **Transport**

Reliable transmission of data segments between nodes on a network, including segmentation, acknowledgment and multiplexing.

## 3. **Network**

Structuring and managing a multi-node network, including addressing, routing and traffic control.

## 2. **Data Link**

Reliable transmission of data frames between two nodes connected by the physical layer.

## 1. **Physical**

Transmission and reception of raw bit streams over a physical medium.



## Links

* [https://en.wikipedia.org/wiki/OSI\_model](https://en.wikipedia.org/wiki/OSI\_model)
* [https://ko.wikipedia.org/wiki/OSI\_모형](https://ko.wikipedia.org/wiki/OSI\_%EB%AA%A8%ED%98%95)
* [https://www.coengoedegebure.com/osi-model/](https://www.coengoedegebure.com/osi-model/)
* [https://mycodecamp.blogspot.com/2019/10/7-layer-of-osi-model.html](https://mycodecamp.blogspot.com/2019/10/7-layer-of-osi-model.html)
* 응용 서비스나 프로세스가 바로 응용계층에서 동작
