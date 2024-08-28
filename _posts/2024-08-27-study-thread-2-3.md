---
title: 3 스레드 제어와 생명 주기 - 1
date: 2024-08-27 23:11:00 +0900
last_modified_at: 2024-08-28 23:33:00 +0900
categories: [Study, Java-advanced1]
tags: [study, java, thread, adv1]
pin: true
---

## 스터디 git 주소
<hr />

- <https://github.com/chj-dev/java-adv1>
- src/chjTest/join 하위 참조

## 스레드 제어와 생명 주기 - 1
<hr />

### 스레드 객체 정보

- threadId(): 스레드 고유 식별자 반환
- getName(): 스레드 이름 반환
- getPriority(): 스레드 우선순위 반환
- getThreadGroup(): 스레드 그룹 반환
- getState(): 스레드 상태 반환

### 스레드 상태와 생명 주기

1. 스레드 상태
: **NEW**: 스레드가 아직 시작되지 않은 상태
: **RUNNABLE**: 스레드가 실행 중이거나 실행될 준비가 된 상태
: **BLOCKED**: 스레드가 동기화 락을 기다리는 상태
: **WAITING**: 스레드가 다른 스레드의 특정 작업이 완료되기를 기다리는 상태 (특정 작업이 완료되지 않으면 무기한 기다림)
: **TIME_WAITING**: 스레드가 일정 시간 동안 기다리는 상태
: **TERMINATED**: 스레드가 오류가 났거나, 정상 완료 되었거나 하여 실행을 마친 상태

## 체크 예외 재정의
<hr />

- Runnable 인터페이스의 run() 메서드를 구현할 때 InterruptedException 체크 예외를 밖으로 던질 수 없다.

### 왜 체크 예외를 밖으로 던질 수 없을까?

- 자바에서 메서드를 오버라이딩할 때, 지켜야 할 규칙이 있다.

1. 체크 예외
: 부모 메서드가 체크 예외를 던지지 않는 경우, 자식 메서드도 던질 수 없다.
: 자식 메서드는 부모 메서드가 던질 수 있는 체크 예외의 하위 타입만 던질 수 있다.

2. 언체크(런타임) 예외
: 예외 처리를 강제 하지 않아서 상관없이 던질 수 있다.

> 위의 설명을 토대로 생각하면 알 수 있듯이, Runnable 인터페이스의 run() 메서드는 아무런 체크 예외를 던지지 않는다.
{: .prompt-info }

### 왜 이런 제약이..??

- 자식 메서드가 더 넓은 범위의 예외를 던지면 예외가 제대로 처리되지 못할 수 있다. 즉 예외 처리의 일관성을 해치고 예상치 못한 런타임 오류가 초래될 수 있다.

> 자바 초창기에는 위와 같은 제약을 두어 프로그램이 비정상으로 종료되는 상황을 방지할 수 있었다. 
> 특히 멀티스레딩 환경에서 스레드의 안전성과 일관성을 유지할 수 있었다.
> 하지만 최근엔 언체크 예외를 더 선호한다.
{: .prompt-warning }

## sleep 과 join

### 기다려, 하지만 누가? 어떻게? 얼마나?

- `sleep` 과 `join` 둘 다 사용 시 스레드가 **기다리는** 상태를 가진다. 하지만 이때 **누가 어떻게** 기다리는지에 따라 사용하는 메서드가 다르다.

### sleep?

```java
Thread.sleep(1000);
```
- 위와 같이 사용하여, **`sleep` 을 호출한 스레드**가 1초 만큼 기다리는 상태가 된다.
- 기다리는 상태이지만, 일정 시간 동안만 기다리는 **TIME_WAITING** 상태이다.

### join?

```java
public static void main(String[] args)  {
  Thread t1 = new Thread(new MyTask(), "t1");
  t1.start();
  t1.join();
  System.out.println("모든 스레드 실행 완료");
}
```

- [2 스레드 생성과 실행](https://chj-dev.github.io/posts/study-thread-2-2/#thread-%EA%B8%B0%EB%B3%B8-%EC%A7%80%EC%8B%9D) 에서 얘기했듯, 메인스레드는 기본적으로 호출한 스레드의 종료를 기다리지 않는다.
- 하지만 위의 예시처럼 `join` 을 사용하면 **`join`을 호출한 스레드가 t1스레드가 종료(TERMINATED)될 때까지** 기다리는 상태가 된다.
- 무기한 기다리는 **WAITING** 상태이다.

### 일정 시간 동안만 join 할래

```java
public static void main(String[] args)  {
  Thread t1 = new Thread(new MyTask(), "t1");
  t1.start();
  t1.join(1000);
  System.out.println("모든 스레드 실행 완료");
}
```

- 위와 같이 사용하면 1초 동안만 t1스레드가 종료되기를 기다리고 **해당 시간동안 종료되지 않으면 깨어나(RUNNABLE)** 본인의 다음 코드를 수행한다.
- 일정 시간 동안만 기다리는 **TIME_WAITING** 상태이다.
