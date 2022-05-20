---
layout: post
title: Stream API
date: 2022-05-20 16:50:00 +0900
categories: [Language, JAVA]
tags: [JAVA, Stream API]
---

## Stream API란?

## Strem API 특징

- Immutable(불변)하므로 **원본의 데이터를 변경하지 않는다.**
- 재사용이 불가능하다. 즉, **일회용이다.**
- 내부 반복(internal iteration)으로 작업을 처리한다.
- filter-map 기반의 API를 사용하여 **지연(lazy) 연산을 통해 성능을 최적화한다.**

### 1. 원본의 데이터를 변경하지 않는다.

 스트림은 데이터를 읽는 것이므로 원본 데이터, 즉 데이터 소스를 변경하지 않는다.

스트림 연산을 통해서 아무리 데이터를 조작한다 해도 스트림에서는 데이터를 변경하지 않는다.

예를 들어 아래와 같이 1 ~ 7의 정수 값이 역순으로 들어간 리스트인 list가 있다. 이 리스트를 Stream을 사용해 역순으로 정렬해 sortedList를 만들더라도 기존의 리스트의 데이터 순서는 변하지 않는 것을 확인할 수 있다.

```java
List<Integer> list = Arrays.asList(7,6,5,4,3,2,1);
List<Integer> sortedList = list.stream().sorted().collect(Collectors.toList());
		
System.out.println("list = " + list);
System.out.println("sortedList = " + sortedList);
```

결과값 : 

``` 
list = [7, 6, 5, 4, 3, 2, 1]
sortedList = [1, 2, 3, 4, 5, 6, 7]
```

### 2. 일회용이다.

Stream은 데이터를 모두 읽고나면 사라지게 되어 재사용이 불가능하다. 동일한 Stream이 다시 필요할 경우 다시 생성해주어야 한다.

아래와 같이 Stream을 재사용하게 될 경우 IllegalStateException이 발생한다.

``` java
List<Integer> list = Arrays.asList(7,6,5,4,3,2,1);
Stream<Integer> stream = list.stream();

int cnt = (int) stream.count();
System.out.println(stream.count());
```

결과값 : 

``` 
Exception in thread "main" java.lang.IllegalStateException: stream has already been operated upon or closed ...
```

### 3. 내부 반복으로 작업을 처리한다.

Stream API는 메소드 내부에 반복 문법을 숨기고 있기 때문에 간결한 코드 작성이 가능하다. 즉, 기존에 사용하던 foreach와 while문을 직접 작성할 필요가 없다.

``` java
List<Integer> list = Arrays.asList(1,2,3,4,5,6,7);

// 기존의 방식
for(int i = 0; i < list.size(); i++) {
    System.out.println(i);
}

// Stream API를 사용한 방식
list.stream().forEach(System.out::println);
```

## Stream API 동작 흐름

Stream은 크게 세가지 단계에 걸쳐 동작한다.

1. 스트림 생성 : 스트림 생성
2. 스트림 중간 연산 : 원본의 데이터를 별도의 데이터로 가공하는 중간 연산
3. 스트림 최종 연산 : 가공된 데이터로 원하는 결과를 만들어내는 최종 연산

### 1. 생성

스트림 API는 다양한 데이터 소스 (컬렉션, 배열, 가변 매개변수, 난수, 람다 표현식, 파일, 빈 스트림 등)에서 생성할 수 있다.

1-1. 컬렉션 Stream

컬렉션 타입의 경우, 인터페이스에 추가된 default method인 `stream()`를 사용해 생성한다.

``` java
List<String> list = Arrays.asList("a","b","c","d","e");
Stream<String> stream = list.stream();
```

1-2. 배열 Stream

배열 Stream은 `Arrays.stream()` 메서드와 `Stream.of()` 메서드를 사용해 생성한다.

``` java
String[] arr = {"a","b","c","d","e"};

Stream<String> arrStream = Stream.of(arr);
Stream<String> arrStream = Arrays.stream(arr);
Stream<String> arrStream = Arrays.stream(arr, 0, 3); // 0 ~ 2 요소 [a, b, c]
```

1-3.  빈 Stream

빈 Stream은 `empty()`메서드를 활용해 생성할 수 있다.

``` java
Stream<Object> stream = Stream.empty();
```

1-4. 가변 매개변수 Stream

`of()`메서드를 활용해 가변적인 크기의 스트림을 생성할 수 있다.

``` java
Stream<Double> stream = Stream.of(4.2, 2.5, 3.1, 1.9);
```

1-5. 원시 Stream

int, long, double 같은 원시 자료형을 위한 스트림이 존재한다. `of()` 메서드와 `range()`메서드를 사용해 생성할 수 있다.

``` java
LongStream longStream = LongStream.of(100L);
IntStream intStream = IntStream.range(1, 10);
```

1-6. 이외

이외에도 난수, 파일, 람다 표현식 스트림 등이 있다.

``` java
// JAVA 8버전에서 추가된 Random 클래스를 사용한 난수 stream
IntStream intStream = new Random.ints(10) // int형 난수 10개 생성
    
// 파일 Stream
String<String> stream = Files.lines(Path path);

// 람다 Stream
IntStream stream = Stream.iterate(2, n -> n + 2); // 2, 4, 6, 8, 10, ...
```

2. 중간 연산

## Reference

- [https://mine-it-record.tistory.com/477](https://mine-it-record.tistory.com/477)
- [https://mangkyu.tistory.com/112?category=872426](https://mangkyu.tistory.com/112?category=872426)
- [https://pamyferret.tistory.com/43](https://pamyferret.tistory.com/43)
- [https://gre-eny.tistory.com/191](https://gre-eny.tistory.com/191)
- [http://www.tcpschool.com/java/java_stream_creation](http://www.tcpschool.com/java/java_stream_creation)
