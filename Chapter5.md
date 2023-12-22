# 📚 내용
``` java
List<Dish> vegetarianDishes = new ArrayList<>();
for(Dish d : menu) {
  if(d.isiVegitarian()) {
    vegetarianDishes.add(d)
  }
}
```

이 외부 반복 코드를 내부 반복으로(stream)으로 변환하자.

<br>

## Filtering
### filter()
filter method는 프레디케이트를 인수로 받아서, 조건에 일치하는 값을 포함하는 스트림을 반환한다.
``` java
List<Dish> vegetarianMenu = menu.stream()
                                .filter(Dish::isVegetarian)
                                .collect(toList());
```

<img width="446" alt="image" src="https://github.com/hyeyoungs/modern-java-in-action/assets/29566893/cbdb5812-afd6-4044-9fea-ea0bb6c63889">

<br>

### distinct()
중복된 요소를 제거하고 고유요소로 이루어진 스트림을 반환한다.
<br>고유 여부는 스트림에서 만든 객체의 **hashCode, equals**로 결정
``` java
List<Integer> numbers = Arrays.asList(1, 2, 1, 3, 3, 2, 4)
                              .stream()
                              .filter(number -> number % 2 == 0)
                              .distinct()
                              .forEach(System.out::println);
```

<br>

## Slicing
스트림의 요소를 선택하거나 스킵
- 프리디케이트를 이용하는 방법
- 스트림의 처음 몇 개의 요소를 무시하는 방법
- 특정 크기로 스트림을 줄이는 방법 등 다양한 방법
<br>

### takeWhile
정렬 되어있는 리스트에, Predicate가 처음으로 거짓되는 시점까지 발견된 요소를 가져온다.
``` java
List slicedMenu = menu.stream()
                      .takeWhile(dish -> dish.getCalories() < 320)
                      .collect(toList());
```

<img width="345" alt="image" src="https://github.com/hyeyoungs/modern-java-in-action/assets/29566893/cd1a0192-1690-4f30-8e08-34658d86df58">

<br>

### dropWhile
dropWhile은 Predicate가 처음으로 거짓되는 시점까지 발견된 요소를 버린다.
``` java
List slicedMenu = menu.stream()
                      .dropWhile(dish -> dish.getCalories() < 320)
                      .collect(toList());
```

### limit, skip 
limit(n)을 통해서 처음 등장하는 주어진 값 N개의 요소를 가지는 스트림을 반환한다.
<br>skip(n)은 처음 n개의 요소를 제외, 이후 요소만을 포함하는 스트림을 반환한다.

``` java
List slicedMenu = menu.stream()
                      .filter(dish -> dish.getCalories() > 300)
                      .limit(3)
                      .collect(toList());
```
``` java
List slicedMenu = menu.stream()
                      .filter(dish -> dish.getCalories() > 300)
                      .skip(2)
                      .collect(toList());
```

<img width="352" alt="image" src="https://github.com/hyeyoungs/modern-java-in-action/assets/29566893/2314d5b3-5967-4230-8258-eb605ec5f9db">

<br><br>

## Mapping
### map
특정 객체에서 특정 데이터를 선택하는 작업

``` java
List<String> dishNames = menu.stream()
                             .map(Dish::getName)
                             .collect(toList());
```

Dish::getName() 메서드를 .map() 메서드로 전달해서 스트림의 요리명을 추출

<br> 그럼, Dish 리스트에서 음식 이름들의 길이를 어떻게 뽑아낼 수 있을까?
<br>→ map을 연결해서 수행

``` java
// map 을 연결하여 요리명의 글자수 추출
List<String> dishNames = menu.stream()
                            .map(Dish::getName)
                            .map(String::length)
                            .collect(toList());
```
<br>

### flatMap
스트림의 각 값을 다른 스트림으로 만든 다음 모든 스트림을 하나의 스트림으로 연결
<br>리스트에서 고유문자로 이루어진 리스트를 반환해보자.
<br>예를 들어 ["hello", "world"] 리스트가 ["h", "e", "l", "o", "w", "r", "d"]가 되도록 변경
``` java
List<String> uniqueCharacters = words.stream()
                                    .map(word -> word.split("")) // 각 단어를 개별 문자를 포함하는 배열로 변환
                                    .distinct()
                                    .collect(toList());
```
이 방식을 사용하면 string이 아닌 string[]으로 반환된다.
<br>따라서, distinct를 대상이 글자 하나하나가 아닌, String[]이 대싱이 된다.
<br>"h", "e", "l" 각각이 아닌, ["h", "e", "l", "l", "o"] 하나가 대상

