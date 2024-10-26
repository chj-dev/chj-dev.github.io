---
title: 7 고급 동기화 concurrent.Lock
date: 2024-10-26 16:27:00 +0900
categories: [Study, Java-advanced1]
tags: [study, java, thread, adv1]
pin: true
---

## 스터디 git 주소
<hr />

- <https://github.com/chj-dev/java-adv1>


## BLOCKED vs WAITING

### 인터럽트 발생 시

- BLOCKED 상태는 인터럽트가 걸려도 대기 상태를 빠져나오지 못한다. -> BLOCKED 상태
- WAITING, TIME_WAITING 상태는 인터럽트가 걸리면 대기 상태를 빠져나온다. -> RUNNABLE 상태

### 용도

- BLOCKED 상태는 `synchronized` 에서 락을 획득하기 위해 대기할 때 사용된다.
- WAITING 상태는 `Thread.join()`, `LockSupport.park()`, `Object.wait()`
  와 같은 메서드를 호출할 때 발생한다.
- TIME_WAITING 상태는 `Thread.sleep(ms)`, `Thread.join(long millis)`,
  `LockSupport.parkNonos(ns)`, `Object.wait(long timeout)` 등과 같이
  시간 제한이 있는 대기 메서드를 호출할 때 발생한다.

> BLOCKED, WAITING, TIMED_WAITING 상태 모두 스레드가 대기하며, 실행 스케줄링에 들어가지 않는다.
{: .prompt-info }


## LockSupport

### 기능

- 스레드를 `WAITING` 상태로 변경한다.

> WAITING 상태: 누가 깨워주기 전까지 계속 대기한다. CPU 스케줄링에 들어가지 않는다.
{: .prompt-info }

- `park()`: 스레드를 WAITING 상태로 변경한다.
- `parkNanos(nanos)`: 스레드를 나노초 동안 TIMED_WAITING 상태로 변경한다.
  - 지정한 시간이 지나면 RUNNABLE 상태로 변경된다.
- `unpark(thread)`: WAITING 상태의 대상 스레드를 RUNNABLE 상태로 변경한다.
  - park() 호출을 통해 대기 상태가 된 스레드 스스로 unpark() 를 호출할 수 없기 때문에, 외부 스레드가 unpark(thread) 와 같이 호출하여 깨어나게 만든다.

### 인터럽트 발생 시

- WAITING 상태의 스레드에 인터럽트가 발생하면 RUNNABLE 상태로 변하면서 깨어난다.
- 즉, unpark 를 대신하여 사용하면 인터럽트의 상태가 true 인 상태로 RUNNABLE 상태가 된다.


## ReentrantLock

### Lock 인터페이스

- 동시성 프로그래밍에서 쓰이는 안전한 임계 영역을 위한 락을 구현하는데 사용된다.
- 대표적인 구현체로 ReentrantLock 이 있다.

> 해당 락은 객체 내부에 있는 모니터 락이 아니다. Lock 인터페이스와 ReentrantLock 이 제공하는 기능이다.
> 모니터 락과 BLOCKED 상태는 synchronized 에서만 사용된다.
{: .prompt-warning }

- `void lock()`
  - 락을 획득한다. 다른 스레드가 이미 락을 획득했다면, 락을 획득할 때까지 현재 스레드는 대기한다. 
  - 이 메서드는 인터럽트에 응답하지 않는다.
  - ex. 맛집에 줄을 서서 기다린다. 다른 연락이 와도 계속 기다린다.

> WAITING 상태의 스레드는 인터럽트에 응답한다고 했는데 해당 메서드는 응답하지 않는다고 한다. 왜일까?
> 정확히 말하면 인터럽트에 걸리긴 한다. 하지만 아주 짧게 WAITING -> RUNNABLE 이 되고, 
> 해당 메서드 안에서 스레드를 다시 WAITING 으로 강제로 변경해버린다.
{: .prompt-info }

