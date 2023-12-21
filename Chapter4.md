# 📚 내용
## 스트림이란 무엇인가?
스트림을 이용하면 선언형 (= 데이터를 처리하는 임시 구현 코드 대신 질의로 표현하는 방식)으로 데이터를 처리할 수 있다.
<br>또한 멀티스레드 코드를 구현하지 않아도 데이터를 투명하게 병렬로 처리할 수 있다.

```java
public static List<String> getLowCaloricDishesNamesInJava7(List<Dish> dishes) {
    List<Dish> lowCaloricDishes = new ArrayList<>();
    for (Dish d : dishes) {
      if (d.getCalories() < 400) {
        lowCaloricDishes.add(d);
      }
    }
    List<String> lowCaloricDishesName = new ArrayList<>();
    Collections.sort(lowCaloricDishes, new Comparator<Dish>() {
      @Override
      public int compare(Dish d1, Dish d2) {
        return Integer.compare(d1.getCalories(), d2.getCalories());
      }
    });
    for (Dish d : lowCaloricDishes) {
      lowCaloricDishesName.add(d.getName());
    }
    return lowCaloricDishesName;
 }
```
```java
public static List<String> getLowCaloricDishesNamesInJava8(List<Dish> dishes) {
    return dishes.stream()
        .filter(d -> d.getCalories() < 400)
        .sorted(comparing(Dish::getCalories))
        .map(Dish::getName)
        .collect(toList());
}
```
- 놀랄정도로 많은 코드가 줄어듬. 자바 8의 경우 세부 구현은 라이브러리 내에서 모두 처리함
- "질의"를 이용하여 컬렉션 데이터를 검색하고 추출하고 병합하여 데이터를 만들었음
- 이제 filter, sorted, map, collect.... 와 같은 고수준 빌딩 블록으로 특정 스레딩 모델에 제한되지 않고 자유롭게 어떤 상황에서든 질의문을 이용하여 데이터를 빠르게, 투명하게 뽑아낼 수 있음


자바 8의 스트림은 다음과 같은 특징을 가짐
- 선언형 : 더 간결하고 가독성이 좋아진다
- 조립할 수 있음 : 유연성이 커진다
- 병렬화 : 성능이 좋아진다

<br>

### 스트림이란?
> 스트림 : 데이터 처리 연산을 지원하도록 소스에서 추출된 연속된 요소

