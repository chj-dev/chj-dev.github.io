---
title: 9 컬렉션 프레임워크 - Map, Stack, Queue
date: 2025-04-27 13:37:00 +0900
categories: [Study, Java-mid2]
tags: [study, java, mid2]
pin: true
---

## 자바 중급2 git 주소
<hr />

- <https://github.com/chj-dev/java-mid2>


## 맵 (Map)
<hr />

- 키-값의 쌍을 저장하는 자료 구조.
- 키는 Map 내에서 유일해야 함. 키를 통해 값을 검색할 수 있다.
- 순서를 유지하지 않음.

### 인터페이스 구조

- 최상위 인터페이스 `Map`
- 그 하위에 각각 `HashMap` 과 `TreeMap`
- HashMap 하위에 `LinkedHashMap`
- **다른 컬렉션 프레임워크와는 다르게 `Collection` 이 최상위 인터페이스가 아니다.**


### 사용 예시

```java
Map<String, Integer> map = new HashMap<>(); //보통 HashMap 을 주로 사용

//키 목록 조회: keySet
Set<String> keySet = map.keySet();
for (String key: keySet) {
  Integer value = map.get(key);
}

//키-값 목록 조회: entrySet
Set<Map.Entry<String, Integer>> entries = map.entrySet();   //Map 내부의 인터페이스 Entry 사용
for (Map.Entry<String, Integer> entry : entries) {
  String key = entry.getKey();
  Integer value = entry.getValue();
}

//값 목록 조회: values
Collection<Integer> values = map.values();  //중복 허용, 입력 순서 보장 X 이기 때문에 Collection 사용
for (Integer value : values) {
  
}
```

### Map 과 Set

- Map의 키는 중복을 허용하지 않고, 순서를 보장하지 않는다. 이는 Set과 같다.
- Set과 Map의 구현체는 거의 같다.
  - HashSet -> HashMap
  - LinkedHashSet -> LinkedHashMap
  - TreeSet -> TreeMap
- HashSet 의 구현은 대부분 HashMap의 구현을 가져다 사용한다. Map에서 value만 비우면 Set으로 사용이 가능하다.

### 인터페이스 구현체

| 항목     |         HashMap         |      LinkedHashMap      |                  TreeMap                  |
|:-------|:-----------------------:|:-----------------------:|:-----------------------------------------:|
| **구조** |    Hash를 사용하여 요소 저장     | HashMap과 유사, 연결 리스트를 사용 |      레드-블랙 트리 기반        |
| **특징** | 삽입, 삭제, 검색에 해시 자료 구조 사용 |  입력 순서에 따라 순회 가능. 무겁다.  |  자연 순서 OR 생성자에 제공된 `Comparator` 에 의해 정렬   |
| **성능** |          O(1)           |          O(1)           | `get`, `put`, `remove` 같은 주요 작업은 O(log n) |
| **순서** |          보장안함           |           보장함           |             보장함            |


## 스택 (Stack)

- **LIFO, Last In First Out**
- 값 넣기: `push`
- 값 꺼내기: `pop`
- `Stack` 클래스는 내부에서 `Vector` 라는 자료 구조를 사용함.
  - Vector 는 지금 사용되지 않고 호환성을 위해 존재한다.
  - 따라서 Vector 를 사용하는 Stack 도 사용하지 않는 것을 권장한다.
- 대신 `Deque` 를 사용하는 것을 권장한다.


## 큐 (Queue)

- **FIFO, First In First Out**
- 값 넣기: `offer`
- 값 꺼내기: `poll`

### 인터페이스 구조

- 최상위 인터페이스 `Collection`
- 그 하위에 `Queue`
- 그 하위에 `Deque`
- Deque 하위에 `ArrayDeque` 와 `LinkedList` 를 가진다.
  - LinkedList 는 Deque와 List 인터페이스를 모두 구현한다.


## 데크 (Deque, Double Ended Queue)

- 양쪽 끝에서 요소를 추가하거나 제거할 수 있음.
- 일반적인 Queue 와 Stack 의 기능을 모두 포함.
- 앞에서 값 추가: `offerFirst`
- 뒤에서 값 추가: `offerLast`
- 앞에서 값 꺼내기: `pollFirst`
- 뒤에서 값 꺼내기: `pollLast`


## 참고자료
<hr />

[김영한의 실전 자바 - 중급 2편](https://www.inflearn.com/course/%EA%B9%80%EC%98%81%ED%95%9C%EC%9D%98-%EC%8B%A4%EC%A0%84-%EC%9E%90%EB%B0%94-%EC%A4%91%EA%B8%89-2/dashboard)