<img width="506" alt="image" src="https://github.com/hyeyoungs/modern-java-in-action/assets/29566893/ee13c9a2-8283-4a3a-9ec3-ccf9c6879052">

<br>해결 방법 : flatMap
``` java
List<String> uniqueCharacters = words.stream()
                                    .map(word -> word.split("")) // 각 단어를 개별 문자로 포함하는 배열로 변환
                                    .flatMap(Arrays::stream) // 생성된 스트림을 하나의 스트림으로 평면화
                                    .distinct() // 
                                    .collect(toList());
```
flatMap은 각 배열은 스트림이 아니라 스트림의 콘텐츠로 매핑
<br>Arrays::stream은 문자열을 받아 스트림으로 바꾸어줌

<img width="426" alt="image" src="https://github.com/hyeyoungs/modern-java-in-action/assets/29566893/c9ee7af4-a24c-423b-abaa-fe80778b5e4a">

단어들이 하나의 스트림으로 묶어지므로, distinct으로 고유요소를 반환 가능해졌다.


<br>

## 스트림 검색과 매칭
특정 속성이 데이터 집합에 있는지 여부, 즉 검색 처리도 스트림 API에서 제공

- anyMatch
- allMatch
- noneMatch
- findFirst
- findAny
  
<br>

### anyMatch
Predicate가 적어도 한 요소와 일치 하는지 확인

boolean 리턴 : 최종연산

``` java
boolean isMatch = menu.stream().anyMatch(Dish -> dish.getCalories() < 1000)
``` 
<br>

### allMatch
Predicate가 모든 요소와 일치하는지 검사

``` java
boolean isMatch = menu.stream().allMatch(Dish -> dish.getCalories() < 1000)
``` 
<br>

### noneMatch
주어진 predicate와 일치하는 요소가 없는지 확인

noneMatch는 allMatch와 반대 연산을 수행

``` java
boolean isMatch = menu.stream().noneMatch(Dish -> dish.getCalories() < 1000)
```

<br>

### findFirst
첫 번째 요소 찾기

리스트 또는 정렬된 데이터로 부터 생성된 순서가 정해진 스트림에서 첫번째 요소를 찾기 위한 방법

``` java
List<Integer> someNumbers = Arrays.asList(1, 2, 3, 4, 5);
Optional<Integer> first = someNumbers.stream()
        .map(n -> n * n)
        .filter(n -> n % 3 == 0)
        .findFirst();
``` 

<br>

### findAny
findAny 메서드는 현재 스트림에서 임의의 요소를 반환

즉, 처음 프레디케이트를 통과하는 요소를 찾자마자 그 요소만 반환

``` java
Optional<Dish> dish = words.stream()
      .filter(Dish::isVegetarian)
      .findAny();   //findAny는 Optional 객체를 반환
``` 
<br>

<img width="537" alt="image" src="https://github.com/hyeyoungs/modern-java-in-action/assets/29566893/da366c04-46e0-47da-97f3-5060e67f49c0">

<img width="448" alt="image" src="https://github.com/hyeyoungs/modern-java-in-action/assets/29566893/c5b56690-2a99-42f8-90b8-64536332dce4">

<br><br>

## Reducing
모든 스트림 요소를 반복적으로 처리해서 결과를 도출하는 작업을 수행

reduce는 두 개의 인수를 갖는다.
- 초깃값
- 스트림의 두 요소를 합쳐서 하나의 값으로 만드는 데 사용할 람다
<br>

### 요소의 합
``` java
int sum = 0;
for ( int x : numbers ) {
  sum += x;
}
```
→
``` java
int sum = numbers.stream().reduce(0, (a, b) -> a + b);
```

<img width="467" alt="image" src="https://github.com/hyeyoungs/modern-java-in-action/assets/29566893/eac9d623-94e9-4263-acd8-7851e356a62d">

