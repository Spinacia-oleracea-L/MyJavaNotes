# 1. 概述

## 1.1 为什么学？

* 大数据量下，处理**集合**的效率高
* 代码可读性高
* 消灭嵌套地狱

```java
//查询未成年作家的评分在 70 以上的书籍由于洋流影响所以作家和书籍可能出现重复，需要进行去重
List<Book> bookList = new ArrayList<>();
Set<Book> uniqueBookValues = new HashSet<>();
Set<Author> uniqueAuthorVa1ues = new HashSet<>();
for (Author author : authors) {
    if (uniqueAuthorVaIues.add(author)) {
        if (author.getAge() < 18){ 
            List<Book> books = author getBooks();
            for (Book book : books) { 
                if (book.getScore() > 70) {
                    if (uniqueB00kVaIues .add(book)) {
                        bookList.add(book);
                    }
                }
            }
        }
    }
}
System. nt1n(bookList);
```

```java
List<Book> collect = authors.stream()
    .distinct()
    .filter(author -> author.getAge() < 18)
    .map(author -> author.getBooks())
    .flatMap(Collection::stream)
    .filter(book -> book.getScore() > 70)
    .distinct()
    .collect(collectors.toList());
System.out.println(collect);
```

## 1.2 函数式编程思想

### 1.2.1 概念

**面向对象**思想需要关注用什么对象完成什么事情。

而**函数式编程**思想就类似于数学中的函数，关注是对数据进行了什么操作。

### 1.2.2 优点

* 代码简洁，开发快速
* 接近自然语言，易于理解
* 易于“并发编程”

# 2. Lambda表达式

## 2.1 概述

Lambda是JDK8中的一个语法糖。可以看作对某些匿名内部类的简化写法，是对函数式编程思想的一个重要体现。让我们不用关注什么是对象，而更关注对数据进行了哪些操作。

### 2.2 核心原则

>  可推到可省略(可以推导出来的代码、关键字，就可以省略不写)

### 2.3 基本格式

```java
(参数列表) -> {代码块}
```

#### Eg.1

创建线程并启动时可以使用匿名内部类的写法：

```java
new Thread(new Runnable() {
    @Override
    public void run() {
        System.out.println("test content");
    }
}).start();
```

使用Lambda格式对其进行修改：

```java
new Thread(() -> {System.out.println("test content");}).start();
```

#### Eg.2

现有方法定义如下，其中IntBinaryOperator是一个接口。先使用匿名内部类调用该方法：

```java
public static void main(String[]args) {
    int result = calculateNum(new IntBinaryOperator() {
        @Override
        public int applyAsInt(int left, int right) {
            return left + right;
        }
    });
    System.out.println(result);
}

public static int calculateNum(IntBinaryOperator operator) {
    int a = 10;
    int b = 20;
    return operator.applyAsInt(a b);
}
```

Lambda写法：

```java
public static void main(String[]args) {
    int result = calculateNum((left, right) -> {left + right});
    System.out.println(result);
}

public static int calculateNum(IntBinaryOperator operator) {
    int a = 10;
    int b = 20;
    return operator.applyAsInt(a b);
}
```

#### Eg.3

现有方法定义如下，其中IntPredicate是一个接口。先使用匿名内部类调用该方法：

```java
public static void main(String[]args) {
    printNum(new IntPredicate() {
        @Override
        public boolean test(int value) {
            return value % 2 == 0;
        }
    });
}
public static void printNum(IntPredicate predicate) {
    int[] arr = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
    for(int i : arr){
        if(predicate.test(i)) {
            System.out.println(i);
        }
    }
}
```

Lambda写法：

```java
public static void main(String[]args) {
	printNum(value -> value % 2 == 0);
}
public static void printNum(IntPredicate predicate) {
    int[] arr = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
    for(int i : arr){
        if(predicate.test(i)) {
            System.out.println(i);
        }
    }
}
```

#### Eg.4

现有方法定义如下，其中Function是一个接口。先使用匿名内部类调用该方法：

```java
public static void main(String[]args) {	        
    Integer result1 = typeConver(new Function<String, Integer>() {
        @Override
        public Integer apply(String s) {
            return Integer.parseInt(s);
        }
     });
    System.out.println(result1);
    
    String result2 = typeConver(new Function<String, String>() {
        @Override
        public String apply(String s) {
            return s + "text";
        }
     });
    System.out.println(result2);
}
public static <R> R typeConver(Function<String, R> function) {
    String str = "12345";
    R result = function.apply(str);
    return result;
}
```

Lambda写法：

```java
public static void main(String[]args) {	        
	Integer result1 = typeConver(s -> Integer.parseInt(s));
    System.out.println(result1);
    
    String result2 = typeConver(s -> s + "text");
    System.out.println(result2);
}
public static <R> R typeConver(Function<String, R> function) {
    String str = "12345";
    R result = function.apply(str);
    return result;
}
```

