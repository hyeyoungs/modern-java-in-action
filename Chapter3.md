# 📚 내용
## 람다란 무엇인가?

람다 표현식은 메서드로 전달할 수 있는 익명 함수를 단순화한 것이다.

- **익명** : 보통의 메서드와 달리 이름이 없으므로 익명이라 표현한다.
- **함수** : 람다는 메서드처럼 특정 클래스에 종속되지 않으므로 함수라고 부른다.
- **전달** : 람다 표현식을 메서드 인수로 전달하거나 변수로 저장할 수 있다.
- **간결성** : 익명 클래스처럼 많은 자질구레한 코드를 구현할 필요가 없다.

람다의 구성 요소

- 파라미터 리스트
- 화살표 : 파라미터 리스트와 바디를 구분한다.
- 바디 : 람다의 반환값에 해당하는 표현식

![image](https://github.com/hyeyoungs/modern-java-in-action/assets/29566893/ac52e79d-9ebb-4fee-b78d-77ea98b7b039)

<br>

## 어디에, 어떻게 람다를 사용하는가?

람다 표현식의 예제

- 불린 표현식 : (List<String> list) -> list.isEmpty()
- 객체 생성 : () -> new Apple(10)
- 객체에서 소비 : (Apple a) -> { System.out.println(a.getWeight()); }
- 객체에서 선택/추출 : (String s) -> s.length()
- 두 값을 조합 : (int a, int b) -> a * b
- 두 객체 비교 : (Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight())
- void 리턴 : (int x, int y) → { System.out.println(x + y); }

<br>

## 형식 검사, 형식 추론, 제약

`람다 표현식` 자체에는 람다가 어떤 `함수형 인터페이스`를 구현하는지의 정보가 포함되어 있지 않다. 
<br>따라서 람다 표현식을 더 제대로 이해하려면 람다의 실제 형식을 파악해야 한다.

<br>

### 형식 검사

`List<Apple> heavierThan150g = filter(inventory, (Apple a) -> a.getWeight() > 150);`

1. 람다가 사용된 콘텍스트는 무엇인가? 우선 `filter`의 정의를 확인하다.
2. 대상 형식은 `Predicate`이다.
3. `Predicate`인터페이스의 추상 메서드는 무엇인가?
4. `Apple`을 인수로 받아 `boolean`을 반환하는 `test` 메서드다
5. 함수 디스크립터는 `Apple` → `boolean`이므로 람다의 시그니처와 일치한다. 람다도 `Apple`을 인수로 받아 `boolean`을 반환하므로 코드 형식 검사가 성공적으로 완료된다.

<br>

### 형식 추론

`자바 컴파일러`는 람다 표현식이 사용된 `콘텍스트`를 이용해서 람다 표현식과 관련된 `함수형 인터페이스`를 추론한다. 
<br>결과적으로 `컴파일러`는 람다 표현식의 파라미터 형식에 접할 수 있으므로 `람다 문법`에서 이를 생략할 수 있다.

<br>

### 지역 변수 사용

람다 표현식에서는 익명 함수가 하는 것처럼 `자유 변수`(파라미터로 넘겨진 변수가 아닌 외부에서 정의된 변수)를 활용할 수 있다. 
<br>이를 `람다 캡처링`이라고 부른다.하지만 람다 표현식은 한 번만 할당할 수 있는 `지역 변수`를 캡처할 수 있다. (`final`로 선언된 변수만 사용 가능)
<br>

#### 🤔 왜 지역 변수에 이런 제약이 필요할까?
인스턴스 변수는 힙에 저장되는 반면 지역 변수는 스택에 위치한다.
<br>람다에서 지역 변수에 바로 접근할 수 있다는 가정 하에 람다가 스레드에서 실행된다면 
<br>변수를 할당한 스레드가 사라져서 변수 할당이 해제되었는데도 람다를 실행하는 스레드에서는 해당 변수에 접근하려 할 수 있다.
<br>따라서 자바 구현에서는 원래 변수에 접근을 허용하는 것이 아니라 자유 지역 변수의 복사본을 제공한다.
<br>따라서 복사본의 값이 바뀌지 않아야 하므로 지역 변수에는 한 번만 값을 할당해야 한다는 제약이 생긴 것이다.

<br>

## 메서드 참조

메서드 레퍼런스는 특정 메서드만을 호출하는 람다의 `축약형`이다. 
메서드 레퍼런스를 새로운 기능이 아니라 하나의 메서드를 참조하는 람다를 편리게 표현할 수 있는 문법으로 간주 할 수 있다.

```java
Function<String, Integer> f = (String s) -> Integer.parseInt(s);
// 메서드 참조
Function<String, Integer> f = Integer::parseInt;
```

<br>

## 람다, 메서드 참조 활용하기

> 코드전달 → 익명 클래스 사용 → 람다 표현식 사용 → 메서드 레퍼런스 사용


`Comparator`는 `Comparable` 키를 추출해서 `Comparator` 객체로 만드는 `Function` 함수를 인수로 받는 정적 메서드 `comparing`을 포함한다. 그러므로 다음처럼 사용할 수 있다.

```java
import static java.util.Comparator.comparing;`
inventory.sort(comparing((a) -> a.getWeight()));
```

메서드 레퍼런스를 이하면 더 깔끔하게 표현도 가능하다.

`inventory.sort(comparing(Apple::getWeight));`

<br>

## 람다 표현식을 조합할 수 있는 유용한 메서드

### 역정렬

`비교자 구현`을 그대로 재사용하여 사과의 무게를 기준으로 `역정렬`할 수 있다.

```java
// 무게를 내림차순으로 정렬
Inventory.sort(comparing(Apple::getWeight).reserved());
```

<br>

### Comparator 연결

`thenComparing`은 함수를 인수로 받아 첫 번째 비교자를 이용해서 두 객체가 같다고 판단되면 두 번째 비교자에 객체를 전달한다.

```java
// 무게를 내림차순으로 정렬하고 두 사과의 무게가 같으면 국가별로 정렬할것
inventory.sort(comparing(Apple::getWeight)
         .recered()
         .thenComparing(Apple::getContry));
```

<br>

### Predicate 조합

`Predicate` 인터페이스는 복잡한 `Predicate`를 만들 수 있도록 `negate`, `and`, `or` 세 가지 메서드를 제공한다.

```java
// 빨간색이면서 무거운(150그램 이상) 사과 또는 그냥 녹색 사과

Predicate<Apple> redAndHeavyAppleOrGreen =
  redApple.and(a -> a.getWeight() > 150)
          .or(a -> "green".equals(a.getColor()));
```

<br>

### Function 조합

`Function` 인터페이스는 `andThen`, `compose` 두 가지 디폴트 메서드를 제공한다. `andThen` 메서드는 주어진 함수를 먼저 적용한 결과를 다른 함수의 입력으로 전달하는 함수를 반환한다.

`Function<Integer, Integer> f = x -> x + 1;`

`Function<Integer, Integer> g = x -> x * 2;`

`Function<Integer, Integer> h = f.andThen(g);`

`int result = h.apply(1); // 4`

<br>

`compose` 메서드는 인수로 주어진 함수를 먼저 실행한 다음에 그 결과를 외부 함수의 인수로 제공한다.

`Function<Integer, Integer> f = x -> x + 1;`

`Function<Integer, Integer> g = x -> x * 2;`

`Function<Integer, Integer> h = f.compose(g);`

`int result = h.compose(1); // 3`

<br>

# 🔎 알게된 점

### ✔️ 람다 표현식은 익명 함수의 일종임. 이름은 없지만, 파라미터 리스트, 바디, 반환 형식을 가지며 예외를 던질 수 있음 <br>람다 표현식으로 간결한 코드를 구현할 수 있음

### ✔️ 함수형 인터페이스는 하나의 추상 메서드 만을 정의하는 인터페이스이며, 함수형 인터페이스를 기대하는 곳에서만 람다 표현식을 사용할 수 있음
