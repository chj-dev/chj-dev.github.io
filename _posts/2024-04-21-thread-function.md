---
title: 스레드 기본 API
date: 2024-04-23 20:37:00 +0900
categories: [Java, Theory]
tags: [java, theory, thread]
pin: true
---

> 작성중..


## 섹션 2
<hr/>

### 질문
Q 1. main 스레드는 모든 실행 후에 다른 스레드의 끝을 기다리지 않고 먼저 종료되는게 맞는지?  
- 맞다. chapter 3 - exam01 - InterruptSleepExample.java 소스를 디버깅하며 확인해보면, 
마지막 main 메서드의 구문 실행 후 RUNNING 상태이던 main 스레드가 사라진다.
- 아니면, main에서 실행시킨 다른 스레드를 종료를 늦게 한다음 거기서 main 스레드가 살아있는지 isAlive()로 테스트 해본다.

Q 2. 스레드가 예외 발생 종료라도 정상적으로 종료(terminated) 가 된다고 했는데,
그렇다면 우리의 애플리케이션(스프링부트 웹플럭스)은 스레드가 정상적으로 종료가 되었었는지, 아니면 좀비상태로 남아있었는지?
- throw new OTAException VS Mono.just (Mono.error)
- exception 상속 (OTAException) 의 동작 원리 (정해진 리턴 json 을 만드는 과정)

## 섹션 3
<hr/>

### 1강, sleep()
Q 1. 네이티브 메서드로 연결되며 시스템 콜을 통해 커널모드에서 수행 후 유저모드로 전환?  
- 컨텍스트 스위칭 유발.  
- 잘 이해가 안되면 운영체제 기본을 다시 보고 오기
- => time waited 단계로 변하니까 다른 실행할 스레드를 찾으려면 os 스케쥴링을 통해야 해서 커널모드로 변환되어야 한다!

Q 2. InterruptedException?
- 인터럽트(중단) 예외(InterruptedException) 이 발생하면, 스레드는 수면상태에서 깨어나고 실행대기 상태로 전환되어 실행을 기다린다.  
- 다른 스레드는 잠자고 있는 스레드에게 인터럽트 (멈춤) 신호를 보낼 수 있음. **아래 설명 참고. 자세한 예시는 추후 관련 강의에서 진행.**  
ㄴ T1 스레드가 sleep 중에 T2 스레드가 T1에게 인터럽트를 걸면 인터럽트 익셉션 발생.  
ㄴ T1 스레드가 진행 중에 T2 스레드가 T1에게 인터럽트를 걸면 T1 스레드가 sleep 구문을 만났을 때 인터럽트 익셉션 발생?

Q 3. 네이티브 메서드?
- 이전 강의에서도 나왔는데 헷갈림...
- JAVA Native Method (JNI)
- 네이티브 메서드란 C나 C++ 같은 네이티브 프로그래밍 언어로 작성한 메서드.
- 시스템 콜로 들어가서 커널모드에서 운영체제 내에서 수행?
- https://escapefromcoding.tistory.com/232

### 2강, join()
Q 1. 

### 3강, interrupt() - 1
Q 1. interrupted() 나 isInterrupted() 메소드를 호출하면 실행대기 상태가 되나?
(interrupted 상태가 true 일 때의 스레드 상태?)
- 스레드가 일시정지 상태인 경우 또는  일시정지에 들어가려는 스레드인 경우 쉬지 못 하게 하는 기능이다.


### 5강, name() ...
추가.
- setName()을 통해 이름을 지정한 스레드가 있다면 다른 기본 스레드는 이름을 지정하지 않을 경우 Thread-1 부터 시작. (보통 0부터 시작함)

### 6강, Priority
추가.
- 자바에서 스레드 우선순위를 지정해도, OS 스케줄링을 통해 스레드가 실행되기 때문에, 해당 우선순위를 참조할 뿐 그대로 실행되지는 않는다. (기아상태 회피)

Q 1. 


## 참고자료
[인프런 자바-동시성-프로그래밍-리액티브-part1](https://www.inflearn.com/course/%EC%9E%90%EB%B0%94-%EB%8F%99%EC%8B%9C%EC%84%B1-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-%EB%A6%AC%EC%95%A1%ED%8B%B0%EB%B8%8C-part1/dashboard)
