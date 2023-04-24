# 📚 내용

## 📝 동작 파라미터화를 이용하면 변화하는 요구사항에 더 잘 대응할 수 있는 코드를 구현할 수 있음 <br>Why? 동작 파라미터화에서는 메서드 내부적으로 다양한 동작을 수행할 수 있도록, 코드를 메서드 인수로 전달하기에, 실행을 뒤로 미룰 수 있기 때문

### 동작 파라미터화란?
> 동작 파라미터화란? 아직은 어떻게 실행할 것인지 결정하지 않은 코드 블록을 의미함, 이 코드 블록은 나중에 프로그램에서 호출함 <br>ex. 나중에 실행될 메서드의 인수로 코드 블록을 전달 하고, 코드 블럭에 따라 메서드의 동작이 파라미터화 됨

변화하는 요구사항에 대응해야 함

동작 파라미터화를 이용하면 자주 바뀌는 요구사항에 효과적으로 대응할 수 있다. 실행을 뒤로 미룰 수 있기 때문임

동작 파라미터화를 추가하려면 쓸데없는 코드가 늘어나는데, 자바 8은 람다 표현식으로 이 문제를 해결함


### 필요성 예시
```java
enum Color {RED, GREEN}
 
public static List<Apple> filterGreenApples(List<Apple> inventory) {
    List<Apple> result = new ArrayList<>();
    for (Apple apple : inventory) {
        if (GREEN.equals(apple.getColor()) {
            result.add(apple);
        }
    }
    return result;
}
```

녹색 사과만 필터링하는 함수임.

만약 요구 조건이 바뀌어 빨간 사과를 필터링하고 싶으면 if문 안의 조건을 바꿔야함

물론 색을 파라미터화 할 수도 있지만, 색이 아닌 무게도 기준으로 추가하고 싶다면 무게 파라미터도 추가해야함

이런식으로 요구사항이 변경하면 위의 코드는 변경에 유연하게 대처하지 못함

<br>

## 📝 동작 파라미터화로 변화하는 요구사항에 대응하기
참 또는 거짓을 반환하는 함수를 프레디케이트라고 함

선택 조건을 결정하는 인터페이스를 정의해서 변화하는 요구사항에 유연하게 대응해보자
```java
public interface ApplePredicate {
    boolean test (Apple apple);
}
```

### 추상적 조건으로 필터링
filterApples에서 ApplePredicate 객체를 인수로 받아 애플의 조건을 검사하도록 메서드륵 고쳐야 함

이렇게 동작 파라미터화, 즉 메서드가 다양한 동작을 받아서 내부적으로 다양한 동작을 수행할 수 있음

filterApples 메서드 내부에서 컬렉션을 반복하는 로직과 컬렉션의 각 요소에 적용할 동작(예제에서 predicate)를 분리할 수 있다는 점에서 큰 이점임

유연한 코드와 가독성이 좋아짐

```java
public static List<Apple> filterGreenApples(List<Apple> inventory, ApplePredicate p) {
    List<Apple> result = new  ArrayList<>();
    for (Apple apple: inventory) {
        if (p.test(apple)) {
            result.add(apple);
        }
    }
    return result;
}
```

이제 필요한 대로 다양한 ApplePredicate를 만들어서 filterApples 전달할 수 있음

```java
List<Apple> redAndHeavyApples = filterApples(inventory, new AppleRedAndHeavyPredicate());
```

### 익명 클래스
현재는 메서드로 새로운 동작을 전달하려면 ApplePredicate 인터페이스를 구현하는 여러 클래스를 정의한 다음 인스턴스화해야 함. 번거로움

자바는 클래스 선언과 인스턴스화를 동시에 수행할 수 있도록 익명 클래스라는 기법을 제공함

> 익명 클래스 : 이름이 없는 클래스

```java
List<Apple> redApples = filterApples(inventory, new ApplePredicate() {
    public boolean test(Apple apple) {
        return RED.equals(apple.getColor());
    }
});
```

하지만 익명클래스는 부족함. 코드가 장황해짐

### 람다 표현식 사용
람다 표현식을 이용하여, 익명 클래스를 사용한 코드를 간단히 구현할 수 있음