![image](https://github.com/hyeyoungs/modern-java-in-action/assets/29566893/0b3cc054-25a1-4009-bcbb-edcb6802d227)

![image](https://github.com/hyeyoungs/modern-java-in-action/assets/29566893/ea50437a-164e-43a4-b10b-e3a2684dbe58)

- filter : 람다를 인수로 받아 스트림에서 특정 요소를 제외시킴
- map : 람다를 이용해서 한 요소를 다른 요소로 변환하거나 정보를 추출함
- limit : 정해준 개수 이상의 요소가 스트림에 저장되지 못하게 스트림 크기를 축소함
- collect : 다양한 변환 방법을 인수로 받아 스트림에 누적된 요소를 특정 결과로 변환함

<br>

```java
package modernjavainaction.chap04;
 
import static java.util.stream.Collectors.toList;
import static modernjavainaction.chap04.Dish.menu;
 
import java.util.List;
 
public class HighCaloriesNames {
 
  public static void main(String[] args) {
    List<String> names = menu.stream()
        .filter(dish -> dish.getCalories() > 300)
        .map(dish -> Dish::getName)
        .limit(3)
        .collect(toList());
    System.out.println(names);
  }
 
}
```

<br>

### 스트림의 특징
- 파이프 라이닝
  스트림 연산은 연산끼리의 거대한 파이프라인을 형성할 수 있음. 그 덕분에 게으름, 쇼트 서킷 같은 최적화도 이룰 수 있음
- 내부 반복
  반복자를 이용해서 명시적으로 반복하는 컬렉션과 달리 스트림은 내부 반복을 지원함
- 스트림을 사용하면 루프나 if 조건문 등 제어 블록을 사용할 필요가 없음
- 선언형으로 데이터를 처리할 수 있었음
- 스트림 라이브러리에서 필터링(filter), 추출(map), 축소(limit) 기능을 제공하므로 직접 기능을 구현할 필요가 없음
- 결과적으로 스트림 API는 파이프라인을 더 최적화 할 수 있는 유연성을 제공

<br>

## 스트림과 컬렉션
### 스트림과 컬렉션의 차이
컬렉션과 스트림의 가장 큰 차이는 "데이터를 언제 계산하느냐"

- 컬렉션의 경우
    - 현재 자료구조가 포함하는 모든 값을 메모리에 저장함
    - 즉, 컬렉션의 모든 요소는 컬렉션에 추가하기 전에 계산되어야 함
    - 이런 연산이 수행할 때마다 컬렉션의 모든 요소를 메모리에 저장해야 하며, 컬렉션에 추가하려는 요소는 미리 계산되어야 함
- 반면 스트림
    - 이론적으로 요청할 때만 요소를 계산하는 고정된 자료구조임
    - 스트림의 경우 삽입, 삭제가 불가능함
    - 사용자가 데이터를 요청할 때만 값을 계산함

<br>

### 딱 한 번만 탐색할 수 있다
반복자와 마찬가지로, 스트림도 한 번만 탐색할 수 있음. 즉, 탐색된 스트림 요소는 소비됨<br>
스트림에서 데이터를 다시 탐색하려면 새로 스트림을 만들어야 함

```java
List<String> names = Arrays.asList("Java8", "Lambdas", "In", "Action");
Stream<String> s = names.stream();
s.forEach(System.out::println); // 탐색된 스트림의 요소는 소비됨
s.forEach(System.out::println); // java.lang.IllegalStateException 발생
```

<br>

### 외부 반복과 내부 반복
컬렉션과 스트림의 또 다른 차이점은 데이터 반복 처리 방법임

- 컬렉션은 for-each와 같은 반복문을 명시적으로 사용해서 반복해야 함 (외부 반복)
- 스트림 라이브러리는 반복을 알아서 처리하고 결과 스트림 값을 어딘가에 저장함 (내부 반복)

![image](https://github.com/hyeyoungs/modern-java-in-action/assets/29566893/985d5900-8d5b-4bd9-810b-f6fe306eed41)

- 외부 반복 : for-each 와 같은 반복문의 명시적 사용
  ```java
  List<String> names = new ArrayList();
  for (Dish dish : menu) { // 명시적으로 순차 반복함 (외부 반복)
      names.add(dish.getName());
  }
  ```
- 내부 반복 : 반복을 알아서 처리하고 결과 스트림 값을 어딘가에 저장해주는 것
  ```java
  menu.stream()
      .map(Dish::getName)
      .collect(toList());  // 내부 반복
  ```
내부 반복을 이용하면 작업을 투명하게 병렬로 처리하거나 더 최적화된 다양한 순서로 처리할 수 있음 <br>
또한, 데이터 표현과 하드웨어를 활용한 병렬성 구현을 자동으로 선택함 (병렬성을 스스로 관리 X)

<br>

## 스트림 연산
스트림 인터페이스의 연산은 크게 두 가지로 구분할 수 있다.

- 중간 연산 : filter, map, limit는 서로 연결되어 파이프라인을 형성한다.
- 최종 연산 : collect로 파이프라인을 실행한 다음 닫는다.

<br>

### 중간 연산
- 중요한 점은 중간 연산의 중요한 특징은 최종 연산을 스트림 파이프라인에 실행하기 전까지는 아무 연산도 수행하지 않는 것임 (즉 게으르다는 것)<br>
- lazy한 특징 때문에 최적화 효과를 얻을 수 있음
  
 ```java
public static void main(String[] args) {
    menu.stream()
            .filter(dish -> {
                System.out.println("filtering : " + dish.getName());
                return dish.getCalories() > 400;
            }) // 필터링한 요리명 출력
            .map(dish -> {
                System.out.println("mapping : " + dish.getName());
                return dish.getName();
            }) // 추출한 요리명 출력
            .limit(3)
            .collect(Collectors.toList());
 
}
 ```

300 칼로리가 넘는 요리는 여러 개지만 오직 처음 3개만 선택됨 (limit 연산, 쇼트서킷기법)<br>
filter와 map은 서로 다른 연산이지만한 과정으로 병합됨

![image](https://github.com/hyeyoungs/modern-java-in-action/assets/29566893/b326fccd-62d3-46e5-8ae5-a823a2525182)


<br>

### 최종 연산
최종 연산은 스트림 파이프라인에서 결과를 도출함. 이를 통해 List, Integer, void 등 스트림 이외의 결과가 반환됨 <br>
ex. collect, forEach, count ...

![image](https://github.com/hyeyoungs/modern-java-in-action/assets/29566893/18034cbb-980f-44fd-aa83-6869b500c19b)

<br>

### 스트림 이용하기
스트림의 이용 과정은 세 가지로 요약할 수 있다.

- 질의를 수행할 데이터 소스
- 스트림 파이프라인을 구성할 중간 연산 연결
- 스트림 파이프라인을 실행하고 결과를 만들 최종연산

<br><br>

# 🔎 알게된 점
### ✔️ 스트림은 소스에서 추출된 연속 요소로, 데이터 처리 연산을 지원함
### ✔️ 스트림은 내부 반복을 지원함. 내부 반복은 filter, map, sorted 등의 연산으로 반복을 추상화함
### ✔️ 스트림에는 중간 연산과 최종 연산이 있음
### ✔️ 중간 연산은 filter와 map처럼 스트림을 반환하면서 다른 연산과 연결되는 연산임. 중간 연산을 이용해서 파이프라인을 구성할 수 있지만 중간 연산으로는 어떤 결과도 생성할 수 없음
### ✔️ forEach나 count처럼 스트림 파이프라인을 처리해서 스트림이 아닌 결과를 반환하는 연산을 최종연산이라고 함
### ✔️ 스트림의 요소는 요청할 때 게으르게(lazily) 계산됨

<br><br>
  
# 📙 읽어볼 거리
[for-loop 를 Stream.forEach() 로 바꾸지 말아야 할 3가지 이유](https://homoefficio.github.io/2016/06/26/for-loop-%EB%A5%BC-Stream-forEach-%EB%A1%9C-%EB%B0%94%EA%BE%B8%EC%A7%80-%EB%A7%90%EC%95%84%EC%95%BC-%ED%95%A0-3%EA%B0%80%EC%A7%80-%EC%9D%B4%EC%9C%A0/)