#### Eg.5

现有方法定义如下，其中IntConsumer是一个接口。先使用匿名内部类调用该方法：

```java
public static void main(String[]args) {	        
    foreachArr(new IntConsumer() {
        @Override
        public void accept(int value) {
            System.out.println(value);
        }
    });
}

public static void foreachArr(IntConsumer consumer) {
    int[] arr = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
    for(int i : arr) {
        consumer.accept(i);
    }
}
```

Lambda写法：

```java
public static void main(String[]args) {	        
	foreachArr(value -> System.out.println(value));
}
public static void foreachArr(IntConsumer consumer) {
    int[] arr = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
    for(int i : arr) {
        consumer.accept(i);
    }
}
```

### 2.4 省略规则

* **参数类型**可以省略
* 只有一个参数时**小括号**可以省略
* 方法体只有一句代码时，“**大括号**”、“**return**” 和唯一一句代码的“**分号**”可以省略

# 3. stream流

## 3.1 概述

Java8的Stream使用的是函数式编程模式，如同他的名字一样，它可以被用来对**集合**或者**数组**进行链状流式的操作。可以方便我们对集合、数组的操作。

## 3.2 案例数据准备

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
@Equa1sAndHashcode//用于后期的去重使用
public class Author {
    private Long id;
    //姓名
    private String name;
    //年龄
    private Integer age;
    //简介
    private String intro;
    //作品
    private List<Book> books;
}
```

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
@Equa1sAndHashcode//用于后期的去重使用
public class Book {
    private Long id;
    //书名
    private String name;
	//分类
    private String category;
    //评分
    private Integer score;
    //简介
    private String intro;
}
```

```JAVA
private static List<Author> getAuthors() {
    //数据初始化
    Author author1 = new Author(1L, "蒙多", 33, "一个从菜刀中明悟哲理的祖安人", null);
    Author author2 = new Author(2L, "亚拉索", 15, "狂风也追逐不上他的思考速度", null);
    Author author3 = new Author(3L, "易", 14, "是这个世界在限制他的思维", null);
    Author author4 = new Author(3L, "易", 14, "是这个世界在限制他的思维", null);
    //书籍列表
    List<Book> books1 = new ArrayList<>();
    List<Book> books2 = new ArrayList<>();
    List<Book> books3 = new ArrayList<>();
    books1.add(new Book(1L, "刀的两侧是光明与黑暗", "哲学,爱情", 88, "用一把刀划分了爱恨"));
    books1.add(new Book(2L, "个人不能死在同一把刀下", "个人成长,爱情", 99, "讲述如何从失败中明悟真理"));
    books2.add(new Book(3L, "那风吹不到的地方", "哲学", 85, "带你用思维去领略世界的尽头"));
    books2.add(new Book(3L, "那风吹不到的地方", "哲学", 85, "带你用思维去领略世界的尽头"));
    books2.add(new Book(4L, "吹或不吹", "爱情,个人传记", 56, "一个哲学家的恋爱观注定很难把他所在的时代理解"));
    books3.add(new Book(5L, "你的剑就是我的剑", "爱情", 56, "无法想象一个武者能对他的伴侣这么的宽容"));
    books3.add(new Book(6L, "风与剑", "个人传记", 100, "两个哲学家灵魂和肉体的碰撞会激起怎么样的火花呢？"));
    books3.add(new Book(6L, "风与剑", "个人传记", 100, "两个哲学家灵魂和肉体的碰撞会激起怎么样的火花？"));
    author1.setBooks(books1);
    author2.setBooks(books2);
    author3.setBooks(books3);
    author4.setBooks(books3);
    List<Author> authorList = new ArrayList<>(Arrays.asList(author1, author2, author3, author4));
    return authorList;
}
```

## 3.3 快速入门

### 3.3.1 需求

我们可以调用getAuthors方法获取到作家集合。现在需要打印所以年龄小于18的作家名字，并且要注意去重。

### 3.3.2 实现

```java
//打印所有年龄小于18的作家的名字，并且要注意去重
List<Author> authors = getAuthors();
authors.stream()//把集合转换成流
    .distinct()//先去除重复的作家
    .filter(author -> author.getAge() < 18)//筛选年龄小于18的
    .forEach(author -> System.out.printn(author.getName()));//遍历打印名字
```

## 3.4 常用操作

### 3.4.1 创建流

单列集合：`集合对象.stream()`

```java
List<Author> authors = getAuthors();
Stream<Author> stream = authors.stream();
```

数组：`Arrays.stream(数组)`或者使用`Stream.of`来创建

```java
lnteger[] arr = {1, 2, 3, 4, 5};
Stream<Integer> stream1 = Arrays.stream(arr);
Stream<Integer> stream2 = Stream.of(arr);
```

双列集合：转换成单列集合后再创建

