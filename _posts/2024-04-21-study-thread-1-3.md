---
title: 3 스레드 기본 API
date: 2024-04-23 20:37:00 +0900
last_modified_at: 2024-06-01 15:48:00 +0900
categories: [Study, Thread]
tags: [study, java, thread]
pin: true
---

> 작성중.. (join 관련 질문)

## 스터디 git 주소
<hr/>

- <https://github.com/chj-dev/Study_Concurrency>


## 섹션 3
<hr/>

### 1강, sleep()

**# 정리.**

1. Sleep() 메서드
: 지정된 시간동안 현재 스레드의 실행을 일시 정지하고 대기 상태로 변환하게 하는 메서드.
: 네이티브 메서드로 연결되며 시스템 콜을 통해 커널모드에서 수행 후 유저모드로 전환함.
: try-catch 구문 필수
: 1) static sleep(long millis) throws InterruptedException   
- 지정한 밀리초 시간동안 스레드가 수면 상태로 변환됨.   
- 파라미터에 음수가 들어갈 경우 IllegalArgumentException 발생
: 2) static sleep(long millis, int nanos) throws InterruptedException   
- 지정한 밀리초에 나노초를 더한 시간 동안 스레드가 수면 상태로 변환됨.   
- 나노초 범위: 0 ~ 999999
: 3) InterruptedException   
- 스레드가 수면 중에 인터럽트 될 경우 해당 예외 발생.   
- 다른 스레드가 수면 중인 스레드에게 인터럽트(중단 신호)를 보낼 수 있음.   
- 해당 예외 발생 시 수면 상태에서 깨어나고 실행 대기 상태로 전환됨.

2. 작동 방식
: 수면 상태 > 실행 상태 변환 시 남은 지점부터 다시 실행함.
: 동기화 메서드 영역에서 수면 시 획득한 모니터나 락을 잃지 않고 계속 유지함.
: 스레드 수면 시간은 n초로 지정한다고 해도 정말 딱 n초 동안 수면되지 않음. = 정확성이 보장되지 않음. = 시스템 부하에 따라 다름

**# 질문.**

1. 네이티브 메서드로 연결되며 시스템 콜을 통해 커널모드에서 수행 후 유저모드로 전환?  
: 컨텍스트 스위칭 유발.  
: 잘 이해가 안되면 운영체제 기본을 다시 보고 오기

> time waited 단계로 변하니까 다른 실행할 스레드를 찾으려면 OS 스케쥴링을 통해야 해서 커널모드로 변환되어야 한다!
{: .prompt-info }

2. InterruptedException?
:  다른 스레드는 잠자고 있는 스레드에게 인터럽트 (멈춤) 신호를 보낼 수 있음. **아래 설명 참고. 자세한 예시는 추후 관련 강의에서 진행.**  
- T1 스레드가 sleep 중에 T2 스레드가 T1에게 인터럽트를 걸면 인터럽트 익셉션 발생.  
- T1 스레드가 진행 중에 T2 스레드가 T1에게 인터럽트를 걸면 T1 스레드가 sleep 구문을 만났을 때 인터럽트 익셉션 발생?

> 위의 2가지 예시 모두 인터럽트 예외가 발생한다. (study git: chapter03 > SleepInterruptTest 참고)
{: .prompt-info }

3. 네이티브 메서드?

> JAVA Native Method (JNI): OS가 바로 읽을 수 있는 형태의 네이티브 코드를 JVM이 호출할 수 있게 하는 인터페이스. 이를 통해 플랫폼에 상관없이 동작한다.   
> 네이티브 메서드: C나 C++ 같은 네이티브 프로그래밍 언어로 작성한 메서드. JNI를 사용하는 메서드.
{: .prompt-info }


### 2강, join()

**# 정리.**

1. join() 메서드
: 한 스레드가 다른 스레드가 종료될 때까지 실행을 중지하고 대기 상태에 들어가게 하는 메서드.
: 스레드의 순서를 제어하거나, 다른 스레드의 작업을 기다려야 하는 등 순차적인 흐름을 구성하고자 할 때 사용 가능.
: Object 클래스의 wait() 네이티브 메서드로 연결되며 시스템 콜을 통해 커널모드로 수행함.   
- 내부적으로 wait() & notify() 흐름을 가짐
: 1) void join() throws InterruptedException   
- 스레드의 작업이 종료될 때까지 대기 상태 유지
: 2) void join(long millis) throws InterruptedException   
- 지정한 밀리초 시간 동안 대기 상태 유지   
- 파라미터에 음수가 들어갈 경우 IllegalArgumentException 발생
: 3) void join(long millis, int nanos) InterruptedException   
- 지정한 밀리초에 나노초를 더한 시간 동안 스레드의 대기 상태를 유지함.   
- 나노초 범위: 0 ~ 999999
: 4) InterruptedException   
- 스레드가 인터럽트 될 경우 해당 예외 발생.   
- 다른 스레드가 join() 을 수행 중인 스레드에게 인터럽트(중단 신호)를 보낼 수 있음.   
- 해당 예외 발생 시 대기 상태에서 실행 대기 상태로 전환됨.

2. 작동 방식
: 대기 상태 > 실행 상태 변환 시 남은 지점부터 다시 실행함.
: 호출 대상 스레드가 여러 개일 경우 각 스레드 작업이 종료될 때까지 join()을 호출한 스레드는 대기와 실행을 재개하는 흐름을 반복함.


**# 질문.**

1. join() 호출 대상 스레드가 sleep() 상태에 들어갔을 때, join() 을 실행 중인 스레드는 계속 대기 상태인지?
: 실제로 코드 작성하여 확인해보기


