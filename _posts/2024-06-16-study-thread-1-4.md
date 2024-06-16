---
title: 4 스레드 활용
date: 2024-04-23 20:37:00 +0900
categories: [Study, Thread]
tags: [study, java, thread]
pin: true
---

> 작성중..

## 스터디 git 주소
<hr/>

- <https://github.com/chj-dev/Study_Concurrency>


## 섹션 3
<hr/>

### 1강, UncaughtExceptionHandler

**# 정리.**

**# 질문.**

1. 스레드 예외를 받을 수 없어서 run() 메서드 안에서 처리를 하거나, UncaughtExceptionHandler 로 처리를 해줘야 한다고 하는데, 
그렇다면 실제로 우리가 웹개발을 할 때 쓰는 자바 프레임워크들은 뭐지...??? 얘네는 스레드 작성 없이 스레드로 동작을 하는데 예외 발생하면 캐치가 된다.
그 구문들이 스레드 run() 내에 동작해서 예외처리를 할 수 있는건가..???
한 스레드에서 동작하는 건가..?? 아니면 static setDefaultUncaughtException 으로 세팅해놔서 그걸 타고 오는건가?


### 2강, flag variable vs interrupt() - 1

**# 정리.**

+ 대기나 차단 상태 (sleep 등이 걸린 상태) 인 스레드만 인터럽트익셉션이 걸려서 해당 캐치 구문으로 빠진다.
  그런 경우가 아닐 경우에는 익셉션으로 걸리지 않음. 걸리게 하려면 예제 2번째 처럼,
  => thread.interrupt() 호출하고, 받는 스레드가 if(Thread.interrupted()) throw new InterruptedException("") 와 같이 강제로 익셉션 호출해야함

**# 질문.**


### 3강, flag variable vs interrupt() - 2

**# 정리.**

+ sleep 등 컨텍스트 스위칭이 발생하는 구문이 있으면, volatile 이나 automic 변수를 쓰지 않아도, 
캐시 메모리를 스위칭마다 비우기 때문에 공유변수를 안써도 되는것처럼 보인다.
+ 진짜 대기나 차단 상태일 때 인터럽트 익셉션이 걸려야 인터럽트 값이 true->false 로 초기화 된다. 인위적으로 throw new 했을 경우에는 상태값이 초기화 되지 않는다.

**# 질문.**



### 4강, 사용자 스레드 vs 데몬 스레드

**# 정리.**


**# 질문.**
