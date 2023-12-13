---
layout: default
title: Kotlin-in-action 8장 고차함수 파라미터와 반환 값으로 람다 사용
parent: study
nav_order: 8
---


# 8장 고차함수 : 파라미터와 반환 값으로 람다 사용

8장에서 다루는 내용

◼︎ 함수 타입

◼︎ 고차 함수와 코드를 구조화할 때 고차함수를 사용하는 방법 

◼︎ 인라인 함수

◼︎ 비로컬 return과 레이블

◼︎ 무명 함수

고차함수는 람다를 인자로 받거나 반환하는 함수이다.

인라인 함수는 람다를 사용함에 따라 발생할 수 있는 성능상 부가비용을 없애고 람다 안에서 더 유연하게 흐름을 제어할 수 있다.

> 고차 함수는 다른 함수를 인자로 받거나 함수를 반환하는 함수이다. 코틀린에서는 람다나 함수 참조를 사용해 함수를 값으로 표현할 수 있다. 따라서 고차함수는 람다나 함수 참조를 인자로 넘길 수 있거나 람다나 함수 참조를 반환하는 함수이다.(함수를 인자로 받는 동시에 반환도 가능하다)
> 

## 함수타입

람다를 인자로 받는 함수를 정의하려면 먼저 람다 인자의 타입을 어떻게 선언할 수 있는지 알아야 한다.

앞서 코틀린의 타입추론으로 인해 변수 타입을 지정하지 않아도 람다를 변수에 대입할 수 있음을 알게 되었다.

```kotlin
val sum = { x: Int, y: Int -> x + y }
val action = { println(42) }
```

함수 타입을 정의하려면 함수 파라미터의 타입을 괄호 안에 넣고, 그 뒤에 화살표(→)를 추가한 다음, 함수의 반환 타입을 지정하면 된다.

