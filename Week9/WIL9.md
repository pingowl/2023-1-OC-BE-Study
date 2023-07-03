# 9주차

## **목표**

자바 8의 람다와 스트림, Optional에 대해 알아봅시다.

**학습 범위 : 스프링 입문을 위한 자바 객체 지향의 원리와 이해 부록 B**

---

### **람다 도입 배경**

**빅데이터**의 분석 및 활용을 위해 **병렬화** 기술(멀티 코어를 활용한 분산 처리) 필요해짐.

자바8에서 병렬화를 위해 **컬렉션**(배열, list, set, map)을 강화하고, 컬렉션을 더 효율적으로 사용하기 위해 **stream**을 강화했다.

**스트림**을 효율적으로 사용—>**함수형 프로그래밍** 변화 —> **람다** —> **인터페이스** 변화 

- **함수형 인터페이스** : 람다를 지원하기 위한 인터페이스
- `parallelStream()` : 스트림 작업 병렬로 처리

<br>

### 람다

**람다 = 변수에 저장 가능한 로직**

람다란 코드블럭이다. 기존에는 코드 블럭은 반드시 메서드 내부에 있어야 했다.

코드 블록인 람다를 메서드의 인자나 반환값으로 사용할 수 있다. 코드 블럭을 변수처럼 사용할 수 있게 됐다.

```java
// 기존 방식 코드 블럭 - 익명 객체 생성

public class B002 {
	public static void main(String[] args) {
		**Runnable r = new Runnable() {
			public void run() {
				System.out.println("Hello Lambda 2!!!");
			}
		};**

		r.run();
	}
}
```

```java
// 람다 사용

public class B003 {
	public static void main(String[] args) {
		**Runnable r = () -> {
			System.out.println("Hello Lambda 3!!!");
		};**

		r.run();
	}
}

// 더 간결하게---------------

public class B004 {
	public static void main(String[] args) {
		**Runnable r = () -> System.out.println("Hello Lambda 4!!!");**

		r.run();
	}
}
```

- 람다 구조 : `(인자목록) -> {로직}`
- 람다에서 로직이 단 한 줄로 표기되는 경우, 블록 기호 {} 생략 가능

<br>

### 함수형 인터페이스

**= 추상 메서드를 하나만 갖고 있는 인터페이스**

함수형 인터페이스만을 람다식으로 변경할 수 있다.

- `@FunctionalInterface` 를 붙이면 컴파일러는 해당 인터페이스가 함수형 인터페이스의 조건(추상 메서드 1개 갖고 있는지)을 검사한다.
    
    ```java
    public class B005 {
    	public static void main(String[] args) {
    		**MyFunctionalInterface mfi = (int a) -> {	return a * a;	};**
    
    		int b = mfi.runSomething(5);
    
    		System.out.println(b);
    	}
    }
    
    **@FunctionalInterface**
    interface MyFunctionalInterface {
    	public abstract int runSomething(int count);
    }
    ```
    

- 람다식에서 타입 추정 가능.
- 파라미터가 1개이고 자료형을 표기하지 않는 경우 소괄호 생략 가능

간소화된 버전 : `MyFunctionalInterface mfi = **a -> a*a;` //** 세미콜론은 블록기호 뒤에 있었던 것임

<br>

### 메서드 호출 인자로 람다 사용하기

#1

```java
public class B007 {
	public static void main(String[] args) {
		MyFunctionalInterface mfi = a -> a * a;

		doIt(mfi);
	}

	public static void doIt(MyFunctionalInterface mfi) {
		int b = mfi.runSomething(5);

		System.out.println(b);
	}
}
```

#2

람다식을 1번만 사용한다면 변수에 할당할 필요 없으므로

```java
public static void main(String[] args) {
		doIt(a -> a * a);
}

public static void doIt(MyFunctionalInterface mfi) {
```

<br>

### 메서드 반환값으로 람다 사용하기

```java
public class B009 {
	public static void main(String[] args) {
		MyFunctionalInterface mfi = todo();

		int result = mfi.runSomething(3);

		System.out.println(result);
	}

	public static MyFunctionalInterface todo() {
		return num -> num * num;
	}
}
```

<br>

### 자바8에서 제공하는 함수형 인터페이스

`java.util.function` 패키지 등에서 기능 제공함


제공 인터페이스 전체 정보 : [링크](https://docs.oracle.com/javase/8/docs/api/java/util/function/package-summary.html)

이중에서 필요한 게 없다면 `사용자 정의 함수형 인터페이스`를 정의해서 사용하면 된다.

<br>

### 컬렉션 스트림에서 람다 사용하기

```java
Integer[] ages = { 20, 25, 18, 27, 30, 21, 17, 19, 34, 28 };

// 1. for each 구문 사용
for (int age : ages) {
		if (age < 20) {
			System.out.format("Age %d!!! Can't enter\n", age);
		}
}

// 2. 컬렉션 스트림 이용
Arrays.stream(ages)
			.filter(age -> age < 20)
			.forEach(age -> System.out.format("Age %d!!! Can't enter\n", age));
}
```

- 배열을 이용해서 스트림을 얻으려면 Arrays 클래스의 stream() 정적 메서드를 사용한다.
- `filter` 메서드는 SQL구문에서의 where 절과 같은 역할한다. `Predicate` 함수형 인터페이스를 filter 메서드의 인자로 제공하면 된다.
- `forEach` 메서드는 스트림 내부 반복을 실행한다. forEach 구문은 `Consumer` 즉 전달된 인자를 소비하는 함수형 인터페이스가 필요하다.
- 스트림은  `메서드 체인 패턴`을 이용해서 모든 중간 연산을 다시 스트림을 반환해서 코드를 간략하게 작성할 수 있다.
- 스트림은 요구사항을 **선언적으로 코딩**할 수 있게 한다.  의사소통 내용이 그대로 코드로 구현되는 것이 선언적 프로그래밍.
- 추가) 스트림은 **map**, **집계함수**(sum, count, average, min, max), **grouping** 등 많은 메서드를 지원한다.
    
    ```java
    	Integer[] ages = { 20, 25, 18, 27, 30, 21, 17, 19, 34, 28 };
    
    	System.out.println(Arrays.stream(ages).count());
    ```
    

<br>

### 메서드 레퍼런스

```java
// 람다식
Arrays.stream(ages).sorted().forEach(**age-> System.out.println(age)**);

// 메서드 레퍼런스
Arrays.stream(ages).sorted().forEach(**System.out::println**);
```

메서드 레퍼런스는 `인스턴스::인스턴스메서드` , `클래스::정적메서드` ,  `클래스::인스턴스메서드` 3가지 형태로 사용할 수 있다. 

<br>

### 생성자 레퍼런스

`클래스::new`

<br>

### **Optional**

Java8에서는 Optional<T> 클래스를 사용해 NPE(NullPointerException)를 방지할 수 있도록 도와준다. 

Optional<T>는 null이 올 수 있는 값을 감싸는 Wrapper 클래스로, 참조하더라도 NPE가 발생하지 않도록 도와준다.

** [참고자료](https://homoefficio.github.io/2019/10/03/Java-Optional-%EB%B0%94%EB%A5%B4%EA%B2%8C-%EC%93%B0%EA%B8%B0/)