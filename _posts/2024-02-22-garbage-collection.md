---
title: GC (Garbage Collection)
date: 2024-02-22 23:25:00 +0900
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

> 개발서버가 계속 죽었다. 불가피하게 개발서버에도 돌아가고 있는 배치가 있었는데, 해당 배치 시간마다 문제가 발생하여 작업이 계속 제대로 처리되지 않았다.   
> 해당 배치는 30만 건이 넘는 데이터를 DB에서 조회하여 레디스에 put하는 배치인데, 일배치여서 서버가 계속 좀비상태로 되는 문제가 있었다.   
> 그런데 마침 얼마 전에 로컬에서 테스트를 하면서 비슷한 오류가 발생했다.   
> 약 25만 건의 데이터를 DB에서 조회하는 쿼리를 작성했는데, 시간이 걸리다가 OOME: java heap space 오류가 발생했다.
> 
> 아직 주니어 개발자인지라 개발서버가 계속 죽는 것을 보고 배치가 문제이구나 추측만 하고 어디서 문제가 발생하는 지는 잘 몰랐는데, 이제야 알게 된 것이다..   
> 운영은 위의 배치로 서버가 뻗는 일은 없었지만 (배치 서버는 분리되어 있다.), 제니퍼에서 확인해 보니 해당 시간에 힙 메모리가 계속 6000MB 이상 치솟고 있었다.   
> (여기서 개발은 -Xmx4g, 운영은 -Xmx16g 로 되어 있었다! 운영은 괜찮고 개발 서버는 계속 죽을 수 밖에...)
>
> OOME 와 GC...   
> 제대로 알아볼 필요성을 느꼈다.

