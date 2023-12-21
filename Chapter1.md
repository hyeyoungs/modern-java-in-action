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
<br>기존에는 객체 참조 (new로 객체 참조를 생성함)를 이용해서 객체를 이리저리 주고 받았던 것 처럼 자바 8에서는 File::isHidden을 이용해서 메서드 참조를 만들어 전달할 수 있게 됨

<br>

### 람다 함수 = 익명 함수
> 람다 함수 : 익명 함수(Anonymous Functions)를 지칭하는 용어
> <br>익명 함수? 함수의 이름이 없는 함수, 1급 객체라는 특징 가짐 (즉, 함수를 값으로 사용할 수도 있고 파라미터로 전달 및 변수에 대입 가능)

<br>

### 자바 8의 메서드 전달 예제
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


## 디폴트 메서드라는 새로운 자바 8의 기능을 인터페이스, 라이브러리의 간결성 유지 및 재컴파일을 줄이는 데 어떻게 활용할 수 있는지

> 디폴트 메서드 = 메서드 본문은 클래스 구현이 아니라 인터페이스의 일부로 포함
1. 자바8은 기존의 구현하지 않아도 되는 메서드를 인터페이스에 추가할 수 있는 기능을 제공함 <br>왜? 인터페이스를 쉽게 바꿀 수 있게 하기 위함 <br>→ 디폴트 메서드를 이용하면 기존의 코드를 건드리지 않고도 원래의 인터페이스 설계를 자유롭게 확장할 수 있음
2. 자바9에서 모듈을 이용해 시스템의 구조를 만들 수 있음 <br>모듈 덕분에 JAR같은 컴포넌트에 구조를 적용할 수 있으며 문서화와 모듈 확인 작업이 용이해짐

모듈을 이용해 시스템의 구조를 만들 수 있고 디폴트 메소드를 이용해 기존 인터페이스를 구현하는 클래스를 바꾸지 않고도 인터페이스를 변경할 수 있음

<br><br>

# 🔎 알게된 점

### 기존의 자바 프로그래밍 기법으로는 멀티 코어프로세서를 온전히 활용하기 어려움 <br>이를 위해, 자바8은 프로그램을 더 효과적이고 간결하게 구현할수 있도록 스트림 처리, 동적 파라미터가 나옴
- 동적 파라미터를 사용하면, 함수의 매게변수에 메서드를 전달할 수 있으므로, <br>실행될 때 컴포넌트 간의 상호작용이 일어나지 않기 때문에 병렬성을 보장할 수 있게 됨 (전달된 메서드에 대한 접근 불가)