```java
Map<String, Integer> map = new HashMap<>();
map.put("蜡笔小新", 19);
map.put("黑子", 17);
map.put("日向翔阳", 16);
Stream<Map.Entry<String, Integer>> stream  = map.entrySet().stream();
```

### 3.4.2 中间操作

#### filter

> 可以对流中的元素进行条件过滤，符合过滤条件的才能继续留在流中。

例如：打印所有姓名长度大于1的作家姓名

```java
List<Author> authors = getAuthors();
authors.stream()
    .filter(author -> author.getName.lenght() > 1)
    .forEach(author -> System.out.println(author.getName()));
```

#### map

> 可以对流中的元素进行计算或转换

例如：打印所有作家的姓名

```java
List<Author> authors = getAuthors();
authors.stream()
    .map(author -> author.getName)// 转换 Author -> String
    .forEach(name -> System.out.println(name));

authors.stream()
    .map(author -> author.getAge())// 转换 Author -> Integer
    .map(age -> age + 10)// 计算
    .forEach(age -> System.out.println(age));
```

#### distinct

> 可以去除流中的重复元素

例如：打印所有作家的姓名，并且不能有重复元素

```java
// 注意！！！distinct方法是依赖Object的equals方法来判断是否是相同对象的，所以需要注意重写equals方法。
List<Author> authors = getAuthors();
authors.stream()
    .distinct()
    .forEach(author -> System.out.println(author.getName()));
```

#### sorted

> 可以对流中的元素进行排序

例如：打印所有作家的年龄，并且按照年龄进行降序排序

```java
// 空参sorted()与distinct()类似
List<Author> authors = getAuthors();
authors.stream()
    .distinct()
    .sorted() // 调用无参sorted()方法时，stream中的元素，需要实现comparable接口
    .forEach(author -> System.out.println(author.getAge()));

@Data
@NoArgsConstructor
@AllArgsConstructor
@Equa1sAndHashcode//用于后期的去重使用
public class Author implements Comparable<Author> {
    private Long id;
    //姓名
    private String name;
    //年龄
    private Integer age;
    //简介
    private String intro;
    //作品
    private List<Book> books;
    
    @Override
    public int compareTo(Author o) {
        return o.getAge() - this.getAge();
    }
}
```

```java
// 有参sorted()
List<Author> authors = getAuthors();
authors.stream()
    .distinct()
    .sorted(new Comparator<Author>() {
        @Override
        public int compare(Author o1, Author o2) {
            return o2.getAge() - o1.getAge();
        }
    })
    .forEach(author -> System.out.println(author.getAge()));

List<Author> authors = getAuthors();
authors.stream()
    .distinct()
    .sorted((o1, o2) -> o2.getAge() - o1.getAge())
    .forEach(author -> System.out.println(author.getAge()));
```

#### limit

> 可以设置流的最大长度，超出的部分将被抛弃

例如：对流中的元素按照年龄进行降序排序，并且要求不能有重复的元素，然后打印其中年龄最大的两个作家的姓名

```java
List<Author> authors = getAuthors();
authors.stream()
    .distinct()
    .sorted((o1, o2) -> o2.getAge() - o1.getAge())
    .limit(2)
    .forEach(author -> System.out.println(author.getName()));
```

#### skip

> 跳过流中的前n个元素，返回剩下的元素

例如：打印除了年龄最大的作家以为的其他作家，要求不能有重复元素，并且按照年龄降序排序

```java
List<Author> authors = getAuthors();
authors.stream()
    .distinct()
    .sorted((o1, o2) -> o2.getAge() - o1.getAge())
    .skip(1)
    .forEach(author -> System.out.println(author.getAge()));
```

#### flatMap

> map只能把一个对象转换成另一个对象来作为流中的元素(1对1)
>
> 而flatMap可以把一个对象转换成多个对象作为流中的元素(1对N)

例一：打印所有数据的名字，并对元素去重

```java
List<Author> authors = getAuthors();
authors.stream()
    .flatMap(new Function<Author,Stream<Book>>() {
        @Override
        public Stream<Book> apply(Author author) {
            // author.getBooks()获取的是Book对象的List集合；并且通过`集合对象.stream()`再转换为流
            return author.getBooks().stream()
        }
    })
    .distinct()
    .forEach(new Consumer<Book>() {
        @Override
        public void accept(Book book) {
            System.out.println(book.getName());
        }
    });

List<Author> authors = getAuthors();
authors.stream()
    .flatMap(author -> author.getBooks().stream())
    .distinct()
    .forEach(book -> System.out.println(book.getName()));
```

例二：打印现有数据的所有分类，要求将“哲学，爱情”分开分别作为分类元素，并对分类去重

```java
List<Author> authors = getAuthors();
authors.stream()
    .flatMap(author -> author.getBooks().stream())
    .distinct()
    .flatMap(book -> Arrays.stream(book.getCategory().split(",")))
    .distinct()
    .forEach(category -> System.out.println(category));
```

### 3.4.3 终结操作

#### forEach

