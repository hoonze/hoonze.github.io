---
layout: post
title: equals()와 hashCode()
date: 2022-04-21 17:44:00 +0900
categories: [Language, JAVA]
tags: [JAVA, equals, hashCode]
---

이 포스트는 면접 과정에서 받은 "HashMap에서 키가 같은지 어떻게 판단하는가?"라는 질문에서 시작됐다. 이 질문을 받을 당시 "Map의 Key값에는 객체가 들어갈 수 도 있는데 기본 정의된 equals 메서드로는 두 객체가 같은지 판단할 수 없다"라고 생각해 제대로된 답변을 하지 못했다. 이 질문에 대한 해답을 찾고자 공부한 내용을 적어본다.

## equals?

equals에 대한 설명

## hashCode?

hashCode에 대한 설명

## 단순비교

Person이라는 객체가 있고, 이름과 나이가 같다면 동일한 객체인지 판단한다고 가정해보자.

아래는 예제로 사용할 Person 클래스이다.

```java
public class Person {
	private final String name;
	private final int age;
	
	public Person(String name, int age) {
		this.name = name;
		this.age = age;
	}
}
```

이 클래스를 기반으로 이름과 나이가 동일한 p1, p2라는 객체를 만들고 equals 메서드로 비교해보자.

```java
public static void main(String[] args) {
    Person p1 = new Person("김자바", 27);
    Person p2 = new Person("김자바", 27);

    System.out.println(p1.equals(p2));
}
```

결과값 : 

```
false
```

왜 결과값으로 false가 출력이 될까? 그 이유는 두 객체의 주소값이 다르기 때문이다.

`equals`메서드는 참조 비교 메서드로 주소값이 다른 두 객체는 서로 다른 객체로 판단한다.

p1, p2 두 객체를 논리적으로 같은 객체로 판단하기 위해서는 Person 클래스의 `equals`메서드를 재정의 해야한다.

## equals 재정의

equals를 재정의 하기 위해서는 아래와 같은 규약을 반드시 지켜야 한다.

- **반사성(reflexivity)** : null이 아닌 모든 참조 값 x에 대해, **x.equals(x)는 true**다.
- **대칭성(symmetry)** : null이 아닌 모든 참조 값 x,y에 대해 **x.equals(y)가 true면 y.equals(x)도 true**다.
- **추이성(transitivity)** : null이 아닌 모든 참조 값 x,y,z에 대해, **x.equals(y)가 true이고, y.equals(z)도 true면, x.equals(z)도 true**다.
- **일관성(consistency)** : null이 아닌 모든 참조 값 x,y에 대해 **x.equals(y)를 반복해서 호출하면 항상 true를 반환하거나 항상 false를 반환**한다.
- **null-아님** : null이 아닌 모든 참조 값 x에 대해 x.equals(null)은 false다.

예제로 사용하는 Person객체에 `equals` 메서드를 오버라이딩 해준다. 

```java
public class Person {
	private final String name;
	private final int age;
	
	public Person(String name, int age) {
		this.name = name;
		this.age = age;
	}
	
	@Override
	public boolean equals(Object o) {
		if(this == o)
			return true;
		
		if(!(o instanceof Person))
			return false;
		
		Person person = (Person)o;
		
		return person.name.equals(this.name) && Integer.compare(person.age, age) == 0;
	}
}
```

다시 p1, p2 객체를  equals 메서드로 비교해보자.

```java
public static void main(String[] args) {
    List<Person> list = new ArrayList<>();
    Person p1 = new Person("김자바", 27);
    Person p2 = new Person("김자바", 27);

    list.add(p1);
    list.add(p2);

    System.out.println(p1.equals(p2));
    System.out.println(list.size());
}
```

결과값 : 

```
true
2
```

이번에는 `equals`를 재정의했기 때문에 처음에 구상한 조건에 맞게 이름과 나이가 같다면 같은 객체로 판단한다. List에 넣은 결과도 2로 맞게 출력이 된다.

여기서 `equals`메서드의 값이 true인 두 객체를 HashMap에 넣어 같은 Key로 인식시키면 어떻게 될까?

```java
public static void main(String[] args) {
	Map<Person, Integer> map = new HashMap<>();
	Person p1 = new Person("김자바", 27);
	Person p2 = new Person("김자바", 27);
		
	map.put(p1, 1);
	map.put(p2, 1);
		
	System.out.println(map.size());
}
```

결과값 : 

```
2
```

결과값이 2로 나온다. 두 객체가 equals메서드로 같은 객체라도 판단이 되어도 HashMap에서는 p1과 p2를 다른 객체로 판단한다. 이유는 무엇일까?

이유는 **Hash를 사용한** **Collection(HashMap, HashTable, HashSet 등)은 key를 결정할때 hashCode()를 사용하기 때문**이다.

Collection은 객체가 논리적으로 같은지 판단하기 위해 `hashCode`의 return값이 일치하는지 우선 판단한다. 그 이후 `equals`메서드의 return값이 true라면 두 객체가 논리적으로 같다고 판단한다. 따라서 `hashCode` 메서드를 재정의할 필요가 있다.

## hashCode 재정의

문제 해결을 위해 Person 클래스에 `hashCode` 메서드를 재정의해준다.

아래와 같이 `hashCode` 메서드를 재정의해주면 nam과 age의 값을 기반으로 hash값을 생성하게된다.

```java
public class Person {
	private final String name;
	private final int age;
	
	public Person(String name, int age) {
		this.name = name;
		this.age = age;
	}
	
	@Override
	public boolean equals(Object o) {
		if(this == o)
			return true;
		
		if(!(o instanceof Person))
			return false;
		
		Person person = (Person)o;
		
		return person.name.equals(this.name) && Integer.compare(person.age, age) == 0;
	}
	
	@Override
	public int hashCode() {
		return Objects.hash(name, age);
	}
}
```

마지막으로 p1과 p2객체를 HashMap에 넣고 size를 출력해보자

```java
public static void main(String[] args) {
	Map<Person, Integer> map = new HashMap<>();
	Person p1 = new Person("김자바", 27);
	Person p2 = new Person("김자바", 27);
		
	map.put(p1, 1);
	map.put(p2, 1);
		
	System.out.println(map.size());
}
```

결과값 : 

```
1
```

Person 클래스에 주어진 조건에 맞게 `equals` 메서드와 `hashCode`를 재정의하자 HashMap에서도 두 객체가 일치한다고 판단하게 되었다. 이제 HashMap의 Key로 Person 타입을 사용할 수 있게 된다.

## 정리

- `equals`와 `hashCode` 메서드를 재정의함으로서 객체가 논리적으로 일치하는지 판단할 수 있다.
- 어플리케이션의 특성에 따라 다르겠지만 특수한 경우가 아니라면 **equals를 재정의할 땐 hashCode도 반드시 재정의하자.**

## Reference

- https://donghyeon.dev/이펙티브자바/2021/01/04/eqauls를-재정의-하는-방법/

- https://tecoble.techcourse.co.kr/post/2020-07-29-equals-and-hashCode/
