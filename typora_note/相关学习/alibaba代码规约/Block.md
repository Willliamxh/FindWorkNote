# Block

## 1.**[Block] AlwaysThrows**

**类型**

代码缺陷

**描述**

编译时检测使用字面量作为部分类的方法的参数进行调用时，必定会导致运行时抛出异常的方法调用。

这些方法包括：

```java
// Time classes
java.time.Duration::parse,
java.time.Instant::parse,
java.time.LocalDate::parse,
java.time.LocalDateTime::parse,
java.time.LocalTime::parse,
java.time.MonthDay::parse,
java.time.OffsetDateTime::parse,
java.time.OffsetTime::parse,
java.time.Period::parse,
java.time.Year::parse,
java.time.YearMonth::parse,
java.time.ZonedDateTime::parse,

// Google map classes
com.google.common.collect.ImmutableMap::of,
com.google.common.collect.ImmutableBiMap::of,
com.google.common.collect.ImmutableMap.Builder::put,
com.google.common.collect.ImmutableBiMap.Builder::put,

// Google bytestring class
com.google.protobuf.ByteString::fromHex;

// UUID class
java.util.UUID::fromString;
```

**正例**

```
LocalDateTime time = LocalDateTime.parse("2022-08-02T22:45:00");

void function(String timeString) {
    LocalDateTime time = LocalDateTime.parse(timeString); // 不检测变量作为参数的场景
}
```

**反例**

```
LocalDateTime time = LocalDateTime.parse("2022-08-02T22:45:00+08:00"); // 需要拿掉时区参数
```



## 2.**[Block] ArrayEquals**

**类型**

代码缺陷

**描述**

在比较数组的相等性时，通常我们希望比较的是数组内容的相等性，而非它们是否为同一对象。但许多常用的比较方法比较的是数组引用而非数组内容是否相等，包括对象自身的equals()方法，Guava的com.google.common.base.Objects#equal()方法，JDK的java.util.Objects#equals()方法等。如果确实需要比较数组引用是否相等，明确使用“==”以避免歧义，否则应该使用java.util.Arrays#equals()来比较数组的内容是否相等。

**正例**

```
boolean arrayContentsEquality(Object[] array1, Object[] array2) {
    return Arrays.equals(array1, array2); // 比较内容是否相等
}

boolean arrayReferenceEquality(Object[] array1, Object[] array2) {
    return array1 == array2; // 比较引用是否相等
}
```

**反例**

```
boolean arrayEquality(Object[] array1, Object[] array2) {
    return array1.equals(array2); // 实际比较的是引用是否相等
}
```



## 3.**[Block] ArrayFillIncompatibleType**

**类型**

代码缺陷

**描述**

Arrays.fill(Object[], Object)是用于将对象的引用填充到数组的每一个元素中，如：

```java
String[] foo = new String[42];
Arrays.fill(foo, "life"); // foo数组中的每个元素都是指向“life”字符串在常量池中的引用
```

然而，由于数组允许子类型的数组赋值，且Arrays.fill方法的参数为Arrays.fill(Object[], Object)，因此下面的语句在编译时不会报错：

```java
String[] foo = new String[42];
Arrays.fill(foo, 42); // 但会在运行时抛出ArrayStoreException
```

这条规则检测上述场景，不允许尝试将不匹配的类型放置到数组中。

**正例**

```
String[] foo = new String[42];
Arrays.fill(foo, "life");
```

**反例**

```
String[] foo = new String[42];
Arrays.fill(foo, 42); // 会在运行时抛出ArrayStoreException
```



## 4.**[Block] ArrayToString**

**类型**

代码缺陷

**描述**

