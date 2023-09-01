---
title: "API 디자인 10개 체크 리스트(Java 8)"
layout: post
date: 2023-08-30 08:26
tags: [java, skill]
star: false
category: blog
author: gyuhwan
description: API 디자인에 대한 10가지 정보
toc: true
toc_sticky: true
excerpt: "API 디자인에 대한 10가지 정보"

---
# API 디자인 10개 체크리스트(Java 8)

API를 설계함에 있어서 가장 중요한 것은 **사용자 친화적**인가 입니다. 사용자 친화적이란, 읽기 쉽고 해석하기 쉬우며 실행의 결과가 예측이 가능한 것을 의미합니다.

좋은 API 디자인을 위해서는 깊은 생각과 많은 경험이 필요합니다.  API가 오픈되고 사람들이 사용하게 되면 수정하기가 어렵기 때문에 처음 시작부터 올바르게 작성하는게 중요합니다.

확고하며 유연성있게 구현하면 API 설계자나 사용자 모두 도움이 됩니다. 그러기 위해선 **클라이언트의 관점에서 생각**하고 **단순**하며 **사용하기 편하며** **일관적**으로 작성해야 합니다. 동시에 내부 구현 정보는 숨겨서 캡슐화를 유도해야 합니다.



# 1. **값이 없을 때 절대 null 값을 반환하지 말자.**

API에 null 값을 반환한다고 해봅시다. 그러면 클라이언트는 유효성 검사를 위해 null 값 처리 로직을 작성할 것입니다. 만약 클라이언트가 이 검증을 잊게 된다면 예상치 못한 곳에서 **NullPointException**이 발생하게 됩니다.  그러니 null 값을 반환하는 대신 방법으로는 반환 값으로 **Optional**을 사용합시다. `Optional.ofNullable(value)` 를 사용한 뒤 value에 null 값이 들어온다면 비어있는 Optional을 반환하게 됩니다.

Optional을 사용함으로써 **클라이언트에게 값이 있거나 비어 있을 수 있다고 메시지를 전달 할 수 있습니다**

**Prefer :**

```java
public Optional<String> getComment() {
	return Optional.ofNullable(comment);
}
```

**Avoid :**

```java
public String getCommnet() {
	return comment;
}
```



# 2. **반환 값들을 배열에 담지 말자.**

*Java 5*에 처음 소개된 *Enum* 에는 큰 실수가 있었습니다. 바로 **values()** 메소드인데. 이 메서드를 호출하면 value들을 배열에 담아서 반환해 주게 됩니다. 배열은 불변을 보장하지 않기 때문에 이를 보완하기 위해 자바 프레임워크에서는 배열을 새로 생성하여 복사해주는 작업을 추가로 진행합니다.

이 결과 성능은 하락하고 사용성이 낮아졌습니다. 만약 values() 의 반환 값이 **unmodifiable List** 였다면 사용성이 올라갔을 것입니다. 가능하면 배열보다는 **Stream**으로 감싸서 반환하는게 좋습니다. **Stream을 반환한다는 것은 read-only 라는 것을 분명히 표현하게 됩니다**.

또 다른 자료구조를 쉽게 가져와서 사용할 수 있으니 유연함도 가지고 있습니다.

**Prefer :**

```java
public Stream<String> getComment() {
	return Stream.of(comments);
}
```

**Avoid :**

```java
public String[] commnets() {
	return comments;
}
```



# 3. **객체 생성에는 정적 인터페이스 메소드를 제공하자.**

API의 클라이언트들이 클래스의 구현체를 직접 지정하는 것은 피하는게 좋습니다. 클라이언트가 객체를 생성하는데 구현체를 직접 결정하게 되면 API와 클라이언트와의 결합도가 증가하게 됩니다. 구현체가 퍼지게 되면 관리해야 할 대상이 늘어나게 됩니다. 구현체의 수정이 어려워지게 됩니다.

그러니 **정적 인터페이스 메소드**를 제공하는 것이 좋습니다. 클라이언트에게 클래스를 구현하려면 이 정적 인터페이스 메소드만 사용하도록 허락하는 것입니다. 그렇게 하면 관리의 범위가 줄어들고 구현체의 교체를 손쉽게 할 수 있게 됩니다 (캡슐화)

예를 들어, 내부 메소드가 int x(), int y() 가 있는 Point 인터페이스가 있다고 해봅시다. 그러면 외부에 노출할 정적 메소드는 **Point.of(int x, int y)** 로 만들어 줍니다.

**Prefer :**

```java
Point point = Point.of(1,2)
```

**Avoid :**

```java
Point point = new Pointimpl(1,2);
```



# 4. **Optional 값이 없을 때**

Optional로 감싼 값을 가져올 때, `isPresent()-get()` 보다는 `orElse() , orElseThrow(), orElseGet()` 를 사용하는게 좋습니다.  `orElse()`의 경우, orElse(value) 를 사용하게 되면 값의 존재 유무와 상관없이 value를 생성합니다.

`orElseGet(supplier)` 는 함수형 인터페이스 이기 때문에 값이 없을 때만 내부 supplier가 호출됩니다. 그렇기 때문에 성능상 더 좋습니다.

예시 ) `orElse(Collections.emptyList()); or orElseGet(Collections::emptyList)`

예시로는 Collectiot을 들었지만 가능하면 Optional에 Collection을 넣지 맙시다. 또 단순히 `orGet()` 메서드만 사용하지 맙시다.

