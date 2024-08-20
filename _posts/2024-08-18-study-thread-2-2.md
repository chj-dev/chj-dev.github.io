---
title: 2 스레드 생성과 실행
date: 2024-08-20 23:37:00 +0900
categories: [Study, Java-advanced1]
tags: [study, java, thread, adv1]
pin: true
---

> 작성중..

## 스터디 git 주소
<hr />

- https://github.com/chj-dev/java-adv1
- src/chjTest/start 하위 참조

## 스레드 생성과 실행
<hr />

### 자바의 메모리 구조는?

> 이제는 알아야 한다...

1. 메서드 영역
: 프로그램을 실행하는데 필요한 공통 데이터를 관리
: 클래스, static, 리터럴 상수

2. 스택 영역
: 지역 변수, 중간 연산 결 과, 메서드 호출 정보
: 스레드 별로 스택을 가짐

3. 힙 영역
: 객체와 배열이 생성되는 영역
: GC가 이루어짐

## 스레드 생성 방법
<hr />

### Thread 상속

```java
private static class CounterThread extends Thread {
    @Override
    public void run() {
      //...
    }
}
```
- Thread 클래스 상속
- run() 메서드 오버라이딩 (스레드가 실행할 코드를 기술한다.)

```java
public static void main(String[] args) {
    CounterThread ct = new CounterThread();
    ct.start();
}
```
- main 에서 run() 을 바로 호출하는 게 아닌, start() 를 호출한다.

> run() 을 바로 호출하면, CounterThread 스레드가 코드를 실행하는 것이 아닌, 
> main 스레드가 CounterThread 클래스의 메서드를 호출하는 것이 되어버린다. 
> 하지만 start() 를 호출하면 CounterThread 스레드가 run() 메서드 내의 코드를 실행한다.
{: .prompt-warning }

### Thread

- 스레드에 이름을 부여하지 않으면 기본 스레드의 이름은 Thread-0, Thread-1, ... 이다.
- main 클래스의 스레드는 main 이란 이름을 가지고 있다.
- 스레드는 실행 순서를 보장하지 않는다.

> main 클래스에서 호출한 스레드가 main 클래스 종료 전에 실행될 수도 있고, main 클래스 종료 후에 실행될 수도 있다.
{: .prompt-info }

- main 스레드는 기본적으로 호출한 스레드의 종료를 기다리지 않는다.

> 기다리고 싶을 경우 join() 을 사용해야 한다!
{: .prompt-info }

## 데몬 스레드 vs 유저 스레드

1. 유저 스레드 (User Thread)
: 프로그램의 주요 작업을 수행
: 작업이 완료될 때까지 실행
: 모든 user 스레드가 종료되면 JVM도 종료

2. 데몬 스레드 (Daemon Thread)
: 백그라운드에서 보조적인 작업을 수행
: 모든 user 스레드가 종료되면 데몬 스레드는 자동으로 종료됨

### 데몬???

- 그리스 로마 신화에서 데몬은 신과 인간 사이의 중간적 존재로, 보이지 않게 활동하며 일상적인 일들을 도왔다고 한다.
- 따라서 백그라운드에서 보이지 않게 작업을 수행하는 스레드를 데몬 스레드라 한다.






## 참고자료
<hr />

[김영한의 실전 자바 - 고급 1편](https://www.inflearn.com/course/%EA%B9%80%EC%98%81%ED%95%9C%EC%9D%98-%EC%8B%A4%EC%A0%84-%EC%9E%90%EB%B0%94-%EA%B3%A0%EA%B8%89-1/dashboard)