<br>

메서드 참조로 Interger 클래스의 sum 메서드를 사용하면 더 간결하게 구현 가능하다.
``` java
int sum = numbers.stream().reduce(0, Integer::sum);
```

<br>

### 초깃값 없음
초깃값을 받지 않도록 오버로드된 reduce도 있다. 하지만 이 reduce는 Optional 객체를 반환한다.

``` java
Optional<Integer> sum = numbers.stream().reduce(Integer::sum);
```
→ 스트림에 아무 요소도 없다면 초깃값이 없으므로 reduce는 합계를 반환할 수 없기 때문이다.

<br>

### 최댓값과 최솟값
reduce 연산은 새로운 값을 이용해서 스트림의 모든 요소를 소비할 때까지 람다를 반복 수행한다.

이를 통해 최댓값과 최솟값을 찾을 때도 reduce를 활용할 수 있다.

``` java
Optional<Integer> max = numbers.stream().reduce(Integer::max);
```

<img width="417" alt="image" src="https://github.com/hyeyoungs/modern-java-in-action/assets/29566893/98fe43cc-1d63-4d5e-9f79-37a64766d537">

<br><br>

## 그 외
### reduce 메서드의 장점과 병렬화

단계적 반복으로 합계를 구할때는 sum 변수를 공유해야 하므로 쉽게 병렬화가 어렵다.

하지만 reduce를 이용하면 내부 반복이 추상화되면서 내부 구현에서 병렬로 reduce를 실행할 수 있게 된다.

물론 병렬로 실행하기 위해서는 연산이 어떤 순서로 실행되더라도 결과가 바뀌지 않는 구조여야 한다.

<br>

### 스트림 연산 : 상태 있음과 상태 없음
스트림 연산은 각각 다양한 작업을 수행한다. 따라서 각각의 연산은 내부적인 상태를 고려해야한다.

map, filter 등은 입력 스트림에서 각 요소를 받아 0 또는 결과를 출력 스트림으로 보낸다.
<br>따라서 이들은 보통 상태가 없는, 즉 내부 상태를 갖지 않는 연산(stateless operation)이다.

reduce, sum, max 같은 연산은 결과를 누적할 내부 상태가 필요하다.
<br>하지만 내부 상태는 int, double 등과 같이 작은 값이며, 스트림에서 처리하는 요소 수와 관계없이 한정(bounded)되어있다.

반면 sorted나 distinct 같은 연산을 수행하기 위해서는 과거의 이력을 알고있어야 한다.
<br>예를 들어 어떤요소를 출력스트림으로 추가하려면 모든 요소가 버퍼에 추가되어 있어야 한다. 

따라서 데이터 스트림의 크기가 크거나 무한이라면 문제가 생길 수 있다. 이러한 연산을 내부 상태를 갖는 연산(stateful operation)이라 한다.

<br>

## 정리
<img width="463" alt="image" src="https://github.com/hyeyoungs/modern-java-in-action/assets/29566893/05d88200-fb57-4b55-856d-6191c79f1d38">

<br><br>
 
## 숫자형 스트림
다음처럼 메뉴의 칼로리 합계를 계산할 수 있다.
``` java
int calories = menu.stream()
                   .map(Dish::getCalories)
                   .reduce(0, Integer::sum);
``` 
다만 위 작업에서는 박싱 비용이 숨어있다.

따라서, 박싱 타입이 아닌 int 타입으로 계산하고 싶다 : sum

하지만 그럴 수 없다. map 메서드가 Stream<T>를 생성하기 때문이다. 

<br>

### 숫자 스트림으로 맵핑
그렇기 때문에 스트림 API 숫자 스트림을 효율적으로 처리하기 위하여 기본 특화 스트림을 제공한다.

- IntStream
- DoubleStream
- LongStream

``` java
int calories = menu.stream()
                   .mapToInt(Dish::getCalories)
                   .sum();
``` 

IntStream을 반환받아 sum 메서드를 사용할 수 있다.

스트림이 비어있다면 0을 반환하고 IntStream은 max, min, average 등 다양한 유틸리티 메서드도 지원한다.

<br>

### 객체 스트림으로 복원
``` java
IntStream intStream = menu.stream().mapToInt(Dish::getCalories);
Stream<Integer> stream = intStream.boxed();
```

