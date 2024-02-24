---
title: OOME와 GC
date: 2024-02-23 17:59:00 +0900
last_modified_at: 2024-02-24 12:30:00 +0900
categories: [Java, Thoery]
tags: [gc, java, garbagecollection, thoery]
pin: true
math: false
mermaid: false
image:
  path: /assets/img/posts/java/java-heap-ex-job.png
  lqip: /assets/img/posts/java/java-heap-ex-job.png
  alt: 특정 시간(02시)에 치솟은 힙 메모리 제니퍼 화면. 빨간색- 힙 메모리 크기, 파란색- 힙 메모리 사용량 
---

> 개발서버가 계속 죽었다.    
> 불가피하게 개발서버에도 돌아가고 있는 배치가 있었는데, 해당 배치 시간마다 문제가 발생하여 작업이 계속 제대로 처리되지 않았다.   
> 해당 배치는 30만 건이 넘는 데이터를 DB에서 조회하여 레디스에 put하는 배치인데, 일배치여서 서버가 계속 좀비상태(STW?)로 되는 문제가 있었다.   
> 그런데 마침 얼마 전에 로컬에서 테스트를 하면서 비슷한 오류가 발생했다.   
> 약 25만 건의 데이터를 DB에서 조회하는 쿼리를 작성했는데, 시간이 걸리다가 OOME: java heap space 오류가 발생했다.
> 
> 아직 주니어 개발자인지라 개발서버가 계속 죽는 것을 보고 배치가 문제이구나 추측만 하고 어디서 문제가 발생하는 지는 잘 몰랐는데, 이제야 알게 된 것이다..   
> 운영은 위의 배치로 서버가 뻗는 일은 없었지만 (배치 서버도 분리되어 있다.), 제니퍼에서 확인해 보니 해당 시간에 힙 메모리가 계속 6000MB 이상 치솟고 있었다.   
> (여기서 개발은 -Xmx4g, 운영은 -Xmx16g 로 되어 있었다! 운영은 괜찮고 개발 서버는 계속 죽을 수 밖에...)
>
> OOME 와 GC...   
> 제대로 알아볼 필요성을 느꼈다.


## Out Of Memory Error
<hr/>

### 종류
- java.lang.OutOfMemoryError: Java heap space
- java.lang.OutOfMemoryError: PermGen space
- java.lang.OutOfMemoryError: requested bytes for . Out of swap space?
- java.lang.OutOfMemoryError: unable to create new native thread or java.lang.OutOfMemoryError
- etc...

### OOME: Java heap space
Java의 힙 메모리 공간이 부족하여 발생한다.   
이 경우는
- 일시적인 과도한 요구
- 지속적인 메모리 누수

를 원인으로 하는데, 해결 방법으로는
- heap size를 늘리기
- application 누수 원인 및 위치 찾기

가 있다.   
> 여기서 내가 만났던 오류의 원인은 한번에 30만 건의 데이터를 조회하려 해서 발생했단 것이고, 데이터를 n건 씩 나눠서 조회하는 방향으로 수정할 예정이다.
{: .prompt-info }

### OOME: PermGen space PermGen space
JVM의 Heap 메모리는 young, old, permanent 영역으로 나뉜다. 이때 이 permanent 영역엔 클래스, 메소드 객체 등의 메타데이터가 저장된다.   
따라서 적재된 클래스, 메소드 정보들이 많다면 위 오류가 발생할 수 있다.

### OOME: requested bytes for . Out of swap space?
JVM은 Heap 외에도 Native 메모리를 사용하는데, 이 에러는 Native Heap 메모리가 부족할 때 발생한다.   
물리적 메모리 크기 안에서, Heap, Native 영역의 사이즈를 잘 조율하여 사용해야 한다.   
ex. 가용 메모리가 4G 일 때, Heap size를 -Xmx4g 로 해버리면 위의 오류가 발생할 수 있다.

### OOME: unable to create new native thread
스레드를 생성할 때 메모리가 부족하거나, 너무 많은 스레드가 생성되어 있어 스레드를 생성하는데 실패했을 때 발생한다.


## Garbage Collection
<hr/>

GC는 Java 프로세스에서 사용하지 않는 **메모리를 자동으로 해제**해주는 JVM의 작업이다.   
이 작업이 없으면 개발자가 직접 메모리를 할당하거나, 수동으로 해제 해야 하고, 메모리를 해제하지 않으면 결국 OOME 가 발생하게 된다.   
Java에서는 개발자가 메모리 관리를 하지 않도록 GC 기능을 제공한다.

### 힙 영역과 역할
GC의 Heap 영역에는 young generation, old generation, permanent generation 이 존재한다.    
영역 별로 GC를 진행한다.

**young generation**

young 영역의 내부는 또 Eden, Survivor1, Survivor2 로 나뉜다.
1. Eden   
새롭게 생성된 객체들이 할당되는 영역
2. Survivor   
GC 후에 해제되지 않고 Old에 가지 않은, *Minor GC에서 살아남은 객체들이 존재하는 영역이다. 1과 2중 하나는 꼭 비어있어야 한다.

* Minor GC   
young 영역의 GC 작업.   
처리 시간이 짧고, Eden 영역이 가득차면 발생한다. 특정 횟수 이상 GC 대상이 되면 old 영역으로 이동한다.

**old generation**

young 영역에서 저장되었던 객체 중 오래된 객체가 이동하여 저장되는 영역이다.   
이 영역에서 *Major GC가 발생한다.

* Major GC   
old 영역과 young 영역의 GC 작업. Full GC 라고도 한다.   
old 영역이 가득차면 발생한다.  

**permanent generation**

Class loader에 의해 로드되는 class, method 등의 메타데이터가 저장되는 영역이다.

## STW (stop-the-world)
<hr/>

GC를 실행하는 스레드를 제외한 나머지 스레드가 멈추는 것이다.   
GC 작업이 완료된 이후에 작업을 다시 시작하기 때문에, STW가 발생하는 동안은 애플리케이션이 중지 되어 장애로 이어질 수 있다.

## 참고자료
<hr/>

<https://steady-coding.tistory.com/584>   
<http://www.chlux.co.kr/bbs/board.php?bo_table=board02&wr_id=110&page=8>   
<https://www.devkuma.com/docs/jvm/garbage-collection/>