```java
List<Apple> result = filterApples(inventory, (Apple apple) -> RED.equals(apple.getColor()));
```

### 리스트 형식으로 추상화
```java
public interface Predicate<T> {
    booelan test(T t);
}
```

파라미터 T를 사용하여 LIst의 제네릭을 사용하면, 필터 메서드와 대상 객체도 유연하게 지정 할 수 있다.
```java
public static <T> List<T> filter(List<T> list, Predicate<T> p) {
    List<T> result = new ArrayList<>();
    for (T e : list) {
        if(p.test(e)) {
            result.add(e);
        }
    }
    return result;
}
```
이제 사과가 아니더라도 바나나, 오렌지, 정수, 문자열 등의 리스트에도 필터 메서드를 적용할 수 있음

<br>

## 📝 자바 API의 많은 메서드는 정렬, 스레드, GUI 처리등을 포함한 다양한 동작으로 파라미터화할 수 있음
### Comparator로 정렬하기
컬렉션 정렬은 반복되는 작업이다. 요구사항이 쉽게 바뀔 수 있음

자바 8의 List에는 sort 메서드가 포함되어 있는데, java.util.Comparator 객체를 이용해서 sort 동작을 파라미터화 할 수 있음

```java
public interface Comparator<T> {
    int compare(T o1, T o2);
}
```

아래와 같이 익명클래스를 만들어서 전달할 수 있음

```java
inventory.sort(new Comparator<Apple>() {
    public int compare(Apple a1, Apple a2) {
        return a1.getWeight().compareTo(a2.getWeight());
    }
});
```

아래처럼 람다 표현식을 이용할 수도 있음
```java
inventory.sort((Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight()));
```

### Runnable로 코드 블록 실행하기
자바 스레드를 이용하면 병렬로 코드 블록을 실행할 수 있음. 어떤 코드를 실행할 것인지 스레드에게 알려줄 수 있음

자바 8까지는 Thread 생성자에 객체만을 전달할 수 있었으므로 보통 결과를 반환하지 않는 void run 메소드를 포함하는 익명 클래스가 Runnable 인터페이스를 구현하도록 하는 것이 일반적인 방법이었음

자바에서는 Runnable 인터페이스를 이용해서 실행할 코드 블록을 지정할 수 있음
```java
public interface Runnable {
    void run();
}
Thread t = new Thread(new Runnable() {
    public void run() {
        System.out.println("Hello World");
});
```

람다 표현식을 이용해서도 구현가능함
```java
Thread t = new Thread(() -> System.out.println("Hello world"));
```

### Callable을 결과로 반환하기
ExecutorService 인터페이스를 이용하면 태스크를 스레드 풀로 보내고 결과를 Future로 저장할 수 있음

```java
// java.util.concurrent.Callable
public interface Callable<V> {
  V call();
}
```


아래 코드와 같이 실행 서비스에 태스크를 제출해서 위 코드를 활용할 수 있음

```java
ExecutorService executorService = Executors.newCachedThreadPool();
Future<String> threadName = executorService.submit(new Callable<String>() {
  @Override public String call() throws Exception {
    return Thread.currentThread().getName();
  }
});
```

람다를 이용해 다음과 같이 줄일 수 있음
```java
Future<String> threadName = executorService.submit(() -> Thread.currentThread().getName());
```

# 🔎 알게된 점
### ✔️ 동작 파라미터화에서는 메서드 내부적으로 다양한 동작을 수행할 수 있도록, 코드를 메서드 인수로 전달
### ✔️ 동작 파라미터화를 이용하면 변화하는 요구사항에 더 잘 대응할 수 있는 코드를 구현할 수 있음
### ✔️ 코드 전달 기법을 이용하면 동작을 메서드의 인수로 전달 할 수 있음 <br> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 또한, 자바 8에서는 인터페이스를 상속받아 여러 클래스를 구현해야 하는 수고를 없앨 수 있고,<br> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 람다 표현식 사용하여 간단히 코드를 직접 파라미터화 할 수 있음
### ✔️ 자바 API의 많은 메서드는 정렬, 스레드, GUI 처리등을 포함한 다양한 동작으로 파라미터화 할 수 있음