### 3강, interrupt()

**# 정리.**

1. interrupt()
: 스레드에게 인터럽트가 발생했다는 신호를 보내는 매커니즘.
: 현재 실행 흐름을 멈추고 인터럽트 이벤트를 먼저 처리하도록 시그널을 보내는 장치.
: **interrupted 속성**   
- 인터럽트 발생 여부 확인용 상태 값. 기본값: false   
- 인터럽트 상태를 이용하여 스레드 중지, 작업 취소, 스레드 종료 등의 기능 구현 가능.   
- 다른 스레드를 인터럽트 하거나, 자기 자신을 인터럽트 할 수 있음.   
- 인터럽트되면 스레드의 인터럽트 상태를 true 로 변경함.

2. 인터럽트 상태 확인 방법
: 1) static boolean interrupted()   
- 스레드의 인터럽트 상태를 반환하는 정적 메서드.   
- 현재 인터럽트 상태값을 반환하고 false 로 초기화함.
: 2) boolean isInterrupted()   
- 스레드의 인터럽트 상태를 반환하는 인스턴스 메서드.

3. InterruptedException
: 대기/차단 등 블록킹 상태에 있거나 만나는 시점의 스레드에 인터럽트할 때 발생하는 예외.
: 해당 예외가 발생하면 인터럽트 상태는 자동으로 초기화됨 = Thread.interrupt() 실행한 것과 같은 상태 (interrupted = false)
: 예외 발생 케이스   
- Thread.sleep(), Thread.join(), Object.wait()   
- Future.get(), BlockingQueue.take()

**# 질문.**

1. interrupted() 나 isInterrupted() 메소드를 호출하면 실행대기 상태가 되나? (interrupted 상태가 true 일 때의 스레드 상태?)

> 엄밀히 말하면, 실행 중인 스레드가 sleep() 등을 통해 일시정지 상태가 되었을 때 인터럽트가 작동하는 것이기 때문에 위의 질문은 틀린 질문.   
{: .prompt-info }


### 5강, Thread 메서드

**# 정리.**

1. Thread Name
: 스레드마다 이름을 지정할 수 있음. 지정하지 않을 시 자동으로 주어지는 이름을 갖는다.   
- 메인 스레드 이름: main
- 스레드 이름: Thread-0, Thread-1, ... Thread-n
: 이름을 지정한 스레드가 있다면 다른 기본 스레드는 이름을 지정하지 않을 경우 Thread-1 부터 시작함.
: 스레드 이름 지정 방법은 2가지가 존재함.
: 1) 스레드 객체 생성 시 인자로 전달   
- Thread thread = new Thread([ThreadGroup], [Runnable], "스레드이름");
: 2) setName(String name) 메서드 이용   
- thread.setName("스레드이름");

2. currentThread()
: 현재 실행중인 스레드 개체에 대한 참조를 반환함.
: Thread 클래스의 정적 메서드
: ex) Thread.currentThread() / Thread.currentThread().getName()

3. isAlive()
: 스레드 활성 여부 확인 메서드.
: 스레드의 start() 메서드가 호출되고 종료되지 않은 경우 true 반환

**# 질문.**

1. main 스레드는 모든 실행 후에 다른 스레드의 끝을 기다리지 않고 먼저 종료되는게 맞는지?
   : 맞다. chapter 3 - exam01 - InterruptSleepExample.java 소스를 디버깅하며 확인해보면,
   마지막 main 메서드의 구문 실행 후 RUNNING 상태이던 main 스레드가 사라진다.
   : 아니면, main에서 실행시킨 다른 스레드를 종료를 늦게 한다음 거기서 main 스레드가 살아있는지 isAlive()로 테스트 해본다.

> 다른 처리가 없을 경우 메인 스레드는 먼저 종료됨. (study git: chapter03 > MainThreadAlive 참고)
{: .prompt-info }


### 6강, Priority

**# 정리.**

1. 스레드 우선순위(Priority)
: 자바 런타임은 고정 우선순위 선점형 스케줄링 (fixed priority pre-emptive scheduling) 이라는 스케줄링 알고리즘을 지원함.   
- 실행 대기 상태의 스레드 중 우선 순위에 따라 스레드를 예약함.

2. 우선순위 개념
: 1 ~ 10 사이의 정수, 값이 높음 = 우선순위 높음.
: 기본 우선순위: 5
: 비교 스레드의 우선순위가 같을 경우 라운드 로빈(순환 할당) 스케줄링 방식을 통해 다음 스레드를 선택함.
: **반드시 우선순위가 높은 스레드를 실행하는 것은 아님. 기아 상태 회피를 위한 정책이 존재함**

3. 우선순위 유형
: 최소 우선순위: 1, public static int MIN_PRIORITY
: 기본 우선순위: 5, public static int NORM_PRIORITY
: 최대 우선순위: 10, public static int MAX_PRIORITY

4. 우선순위 변경 및 확인
: 1) void setPriority(int newPriority)   
- 우선순위 변경 메서드.   
- 1 ~ 10 외의 값 입력 시 오류 발생
: 2) int getPriority()   
- 우선순위 반환 메서드.


## 참고자료
<hr />

[인프런 자바-동시성-프로그래밍-리액티브-part1](https://www.inflearn.com/course/%EC%9E%90%EB%B0%94-%EB%8F%99%EC%8B%9C%EC%84%B1-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-%EB%A6%AC%EC%95%A1%ED%8B%B0%EB%B8%8C-part1/dashboard)
