---
title: java8
tags: java
categories: java基础
abbrlink: 92a2fe0d
date: 2021-06-06 07:21:00
---

### 一、接口里可以书写默认方法

~~~java
// 定义一个公式接口
interface Formula {
    // 计算
    double calculate(int a);

    // 求平方根
    default double sqrt(int a) {
        return Math.sqrt(a);
    }
}
~~~

**通过 `default` 关键字这个新特性，可以非常方便地对之前的接口做拓展，而此接口的实现类不必做任何改动。**

### 二、函数式接口Functional Interface

所谓函数式接口（Functional Interface）就是只包含一个抽象方法的声明。针对该接口类型的所有 Lambda 表达式都会与这个抽象方法匹配。

例如：

~~~java
@FunctionalInterface
public interface Supplier<T> {

    /**
     * Gets a result.
     *
     * @return a result
     */
    T get();
}
~~~

总结：只要接口中仅仅包含一个抽象方法，我们就可以将其改写为 Lambda 表达式。为了保证一个接口明确的被定义为一个函数式接口（Functional Interface），我们需要为该接口添加注解：`@FunctionalInterface`。这样，一旦你添加了第二个抽象方法，编译器会立刻抛出错误提示，如果此接口中添加默认方法无影响。且函数式接口都可用lambda进行书写。

### 三、jdk中常用的函数式接口

#### Predicate

Predicate断言可以指定入参类型，并返回 boolean 值的函数式接口。它内部也提供了一些带有默认实现的方法，可以 被用来组合一个复杂的逻辑判断（`and`, `or`, `negate`）。

~~~java
//定义一个断言的实现
Predicate<String> predicate = (s) -> s.length() > 0;
//测试
predicate.test("foo");              // true
predicate.negate().test("foo");     // false

Predicate<String> isEmpty = String::isEmpty;
//实现默认方法negate
Predicate<String> isNotEmpty = isEmpty.negate();
~~~

#### function

`function`有一个入参类型和一个返回值类型，它的默认方法有组合,链行处理(`compose`, `andThen`)；

~~~java
Function<String, Integer> toInteger = Integer::valueOf;
Function<String, String> backToString = toInteger.andThen(String::valueOf);

backToString.apply("123");     // "123"
~~~

#### Supplier

`Supplier` 与 `Function` 不同，它不接受入参，直接为我们生产一个指定的结果；

~~~java
Supplier<Person> personSupplier = Person::new;
personSupplier.get();   // new Person
~~~

#### Consumer

`Consumer`和`Supplier`不同之处在于，Consumer接受一个入参，但无返回值。

~~~java
Consumer<Person> greeter = (p) -> System.out.println("Hello, " + p.firstName);
greeter.accept(new Person("Luke", "Skywalker"));
~~~

#### 总结：其他后续多入参，多出参可去函数式接口中查询对应的关键字

### 四：optional

 `Optional` 看做是包装对象（可能是 `null`, 也有可能非 `null`）的容器。当你定义了 一个方法，这个方法返回的对象可能是空，也有可能非空的时候，你就可以考虑用 `Optional` 来包装它，这也是在 Java 8 被推荐使用的做法。

~~~java
Optional<String> optional = Optional.of("bam");

optional.isPresent();           // true
optional.get();                 // "bam"
optional.orElse("fallback");    // "bam"

optional.ifPresent((s) -> System.out.println(s.charAt(0)));     // "b"
~~~

### 五、Stream流

`Stream`对一个包含一个或多个元素的集合做各种操作。这些操作可能是 ***中间操作*** 亦或是 ***终端操作***。

 终端操作会返回一个结果，而中间操作会返回一个 `Stream` 流。

需要注意的是，你只能对实现了 `java.util.Collection` 接口的类做流的操作。但不支持对Map进行操作。

常用的Stream流的方法

首先创建一个List集合

~~~java
List<String> stringCollection = new ArrayList<>();
stringCollection.add("ddd2");
stringCollection.add("aaa2");
stringCollection.add("bbb1");
stringCollection.add("aaa1");
stringCollection.add("bbb3");
stringCollection.add("ccc");
stringCollection.add("bbb2");
stringCollection.add("ddd1");
~~~

#### foreach（终端操作）

终端操作一般都需要搭配

~~~java
stringCollection
    .stream() //把集合转换成流进行操作
    .forEach(System.out::println);
~~~

#### filter过滤（中间操作）

~~~java
stringCollection
    .stream()
    .filter((s) -> s.startsWith("a"))
    .forEach(System.out::println);
// "aaa2", "aaa1"
~~~

#### sorted排序(中间操作)

~~~JAVA
stringCollection
    .stream()
    .sorted()
    .filter((s) -> s.startsWith("a"))
    .forEach(System.out::println);
// "aaa1", "aaa2"
~~~

#### map转换(中间操作)

~~~java
stringCollection
    .stream()
    .map(String::toUpperCase)
    .sorted((a, b) -> b.compareTo(a))
    .forEach(System.out::println);
// "DDD2", "DDD1", "CCC", "BBB3", "BBB2", "AAA2", "AAA1"
~~~

#### map匹配(中间操作)

~~~java
// 验证 list 中 string 是否有以 a 开头的, 匹配到第一个，即返回 true
boolean anyStartsWithA =
    stringCollection
        .stream()
        .anyMatch((s) -> s.startsWith("a"));

System.out.println(anyStartsWithA);      // true

// 验证 list 中 string 是否都是以 a 开头的
boolean allStartsWithA =
    stringCollection
        .stream()
        .allMatch((s) -> s.startsWith("a"));

System.out.println(allStartsWithA);      // false

// 验证 list 中 string 是否都不是以 z 开头的,
boolean noneStartsWithZ =
    stringCollection
        .stream()
        .noneMatch((s) -> s.startsWith("z"));

System.out.println(noneStartsWithZ);      // true
~~~

#### count计数(终端操作)

~~~java
// 先对 list 中字符串开头为 b 进行过滤，让后统计数量
long startsWithB =
    stringCollection
        .stream()
        .filter((s) -> s.startsWith("b"))
        .count();
System.out.println(startsWithB);    // 3
~~~

#### Reduce归约（终端操作）

~~~java
//返回类型是Optional
Optional<String> reduced =
    stringCollection
        .stream()
        .sorted()
        .reduce((s1, s2) -> s1 + "#" + s2);

reduced.ifPresent(System.out::println);
// "aaa1#aaa2#bbb1#bbb2#bbb3#ccc#ddd1#ddd2"
~~~

### 六、parallelStream并行流

`stream` 流是支持**顺序**和**并行**的。顺序流操作是单线程操作，而并行流`parallelStream`是通过多线程来处理的，能够充分利用物理机 多核 CPU 的优势，同时处理速度更快。他们常用操作方式是一样的。

### 七、Map集合

`map` 是不支持 `Stream` 流的，因为 `Map` 接口并没有像 `Collection` 接口那样，定义了 `stream()` 方法。但是，我们可以对其 `key`, `values`, `entry` 使用 流操作，如 `map.keySet().stream()`, `map.values().stream()` 和 `map.entrySet().stream()`.

### 八、日期

此处不做代码总结了，后续可看api进行总结。

#### LocalTime

#### LocalDate

#### LocalDateTIme






