---
title: 1 제네릭 (Generic) - 2
date: 2025-03-23 15:25:00 +0900
categories: [Study, Java-mid2]
tags: [study, java, mid2]
pin: true
---

## 자바 중급2 git 주소
<hr />

- <https://github.com/chj-dev/java-mid2>


## 타입 매개변수 제한
<hr />

- 일반적인 제네릭을 사용하면, 타입 인자의 메서드를 사용하지 못한다. 어떤 타입이 들어올지 알 수 없으니 당연하다.
- 자바 입장에선 모든 객체의 최종 부모인 Object 타입으로 가정하기 때문에, Object 가 제공하는 메서드만 호출할 수 있다.
- 그럼에도 사용하고 싶을 땐 어떻게 할 수 있을까?

### 상한 제한

- `<T extends Animal>` 과 같이 사용하면 Animal 클래스와 그 자식만 받을 수 있도록 제한을 둘 수 있게 된다.
- 만약 전혀 다른 타입이 들어올 경우 컴파일 오류가 발생한다.
- 제한을 둘 경우 제한한 타입의 기능(메서드)를 사용할 수 있다.

### 타입 추론

1. 메소드 파라미터에 넘겨진 인자들의 타입으로 타입 파라미터의 타입을 추론
```java
public static <K, V> boolean methodName(K key, V value) {
    return true;
}
boolean result = GenericMethods.<String, Integer>methodName("key", 3);
boolean result = GenericMethods.methodName("key", 3);
```
2. 메소드의 반환 타입으로 타입 파라미터의 타입을 추론
```java
public static <T> List<T> emptyList() {
    return new ArrayList<T>();
}
List<Integer> list = GenericMethods.<Integer>getList();
List<Integer> list = GenericMethods.getList();
```


## 제네릭 메서드 (Generic Method)
<hr />

- 특정 메서드 단위로 제네릭을 도입할 때 사용한다.
- 메서드의 반환 타입 앞에 다이아몬드 기호를 사용하여 타입 매개변수를 적어서 사용한다.
```java
public <T extends Integer> T genericMethod(T t) {}

GenericMethod.<Integer>genericMethod(1);
```

### static 메서드

- 제네릭 타입은 static 메서드에 타입 매개변수를 사용할 수 없다.
- 제네릭 타입은 객체 생성 시점에 타입이 정해지지만, static 메서드는 인스턴스 단위가 아니라 클래스 단위로 동작하기 때문이다.
- 따라서 static 메서드는 제네릭 메서드를 사용해서 적용해야 한다.
```java
class GenericTest<T> {
  T instanceMethod(T t) {} //가능
  static T staticMethod(T t) {} //제네릭 타입의 T 사용 불가
}
```

## 와일드카드 (wildcard)
<hr />

- `*`, `?` 와 같이 하나 이상의 문자들을 상징하는 특수 문자를 뜻한다.
- 여러 타입이 들어올 수 있다.

### 상한 제한

- `? extends Animal` 과 같이 상한 제한이 가능하다.
- `Animal` 과 그 하위 타입만 받을 수 있게 된다.
  - ex. Animal, Dog, Cat...
- `Animal` 타입의 기능(메서드)를 사용할 수 있다.

### 하한 제한

- `? super Animal` 과 같이 하한 제한이 가능하다.
- `Animal` 을 포함한 상위 타입만 입력 받을 수 있게 된다.
  - ex. Animal, Object

### 제네릭 메서드 vs 와일드카드

- 와일드카드는 단순히 매개변수로 제네릭 타입을 받을 수 있는 것 뿐이다. 반환타입으로는 지정이 불가하다.
  - 와일드카드는 전달한 타입을 명확하게 반환하는 것이 불가하다.
  - 필요할 경우, 상한 제한 등을 사용할 경우 제한을 둘 때 명시한 타입으로 반환해야 한다.
- 제네릭과는 다르게 하한 제한도 가능하다.


## 타입 이레이저
<hr />

- 제네릭 타입은 컴파일 단계에서만 사용되고, 이후에는 제네릭 정보가 삭제된다. => 타입 이레이저.
- .class 파일은 제네릭이 아닌 Object 로 변환되어 작동한다.
- 즉 제네릭은 개발자가 직접 캐스팅 해야 하는 코드를 컴파일러가 대신 처리해주는 것이다.

### 타입 이레이저 방식의 한계

- **제네릭은 런타임에 타입을 활용하는 코드는 작성할 수 없다.**

```java
class EraserTest<T> {
  public boolean instanceCheck(Object param) {
    return param instanceof T; // error
  }
  
  public T create() {
    return new T(); // error
  }
}
```
- 타입 이레이저의 특징으로, 위 소스 코드는 런타임에 모두 `T`가 `Object`로 변하게 된다.
- 따라서 instanceCheck 의 결과는 항상 참, create 는 항상 Object 를 생성하게 된다.
- 개발자가 의도한 것과는 다르게 동작한다.


## 참고자료
<hr />

[김영한의 실전 자바 - 중급 2편](https://www.inflearn.com/course/%EA%B9%80%EC%98%81%ED%95%9C%EC%9D%98-%EC%8B%A4%EC%A0%84-%EC%9E%90%EB%B0%94-%EC%A4%91%EA%B8%89-2/dashboard)   
[자바 제네릭 메소드 선언 방법과 타입 추론](https://thecodinglog.github.io/java/2020/12/14/java-generic-method.html)