> 对流中的元素进行遍历操作，我们通过传入的参数去指定对遍历元素进行什么具体操作

例如：输出所有作家姓名

```java
List<Author> authors = getAuthors();
authors.stream()
    .map(author -> author.getName())
    .distinct()
    .forEach(name -> System.out.println(name));
```

#### count

> 可以用来获取当前流中元素的个数

例如：打印这些作家的所出书籍数目，注意删除重复元素。

```java
List<Author> authors = getAuthors();
long count = authors.stream()
    .flatMap(author -> author.getBooks().steam())
    .distinct()
    .count();
System.out.println(count);
```

#### max&min

> 可以用来获取流中的最值

例如：分别获取这些作家所出书籍的最高分和最低分

```java
List<Author> authors = getAuthors();
Optional<Integer> maximum = authors.stream()
    .flatMap(author -> author.getBooks().steam())
    .map(book -> book.getScore())
    .max(new Comparator<Integer>() {
        @Override
        public int compare(Integer score1, Integer score2) {
            return score1 - score2;
        }
    });
System.out.println(maximum.get());

List<Author> authors = getAuthors();
Optional<Integer> maximum = authors.stream()
    .flatMap(author -> author.getBooks().steam())
    .map(book -> book.getScore())
    .max((score1, score2) -> score1 - score2);
System.out.println(maximum.get());
```

```java
List<Author> authors = getAuthors();
Optional<Integer> minimum = authors.stream()
    .flatMap(author -> author.getBooks().steam())
    .map(book -> book.getScore())
    .min((score1, score2) -> score1 - score2);
System.out.println(minimum.get());
```

#### collect

> 把当前流转换成一个集合

例如：获取一个存放所有作者名字的List集合

```java
List<Author> authors = getAuthors();
List<String> nameList = authors.stream()
    .map(author -> author.getName())
    .collect(Collectors.toList()); // java.util.stream.Collectors;
System.out.println(nameList);
```

例如：获取一个所有书籍的Set集合

```java
List<Author> authors = getAuthors();
List<Book> bookSet = authors.stream()
    .flatMap(author -> author.getBooks().stream())
    .collect(Collectors.toSet()); // java.util.stream.Collectors;
System.out.println(bookSet);
```

例如：获取一个key为作者名，value为`List<Book>`map集合

```java
List<Author> authors = getAuthors();
Map<String, List<Book>> authorMap = authors.stream()
    .distinct()
    .collect(Collectors.toMap(new Function<Author, String>() {
        @Override
        public String apply(Author author) {
            return author.getName();
        }
    }, new Function<Author, List<Book>>) {
        @Override
        public List<Book> apply(Author author) {
            return author.getBooks();
        }
    }));
System.out.println(authorMap);

List<Author> authors = getAuthors();
Map<String, List<Book>> authorMap = authors.stream()
    .distinct()
    .collect(Collectors.toMap(author -> author.getName(), author -> author.getBooks()));
System.out.println(authorMap);
```

#### 查找与匹配

##### anyMatch

> 可以用来判断是否有任意符合匹配条件的元素，结果为boolean类型

例如：判断是否有年龄在29岁以上的作家

```java
List<Author> authors = getAuthors();
boolean flag = author.stream()
    .anyMatch(new Predicate<Author>() {
        @Override
        public boolean test(Author author) {
            return author.getAge() > 29;
        }
    });
System.out.println(flag);

List<Author> authors = getAuthors();
boolean flag = author.stream()
    .anyMatch(author -> author.getAge() > 29);
System.out.println(flag);
```

##### allMatch

> 可以用来判断是否都符合条件，结果为boolean类型(都符合结果才为true)

例如：判断是否所有作家都是成年人

```java
List<Author> authors = getAuthors();
boolean flag = author.stream()
    .allMatch(author -> author.getAge() >= 18);
System.out.println(flag);
```

##### noneMatch

> 可以判断流中的元素是否都不符合匹配条件(都不符合结果才为true)

例如：判断作家是否都没有超过100岁的

```java
List<Author> authors = getAuthors();
boolean flag = author.stream()
    .noneMatch(author -> author.getAge() > 100);
System.out.println(flag);
```

##### findAny

> 获取流中的任意一个元素(该方法无法保证获取的一定是流中的第一个元素)

例如：获取任意一个年龄大于18的作家，如果存在就输出他的姓名

```java
List<Author> authors = getAuthors();
Optional<Author> optionalAuthor = author.stream()
    .filter(author -> author.getAge() > 18)
    .findAny();
optionalAuthor.ifPresent(author -> System.out.println(author.getName()));
```

##### findFirst

> 获取流中的第一个元素

例如：获取一个年龄最小的作家，并输出他的姓名

```java
List<Author> authors = getAuthors();
Optional<Author> optionalFirst = author.stream()
    .sorted((author1, author2) -> author1.getAge() -> author2.getAge())
    .findFirst();
optionalFirst.ifPresent(author -> System.out.println(author.getName()));
```