<br>

### OptionalInt
참고로 스트림에 요소가 없는 상황과 실제 최댓값이 0인 상황을 구별하려면 OptionalInt 같이 Optional을 사용하여 최댓값 요소를 찾을 수 있다.

``` java
OptionalInt maxCalories = menu.stream()
        .mapToInt(Dish::getCalories)
        .max();
```
``` java
int max = maxCalories.orElse(-9999999);     // max 칼로리가 없으면 값이 -9999999 으로 표기
```

<br>

### 숫자 범위 : range, rangeClosed
특정 범위의 숫자를 이용해야 할 때 range와 rangeClosed 메서드를 사용할 수 있다.

이는 IntStream, LongStream 두 기본형 특화 스트림에서 지원된다. 

range는 시작과 종료 값이 결과에 포함되지 않고, rangeClosed는 포함 된다.

``` java
IntStream evenNumbers = IntStream.rangeClosed(1, 100)
        .filter(n -> n % 2 == 0);
System.out.println(evenNumbers.count());    // 50
```

<br>

## 스트림 만들기
다양한 방식으로 스트림을 만들 수 있다.

<br>

### 값으로 스트림 만들기
정적 메서드 Stream.of을 이용하여 스트림을 만들 수 있다.

``` java
Stream<String> stream = Stream.of("jiny","choi","samuel");
// Empty 스트림
Stream<String> emptyStream = Stream.empty();
```
<br>

### 배열로 스트림 만들기
배열을 인수로 받는 정적 메서드 Arrays.stream을 이용하여 스트림을 만들 수 있다.

``` java
int [] numbers = {2,3,4,5,6,7};
int sum = Arrays.stream(numbers).sum(); // 41
```
<br>

### 파일로 스트림 만들기
Files.lines는 주어진 파일의 행 스트림을 문자열로 변환한다.

``` java
long uniqueWords = 0L;
try (Stream<String> lines = Files.lines(Paths.get(System.getProperty("user.dir") + "/data.txt"), Charset.defaultCharset())) {
            uniqueWords = lines.flatMap(line -> Arrays.stream(line.split("")))
                    .peek(data -> System.out.println(data))
                    .distinct()
                    .count();
} catch (IOException e) {
            e.printStackTrace();
}
System.out.println(uniqueWords);
```
<br>

### 함수로 무한 스트림 만들기
Stream.iterate와 Stream.generate를 통해 함수를 이용하여 무한 스트림을 만들 수 있다.

iterate와 generate에서 만든 스트림은 요청할 때마다 주어진 함수를 이용해서 값을 만든다.

따라서 무제한으로 값을 계산할 수 있지만, 보통 무한한 값을 출력하지 않도록 limit(n) 함수를 함께 연결해서 사용한다.

``` java
Stream.iterate
Stream.iterate(0, n-> n + 2)
      .limit(10)
      .forEach(System.out::println);
// Fibonacci with iterate
Stream.iterate(new int[] { 0, 1 }, t -> new int[] { t[1], t[0] + t[1] })
      .limit(10)
      .forEach(t -> System.out.printf("(%d, %d)", t[0], t[1]));
```
iterate 메서드는 프레디케이트를 지원한다. 두 번째 인수로 프레디 케이트를 받아 작업 중단의 기준으로 사용한다.

0에서 시작해서 100보다 크면 숫자 생성을 중단하는 코드를 다음처럼 구현할 수 있다.

``` java
IntStream.iterate(0, n -> n < 100, n -> n + 4)
        .forEach(System.out::println);
``` 
filter로도 같은 결과를 얻을수 있다고 생각할 수 있지만, filter 메서드는 언제 이 작업을 중단해야 하는지 알 수 없다.

이럴 때에는 talkWhile 메서드를 사용해야 한다.

``` java
// 불가
IntStream.iterate(0, n -> n + 4)
   .filter(n -> n < 100)
   .forEach(System.out::println);
    
// 가능
IntStream.iterate(0, n -> n + 4)
   .talkWhile(n -> n < 100)
   .forEach(System.out::println);
Stream.generate
Stream.generate(Math::random)
  .limit(5)
  .forEach(System.out::println);

```
