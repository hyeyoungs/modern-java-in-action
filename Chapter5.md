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
                      .filter(dish -> dish.getCalories() > 300)
                      .limit(3)
                      .collect(toList());
```
``` java
List slicedMenu = menu.stream()
                      .filter(dish -> dish.getCalories() > 300)
                      .skip(2)
                      .collect(toList());
```

<img width="352" alt="image" src="https://github.com/hyeyoungs/modern-java-in-action/assets/29566893/2314d5b3-5967-4230-8258-eb605ec5f9db">

<br>

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
<br>

<br>

## 스트림 검색과 매칭
특정 속성이 데이터 집합에 있는지 여부, 즉 검색 처리도 스트림 API에서 제공

- allMatch
- anyMatch
- noneMatch
- findFirst
- findAny
  
<br>

### allMatch

<br>

### anyMatch

<br>

### noneMatch

<br>

### findFirst

<br>

### findAny

<br>

## Reducing
모든 스트림 요소를 반복적으로 처리해서 결과를 도출하는 작업을 수행

reduce는 두 개의 인수를 갖는다.

초깃값
스트림의 두 요소를 합쳐서 하나의 값으로 만드는 데 사용할 람다