#### reduce

> **归并**：对流中的数据按照你指定的计算方式计算出一个结果(缩紧操作)。
>
> reduce的作用是把stream中的元素给组合起来，我们可以传入一个初始值，它会按照我们的计算方式，依次拿流中的元素在初始值的基础上进行计算，计算结果再和后面的元素计算。
>
> reduce两个参数的重载，内部计算方式如下：
>
> ```java
> T result = identity;
> for(T element : this stream)
>     result = accumulator.apply(result, element)
> return result;
> ```
>
> 其中identity就是我们可以通过方法参数传入的初始值，accumulator的apply具体进行什么计算也是我们通过方法参数来确定的

例一：使用reduce求所有作者年龄和

```java
List<Author> authors = getAuthors();
Integer sum = author.stream()
    .distinct()
    .map(author -> author.getAge())
    // 传入初始值'0'，binary adj.二进制的 n.二进制数
    .reduce(0, new BinaryOperator<Integer>() {
        @Override
        public Integer apply(Integer result, Integer element) {
            return result + element
        }
    });
System.out.println(sum);

List<Author> authors = getAuthors();
Integer sum = author.stream()
    .distinct()
    // map&reduce，在google内部十分流行，传播后称为map-reduce模式
    .map(author -> author.getAge())
    .reduce(0, (result, element) -> result + element);
System.out.println(sum);
```

例二：使用reduce求所有作者中的年龄最大值

```java
List<Author> authors = getAuthors();
Integer max = author.stream()
    .map(author -> author.getAge())
    .reduce(Integer.MIN_VALUE, (result, element) -> result < element ? element : result);
System.out.println(max);
```

例三：使用reduce求所有作者中的年龄最小值

```java
List<Author> authors = getAuthors();
Integer min = author.stream()
    .map(author -> author.getAge())
    .reduce(Integer.MAX_VALUE, (result, element) -> result > element ? element : result);
System.out.println(min);
```

> 补充：reduce单参数的重载(无初始值)，内部计算方式如下：
>
> ```java
> boolean foundAny = flase;
> T result = null;
> for(T element : this stream) {
>     if(!foundAny) {
>         foundAny = true;
>         result = element;
>     }
>     else
>         result = accumulator.apply(result, element);
> }
> return foundAny ? Optional.of(result) : Optional.empty();
> ```
>
> ```java
> List<Author> authors = getAuthors();
> Optional<Integer> minOptional = author.stream()
>     .map(author -> author.getAge())
>     .reduce(new BinaryOperator<Integer>() {
>         @Override
>         public Integer apply(Integer result, Integer element) {
>             return result > element ? element : result;
>         }
>     });
> minOptional.ifPresent(age -> System.out.println(age));
> 
> List<Author> authors = getAuthors();
> Optional<Integer> minOptional = author.stream()
>     .map(author -> author.getAge())
>     .reduce((result, element) -> result > element ? element : result);
> minOptional.ifPresent(age -> System.out.println(age));
> ```

## 3.5 注意事项

* 惰性求值（如果没有终结操作，中间操作是不会得到执行的）
* 流是一次性的（一旦一个流对象经过一个终结操作后，这个流就不能再被使用）
* 不会影响原数据（我们在流中可以对数据进行很多处理，但正常情况下不会影响集合中原本的元素，这符合我们的需求）

# 4. Optional

## 4.1 概述

我们在编写代码时，出现最多的就是空指针异常。所以通常需要进行各种非空判断

```java
Author author = getAuthor();
if(author != null) {
    System.out.println(author.getName());
}
```

尤其是对象中的属性还是一个对象的情况下，这种判断会更多。

而过多的判断语句会让我们的代码臃肿不堪。

所以在JDK8中引入了Optional，养成使用Optional的习惯后，你可以写出更优雅的代码来避免空指针异常。

并且在很多函数式编程相关的API中也都用到了Optional

## 4.2 使用

### 4.2.1 创建对象

Optional就像是包装类，可以把我们的具体数据封装到Optional对象内部。使用Optional中封装好的方法操作封装进去的数据，可以优雅的避免空指针异常。

#### `ofNullable()`

> 我们一般使用Optional的静态方法`ofNullable()`来把数据封装成一个Optional对象。无论传入的参数是否为null都不会出现异常
>
> ```java
> Author author = getAuthor();
> Optonal<Author> authorOptional = Optional.ofNullable(author);
> ```
> 

你可能会觉得还要加一行代码来封装数据比较麻烦。但是如果改造下`getAuthor()`方法，让其的返回值就是封装好的Optional的话，我们使用时就会方便很多。

而且在实际开发中我们的数据很多是从数据库获取的。Mybatis从 3.5 版本可以也已经支持 Optional了。我们可以直j接把dao方法的返回类型定义成Optional类型，MyBastis会自己把数据封装成Optional对象返回。封装的过程也不需要我们自己操作。