- `void lockInterruptibly()`
  - 락을 획득한다. 다른 스레드가 이미 락을 획득했다면, 락을 획득할 때까지 현재 스레드는 대기한다.
  - 대기 중에 인터럽트가 발생하면 `InterruptedException` 이 발생하며 락 획득을 포기한다.
  - ex. 맛집에 줄을 서서 기다린다. 다른 연락이 오면 기다리지 않고 포기한다.

- `boolean tryLock()`
  - 락 획득을 시도하고, 즉시 성공 여부를 반화한다. 다른 스레드가 이미 락을 획득했다면 false, 그렇지 않으면 락을 획득하고 true 를 반환한다.
  - ex. 맛집에 줄이 없으면 바로 들어가고, 줄이 있으면 즉시 포기한다.

- `boolean tryLock(long time, TimeUnit unit)`
  - 주어진 시간 동안 락 획득을 시도한다. 시간 안에 획득 시 true 를 반환한다. 그렇지 않으면 false 를 반환한다.
  - 대기 중에 인터럽트가 발생하면 `InterruptedException` 이 발생하며 락 획득을 포기한다.
  - ex. 맛집에 줄을 서지만 특정 시간만큼만 기다린다. 시간이 지나면 포기한다. 다른 연락이 오면 기다리지 않고 포기한다.

- `void unlock()`
  - 락을 해제한다. 락 획득을 대기 중인 다른 스레드 중 하나가 락을 획득할 수 있게 된다.
  - 락을 획득한 스레드 본인이 호출해야 한다. 그렇지 않으면 `IllegalMonitorStateException` 이 발생할 수 있다.
  - ex. 손님이 밥을 먹고 나가면 식당에 자리 하나가 난다. 기다리는 다른 손님에게 알려주고, 그 기다리던 손님이 식당에 들어간다.

- `Condition newCondition()`
  - `Condition` 객체를 생성하여 반환한다. 해당 객체는 락과 결합되어 사용되며, 스레드가 특정 조건을 기다리거나 신호를 받을 수 있게 한다.
  - Object 클래스의 wait, notify, notifyAll 메서드와 유사한 역할을 한다.

> 위의 메서드들을 사용하면 고수준의 동기화 기법을 구현할 수 있다. 
> synchronized 보다 많은 유연성을 제공하여, synchronized 의 단점인 무한 대기 문제를 해결할 수 있다.
{: .prompt-info }

### 공정성 문제는?

- 위의 기능들 덕분에 synchronized 의 단점인 무한 대기 문제를 해결할 수 있었다.
- 하지만 락 획득을 기다리고 있던 스레드 중 어떤 스레드가 락을 획득할 지 알 수 없는 `공정성` 문제는 어떨까?

1. 비공정 모드 (Non-fair mode)
: ReentrantLock 의 기본 모드이다. 공정성 문제가 해결되지 않는 모드이다.
: 생성자에서 `new ReentrantLock()`
: **성능 우선**: 락 획득 속도가 빠르다.
: **선점 가능**: 새로운 스레드가 기존 대기 스레드보다 먼저 락을 획득할 수 있다.
: **기아 현상 가능성**: 특정 스레드가 락을 계속 획득하지 못할 수 있다.

2. 공정 모드 (Fair mode)
: 락을 요청한 순서대로 스레드가 락을 획득할 수 있게 한다.
: 생성자에서 `new ReentrantLock(true)`
: **성능 저하**: 락 획득 속도가 느려질 수 있다.
: **공정성 보장**: 먼저 대기한 스레드가 먼저 락을 획득한다.
: **기아 현상 방지**: 모든 스레드가 언젠가 락을 획득하게 보장한다.

> 공정 모드를 사용하여 공정성 문제를 해결할 수 있다.
{: .prompt-info }


## 참고자료
<hr />

[김영한의 실전 자바 - 고급 1편](https://www.inflearn.com/course/%EA%B9%80%EC%98%81%ED%95%9C%EC%9D%98-%EC%8B%A4%EC%A0%84-%EC%9E%90%EB%B0%94-%EA%B3%A0%EA%B8%89-1/dashboard)
