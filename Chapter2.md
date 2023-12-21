# 📚 내용

## 동작 파라미터화

### 익명 클래스
메서드로 새로운 동작을 전달하려면 ApplePredicate 인터페이스를 구현하는 여러 클래스를 정의한 다음 인스턴스화해야 함. 번거로움

자바는 클래스 선언과 인스턴스화를 동시에 수행할 수 있도록 익명 클래스라는 기법을 제공함

> 익명 클래스 : 이름이 없는 클래스

```java
List<Apple> redApples = filterApples(inventory, new ApplePredicate() {
    public boolean test(Apple apple) {
        return RED.equals(apple.getColor());
    }
});
```

하지만 익명클래스는 부족함. 여전히 코드가 장황함

<br>

### 람다 표현식 사용
람다 표현식을 이용하여, 익명 클래스를 사용한 코드를 간단히 구현할 수 있음

```java
List<Apple> result = filterApples(inventory, (Apple apple) -> RED.equals(apple.getColor()));
```

<br>

### 리스트 형식으로 추상화
```java
public interface Predicate<T> {
    booelan test(T t);
}
```

파라미터 T를 사용하여 List의 제네릭을 사용하면, 필터 메서드와 대상 객체도 유연하게 지정 할 수 있다.
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

## 자바 API의 많은 메서드는 정렬, 스레드, GUI 처리등을 포함한 다양한 동작으로 파라미터화할 수 있음
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

<br>

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

<br>

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

<br>

# 🔎 알게된 점
### ✔️ 동작 파라미터화에서는 메서드 내부적으로 다양한 동작을 수행할 수 있도록, 코드를 메서드 인수로 전달
### ✔️ 자바 8에서는 람다 표현식 사용하여 간단히 코드를 직접 파라미터화 할 수 있음
### ✔️ 자바 API의 많은 메서드는 정렬, 스레드, GUI 처리등을 포함한 다양한 동작으로 파라미터화 할 수 있음