#### `of()`

> 如果你**确定一个对象不是空**的则可以使用Optional的**静态方法**`of()`来吧数据封装成Optional对象
>
> ```java
> Author author = new Author();
> Optonal<Author> authorOptional = Optional.of(author);
> ```

但是一定要注意，如果使用of()的时候传入的参数必须不为null。

#### `empty()`

> 如果一个方法的返回值类型是Optional类型。而如果我们经判断发现某次计算得到的返回值为null，这个时候就需要把null封装成Optional对象返回。这时则可以使Optional的**静态方法**`empty()`来进行封装
>
> ```java
> Optional.empty();
> ```
>
> ```java
> // 手动ofNullable()
> public static Optional<T> ofNullable(T t) {
>     return t == null ? Optional.empty() : Optional.of(t);
> }
> ```

### 4.2.2 安全消费值

> 我们获取到一个Optional对象后肯定需要对其中的数据进行使用。这时候我们可以使用其 `ifPresent()`方法来消费其中的值。这个方法会判断其内封装的数据是否为空，不为空时才会执行具体的消费代码。这样使用起来就更加安全了。

例如：以下写法就优雅的避免了空指针异常

```java
Optional<Author> authorOptional = Optional.ofNullable(getAuthor());
authorOptional.ifPresent(author -> system.out.println(author.getName()));
```

### 4.2.3 获取值

> **不推荐**：若想获取Optional值自己进行处理，可以使用`get()`方法。不推荐原因：因为Optional内部数据为空时会出现异常。

```java
Optonal<Author> authorOptional = Optional.ofNullable(null);
Author author = authorOptional.get();
```

### 4.2.4 安全获取值

> 如果我们期望安全的获取值，我们不推荐使用get方法，而是使用Optional提供的以下方法：

#### orElseGet

> 获取数据并且设置数据为空时的默认值。如果数据不为空就能获取到该数据，反之根据传入的默认值来创建对象

```java
Optonal<Author> authorOptional = Optional.ofNullable(getAuthor());
Author author = authorOptional.orElseGet(() -> new Author(1L, "默认", 18, "默认信息", null));
```

#### orElseThrow

>  获取数据时，若数据不为空就返回该数据；若为空则根据传入的异常来创建对象抛出(通过统一异常捕获-Spring AOP等，处理异常)

```java
Optonal<Author> authorOptional = Optional.ofNullable(getAuthor());
try {
    Author author = authorOptional.orElseThrow((Supplier<Thorwable>) () -> new RuntimeException("AuthorException : author is null."));
    System.out.println(author.getName());
} catch (Throwable throwable) {
    throwable.printStackTrace();
}
```

### 4.2.5 过滤

> 使用`filter()`方法对数据进行过滤。若原本是有数据的，但是不符合判断，也会变成一个无数据的Optional对象

```java
Optional<Author> authorOptional = Optional.ofNullable(getAuthor());
authorOptional.filter(author -> author.getAge() > 100)
    .ifPresent(author -> System.out.println(author.getName()));
```

### 4.2.6 判断

> 使用`isPresent()`方法进行是否存在数据的判断。若为空返回值为false；反正为true。此方法不能体现Optional的好处，**更推荐使用`ifPresen()`方法**

```java
Optional<Author> authorOptional = Optional.ofNullable(getAuthor());
if (authorOptional.isPresent()) {
    System.out.println(authorOptional.get().getName());
}
```

### 4.2.7 数据转换

> 使用`map()`方法对数据进行转换，并且转换后的数据仍是Optional包装好的，保证了使用安全。

例如：获取作家的书籍集合

```java
Optional<Author> authorOptional = Optional.ofNullable(getAuthor());
Optional<List<Book>> booksOptional = authorOptional.map(author -> author.getBooks());
booksOptional.ifPresent(new Consumer<List<Book>>() {
    @Override
    public void accept(List<Book> books) {
        books.forEach(book -> System.out.println(book.getName()));
    }
});
```

# 5. 函数式接口

## 5.1 概述

**只有一个抽象方法**的接口我们称之为函数接口。

JDK的函数式接口都加上了`@FunctionalInterface`注释进行标记。但是无论是否加上该注解只要接口中只有一个抽象方法，都是函数式接口。

## 5.2 常见函数式接口

### Consumer消费接口

> 根据其中抽象方法的参数列表和返回值类型，可以对方法中传入的参数进行消费

```java
@FunctionalInterface
public interface Consumser<T> {
    void accept(T t);
}
```

### Function计算转换接口

> 根据其中抽方法的参数列表和返回值类型，可以对方法中对传入的参数计算或转换，把结果返回

```java
@FunctionalInterface
public interface Function<T, R> {
    R accept(T t);
}
```

### Predicate判断接口

> 根据其中抽象方法的参数列表和返回值类型，可以在方法中对传入的参数条件判断，返回判断结果

