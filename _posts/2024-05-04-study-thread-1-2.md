---
title: 스레드 생성 및 실행구조
date: 2024-05-04 12:45:00 +0900
categories: [Study, Thread]
tags: [study, java, thread]
pin: true
---

> 작성중..

## 스터디 git 주소
<hr/>

- <https://github.com/chj-dev/Study_Concurrency>


## 섹션 2
<hr/>

### 질문
Q 1. main 스레드는 모든 실행 후에 다른 스레드의 끝을 기다리지 않고 먼저 종료되는게 맞는지?
- 맞다. chapter 3 - exam01 - InterruptSleepExample.java 소스를 디버깅하며 확인해보면,
  마지막 main 메서드의 구문 실행 후 RUNNING 상태이던 main 스레드가 사라진다.
- 아니면, main에서 실행시킨 다른 스레드를 종료를 늦게 한다음 거기서 main 스레드가 살아있는지 isAlive()로 테스트 해본다.
> 다른 처리가 없을 경우 메인 스레드는 먼저 종료됨.
{: .prompt-info }

Q 2. 스레드가 예외 발생 종료라도 정상적으로 종료(terminated) 가 된다고 했는데,
그렇다면 우리의 애플리케이션(스프링부트 웹플럭스)은 스레드가 정상적으로 종료가 되었었는지, 아니면 좀비상태로 남아있었는지?
- throw new OTAException VS Mono.just (Mono.error)
- exception 상속 (OTAException) 의 동작 원리 (정해진 리턴 json 을 만드는 과정)
