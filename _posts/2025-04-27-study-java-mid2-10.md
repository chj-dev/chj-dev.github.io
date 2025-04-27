---
title: 10 컬렉션 프레임워크 - 순회, 정렬
date: 2025-04-27 14:24:00 +0900
categories: [Study, Java-mid2]
tags: [study, java, mid2]
pin: true
---

## 자바 중급2 git 주소
<hr />

- <https://github.com/chj-dev/java-mid2>


## 순회 - Iterable, Iterator
<hr />

- Iterable: 반복 가능한
- Iterator: 반복자

- Iterable 인터페이스는 Collection 인터페이스보다 상위에 존재한다.
  - 모든 컬렉션을 `Iterable` 과 `Iterator` 를 사용하여 순회할 수 있다.
  - Map 의 경우, key 나 value 중 하나를 정해서 순회할 수 있다.

> Iterator (반복자) 디자인 패턴은 객체 지향 프로그래밍에서 컬렉션의 요소들을 순회할 때 사용되는 디자인 패턴이다.
> 컬렉션의 내부 표현 방식을 노출시키지 않으면서 각 요소에 순차적으로 접근할 수 있게 한다.
> Iterator 패턴은 컬렉션의 구현과는 독립적으로 요소들을 탐색할 수 있는 방법을 제공하여, 코드이 복잡성을 줄이고 재사용성을 높인다.
{: .prompt-info }

## 정렬 - Comparable, Comparator
<hr />

- 기본형 배열은 듀얼 피벗 퀵 소트(Dual-Pivot QuickSort)
- 객체 배열은 팀소트(TimeSort)
- 평균 O(n log n) 의 성능

### Comparator

- 두 값을 비교할 때 비교 기준을 직접 제공

```java
public interface Comparator<T> {
  int compare(T o1, T o2);
}
```

- 두 인수를 비교하여 결과 값을 반환한다.
  - 첫 번째 인수가 더 작으면 음수, -1
  - 두 값이 같으면 0
  - 첫 번째 인수가 더 크면 양수, 1

### Comparable - 직접 만든 객체 비교

```java
public interface Comparable<T> {
  public int compareTo(T o);
}
```

- 자기 자신과 인수로 넘어온 객체를 비교하여 반환한다.
  - 현재 객체기 인수로 주어진 객체보다 더 작으면 음수, -1
  - 두 객체의 크기가 같으면 0
  - 현재 객체가 인수로 주어진 객체보다 더 크면 양수, 1
- Comparable 을 통해 구현한 순서를 자연 순서(Natural Ordering) 이라 한다.

### 정렬 기준 제공이 필수인 TreeSet, TreeMap

- Comparable 또는 Comparator 를 필수로 제공해 주어야 함.

```java
//객체(MyUser) 가 구현한 Comparable 사용
TreeSet<MyUser> treeSet1 = new TreeSet<>();

//별도로 넘어온 비교자인 Comparator 사용
TreeSet<MyUser> treeSet2 = new TreeSet<>(new IdComparator());
```

> Comparable, Comparator 모두 제공하지 않으면 런타임 오류가 발생한다.
> java.lang.ClassCastException: class collection.compare.CLASS_NAME cannot be cast to class java.lang.Comparable
{: .prompt-warning }


## 컬렉션 유틸
<hr />

### Collections 정렬 관련 메서드

- `max`: 정렬 기준으로 최대 값을 찾아서 반환한다.
- `min`: 정렬 기준으로 최소 값을 찾아서 반환한다.
- `shuffle`: 컬렉션을 랜덤하게 섞는다.
- `sort`: 정렬 기준으로 컬렉션을 정렬한다.
- `reverse`: 정렬 기준의 반대로 컬렉션을 정렬한다. (컬렉션에 들어있는 결과를 반대로 정렬한다.)

```java
Collections.shuffle(LIST);
Collections.sort(LIST);
```

### 불변 컬렉션 생성

- `List.of(...)`
  - List, Set, Map 모두 of() 메서드를 지원한다.

> 불변 컬렉션을 변경하는 메서드를 호출하면 UnsupportedOperationException 예외가 발생한다.
{: .prompt-info }

### 불변 컬렉션을 가변 컬렉션으로 전환하려면?

- 리스트의 경우,
  - `new ArrayList<>(불변리스트)` 와 같이 사용

### 가변 컬렉션을 불변 컬렉션으로 전환하려면?

- 리스트의 경우,
  - `Collections.unmodifiableList(가변리스트)` 와 같이 사용
- 다양한 unmodifiableXXX() 가 존재한다.

### 빈 리스트 생성

1. 불변 리스트
   - 자바 5: `Collections.emptyList()`
   - 자바 9: `List.of()`

2. 요소 변경이 가능한 고정 크기의 리스트
   - `Arrays.asList()`
     - set() 을 통해 요소 변경 가능
     - add(), remove() 와 같은 메서드는 예외 발생.
       - java.lang.UnsupportedOperationException

### 멀티 스레드 동기화

- `Collections.synchronizedList`
- 동기화 작업으로 성능은 일반 리스트보다 느리다.


## 참고자료
<hr />

[김영한의 실전 자바 - 중급 2편](https://www.inflearn.com/course/%EA%B9%80%EC%98%81%ED%95%9C%EC%9D%98-%EC%8B%A4%EC%A0%84-%EC%9E%90%EB%B0%94-%EC%A4%91%EA%B8%89-2/dashboard)