```java
@FunctionalInterface
public interface Predicate<T> {
    boolean test(T t);
}
```

### Supplier生产型接口

> 根据其中抽象方法的参数列表和返回值类型，可以在方法中创建对象，把创建好的对象返回

```java
@FunctionalInterface
public interface Supplier<T> {
    boolean test(T t);
}
```

## 5.3 常见的默认方法

### `and()`

> 我们在使用Predicate接口时候可能需要进行判断条件的拼接。而`and()`方法相当于是使用&&来拼接两个判断条件

例如：打印作家中年龄大于17**并且**姓名长度大于1的作家

```java
List<Author> authors = getAuthors();
Stream<Author> authorStream = authors.stream();
authorStream.filter(new Predicate<Author>() {
    @Override
    public boolean test(Author author) {
        return author.getAge() > 17;
    }
}.and(new Predicate<Author>() {
        @Override
        public boolean test(Author author) {
            return author.getName().lenght() > 1;
        }
})).forEach(author -> System.out.println(author));
// 可以这样使用，但是不推荐，比较繁琐。一般写在一个filter中即可
// return author.getAge() > 17 && author.getName().lenght() > 1;
```

```java
// 一般作为编写lambda方法时使用
public static void printNum(IntPredicate p1, IntPredicate p2) {
    int[] arr = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
    for(int i : arr) {
        if(p1.and(p2).test(i)) {
            System.out.println(i);
        }
    }
}

public static void main(String[] args) {
    printNum(value -> value % 2 == 0, value -> value >= 4);
}
```

### `or()`

> 我们在使用predicate接口时候可能需要进行判断条件的拼接。而 `or()`方法相当于是使用||来拼接两个判断条件

例如：打印作家中年龄大于17**或者**姓名长度大于2的作家

```java
List<Author> authors = getAuthors();
Stream<Author> authorStream = authors.stream();
authorStream.filter(new Predicate<Author>() {
    @Override
    public boolean test(Author author) {
        return author.getAge() > 17;
    }
}.or(new Predicate<Author>() {
        @Override
        public boolean test(Author author) {
            return author.getName().lenght() < 2;
        }
})).forEach(author -> System.out.println(author));
```

### `negate()`

> predicate接口中的方法。negate方法相当于是在判断添加前面加了个!表示取反

例如：打印作家中年龄不大于17的作家

```java
List<Author> authors = getAuthors();
Stream<Author> authorStream = authors.stream();
authorStream.filter(new Predicate<Author>() {
    @Override
    public boolean test(Author author) {
        return author.getAge() > 17;
    }
}.negate())
    .forEach(author -> System.out.println(author))
```

# 6. 方法引用

## 6.1 概述

我们在使用lambda时，如果方法体中只有一个方法的调用的话(包括构造方法)，我们可以用方法引用进一步简化代码。

> **推荐用法**
>
> 我们在使用lambda时不需要考虑什么时候用方法引用，用哪种方法引用，方法引用的格式是什么。我们只需要在写完lambda方法发现方法体只有一行代码，并且是方法的调用时使用快捷键尝试是否能够转换成方法引用即可。
>
> 当我们方法引用使用的多了慢慢的也可以直接写出方法引用。

## 6.2 基本格式

`类名或对象名::方法名`

## 6.3 语法详情

### 6.3.1 引用静态方法

#### 格式(其实就是引用类的静态方法)：

> `类名::静态方法名`

#### 使用前提

> 如果我们在重写方法的时候，方法体中**只有一行代码**，并且这行代码是**调用了某个类的静态方法**，并且我们把要重写的**抽象方法中所有的参数**都按照**顺序**传入了这个静态方法中，这个时候我们就可以引用类的静态方法

例如：如下代码就可以使用方法引用进行简化

```java
List<Author> authors = getAuthors();
Stream<Author> authorStream = author.stream();
authorStream.map(author -> author.getAge())
    .map(age -> String.valueOf(age));
```

```java
List<Author> authors = getAuthors();
Stream<Author> authorStream = author.stream();
authorStream.map(author -> author.getAge())
    .map(String::valueof);
```

> **注意**
>
> 如果我们所重写的方法是**没有参数**的，调用的方法也是没有参数的也相当于符合以上条件

### 6.3.2 引用对象的实例方法

#### 格式

> `对象名::方法名`

#### 使用前提

> 如果我们在重写方法的时候，方法体中**只有一行代码**，并且这行代码是**调用了某个对象的成员方法**，并且我们把要写的**抽象方法中所有的参数**都按照**顺序**传入了这个成员方法中，这个时候我们就可以引用对象的实例方法

例如：如下代码就可以使用方法引用进行简化

```java
List<Author> authors = getAuthors();
Stream<Author> authorStream = author.stream();
StringBuilder sb = new StringBuilder();
authorStream.map(author -> author.getName())
    .forEach(name -> sb.append(name));
```

