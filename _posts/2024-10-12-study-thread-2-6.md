---
title: 6 동기화 synchronized
date: 2024-10-12 11:27:00 +0900
categories: [Study, Java-advanced1]
tags: [study, java, thread, adv1]
pin: true
---

## 스터디 git 주소
<hr />

- <https://github.com/chj-dev/java-adv1>

## 공유 자원 사용

- 여러 스레드가 접근하는 자원을 **공유 자원** 이라고 한다.
- 멀티 스레드 환경에서, 공유 자원에 여러 스레드가 동시에 접근할 때 **동시성 문제**가 발생할 수 있다.
- 대표적인 공유 자원의 예시로는 인스턴스의 필드(멤버 변수)가 있다.

### 동시성 문제?

- 은행 잔고 입출금 예시
- 상품 주문 수량 검증 예시

> 동시성 문제 제어가 되지 않으면, 한 계좌에 현금을 동시에 입금 할 때, +1000원, +1000원 하여 총 2000원이 되는 것이 아니라
> 0원 + 1000원, 0원 + 1000원 이 되어 잔액이 1000원이 된다. 혹은 상황에 따라 정상적으로 2000원이 될 수 있다.   
> 출금도 마찬가지다. 잔고 1000원에 동시에 800원, 800원을 출금할 때 
> 상식적으론 첫번째 출금 때 잔고가 200원이 되어 두번째 출금 시엔 출금 실패해야 한다.
> 하지만 동시성 문제가 해결되지 않으면 200원이 되거나 -600원이 될 수 있다.
{: .prompt-danger }

> 이 문제가 계속 발생했다면 은행은 이미 다 망했다...

### 왜 이런 일이?

> 멀티 스레드 환경에서 각각의 스레드가 같은 인스턴스 참조값을 갖고 있기 때문에
{: .prompt-tip }

### 잔고 체크 로직이 있으면?

- A 스레드가 잔액을 변경하려 하기 전, B 스레드가 잔고 체크하는 로직을 이미 수행 중이라면 로직이 있어도 소용이 없다.
- 아래는 단순 예시이다.

```java
//balance: 잔고, amount: 출금할 금액
if (balance < amount) {
  return false;
}   //B스레드 위치
    
balance = balance - amount; //A 스레드 위치
```

### 이전에 배운 volatile 을 도입하면?

- volatile 은 메모리 가시성 문제(한 스레드가 값을 변경했을 때 다른 스레드에서 변경된 값을 즉시 볼 수 있게 하는 것)를 해결할 뿐이다.
- 완전히 동시에 입출금이 이루어지면 해결이 불가능하다.
- 혹은 위 예시의 상황에서도 해결이 불가능하다.


## 임계 영역 (critical section)

- 여러 스레드가 동시에 접근하면 데이터 불일치나 예상치 못한 동작이 발생할 수 있는 위험하고 중요한 코드 부분
- 여러 스레드가 동시에 접근하면 안되는 공유 자원을 접근하거나 수정하는 부분
- 예시의 잔고 검증/출금 부분이 임계 영역에 해당한다.

### 위의 문제를 해결하려면 어떻게 해야할까?

- 쉽게 생각하면 A 스레드가 해당 작업을 하고 있을 때 B 스레드는 순서를 기다리는 것이다.
- 한번에 하나의 스레드만 접근할 수 있도록 자바의 `synchronized` 키워드를 통해 임계 영역을 보호할 수 있다.


## synchronized 키워드

- synchronized 키워드를 사용하면 메모리 가시성 문제와 함께 동시성 문제를 해결할 수 있다.

> 키워드 영역 안에 있는 변수에 volatile 을 사용하지 않아도 된다.
> {: .prompt-info }

   
- 즉, 아래의 문제 해결이 가능하다.

1. 경합 조건(Race condition)
: 두 개 이상의 스레드가 경쟁적으로 동일한 자원을 수정할 때 발생하는 문제

2. 데이터 일관성
: 여러 스레드가 동시에 읽고 쓰는 데이터의 일관성을 유지

### 객체의 락(lock)

- 모든 객체(인스턴스)는 내부에 자신만의 락(lock)을 하나 가지고 있다.
- 모니터 락(monitor lock) 이라고도 한다.
- 스레드가 synchronized 키워드가 있는 메서드에 진입하려면 반드시 해당 인스턴스의 락이 있어야 한다.

### 동작 순서

- A 스레드가 객체의 lock 을 획득하면, B 스레드는 lock 을 획득할 때 까지 `BLOCK` 상태로 **무한 대기**한다.
- A 스레드의 작업이 끝나 객체에 lock 이 반납되면 대기하던 스레드는 자동으로 lock 을 획득한다. 이때 락을 획득한 스레드는 `BLOCK -> RUNNABLE` 상태가 된다.
- 대기하던 스레드 중 어떤 스레드가 어떤 순서로 lock 을 획득하는 지는 자바 표준에 정의되어 있지 않다.

### 메서드, 코드 블럭 동기화

- 메서드 자체에 키워드를 넣을 수도 있고, 특정 구문에만 키워드를 넣을 수도 있다.

```java
//메서드 자체가 임계 영역인 경우
public synchronized boolean withdraw(int amount) {
  if (balance < amount) {
    return false;
  }

  balance = balance - amount;
  return true;
}
```

```java
//메서드 내의 특정 블럭만 임계 영역인 경우
public boolean withdraw(int amount) {
  log("withdraw 시작 로그");
  
  synchronized (this) {
    if (balance < amount) {
      return false;
    }

    balance = balance - amount;
    return true;
  }
}
```

### 장점, 단점

1. 장점
: 프로그래밍 언어에 문법으로 제공
: 편리한 사용
: **자동 잠금 해제**: 작업이 끝나면 자동으로 lock 을 대기중인 다른 스레드의 잠금이 해제된다. 
개발자가 직접 특정 스레드를 깨우도록 관리하지 않아도 된다.

2. 단점
: **무한 대기**: BLOCK 상태의 스레드는 lock 이 풀릴 때 까지 무한 대기한다. 인터럽트도 안된다.
: **공정성**: BLOCK 상태의 스레드 중 어떤 스레드가 lock 을 획득할 지 알 수 없다. 
따라서 특정 스레드가 오랜 시간 lock 을 획득하지 못할 수도 있다.

### 새로운 등장

- synchronized 의 단점을 해결하기 위한 더 유연하고 세밀한 방법들이 필요하게 되었다.
- 자바 1.5 부터 `java.util.concurrent` 라는 동시성 문제 해결을 위한 패키지가 추가되었다.


## 참고자료
<hr />

[김영한의 실전 자바 - 고급 1편](https://www.inflearn.com/course/%EA%B9%80%EC%98%81%ED%95%9C%EC%9D%98-%EC%8B%A4%EC%A0%84-%EC%9E%90%EB%B0%94-%EA%B3%A0%EA%B8%89-1/dashboard)