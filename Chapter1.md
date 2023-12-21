# 📚 내용

## 자바가 멀티코어 병렬성을 더 쉽게 이용할 수 있도록 진화
자바 8에서의 대표적인 변화 예시
``` java
Collections.sort(inventory, new Comparator <Apple>() {
    public int compare(Apple a1, Apple a2) {
        return a1.getWeight().compareTo(a2.getWeight);
    }
});
```
자바 8을 이용하면, 더 간단한 방식으로 코드 구현할 수 있음
``` java
inventory.sort(comparing(Apple::getWeight));
```

<br>

### 1. 스트림 처리
> 스트림 : 한 번에 한 개씩 만들어지는 연속적인 데이터 항목들의 모임

기존에는 한 번에 한 항목을 처리했지만 
<br>이제 자바 8에서는 스트림 API를 통해 우리가 하려는 작업을 고수준으로 추상화하여 일련의 스트림으로 만들어 처리 할 수 있음 
<br> → 스트림 파이프라인을 이용해서 입력 부분을 여러 CPU 코어에 쉽게 할당할 수 있음 
<br> → 공짜로 병렬성을 얻을 수 있음

<br>

### 2. 동작 파라미터화
> 동적 파라미터화 : 코드 일부를 API로 전달하는 기능 (= 메서드를 다른 메서드의 인수로 넘겨주는 기능)

``` java
inventory.sort(comparing(Apple::getWeight));    // comparing(Apple::getWeight) 코드를 정렬 기준으로 전달
```

메서드를 다른 메서드의 인수로 넘겨줌으로써 공유되지 않은 가변 데이터 요구사항이 됨 
<br> 왜? 전달된 메서드가 상호작용을 하지 않기 때문 
<br> → 병렬성을 안전하게 실행할 수 있음

<br>

## 메서드와 람다를 1급 객체로
메서드를 1급 객체로 사용하면, 프로그래머가 활용할 수 있는 도구가 다양해지면서 프로그래밍이 수월해짐
자바 8은 메서드를 값으로 취급할 수 있게 함
> 1급 객체 : 아래의 3가지를 충족하면 1급 객체라고 할 수 있음
> 1. 변수나 데이터에 할당 할 수 있음
> 2. 객체의 인자로 넘길 수 있음
> 3. 객체의 리턴값으로 리턴 할 수 있음
> - ex. Primitive Type, Class

<br>

### 메서드 참조
메서드 참조(method reference)는 새로운 자바 8의 기능임
<br>다음은 디렉토리에서 모든 숨겨진 파일을 필터링하는 코드임
``` java
File[] hiddenFiles = new File(".").listFiles(new FileFilter() (
    public boolean accept(File file) (
        return file.isHidden();
    )
));
```
자바 8에서는 다음과 같이 코드 구현 가능
``` java
File[] hiddenFiles = new File(".").listFiles(File::isHidden);
``` 
자바 8에서는 메서드 참조 :: (= 이 메서드를 값으로 사용하라는 의미)를 이용해서 listFiles에 직접 전달할 수 있음
<br>자바 8에서는 메서드가 일급 객체가 됨
<br>기존에는 객체 참조 (new로 객체 참조를 생성함)를 이용해서 객체를 이리저리 주고 받았던 것 처럼 
<br>자바 8에서는 File::isHidden을 이용해서 메서드 참조를 만들어 전달할 수 있게 됨

<br>

### 람다 함수 = 익명 함수
> 람다 함수 : 익명 함수(Anonymous Functions)를 지칭하는 용어
> <br>익명 함수? 함수의 이름이 없는 함수, 1급 객체라는 특징 가짐 (즉, 함수를 값으로 사용할 수도 있고 파라미터로 전달 및 변수에 대입 가능)

<br>