```java
List<Author> authors = getAuthors();
Stream<Author> authorStream = author.stream();
StringBuilder sb = new StringBuilder();
authorStream.map(author -> author.getName())
    .forEach(sb::append);
```

### 6.3.4 引用类的实例方法

#### 格式

> `类名::方法名`

#### 使用前提

> 如果我们在重写方法的时候，方法体中**只有一行代码**，并且这行代码是**调用了第一个参数的成员方法**，并且我们把要重写的**抽象方法中剩余的所有的参数**都按照**顺序**传入了这个成员方法中，这个时候我们就可以引用类的实例方法

例如：如下代码就可以使用方法引用进行简化

```java
interface UseString{
	String use(String str, int start, int length);
}
public static String subAuthorName(String str, UseString useString){
    int start = 0;
    int length = 1;
    return useString.use(str, start, length);
}
public static void main(String[] args) {
    subAuthorName("testAuthor", new UseString() {
        @Override
		public String use(String str, int start, int length) {
			return str.substring(start, length);
        }
    });
}
```

```java
public static void main(String[] args) {
    subAuthorName("testAuthor", String::substring);
}
```

### 6.3.5 构造器引用

#### 格式

> `类名::new`

#### 使用前提

> 如果我们在重写方法的时候，方法体中**只有一行代码**，并且这行代码是**调用了某个类的构造方法**，并且我们把要重写的**抽象方法中的所有的参数**都按照**顺序**传入了这个构造方法中，这个时候我们就可以引用造器。

例如：如下代码就可以使用方法引用进行简化

```java
List<Author> authors = getAuthors();
authors.stream()
    .map(author -> author.getName())
    .map(name -> new StringBuilder(name))
    .map(sb -> sb.append("-appendText").toString())
    .forEach(str -> System.out.println(str));
```

```java
List<Author> authors = getAuthors();
authors.stream()
    .map(author -> author.getName())
    .map(StringBuilder::new)
    .map(sb -> sb.append("-appendText").toString())
    .forEach(str -> System.out.println(str));
```

# 7. 高级用法

## 7.1 基本数据类型优化

我们之前用到的很多Stream的方法由于都使用了泛型。所以涉及到的参数和返回值都是**引用数据类型**。

即使我们操作的是整数小数，但是实际用的都是他们的**包装类**。JDK5中引入的自动装箱和自动拆箱让我们在使用对应的**包装类**时就好像使用基本数据类型一样方便。但是你一定要知道**装箱**和拆箱肯定是要**消耗时间**的。

虽然这个时间消耗很下，但是在大量的数据不断的重复装箱拆箱的时候，你就不能无视这个时间损耗了。

所以为了让我们能够对这部分的时间消耗进行优化。 Stream还提供了很多专门针对基本数据类型的方法。

例如： `mapToInt(), mapToLong(), mapToDouble(), flatMapToInt(), flatMapToDouble()` 等。

```java
private static void test() {
    List<Author> authors = getAuthors();
    authors.stream() // 此时Stream<Author>
        .map(author -> author.getAge()) // 此时Stream<Integer>
        .map(age -> age + 10) // 此时Stream<Integer> 进行基本类型运算，拆箱装箱
        .filter(age -> age > 18) // 此时Stream<Integer> 进行基本类型运算，拆箱装箱
        .map(age -> age + 2) // 此时Stream<Integer> 进行基本类型运算，拆箱装箱
        .forEach(System.out::println);
    
    authors.stream() // 此时Stream<Author>
        .mapToInt(author -> author.getAge()) // 此时IntStream
        .map(age -> age + 10) // 此时IntStream
        .filter(age -> age > 18) // 此时IntStream
        .map(age -> age + 2) // 此时IntStream
        .forEach(System.out::println);
}
```

## 7.2 并行流

当流中有大量元素时，我们可以使用并行流去提高操作的效率。其实并行流就是吧任务分配给多个线程去执行。如果我们自己去用代码实现的话其实会非常的复杂，并且要求你对并发编程有足够的理解和认识。而如果我们使用 Stream的话，我们只需要修改一个方法的调用，就可以使用并行流来帮我们实现，从而提高效率。

> `parallel()`方法可以把串行流转换成并行流。

```java
private static void test() {
	Stream<Integer> stream = Stream.of(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
    Integer sum = stream.parallel()
        .peek(new Consumer<Integer>() {
            @Override
            public void accept(Integer num) {
                System.out.println(num + Thread.currentThread().getName());
            }
        })
        .filter(num -> num > 5)
        .reduce((result, element) -> result + element)
        .get();
	System.out.println(sum);
}
```

也可以通过`parallelStream()`直接获取并行流对象。

```java
List<Author> authors = getAuthors();
authors.parallelStream()
    .map(author -> author .getAge())
    .map(age -> age + 10)
    .filter(age -> age > 18)
    .map(age -> age + 2)
    .forEach(System.out::println);
```

