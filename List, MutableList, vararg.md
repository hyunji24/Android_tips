**List와 MutableList**

1) **List** : Read-only, 만든 후 수정할 수 없음

   ```kotlin
   val numbers : List<Int> = listOf(1,2,3,4,5,6)
   val numbers = listOf(1,2,3,4,5,6)
   
   val colors = listOf("green", "orange", "blue")
   ```

   

2. **MutableList** : 수정 가능

   - `MutableList`나 `List`를 만들 때 Kotlin은 전달된 인수에서 목록에 포함된 요소의 유형을 추론
   - 요소 없이 빈 목록을 초기화하면 Kotlin은 요소의 유형을 추론할 수 없으므로 유형을 명시적으로 표시해야 합니다. `mutableListOf`나 `listOf` 바로 뒤에 유형을 꺾쇠괄호로 묶어 추가하면 됨. <T>

   ```kotlin
   val entrees = mutableListOf<String>()
   val entrees: MutableList<String> = mutableListOf()
   
   entrees.add("hello")
   
   val moreItems = listOf("ravioli", "lasagna", "fettuccine")
   entrees.addAll(moreItems)
   ```

   

**vararg 수정자**

: 동일한 유형의 가변적 인수를 함수나 생성자에 전달 -> 목록 대신 개별 문자열로 함수에 전달 가능

```kotlin
//수정 전
class Vegetables(val toppings: List<String>) : Item("Vegetables", 5) 

//main에서 토핑 목록 만들고 Vegetables생성자에 전달해야함
Vegetables(listOf("Cabbage", "Sprouts", "Onion")) 

//수정 후
class Vegetables(vararg val toppings: String) : Item("Vegetables", 5) {
    override fun toString(): String {
        return name + " " + toppings.joinToString()
    }
}

fun main() {
    ...
    val vegetables = Vegetables("Cabbage", "Sprouts", "Onion")
    ...
}
```



공식문서 예시

```kotlin
open class Item(val name: String, val price: Int)

class Noodles : Item("Noodles", 10) {
    override fun toString(): String {
        return name
    }
}

class Vegetables(vararg val toppings: String) : Item("Vegetables", 5) {
    override fun toString(): String {
        if (toppings.isEmpty()) {
            return "$name Chef's Choice"
        } else {
            return name + " " + toppings.joinToString()
        }
    }
}

class Order(val orderNumber: Int) {
    private val itemList = mutableListOf<Item>()

    fun addItem(newItem: Item): Order {
        itemList.add(newItem)
        return this
    }

    fun addAll(newItems: List<Item>): Order {
        itemList.addAll(newItems)
        return this
    }

    fun print() {
        println("Order #${orderNumber}")
        var total = 0
        for (item in itemList) {
            println("${item}: $${item.price}")
            total += item.price
        }
        println("Total: $${total}")
    }
}

fun main() {
    val ordersList = mutableListOf<Order>()

    // Add an item to an order
    val order1 = Order(1)
    order1.addItem(Noodles())
    ordersList.add(order1)

    // Add multiple items individually
    val order2 = Order(2)
    order2.addItem(Noodles())
    order2.addItem(Vegetables())
    ordersList.add(order2)

    // Add a list of items at one time
    val order3 = Order(3)
    val items = listOf(Noodles(), Vegetables("Carrots", "Beans", "Celery"))
    order3.addAll(items)
    ordersList.add(order3)

    // Use builder pattern
    val order4 = Order(4)
        .addItem(Noodles())
        .addItem(Vegetables("Cabbage", "Onion"))
    ordersList.add(order4)

    // Create and add order directly
    ordersList.add(
        Order(5)
            .addItem(Noodles())
            .addItem(Noodles())
            .addItem(Vegetables("Spinach"))
    )

    // Print out each order
    for (order in ordersList) {
        order.print()
        println()
    }
}
```