## 자바 8의 메서드 전달 예제
자바 8 이전
``` java
// 모든 녹색 사과를 선택해서 리스트를 반환하는 프로그램 구현
public static List<Apple> filterGreenApples(List<Apple inventory) {
    List<Apple> result = new ArrayList<>();
    for (Apple apple : inventory) {
        if ("green".equals(apple.getColor())) {
            result.add(apple);
        }
    }
    return result;
}
// 사과를 무게로 필터링 하도록 구현
public static List<Apple> filterHeavyApples(List<Apple> inventory) {
    List<Apple> result = new ArrayList<>();
    for (Apple apple : inventory) {
        if(apple.getWeight() > 150) {
            result.add(apple);
        }
    }
    return result;
}
```
자바 8 이후
<br>자바 8에서는 코드를 인수로 넘겨줄 수 있으므로 filter 메서드를 중복으로 구현할 필요가 없음
``` java
public static boolean isGreenApple(Apple apple) {
    return "green".equals(apple.getColor());
}

public static boolean isHeavyApple(APple apple) {
    return apple.getWeight() > 150;
}

public interface Predicate<T> {
    boolean test(T t);
}

public static List<Apple> filterApples(List<Apple> inventory, Predicate<Apple> p) {
    List<Apple> result = new ArrayList<>();
    for (Apple apple : inventory) {
        if(p.test(apple)) {
            result.add(apple);
        }
    }
    return result;
}
```
다음과 같이 호출
``` java
filterApples(inventory, Apple::isGreenApple);
filterApples(inventory, Apple::isHeavyApple);
```

<br>

### Predicate
filterApples는 Apple::isGreenApple 메서드를 Predciate<Apple>이라는 타입의 파라미터로 받음
<br>인수로 값을 받아 true나 false를 반환하는 함수를 Predicate라고 함

<br>

## 람다 활용
한 두번만 사용할 메서드를 매번 정의하는 것은 번거로움
<br>자바 8에서는 익명 함수 또는 람다라는 새로운 개념을 이용해서 이 문제도 간단히 해결 가능함
``` java
filterApples(inventory, (Apple a) -> "green".equals(a.getColor()));
filterApples(inventory, (Apple a) -> a.getWeight() > 150);
filterApples(inventory, (Apple a) -> a.getWeight() < 80 || "brown".equals(a.getColor()));
``` 
❗️하지만 람다가 몇 줄 이상으로 길어진다면 
<br> &nbsp;&nbsp;&nbsp;&nbsp; 익명 람다 보다는 코드가 수행하는 일을 잘 설명하는 이름을 가진 메서드를 정의하고 메서드 참조를 활용하는 것이 바람직함
<br> &nbsp;&nbsp;&nbsp;&nbsp; 코드의 명확성이 우선시 되어야 함

<br>

## 스트림 API
스트림 API를 이용하면, 컬렉션 API는 상당히 다른 방식으로 데이터를 처리할 수 있음
- 컬렉션에서는 for-each 루프를 이용해서 반복과정을 직접 처리해야 했음 (외부 반복)
- 스트림은 루프를 신경 쓸 필요 없이 라이브러리 내부에서 모든 데이터가 처리 됨 (내부 반복)

컬렉션을 이용할 때, 많은 요소를 가진 목록을 반복한다면 오랜 시간이 걸릴 수 있음
<br>거대한 리스트의 경우 단일 CPU로는 처리하기 힘듦
<br>멀티 코어 환경이라면 CPU 코어에 작업을 각각 할당해서 처리 시간을 줄일 수 있을 것임
<br>

### 멀티 스레딩 어려움
자바 8은 스트림 API로 '컬렉션을 처리하면서 발생하는 반복적인 코드 문제'와 
<br>'멀티 코어 활용 어려움'이라는 두 가지 문제를 모두 해결함
스트림은 자주 반복되는 패턴으로 주어진 조건에 따라
- 데이터를 필터링하거나 : .filter()
- 데이터를 추출 : .map()
- 데이터를 그룹화하는 등의 기능 제공 : .collect()

<br>컬렉션은 어떻게 데이터를 저장하고 접근할지에 중점 두는 반면,
<br>스트림은 데이터에 어떤 계산을 할 것인지 묘사하는 것에 중점 둠

``` java
List<Apple> heavyApples =
    inventory.stream().filter((Apple a) -> a.getWeight() > 150)
                                            .collect(toList());
List<Apple> heavyApples =
    inventory.parallelStream().filter((Apple a) -> a.getWeight() > 150)
                                                    .collect(toList());
```

<br>

# 🔎 알게된 점

### 기존의 자바 프로그래밍 기법으로는 멀티 코어프로세서를 온전히 활용하기 어려움 <br>이를 위해, 자바8은 프로그램을 더 효과적이고 간결하게 구현할수 있도록 스트림 처리, 동적 파라미터가 나옴
- 동적 파라미터를 사용하면, 함수의 매게변수에 메서드를 전달할 수 있으므로, <br>실행될 때 컴포넌트 간의 상호작용이 일어나지 않기 때문에 병렬성을 보장할 수 있게 됨 (전달된 메서드에 대한 접근 불가)

