---
title: 9 생산자 소비자 문제2
date: 2024-11-24 14:30:00 +0900
categories: [Study, Java-advanced1]
tags: [study, java, thread, adv1]
pin: true
---

## 스터디 git 주소
<hr />

- <https://github.com/chj-dev/java-adv1>


## Lock Condition

### 생산자가 생산자를 깨우고, 소비자가 소비자를 깨우는 비효율 문제를 어떻게 해결할까?

- 답은 간단하다. 생산자가 소비자를 깨우고, 소비자가 생산자를 깨우면 해결되는 간단한 문제이다. 
- 생산자 스레드가 대기하는 대기 집합과, 소비자 스레드가 대기하는 대기 집합을 둘로 나눠서 관리하면?
- 어떻게 분리할 수 있을까?

> 자바는 synchronized 와 BLOCKED 상태를 통한 임계 영역 관리의 단점을 해결하기 위해 
> 자바 1.5부터 Lock 인터페이스와 ReentrantLock 구현체를 제공한다.
{: .prompt-info }

### Lock 인터페이스와 ReentrantLock 구현체

- `Condition`
  - ReentrantLock 을 사용하는 스레드가 대기하는 스레드 공간이다.
  ```java
  Lock lock = new ReentrantLock;    //synchronized 대신 사용하는 락
  Condition condition = lock.newCondition();    //대기 공간 생성. 이 대기 공간을 생산자용, 소비자용 2개를 생성한다.
  ```

> Object.wait() 에서 사용한 스레드 대기 공간은 모든 객체 인스턴스가 내부에 기본으로 가지고 있다.
> 하지만 Lock(ReentrantLock) 을 사용하는 경우 대기 공간을 직접 만들어야 한다.
{: .prompt-warning }

- `condition.await()`
  - Object.wait() 와 유사한 기능이다. 지정한 condition 에 현재 스레드를 대기 상태로 보관한다.
  - 이때 ReentrantLock 에서 획득한 락을 반납한다.
- `condition.signal()`
  - Object.notify() 와 유사한 기능이다. 지정한 condition 에서 대기중인 스레드를 하나 깨운다.
  - 깨어난 스레드는 condition 에서 빠져나온다.

### Object.notify() vs Condition.signal()

- `Object.notify()`
  - 대기 중인 스레드 중 임의의 하나를 깨운다. 깨어나는 순서는 정의되어 있지 않고 JVM 구현에 따라 다르다.
  - synchronized 블록 내에서 모니터 락을 가지고 있는 스레드가 호출해야 한다.
- `Condition.signal()`
  - 대기 중인 스레드 중 하나를 깨운다. 일반적으로 Queue 구조를 사용하기 때문에 FIFO 순서로 깨운다.
  - ReentrantLock 을 가지고 있는 스레드가 호출해야 한다.

## 스레드의 대기

### synchronized 대기

- BLOCKED 상태로 대기
- synchronized 를 시작할 때 락이 없으면 대기
- 다른 스레드가 synchronized 를 빠져나갈 때 대기가 풀리며 락 획득 시도

### wait() 대기

- WAITING 상태로 대기
- wait() 을 호출했을 때 스레드 대기 집합에서 대기
- 다른 스레드가 notify() 를 호출했을 때 빠져나감

### 스레드의 대기 집합

- 모니터 락
- 락 대기 집합(모니터 락 대기 집합)
- 스레드 대기 집합


- 락 대기 집합이 1차 대기소이고, 스레드 대기 집합이 2차 대기소이다.
- 2차 대기소에 들어간 스레드는 2차, 1차 대기소를 모두 빠져나와야 임계 영역을 수행할 수 있다.

### ReentrantLock 락 대기

- 1.ReentrantLock 락 획득 대기
  - ReentrantLock 대기 큐에서 관리
  - WAITING 상태로 락 획득 대기
- 2.await() 대기
  - condition.await() 호출했을 때, condition 객체의 스레드 대기 공간에서 관리
  - WAITING 상태로 대기
  - condition.signal() 호출했을 때 condition 객체의 스레드 대기 공간에서 빠져나감

## Blocking Queue

- 생산자 소비자 문제를 위해 학습한 내용이 이미 BlockingQueue 라는 이름으로 구현되어 있다.


## 참고자료
<hr />

[김영한의 실전 자바 - 고급 1편](https://www.inflearn.com/course/%EA%B9%80%EC%98%81%ED%95%9C%EC%9D%98-%EC%8B%A4%EC%A0%84-%EC%9E%90%EB%B0%94-%EA%B3%A0%EA%B8%89-1/dashboard)