数组的toString方法会输出它的引用信息，如[I@4488aabb，这在绝大多数情况下都不表达任何实际含义。考虑使用Arrays.toString方法来输出具有可读性的信息。

**正例**

```
Integer[] intArray = new Integer[]{1, 2, 3};
System.out.println(Arrays.toString(intArray)); // 将会输出数组内容[1, 2, 3]
```

**反例**

```
Integer[] intArray = new Integer[]{1, 2, 3};
System.out.println(intArray.toString()); // 将会输出类似[I@4488aabb的信息，不表达任何实际含义
```



## 5.**[Block] ArraysAsListPrimitiveArray**

**类型**

代码缺陷

**描述**

Arrays.asList方法不会如期望地将基础类型的数组自动装箱并生成新的列表，如果想要将基础类型的数组自动装箱，可以考虑使用Guava包中提供的asList方法。如果确实需要创建只包含一个基础类型的数组为元素的列表，明确地使用Collections.singletonList方法来表达意图。

Arrays.asList():https://www.cnblogs.com/java-spring/p/15308721.html

**正例**

```java
List<Integer> intArrayToIntegerList2(int[] intArray) {
    // 正确使用jdk自建的Arrays类
    List<Integer> integerList = Arrays.stream(intArray).boxed().collect(Collectors.toList());
    return integerList;
}

List<Integer> intArrayToIntegerList(int[] intArray) {
    // 使用guava包中的com.google.common.primitives.Ints类
    List<Integer> integerList = Ints.asList(intArray);
    return integerList;
}

List<Integer> convertToIntArraySingleton(int[] intArray) {
    // 如果确实需要创建只包含一个基础类型的数组为元素的列表，使用Collections.singletonList方法
    List<int[]> intArraySingletonList = Collections.singletonList(intArray);
    return intArraySingletonList;
}
```

**反例**

```java
List<Integer> intArrayToInteger(int[] intArray) {
    List<Integer> integerList = Arrays.asList(intArray); // 编译错误，Arrays.asList方法返回值类型为List<int[]>
    return integerList;
}
```



## 6.**[Block] AvoidUsingExecutors**

**类型**

业务军规

**描述**

线程池不允许使用 Executors 去创建，而是通过 ThreadPoolExecutor的方式，规避资源耗尽的风险。

**正例**

```java
// 通过ThreadPoolExecutor方式创建受控的线程池，规避资源耗尽的风险
static final ThreadPoolExecutor THREAD_POOL_EXECUTOR = new ThreadPoolExecutor(
    coorPoolSize, maxiumPoolSize, 1, TimeUnit.HOURS, new ArrayBlockingQueue<>(maxiumPoolSize));

void function(Runnable task) {
    THREAD_POOL_EXECUTOR.submit(task);
}
```

**反例**

```java
// 通过Executors创建的线程池未设定线程数上限，存在导致资源耗尽的风险
static final ExecutorService EXECUTOR_SERVICE = Executors.newCachedThreadPool();

void function(Runnable task) {
    EXECUTOR_SERVICE.submit(task);
}
```



## 7.**[Block] BadShiftAmount**

**类型**

代码缺陷

**描述**

以对int类型进行移位运算为例：移位运算的距离参数d显然应当小于32，否则应当以d%32的结果作为移位运算的参数输入。因此移位运算会潜在地将超过31的值映射到0~31的范围内。参考JLS文档：[JLS#15.19](https://docs.oracle.com/javase/specs/jls/se8/html/jls-15.html#jls-15.19)

**正例**

```
long l = 114514L << 32; // 长整形位长度为64，因此左移32位是有意义的，
```

**反例**

```
long l = 114514 << 32; // 114514没有长整形标记L，因此类型为int，对int左移32位等同于左移0位等同于不进行任何操作
```



## 8.**[Block] BigDecimalLiteralDouble**

**类型**

业务军规

**描述**

避免使用new BigDecimal(double d)构造器将double转换为BigDecimal，这种方式通常都会导致出现奇怪的精度损失，推荐使用工厂方法BigDecimal.valueOf(double d)或是new BigDecimal(String s)构造器。

**正例**

```
// 使用工厂方法
BigDecimal oneFifth = BigDecimal.valueOf(0.2);
System.out.println(oneFifth);

// 或者使用String为参数的构造器
BigDecimal oneFifth = new BigDecimal("0.2");
System.out.println(oneFifth);
```

**反例**

```java
// 避免使用double为参数的构造器
BigDecimal oneFifth = new BigDecimal(0.2);
// 输出 0.200000000000000011102230246251565404236316680908203125
System.out.println(oneFifth);
```



## 9.**[Block] BoxedPrimitiveEquality**（重点）

**类型**

代码缺陷

**描述**

对于基本类型的包装类，比较它们的引用而非值的相等性。对包装类引用的比较更容易出现问题：基本类型的包装类缓存了部分（但不是全部）值，因此只有在存在缓存的值之间，其==运算与equals()方法的返回值是一致的。而缓存也会随着运行时和库的不同而变化，导致对Java运行时或库的升级也可能会导致缓存策略的变化。此外，基本类型包装类的引用通常也是无意义的，且提供了equals()方法去比较它们值的相等性。

**正例**



```
void compareBoxedPrimitive(Long x, Long y) {
    return Objects.equals(x, y);
}
```

**反例**

```
void compareBoxedPrimitive(Long x, Long y) {
    return x == y; // 假如Long对于-65536~65535范围内的值存在缓存，那么仅当x，y均位于这个范围内时，这个方法能正确地返回结果，否则不会
}
```



## 10.[Block] CollectionIncompatibleType

**类型**

代码缺陷

**描述**

查询一个集合中不可能存在的某个元素基本可以认定为是bug。对于一般的集合类型来说，诸如Map.get(Object)和Collection.remove(Object)的查询方法接受任意传入的参数，并尝试在集合中进行查找。这条规则会检测由于传入的参数与集合指定的泛型参数不兼容因而该参数不可能存在于这个集合中的情况。一个典型的例子是：

```
Set<Long> values = ...
if (values.contains(42)) { ... }
```

上面的代码看起来很合理，但存在着一个问题：Set包含的是Long类型的实例，而contains方法传入的参数却是Integer类型。又因为Integer类型和Long类型之间不可能存在两个相等的实例，所以这里调用的contains方法一定会返回false，显然这并不是我们期望的结果。

**正例**

```
Map<Long, String> idToValueMap = Map.of(
        1L, "1",
        2L, "2",
        3L, "3");
System.out.println(idToValueMap.get(1L));
```

**反例**

```java
Map<Long, String> idToValueMap = Map.of(
        1L, "1",
        2L, "2",
        3L, "3");
// 由于(Integer)1与(Long)1不匹配，输出为null
System.out.println(idToValueMap.get(1));
```

## 11.[Block] CollectionToArraySafeParameter

**类型**

代码缺陷

**描述**

Collection类的toArray方法有三种不同的重载，分别是toArray()，toArray(IntFunction<T[]> generator)，toArray(T[] a)，除无参的重载返回Object[]外，其余重载均会尝试将集合放置到传入的数组参数中并返回对应类型的数组。但当向toArray方法传入与原集合类型不一致类型的数组时，编译器不会在编译期给出警告，但在运行时若集合中任何元素无法转换为传入数组元素的类型时，toArray方法会抛出ArrayStoreException。为了避免发生上述情况，本条规则检测传入的数组元素类型与原集合类型参数不一致的情况并给出警告。如果确实需要进行类型转换，应当使用Stream类进行类型转换后调用Stream类的toArray(IntFunction<T[]> generator)方法获取对应类型的数组。

**正例**

```
Integer[] collectionToArray(Collection<Integer> collection) {
    return collection.toArray(new Integer[0]);
}

String[] collectionToArrayWithCast(Collection<Integer> collection) {
    return collection.stream().map(String::valueOf).toArray(String[]::new);
}
```

**反例**

```
String[] collectionToArray(Collection<Integer> collection) {
    return collection.toArray(new String[0]);
}
```

## 12.[Block] ComparableType

**类型**

代码缺陷

**描述**

Comparable接口的类型参数应当与声明实现它的类的类型相同。否则根据Comparable接口的约定要求：对于任意的x、y，x.compareTo(y) == -y.compareTo(x)。如果x和y是两个不同的类，显然无法保证这条性质成立。

**正例**

```
class A implements Comparable<A> {

    @Override
    public int compareTo(@NotNull A other) {
        // ...
    }
}
```

**反例**

```
class A implements Comparable<B> {

    @Override
    public int compareTo(@NotNull B other) {
        // ...
    }
}
```

## 13.[Block] ComparingThisWithNull

**类型**

代码缺陷

**描述**

显然this不可能为null，否则必定会先抛出NullPointerException。因此布尔运算this == null 必定为false， this != null 必定为 true。

**正例**



**反例**

```
void function() {
    if (this == null) { // this == null 始终返回false
    }
    if (this != null) { // this != null 始终返回true
    }
}
```



## 14.[Block] ConditionalExpressionNumericPromotion（重点）

**类型**

代码缺陷

**描述**

使用两个不同类型的数值作为条件运算符的第二、三个参数可能会得到一个意料之外的结果。例如：

```
Object getZeroOnCondition(boolean flag) {
    return flag ?
            Double.valueOf(0) :
            Integer.valueOf(0);
}

Object o1 = getZeroOnCondition(true);
// 将输出java.lang.Double，符合预期
System.out.println(o1.getClass());
Object o2 = getZeroOnCondition(false);
// 将输出java.lang.Double，与预期不符
System.out.println(o2.getClass());
```

即使我们明确地声明希望在两种不同的情况下分别获得Double和Integer的0值，结果却始终返回的是Double类型的0值。这是因为根据[JLS#15.25](https://docs.oracle.com/javase/specs/jls/se8/html/jls-15.html#jls-15.25)，不同的数值类型间的运算首先会经过双目类型提升（Binary Numeric Promotion）将不同类型的数值转化为同一类型后再进行计算。因此上述的运算实际上与下面的运算是等同的：

```
Object getZeroOnCondition(boolean flag) {
    return Double.valueOf(
            flag ?
                    Double.valueOf(0).doubleValue() :
                    (double) Integer.valueOf(0).intValue());
}
```

如果想要在两种不同的情况下获得不同类型的0值，可以直接显式地将数值类型转换为非数值类型，或者使用分支语句。

**正例**

```
// 显式地将数值类型转换为非数值类型
Object getZeroOnConditionWithCast(boolean flag) {
    return flag ?
            (Object) Double.valueOf(0) :
            (Object) Integer.valueOf(0);
}

// 或者使用分支语句
Object getZeroOnConditionWithBranch(boolean flag) {
    if (flag) {
        return Double.valueOf(0);
    } else {
        return Integer.valueOf(0);
    }
}
```

**反例**

```
Object getZeroOnCondition(boolean flag) {
    return flag ?
            Double.valueOf(0) :
            Integer.valueOf(0);
}
```

### 

## 15.[Block] ConstantOverflow

**类型**

代码缺陷

**描述**

编译期表达式结果溢出通常都是潜在的bug来源。不以L结尾的数值常量类型为int，对其进行乘法运算的结果完全有可能超出int能表示的范围而导致溢出。

**正例**

```
// 显式声明24为长整形，避免结果溢出
static final long NANOS_PER_DAY = 24L * 60 * 60 * 1000 * 1000 * 1000;
```

**反例**

```
// 由于先执行乘法运算获取结果后最后进行类型转换，而中间结果超过INT_MAX，导致溢出
static final long NANOS_PER_DAY = 24 * 60 * 60 * 1000 * 1000 * 1000; 
```



## 16.[Block] DeadException

**类型**

代码缺陷

**描述**

创建了新的异常实例，且从未抛出或传递其引用，通常都是忘记抛出该异常。

**正例**

```
void function() {
    SomeException someException = new SomeException();
    // ...
    throw someException;
}

// 如果需要获取堆栈信息，使用Thread#getStackTrace()方法
StackTraceElement[] ste = Thread.currentThread().getStackTrace();
```

**反例**

```
void function() {
    SomeException someException = new SomeException(); 
    // ...
    // 异常被声明但从未被抛出过或传递其引用
}

// 如果需要获取堆栈信息，不要直接创建新的Exception并调用getStackTrace()方法
StackTraceElement[] ste = (new Exception()).getStackTrace();
```



## 17.[Block] DeadThread

**类型**

代码缺陷

**描述**

创建了新的Thread实例但是从未调用过start()方法或传递其引用。Thread需要调用start()方法来实际执行。

**正例**

```
void function() {
    Thread someThread = new Thread();
    // ...
    someThread.start();
}
```

**反例**

```
void function() {
    Thread someThread = new Thread(); // 线程被声明但从未被使用或调用start方法
    // ...
}
```



## 18.[Block] EqualsNull

**类型**

代码缺陷

**描述**

Object.equals方法约定：对于任意不为null的引用值x，x.equals(null)应当返回false，因此调用x.equals(null)要么返回false ，要么会在x为null的情况下抛出NullPointerException。如果需要判断x是否为null，直接使用“==”运算符。

**正例**

```
void function(Object obj) {
    if (obj == null) {
        // ...
    }
}
```

**反例**

```
void function(Object obj) {
    // obj.equals(null)根据约定应该始终返回false,或因为obj为null导致抛出NullPointerException
    if (obj.equals(null)) {
        // ...
    }
}
```



## 19.[Block] EqualsWrongThing

**类型**

代码缺陷

**描述**

equals方法比较了两个不相关的属性。

**正例**

```java
class Frobnicator {
    private int a;
    private int b;

    @Override
    public boolean equals(Object other) {
        if (!(other instanceof Frobnicator)) {
            return false;
        }
        Frobnicator that = (Frobnicator) other;
        return a == that.a && b == that.b;
    }
}
```

**反例**

```java
class Frobnicator {
    private int a;
    private int b;

    @Override
    public boolean equals(Object other) {
        if (!(other instanceof Frobnicator)) {
            return false;
        }
        Frobnicator that = (Frobnicator) other;
        return a == that.a && b == that.a; // 应该为b == that.b;
    }
}
```

## 20.[Block] FloatValueEquality

**类型**

业务军规

**描述**

避免使用“==”运算符或equals方法对浮点数之间的相等性进行比较，浮点数之间相等性比较应当通过BigDecimal类实现，如果确实需要直接比较浮点数之间的相等性，应当给定对应的误差值。

**正例**

```
// 通过BigDecimal类实现比较
boolean floatEquals(double x, double y) {
    return BigDecimal.valueOf(x).equals(BigDecimal.valueOf(y));
}
```

**反例**

```java
boolean floatEquals(double x, double y) {
    return x == y;
}
```



## 21.[Block] FormatString

**类型**

代码缺陷

**描述**

对字符串进行格式化需要遵循[java.util.Formatter](https://docs.oracle.com/javase/8/docs/api/java/util/Formatter.html)的文档。若格式化字符串要求的参数与提供的参数类型或数量不一致时，问题代码在编译时不会报错，但在执行到问题代码处时会抛出“IllegalFormatConversionException”。

**正例**

```
String format(int id, String path) {
    return String.format(
                // 格式化字符串要求两个参数
                "User:%d(%s)%n",
                // 提供两个参数
                id, path);
}
```

**反例**

```
String format(int id, String path) {
    return String.format(
                // 格式化字符串要求四个参数
                "User:%d(%s)%nNameSpace:%d(%s)",
                // 仅仅提供两个参数，只有当执行到此处时才会抛出IllegalFormatConversionException
                id, path);
}
```

## 22.[Block] GetClassOnAnnotation

**类型**

代码缺陷

**描述**

调用注解接口实例的getClass()方法通常会返回一个随机的代理类，如果想要获取实际的注解类型，应当使用annotationType()方法。

**正例**

```
@Deprecated
public class Test {
    static void printAnnotationClass(Annotation annotation) {
        // 将会输出java.lang.Deprecated
        System.err.println(annotation.annotationType());
    }

    public static void main(String[] args) {
        printAnnotationClass(Test.class.getAnnotation(Deprecated.class));
    }
}
```

**反例**

```
@Deprecated
public class Test {
    static void printAnnotationClass(Annotation annotation) {
        // 将会输出com.sun.proxy.$Proxy1
        System.err.println(annotation.getClass());
    }

    public static void main(String[] args) {
        printAnnotationClass(Test.class.getAnnotation(Deprecated.class));
    }
}
```

## 23.[Block] HashtableContains

**类型**

代码缺陷

**描述**

Hashtable和ConcurrentHashMap的contains方法是用于检测传入的实例是否存在于值集合（Value Set）中。这个方法经常会被误用为containsKey，而containsKey方法是用于检测传入的参数是否存在于其键集合（Key Set）中。如果希望检测某个值是否存在于值集合中，应当明确调用containsValue方法。

**正例**

```
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("1", "01");
hashtable.put("2", "02");
hashtable.put("3", "03");
hashtable.containsKey("1"); // true
hashtable.containsValue("01"); // true
```

**反例**

```
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("1", "01");
hashtable.put("2", "02");
hashtable.put("3", "03");
hashtable.contains("1"); // false
hashtable.contains("01"); // true
```

## 24.[Block] IdentityBinaryExpression

**类型**

代码缺陷

**描述**

使用同一对象或表达式作为二元运算的参数一般来说都是误用，因为这样的二元表达式的值都是不变的：

- a && a， a || a， a & a， a | a 的结果始终为a
- a <= a， a >= a， a == a 的结果始终为true
- a < a， a > a， a != a，a ^ a的结果始终为false
- a / a 的结果始终为1
- a % a， a - a 的结果始终为0

如果对该对象的引用或执行该表达式有其他副作用，应当明确地声明一个临时变量以标识。

**正例**

```java
class SomeClass {
    int useCount;

    public boolean check() {
        boolean checkResult;
        // check方法存在逻辑
        useCount++;
        // ...
        return checkResult;
    }
}

void function(SomeClass instance) {
    // 声明临时变量以标识
    boolean checkResult = instance.check();
    if (checkResult && instance.check()) {
        // ...
    }
}
```

**反例**

```java
class SomeClass {
    int useCount;

    public boolean check() {
        boolean checkResult;
        // ...
        return checkResult;
    }
}

void function(SomeClass instance) {
    if (instance.check() && instance.check()) {
        // ...
    }
}
```

### 

## 25.[Block] IdentityHashMapBoxing

**类型**

代码缺陷

**描述**

由于IdentitiyHashMap通过引用来判断键值的相等性，而基础类型包装类的引用相等性存在着严重的潜在问题：基础类型包装类会缓存部分（而非所有）的值，因此位于缓存范围内值的对象会指向同一引用，而缓存范围外的对象的引用则各不相同。此外，缓存范围会随着JDK版本与库的不同而变化。使用基础类型的包装类作为IdentityHashMap的键值存在严重的风险。

**正例**

```java
void function() {
    Random random = new Random();
    // 不要使用IdentityHashMap
    Map<Integer, String> map = new HashMap<>();
    for (int i = 0; i < 100; ++i) {
        int randomInt = random.nextInt();
        map.put(randomInt, String.valueOf(randomInt));
        String value = map.get(randomInt);
        // HashMap使用equals方法进行值比较，不会出现问题
        if (value == null) {
            throw new RuntimeException("Value is null");
        }
    }
}
```

**反例**

```java
void function() {
    Random random = new Random();
    Map<Integer, String> map = new IdentityHashMap<>();
    for (int i = 0; i < 100; ++i) {
        int randomInt = random.nextInt();
        map.put(randomInt, String.valueOf(randomInt));
        String value = map.get(randomInt);
        if (value == null) {
            // 由于包装类的缓存机制，在缓存内的值将返回同一实例，而缓存外的值则会创建一个新的实例之后返回
            throw new RuntimeException("Value is null");
        }
    }
}
```

## 26.[Block] IndexOfChar

**类型**

代码缺陷

**描述**

String.indexOf(int, int)方法中的两个整型参数，第一个参数应为需要查找的字符对应的整型值，第二个参数为开始查找的起始下标。

**正例**

```
int findIndexOfChar(String s, char ch, int fromIndex) {
    // indexOf方法的签名为String.indexOf(int ch, int fromIndex)，即字符在前，开始搜索的位置在后
    return s.indexOf(ch, fromIndex); 
}
```

**反例**

```
int findIndexOfChar(String s, char ch, int fromIndex) {
    // 但由于两个参数类型均为int，所以调换参数也可以通过编译，但是会导致运行结果不符合预期
    return s.indexOf(fromIndex, ch); 
}
```



## 27.[Block] InexactVarargsConditional

**类型**

代码缺陷

**描述**

对于可变长参数方法，它接受一个明确的数组数组作为参数，或是单独的每个参数：

```java
void varargsFunction(Object... args) {
    System.out.println(Arrays.deepToString(args));
}

// 两种方式传递的参数是相同的，都会输出[1, 2, 3.0]
varargsFunction(new Object[]{"1", 2, 3.0});
varargsFunction("1", 2, 3.0);
```

如果变长参数是一个条件表达式，而且存在一个分支不是数组，那么表达式的结果会潜在地被包装在一个新的数组中，例如：

```java
// 将会输出[1]
varargsFunction(true ? 1 : 2);
// 将会输出[2]
varargsFunction(false ? 1 : 2);
```

因此如果在条件表达式中混用数组和非数组参数的话，会导致数组分支的参数成为一个高维数组，而这一般都不是我们期望的结果。

**正例**

```java
void varargsFunction(Object... args) {
    System.out.println(Arrays.deepToString(args));
}

// 将非数组分支的参数也包装在数组中
varargsFunction(flag ? new Object[] {1, 2} : new Object[] {3});
```

**反例**

```java
void varargsFunction(Object... args) {
    System.out.println(Arrays.deepToString(args));
}

// 存在非数组分支，导致数组分支的参数成为了高维数组
varargsFunction(flag ? new Object[] {1, 2} : 3); // 将会输出 [[1, 2]] 或 [3]，即数组被额外包装了一层
```

## 28.[Block] InfiniteRecursion

**类型**

代码缺陷

**描述**

一个总是递归调用自身的方法会导致堆栈溢出。

**正例**

```
void function(boolean condition) {
    boolean newCondition;
    // 头递归
    if (condition) {
        function(newCondition);
    }
    // 尾递归
}
```

**反例**

```
void function() {
    // 递归调用自身，必然导致堆栈溢出
    function();
}
```

### 