**Prefer :**

```java
Optional<String> status = ...;
status.orElse(USER_STATUS);
```

**Avoid :**

```java
Opitonal<String> status = ...;
if (status.isPresent()) {
    return status.get();
} else {
    return USER_STATUS;
}
```



# 5. **상속보다는 함수형 인터페이스나 람다를 사용하는게 좋다.**

클라이언트에 의해 추상이나 기본 클래스를 노출하는 것은 좋지 않습니다. 가능하면 API 상속을 허용하지 않게 하고 정적 인터페이스를 제공해주는 것이 좋습니다.

예를 들어, Public API 클래스인 AbstractReader의 추상 메서드인 `void handleError(IOException ioe)` 를 직접 재정의하는 것보다는 `Consumer<IOException>` 을 가져와서 정적 메서드를 사용하는 것이 좋습니다.

**Prefer :**

```java
Reader reader = Reader.builder()
		.withErrorHandler(IOException::printStackTrace)
		.build();
```

**Avoid :**

```java
Reader reader = new AbstractReader() {
    @Override
    public void handleError(IOException ioe) {
        ioe. printStackTrace();
    }
};
```



# 6. **함수형 인터페이스에는 @FunctionalInterface 애너테이션을 붙여주자**

**@FunctionalInterface**를 붙여줌으로써 API 사용자에게 람다식으로 구현할 수 있도록 표현할 수 있으며 또 추상 메서드가 추가되는 것을 막을 수 있습니다.

**Prefer :**

```java
@FunctionalInterface
public interface CircleSegmentConstructor {
    CircleSegment apply(Point cntr, Point p, double ang);
    // abstract methods cannot be added
}
```

**Avoid :**

```java
public interface CircleSegmentConstructor {
    CircleSegment apply(Point cntr, Point p, double ang);
    // abstract methods may be accidently added later
}
```



# 7. **오버로딩에는 함수형 인터페이스 사용을 피하라**

만약 동일한 메서드 명으로 함수형 인터페이스를 매개변수로 받는게 여러 가지 존재한다면 람다 모호성이 발생할 수 있습니다.

예를 들어,  아래의 코드를 살펴봅시다.

**Avoid :**

```java
public interface Point {
    add(Function<Point, String> renderer);
    add(Predicate<Point> logCondition);
}
```

위의 코드를 **point.add(p → p + “ lambda”)** 로 호출하게 되면 컴파일러는 어떤 메서드를 선택해야 할지 결정하지 못하여 에러가 발생할 수 있습니다.  그러니 오버로딩보다는 명확한 메서드명으로 표현하는 것이 좋습니다.

**Prefer :**

```java
public interface Point {
    addRenderer(Function<Point, String> renderer);
    addLogCondition(Predicate<Point> logCondition);
}
```



# 8. **인터페이스에 과도한 default Method 는 피하라**

default Method는 인터페이스에 쉽게 추가할 수 있으며 필요할 경우 아주 합리적인 선택입니다. 상속받을 구현체들에게 제공할 기본 메서드로서 필요할 때 사용한다면 아주 좋은 선택이 될 것입니다.

하지만 확실하지 않거나 불필요한 경우에는 사용하면 안됩니다. 차라리 별도의 유틸리티 클래스를 생성하거나 아예 구현 클래스에 옮겨주는게 좋습니다.

**Prefer :**

```java
public interface Line {
    Point start();
    Point end();
    int length();
}
```

**Avoid :**

```java
public interface Line {
    Point start();
    Point end();
    default int length() {
        int deltaX = start().x() - end().x();
        int deltaY = start().y() - end().y();

    return (int) Math.sqrt(
        deltaX * deltaX + deltaY * deltaY
        );
    }
}
```



# 9. **API 메소드가 실행될 때 유효성 검사를 먼저 시작하라**

만약 유효성 검사를 대충하거나 안하게 되면 이로 인해 만들어진 오류는 나중에 발생하여 원인을 찾기 힘들어집니다.

매개변수가 사용되기 전에 null 값 확인과 제약 조건에 맞는지 꼭 확인합시다. null 값을 확인하기 위해 `Objects.requireNonNull()` 사용을 권장합니다.

**Prefer :**

```java
public void addToSegment(Segment segment, Point point) {
    Objects.requireNonNull(segment);
    Objects.requireNonNull(point);
    segment.add(point);
}
```

**Avoid :**

```java
public void addToSegment(Segment segment, Point point) {
    segment.add(point);
}
```



# 10. **스트림 파이프라인은 한줄 씩 자리하라.**

API 사용자로부터 오류를 확인할 때, Stream 파이프라인이 한 줄로 표현되는 것보다 별도의 줄로 표현되는게 원인을 파악하기 더 쉬우며 가독성을 올려주게 됩니다.

**Prefer :**

```java
Stream.of("this", "is", "secret") 
  .map(toGreek()) 
  .map(encrypt()) 
  .collect(joining(" "));
```

**Avoid :**

```java
Stream.of("this", "is", "secret").map(toGreek()).map(encrypt()).collect(joining(" "));
```



### 참조

[API Design With Java 8 - DZone Java](https://dzone.com/articles/the-java-8-api-design-principles)

[26 Reasons Why Using Optional Correctly Is Not Optional - DZone Java](https://dzone.com/articles/using-optional-correctly-is-not-optional)