![](https://velog.velcdn.com/images/philipy/post/af3f9c17-c690-4b58-9485-3a9425bd0593/image.png)


그냥 함수를 정의한다면 함수의 파라미터 목록 뒤에 오는 Unit 반환 타입지정을 생략해도 되지만, 함수의 타입을 선언할 때는 반환타입을 반드시 명해야 하므로 Unit을 빼먹어서는 안된다.

> 다른 함수와 마찬가지로 함수 타입에서도 반환타입을 널이 될 수 있는 타입으로 지정할 수 있다.
> 

```kotlin
val canReturnNull: (Int, Int) -> Int? = { x, y -> null }
```

> 널이 될 수 있는 함수 타입 변수를 정의할 수도 있다. 함수 타입 전체가 널이 될 수 있는 타입임을 선언하기 위해 함수 타입을 괄호로 감싸고 그 뒤에 물음표를 붙여야만 한다.
> 

```kotlin
var funOrNull: ((Int, Int) -> Int) ?= null
```

## 인자로 받은 함수 호출

```kotlin
fun twoAndThree(operation: (Int, Int) -> Int){
    val result = operation(2, 3)
    println("The result is $result")
}

fun main(args: Array<String>) {
    twoAndThree{ a, b -> a + b}
    twoAndThree { a, b -> a * b }
}
```

String에 대한 filter를 구현해보자. 

![](https://velog.velcdn.com/images/philipy/post/e6ebe6ac-d687-41eb-9f65-d771043cf895/image.png)


> filter 함수는 술어를 파라미터로 받는다. predicate 파라미터는 문자(Char 타입)를 파라미터로 받고 불리언(Boolean 타입) 결과 값을 반환한다. *술어는 인자로 받은 문자가 filter 함수가 돌려주는 결과 문자열에 남아 있기를 바란다면 true를 반환하고 문자열에서 사라지기를 바라면 false를 반환하면 된다. ????*
> 

```kotlin
fun String.filter(predicate: (Char) -> Boolean) : String{
    val sb = StringBuilder()
    for(index in 0 until length){
        val element = get(index)
        if(predicate(element)) sb.append(element)
    }
    return sb.toString()
}

fun main(args: Array<String>) {
    println("ab1c".filter { it in 'a'..'z' })
}
```

<aside>
💡 인텔리J 아이디어는 디버깅할 때 람다 코드 내부를 한단계씩 실행해 볼수 있는 스마트 스테핑을 제공한다!

</aside>

## 자바에서 코틀린 함수 타입 사용

- 컴파일된 코드 안에서 함수 타입은 일반 인터페이스로 바뀐다.
    - 함수 타입의 변수는 FunctionN 인터페이스를 구현하는 객체를 저장한다.
    - 코틀린 표준 라이브러리는 함수 인자의 갯수에 따라 Function0<R>(인자가 없는 함수), Function1<P1, R>(인자가 하나인 함수) 등의 인터페이스를 제공한다.
    - 인터페이스에는 invoke 메소드 정의가 하나 들어있다. invoke를 호출하면 함수를 실행할 수 있다.
    - 함수 타입인 변수는 인자 개수에 따라 적당한 FunctionN 인터페이스를 구현하는 클래스의 인스턴스를 저장하며, 그 클래스의 invoke 메소드 본문에는 람다의 본문이 들어간다.
    - 함수 타입을 사용하는 코틀린 함수를 자바에서도 쉽게 호출 할 수 있다.
        
        ```kotlin
        fun processTheAnswer(f: (Int) -> Int){
            println(f(42))
        }
        
        /*
        public final class ProcessKt {
           public static final void processTheAnswer(@NotNull Function1 f) {
              Intrinsics.checkNotNullParameter(f, "f");
              int var1 = ((Number)f.invoke(42)).intValue();
              boolean var2 = false;
              System.out.println(var1);
           }
        }
        */
        
        public class Main {
            public static void main(String[] args) {
                ProcessKt.processTheAnswer(number-> number+1);
            }
        }
        ```
        

## 디폴트 값을 지정한 함수 타입 파라미터나 널이 될 수 있는 함수 타입 파라미터

- 파라미터를 함수 타입으로 선언할 때도 디폴트 값을 정할 수 있다.

```kotlin
fun <T> Collection<T>.joinToString(
    separator: String = ", ",
    prefix: String = "",
    postfix: String = "",
    transform: (T) -> String = { it.toString() }
): String {
    val result = StringBuilder(prefix)
    for ((index, element) in this.withIndex()) {
        if (index > 0) result.append(separator)
        result.append(transform(element))
    }
    result.append(postfix)
    return result.toString()
}
```

위 함수는 제네릭 함수다. 컬렉션의 원소 타입을 표현하는 T를 타입 파라미터로 받는다. transform 람다는 그 T 타입의 값을 인자로 받는다.

```kotlin
fun <T> Collection<T>.joinToString(
    separator: String = ", ",
    prefix: String = "",
    postfix: String = "",
    transform: ((T) -> String)? = null
): String {
    val result = StringBuilder(prefix)
    for ((index, element) in this.withIndex()) {
        if (index > 0) result.append(separator)
        val str = transform?.invoke(element)
            ?: element.toString()
        result.append(str)
    }
    result.append(postfix)
    return result.toString()
}
```

## 함수를 함수에서 반환

> 함수가 함수를 반환할 필요가 있는 경우보다는 함수가 함수를 인자로 받아야 할 필요가 훨씬 더 많다. 아래 코드는 사용자가 선택한 배송 수단에 따라 배송비를 계산하는, 적절한 로직을 선택해서 함수로 반환하는 함수이다.
> 

```kotlin
enum class Delivery { STANDARD, EXPEDITED }

class Order(val itemCount: Int)

fun getShippingCostCalculator(delivery: Delivery): (Order) -> Double
{
    if(delivery == Delivery.EXPEDITED){
        return { order -> 6 + 2.1 * order.itemCount }
    }
    return { order -> 1.2 * order.itemCount }
}

fun main(args: Array<String>) {
    val calculator = getShippingCostCalculator(Delivery.STANDARD)
    println("Shipping costs ${calculator(Order(3))}")
}
```

> 다른 함수를 반환하는 함수를 정의하려면 함수의 반환 타입으로 함수 타입을 지정해야 한다. 위 getShippingCostCalculator 함수는 Order를 받아서 Double을 반환하는 함수를 반환한다. 함수를 반환하려면 return 식에 람다나 멤버 참조나 함수 타입의 값을 계산하는 식 등을 넣으면 된다.
> 

```kotlin
data class SiteVisit(
    val path: String,
    val duration: Double,
    val os: OS
)

enum class OS { WINDOWS, LINUX, MAC, IOS, ANDROID }

fun List<SiteVisit>.averageDurationFor(os: OS) =
    filter { it.os == os }.map(SiteVisit::duration).average()

fun main(args: Array<String>) {
    val log = listOf(
        SiteVisit("/", 34.0, OS.WINDOWS),
        SiteVisit("/", 22.0, OS.MAC),
        SiteVisit("/", 12.0, OS.WINDOWS),
        SiteVisit("/", 8.0, OS.IOS),
        SiteVisit("/", 16.3, OS.ANDROID)
    )

    val averageWindowsDuration = log
        .filter { it.os == OS.WINDOWS }
        .map(SiteVisit::duration)
        .average()

		val averageMobileDuration = log
		        .filter { it.os in setOf(OS.IOS, OS.ANDROID) }
		        .map(SiteVisit::duration)
		        .average()

    println(log.averageDurationFor(OS.WINDOWS))
    println(log.averageDurationFor(OS.MAC))
    println(averageMobileDuration)
    println(averageWindowsDuration)
}
```

## 인라인 함수 : 람다의 부가 비용 없애기(inline키워드를 통해 람다의 성능 개선하기)

> 5장에서 코틀린이 보통 람다를 무명 클래스로 컴파일하지만 그렇다고 람다식을 사용할 때마다 새로운 클래스가 만들어지는 것은 아니다. 람다가 변수를 포획하면 람다가 생성되는 시점마다 새로운 무명 클래스 객체가 생긴다. 이런 경우 실행 시점에 무명 클래스 생성에 따른 부가 비용이 든다. 이때 inline 변경자를 어떤 함수에 붙이면 컴파일러는 그 함수를 호출하는 모든 문장을 함수 본문에 해당하는 바이트 코드로 바꿔치기 해준다.
> 

### 인라이닝이 작동하는 방식

어떤 함수를 inline으로 선언하면 그 함수의 본문이 인라인(inline)된다. 다른 말로 하면 함수를 호출하는 코드를 함수를 호출하는 바이트코드 대신에 함수 본문을 번역한 바이트 코드로 컴파일 된다.

```kotlin
inline fun <T> synchronized(lock: Lock, action: () -> T): T {
    lock.lock()
    try {
        return action()
    }
    finally {
        lock.unlock()
    }
}

val l = Lock()
synchronized(l) {
    / ...
}
```

## 함수를 인라인으로 선언해야 하는 경우

> inline 키워드의 이점을 배우고 나면 코드를 더 빠르게 만들기 위해 코드 여기저기에서 inline을 사용하고 싶어질 것이다. inline 키워드를 사용해도 람다를 인자로 받는 함수만 성능이 좋아질 가능성이 높기 때문에 좋지 못하다.
> 

```kotlin
fun readFirstLineFromFile(path: String): String {
    BufferedReader(FileReader(path)).use { br -> 
        return br.readLine()            
    }
}
```

- use 함수는 닫을 수 있는 자원에 대한 확장 함수며, 람다를 인자로 받는다. use는 람다를 호출한 다음에 자원을 닫아준다. 이때 람다가 정상 종료한 경우는 물론 람다 안에서 예외가 발생한 경우에도 자원을 확실히 닫는다.

## 고차 함수 안에서 흐름 제어

### 람다 안의 return문 : 람다를 둘러싼 함수로부터 반환

- 일반 루프 안에서 return 사용하기, forEach에 전달된 람다에서 return 사용하기
    
    ```kotlin
    //fun lookForAlice(people: List<Person>){
    //    for(person in people){
    //        if(person.name == "Alice"){
    //            println("Found!")
    //            return
    //        }
    //    }
    //}
    
    fun lookForAlice(people: List<Person>){
        people.forEach {
            if(it.name == "Alice"){
                println("Found!")
                return
            }
        }
    }
    
    fun main(args: Array<String>) {
        val people = listOf(Person("Alice", 29), Person("Bob", 31))
        lookForAlice(people)
    }
    ```
    
- return이 바깥쪽 함수를 반환시킬 수 있는 때는 람다를 인자로 받는 함수가 인라인 함수인 경우뿐이다.
    - 인라이닝되지 않는 함수에 전달되는 람다 안에서 return을 사용할 수는 없다.
    - 인라이닝되지 않는 함수는 람다를 변수에 저장할 수 있다.
    - 바깥쪽 함수로부터 반환된 뒤에 저장해 둔 람다가 호출될 수도 있다.
    - 그런 경우 람다 안의 return이 실행되는 시점이 바깥쪽 함수를 반환시키기엔 너무 늦은 시점일 수 있다.

### 람다로부터 반환: 레이블을 사용한 return

- 람다 식에서도 로컬(local) return을 사용할 수 있다.
    - 람다 안에서 로컬 return은 for 루프의 break와 비슷한 역할을 한다.
    - 로컬 return은 람다의 실행을 끝내고 람다를 호출했던 코드의 실행을 계속 이어간다.
    - 로컬 return과 넌로컬 return을 구분하기 위해 레이블(label)을 사용해야 한다.
        - return으로 실행을 끝내고 싶은 람다 식 앞에 레이블을 붙이고, return 키워드 뒤에 그 레이블을 추가하면 된다.
  			![](https://velog.velcdn.com/images/philipy/post/5bfe2234-dc9c-41fd-a100-d95487fcc8f6/image.png)

            ```kotlin
            fun lookForAlice(people: List<Person>){
                people.forEach label@{
                    if(it.name == "Alice") return@label
                }
                println("Alice might be somewhere")
            }
            
            fun main(args: Array<String>) {
                val people = listOf(Person("Alice", 29), Person("Bob", 31))
                lookForAlice(people)
            }
            ```
            
    
    - 람다에 레이블을 붙여서 사용하는 대신 람다를 인자로 받는 인라인 함수의 이름을 return 뒤에 레이블로 사용해도 된다.
        
        ```kotlin
        fun lookForAlice(people: List<Person>){
            people.forEach{
                if(it.name == "Alice") return@forEach
            }
            println("Alice might be somewhere")
        }
        
        fun main(args: Array<String>) {
            val people = listOf(Person("Alice", 29), Person("Bob", 31))
            lookForAlice(people)
        }
        ```
        

### 무명함수 : 기본적으로 로컬 return

- 무명 함수는 코드 블록을 함수에 넘길 때 사용할 수 있는 다른 방법이다.
    
    ```kotlin
    fun lookForAlice(people: List<Person>){
        people.forEach(fun (person){
            if( person.name == "Alice") return
            println("${person.name} is not Alice")
        })
    }
    
    fun main(args: Array<String>) {
        val people = listOf(Person("Alice", 29), Person("Bob", 31))
        lookForAlice(people)
    }
    ```
    
    ```kotlin
    println(people.filter(fun (person): Boolean{
            return person.age < 30
        }))
    ```
    
    > 무명함수도 일반 함수와 같은 반환 타입 지정 규칙을 따른다. 위 코드처럼 블록이 본문인 무명 함수는 반환 타입을 명시해야 하지만, **식을 본문으로 하는 무명 함수의 반환 타입은 생략할 수 있다.**
    > 
    
    ```kotlin
    println(people.filter(fun (person) = person.age < 30))
    ```
    

- 무명 함수 안에서 레이블이 붙지 않은 return 식은 무명 함수 자체를 반환시킬 뿐 무명함수를 둘러싼 다른 함수를 반환시키지 않는다.
    - 사실 return에 적용되는 규칙은 단순히 return은 fun 키워드를 사용해 정의된 가장 안쪽 함수를 반환시킨다.
    - 람다식은 fun을 사용해 정의되지 않으므로 람다 본문의 return은 람다 밖의 함수를 반환시킨다.
    - 무명함수는 fun을 사용해 정의되므로 그 함수 자신이 바로 가장 안쪽에 있는 fun으로 정의된 함수다.
    - 무명 함수 본문의 return은 그 무명 함수를 반환시키고, 무명 함수 밖의 다른 함수를 반환시키지 못한다.
							  ![](https://velog.velcdn.com/images/philipy/post/55b83f86-31bd-40e5-9596-81310572dae6/image.png)


# 요약

- 함수 타입을 사용해 함수에 대한 참조를 담는 변수나 파라미터나 반환 값을 만들 수 있다.
- 고차 함수는 다른 함수를 인자로 받거나 함수를 반환한다. 함수의 파라미터 타입이나 반환 타입으로 함수 타입을 사용하면 고차 함수를 선언할 수 있다.
- 인라인 함수를 컴파일할 때 컴파일러는 **그 함수의 본문과 그 함수에게 전달된 람다의 본문을 컴파일한 바이트코드를 모든 함수 호출 지점에 삽입해준다.** 이렇게 만들어지는 바이트코드는 람다를 활용한 인라인 함수 코드를 풀어서 직접 쓴 경우와 비교할 때 아무 비가 비용이 들지 않는다.
- 고차 함수를 사용하면 컴포넌트를 이루는 각 부분의 코드를 더 잘 재사용할 수 있다. 또 고차함수를 활용해 강력한 제네릭 라이브러리를 만들 수 있다.
- 인라인 함수에서는 람다 안에 있는 return문이 바깥쪽 함수를 반환시키는 넌 로컬 return을 사용할 수 있다.
- 무명 함수는 람다 식을 대신할 수 있으며 return 식을 처리하는 규칙이 일반 람다 식과는 다르다. 본문 여러 곳에서 return해야 하는 코드 블록을 만들어야 한다면 람다 대신 무명 함수를 쓸 수 있다.
