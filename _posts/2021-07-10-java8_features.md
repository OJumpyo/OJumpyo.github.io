---
layout: post
title: "java8特性总结与实践"
date:   2021-07-10
tags: [java]
comments: true
toc: true
author: OJumpyo
---

# 1、lambda表达式
lambda表达式是java8中最重要的新功能之一，  
使用lambda表达式可以替代只有一个抽象函数的接口实现，告别匿名内部类，代码看起来更简洁易懂。  
lambda表达式同时还提升了对集合、框架的迭代、遍历、过滤数据等操作。  
## 1.1、特点：
- 函数式编程
- 参数类型自动推断
- 代码量少，简洁。

## 1.2、何时使用：
**==函数式编程==**，在实现只有一个方法的接口时使用。注：有且只有一个方法/函数的接口，叫做 **==函数式接口@FunctionalInterface==**，objects类中的方法除外。  

## 1.3、常用的函数式接口
![](https://gitee.com/jumyoright/blog-images/raw/master/images/youdaoNote/java/20210710105109.png)  

## 1.3、使用方式：
![](https://gitee.com/jumyoright/blog-images/raw/master/images/youdaoNote/java/lambda%E8%A1%A8%E8%BE%BE%E5%BC%8F%E7%9A%84%E8%AF%AD%E6%B3%95.png)  

~~~
()->{}

() -> {System.out.println(1);}

() -> System.out.println(1);

() -> {return 100;}

() -> 100

() -> null

(int x) -> {return x+1;}

 (int x) -> x+1
 
(x) -> x+1

x -> x+1  
~~~

~~~
形式一：
这种写法没有参数，用一对圆括号表示。

Runnable noArguments = () -> System.out.println("Hello World");

形式二：
这种写法只有1个参数，可以省略括号，下面的 event 相当于 (event)。

ActionListener oneArgument = event -> System.out.println("点击了按钮");
// 等同于 ↓
//ActionListener oneArgument = (event) -> System.out.println("点击了按钮");

形式三：
这种写法表示有多段代码块，适用于复杂状况。

Runnable multiStatement = () -> {
	System.out.print("Hello");
	System.out.println(" World");
};

形式四：
这种写法表示包含两个参数的方法。
注意：**下面的 functionAdd 并不是将两个数字相加的结果，而是创建了一个函数，用来计算两个数字相加的结果。**变量 functionAdd 的类型并不是两个数字相加的和，而是将两个数字相加的那行代码。

// 创建一个函数
BinaryOperator<Long> functionAdd = (x, y) -> x + y;
// 应用函数，给函数传值，得到计算结果
Long result = functionAdd.apply(1L, 2L);

形式五：
这种写法和形式四的不同之处在于，多了对参数类型的显示声明。

// 创建一个函数
BinaryOperator<Long> function = (Long x, Long y) -> x + y;
// 应用函数，给函数传值，得到计算结果
Long result = function.apply(1L, 2L);
~~~

# 2、方法引用
## 2.1、介绍
是用来直接访问类或者实例的已经存在的方法或者构造方法。方法引用提供了一种引用而不执行方法的方式。
如果抽象方法的实现恰好可以使用调用另一个方法来实现，就 **==有可能==** 可以使用方法引用。

方法引用的结果要是用 **==函数式接口@FunctionalInterface==** 接收。
## 2.2、分类及使用方法
![](https://gitee.com/jumyoright/blog-images/raw/master/images/youdaoNote/java/%E6%96%B9%E6%B3%95%E5%BC%95%E7%94%A8%E7%9A%84%E5%88%86%E7%B1%BB%E4%B8%8E%E4%BD%BF%E7%94%A8.png)  

## 2.3、代码示例
~~~
public class FunctionRefTest2 {
    public FunctionRefTest2() {
        System.out.println("无参... ");
    }

    public FunctionRefTest2(String str) {
        System.out.println("有参... " + str);
    }

    public static String staticMethod(String str) {
        return ("static method " + str);
    }

    public String plainMethod(String str) {
        return ("plain method " + str);
    }

    public void nonArgsMethod() {
        System.out.println("no arguements method " + toString());
    }

    public static void main(String[] args) {
        //获取无参构造函数
        Supplier<FunctionRefTest2> s0 = () -> new FunctionRefTest2();
        Supplier<FunctionRefTest2> s1 = FunctionRefTest2::new;
        System.out.println(s0.get());
        System.out.println(s1.get());

        System.out.println("--无参构造分割线---");
        //获取有参构造函数,要换成function，多个参数去找bifunction，trifunction等
        Function<String, FunctionRefTest2> s2 = (str) -> new FunctionRefTest2(str);
        System.out.println("空str " + s2);
        Function<String, FunctionRefTest2> s3 = FunctionRefTest2::new;
        s3.apply("ref-args");

        System.out.println("--有参构造分割线---");
        //获取静态方法
        //consumer没有返回值，只执行
        Consumer<String> c1 = (str) -> FunctionRefTest2.staticMethod(str);
        c1.accept("consumer-args");
        Function<String, String> s4 = (str) -> FunctionRefTest2.staticMethod(str);
        System.out.println(s4.apply("function-args"));
        Function<String, String> s5 = FunctionRefTest2::staticMethod;
        System.out.println(s5.apply("function-ref-args"));

        System.out.println("---静态方法分割线---");
        //获取普通方法，需要先创建一个实例
        FunctionRefTest2 test2 = new FunctionRefTest2();
        Consumer<String> c2 = (str) -> FunctionRefTest2.staticMethod(str);
        Function<String, String> s6 = test2::plainMethod;
        System.out.println(s6.apply("instance-args"));

        System.out.println("---普通方法分割线---");
    }
}
~~~
输出结果
~~~
无参... 
cn.zy.test.functionRef.FunctionRefTest2@6d311334
无参... 
cn.zy.test.functionRef.FunctionRefTest2@682a0b20
--无参构造分割线---
空str cn.zy.test.functionRef.FunctionRefTest2$$Lambda$3/558638686@448139f0
有参... ref-args
--有参构造分割线---
static method function-args
static method function-ref-args
---静态方法分割线---
无参... 
plain method instance-args
---普通方法分割线---
~~~

# 3、默认方法
## 3.1、介绍
java8新增了 **==接口==** 的默认方法，也就是说接口可以有实现方法，而且不需要实现类去实现这个方法。  

我们只需在方法名前面加个 default 关键字即可实现默认方法。

## 3.2、为什么要有默认方法
首先，之前的接口是个双刃剑，好处是面向抽象而不是面向具体编程，缺陷是，当需要修改接口时候，需要修改全部实现该接口的类，目前的 java 8 之前的集合框架没有 foreach 方法，通常能想到的解决办法是在JDK里给相关的接口添加新的方法及实现。然而，对于已经发布的版本，是没法在给接口添加新方法的同时不影响已有的实现。所以引进的默认方法。他们的目的是为了解决接口的修改与现有的实现不兼容的问题。

## 3.3、语法
### 3.3.1、默认语法：
~~~
public interface Vehicle {
   default void print(){
      System.out.println("我是一辆车!");
   }
}
~~~

### 3.3.2、多个默认方法
一个接口有默认方法，考虑这样的情况，一个类实现了多个接口，且这些接口有相同的默认方法，以下实例说明了这种情况的解决方法：
~~~
public insteeface Vehicle {
    default void print() {
        System.out.println("我是一辆车！");
    }
}

public interface FourWheeler {
    default void print() {
        System.out.println("我是一辆四轮车！");
    }
}
~~~
第一个解决方案是创建一个同名默认方法，来重写接口的默认方法：
~~~
public FourWheelerVehicle implements Vehicle, FourWheeler {
    default void print() {
        System.out.println("我是一辆四轮汽车！");
    }
}
~~~
第二个解决方案，是使用super调用指定接口的默认方法
~~~
public class Car implements Vehicle, FourWheeler {
   public void print(){
      Vehicle.super.print();
   }
}
~~~

## 3.4、静态默认方法
java8开始，接口可以声明（并可以提供实现）静态方法
~~~
public interface Vehicle {
   default void print(){
      System.out.println("我是一辆车!");
   }
    // 静态方法
   static void blowHorn(){
      System.out.println("按喇叭!!!");
   }
}
~~~

## 3.5、代码示例
~~~
public class Java8Tester {
   public static void main(String args[]){
      Vehicle vehicle = new Car();
      vehicle.print();
   }
}
 
interface Vehicle {
   default void print(){
      System.out.println("我是一辆车!");
   }
    
   static void blowHorn(){
      System.out.println("按喇叭!!!");
   }
}
 
interface FourWheeler {
   default void print(){
      System.out.println("我是一辆四轮车!");
   }
}
 
class Car implements Vehicle, FourWheeler {
   public void print(){
      Vehicle.super.print();
      FourWheeler.super.print();
      Vehicle.blowHorn();
      System.out.println("我是一辆汽车!");
   }
}
~~~
执行结果
~~~
我是一辆车!
我是一辆四轮车!
按喇叭!!!
我是一辆汽车!
~~~

# 4、Stream（流）
## 4.1、介绍
~~~
+--------------------+       +------+   +------+   +---+   +-------+
| stream of elements +-----> |filter+-> |sorted+-> |map+-> |collect|
+--------------------+       +------+   +------+   +---+   +-------+
~~~
Stream是Java8新增的用来处理数组集合的API。  
这种风格将要处理的元素集合看作一种流， 流在管道中传输， 并且可以在管道的节点上进行处理， 比如筛选， 排序，聚合等。  
- 代码简洁，使用函数式编程变出的代码简洁并且准确，另外使用stream可以让你从此告别for循环
- 多核友好，java函数式编程让编写 **==并行程序==** 变得简单，只需要调用parallel()方法即可。

## 4.2、特性
![](https://gitee.com/jumyoright/blog-images/raw/master/images/youdaoNote/java/20210710113239.png)  

## 4.3、Stream运行机制
Stream分为  源Source、 中间操作、终止操作  

Stream的源可以是一个数组、一个集合、一个生成器方法、一个I/O通道等等。  

Stream可以有0个或者多个中间操作，每一个中间操作都会返回一个新的Stream，供下一个操作使用。一个Stream只会有一个终止操作。  

Stream只有遇到终止操作，他的源才会开始执行遍历操作。  

## 4.4、生成流
- stream()为集合创建串行流
- parallelStream()为集合创建并行流

~~~
public class StreamTest1 {
    //数组生成
    static void arrGen() {
        String[] strs = {"ab", "c", "de", "fgh"};
        Stream<String> strs1 = Stream.of(strs);
        //还有stream方法
        //Stream<String> strs1 = Arrays.stream(strs);
        strs1.forEach(System.out::print);
        System.out.println();
    }
    //集合生成
    static void collectionGen() {
        List<String> list = Arrays.asList("ab", "c", "de", "fgh");
        Stream<String> serialStream = list.stream();
        System.out.println("串行：");
        serialStream.forEach(System.out::print);
        Stream<String> parallelStream = list.parallelStream();
        System.out.println("并行：");
        parallelStream.forEach(System.out::print);
        System.out.println();
    }
    //generate生成
    static void generatorGen() {
        Stream<Integer> integerStream = Stream.generate(() -> 1);
        integerStream.limit(8).forEach(System.out::print);
        System.out.println();
    }
    //iterator生成
    static void iteratorGen() {
        Stream<Integer> integerStream = Stream.iterate(1, x -> x+1);
        integerStream.limit(8).forEach(System.out::print);
        System.out.println();
    }
    //其他方式
    static void otherGen() {
        String str = "abcdefgh";
        IntStream intStream = str.chars();
        intStream.forEach((x) -> System.out.print(x-96));
        System.out.println();
    }

    public static void main(String[] args) {
        arrGen();
        collectionGen();
        generatorGen();
        iteratorGen();
        otherGen();
    }
}
~~~

~~~
abcdefgh
串行：
abcdefgh并行：
defghabc
11111111
12345678
12345678
~~~

## 4.5、中间操作
- forEach

Stream 提供了新的方法 'forEach' 来迭代流中的每个数据。以下代码片段使用 forEach 输出了10个随机数：
~~~
Random random = new Random();
random.ints().limit(10).forEach(System.out::println);
~~~
- map

map 方法用于映射每个元素到对应的结果，以下代码片段使用 map 输出了元素对应的平方数：
~~~
List<Integer> numbers = Arrays.asList(3, 2, 2, 3, 7, 3, 5);
// 获取对应的平方数
List<Integer> squaresList = numbers.stream().map( i -> i*i).distinct().collect(Collectors.toList());
~~~
- filter

filter 方法用于通过设置的条件过滤出元素。以下代码片段使用 filter 方法过滤出空字符串：
~~~
List<String>strings = Arrays.asList("abc", "", "bc", "efg", "abcd","", "jkl");
// 获取空字符串的数量
long count = strings.stream().filter(string -> string.isEmpty()).count();
~~~
- limit

limit 方法用于获取指定数量的流。 以下代码片段使用 limit 方法打印出 10 条数据：
~~~
Random random = new Random();
random.ints().limit(10).forEach(System.out::println);
~~~
- sorted

sorted 方法用于对流进行排序。以下代码片段使用 sorted 方法对输出的 10 个随机数进行排序：
~~~
Random random = new Random();
random.ints().limit(10).sorted().forEach(System.out::println);
~~~

- 并行（parallel）程序

parallelStream 是流并行处理程序的代替方法。以下实例我们使用 parallelStream 来输出空字符串的数量：
~~~
List<String> strings = Arrays.asList("abc", "", "bc", "efg", "abcd","", "jkl");
// 获取空字符串的数量
long count = strings.parallelStream().filter(string -> string.isEmpty()).count();
~~~
我们可以很容易的在顺序运行和并行直接切换。

- Collectors

Collectors 类实现了很多归约操作，例如将流转换成集合和聚合元素。Collectors 可用于返回列表或字符串：
~~~
List<String>strings = Arrays.asList("abc", "", "bc", "efg", "abcd","", "jkl");
List<String> filtered = strings.stream().filter(string -> !string.isEmpty()).collect(Collectors.toList());
 
System.out.println("筛选列表: " + filtered);
String mergedString = strings.stream().filter(string -> !string.isEmpty()).collect(Collectors.joining(", "));
System.out.println("合并字符串: " + mergedString);
~~~

- 统计

另外，一些产生统计结果的收集器也非常有用。它们主要用于int、double、long等基本类型上，它们可以用来产生类似如下的统计结果。
~~~
List<Integer> numbers = Arrays.asList(3, 2, 2, 3, 7, 3, 5);
 
IntSummaryStatistics stats = numbers.stream().mapToInt((x) -> x).summaryStatistics();
 
System.out.println("列表中最大的数 : " + stats.getMax());
System.out.println("列表中最小的数 : " + stats.getMin());
System.out.println("所有数之和 : " + stats.getSum());
System.out.println("平均数 : " + stats.getAverage());
~~~

## 4.6、中间操作的代码示例
~~~
public class StreamTest1 {
    public static void main(String[] args) {
        //操作的集合
        List<Integer> list = Arrays.asList(1,2,3,4,5,6);
        //中间操作:如果调用方法之后返回的结果是Stream对象就意味着是一个中间操作
        list.stream().filter(x -> x %2 == 0).forEach(System.out::print);
        System.out.println();
        //求出结果集中所有偶数的和
        int sumEven = list.stream().filter(x -> x%2==0).mapToInt(x -> x).sum();
        System.out.println("偶数之和：" + sumEven);
        //求出集合最值
        System.out.println("最大值：" + list.stream().max((a,b) -> a-b).get());
        System.out.println("最大值：" + list.stream().min((a,b) -> b-a).get());
        System.out.println("最小值：" + list.stream().min((a,b) -> a-b).get());
        System.out.println("最小值：" + list.stream().max((a,b) -> b-a).get());

        Optional<Integer> any = list.stream().filter(x -> x % 2 == 0).findAny();//遇到第一个符合条件的就返回了
        System.out.println(any.get());

        Optional<Integer> first = list.stream().filter(x -> x % 10 == 6).findFirst();
        System.out.println(first.get());

        Stream<Integer> integerStream = list.stream().filter(i -> {
            System.out.println("运行代码第" + i + "次");
            return i % 2 == 0;
        });
        System.out.println(integerStream.findAny().get());
        System.out.println("---------------");
        //获取最大值和最小值但是不使用min和max方法
        Optional<Integer> min = list.stream().sorted().findFirst();
        System.out.println(min.get());
        Optional<Integer> max2 = list.stream().sorted((a, b) -> b - a).findFirst();
        System.out.println(max2.get());
        System.out.println("----------------");

        Arrays.asList("java","c#","python","scala").stream().sorted().forEach(x -> System.out.println(x + " "));
        Arrays.asList("java","c#","python","scala").stream().sorted((a,b)->a.length()-b.length()).forEach(x -> System.out.println(x + " "));

        System.out.println("---------------");
        //想将集合中的元素进行过滤同时返回一个集合对象
        List<Integer> collect = list.stream().filter(x -> x % 2 == 0).collect(Collectors.toList());
        collect.forEach(x-> System.out.print(x + " "));
        System.out.println();
        System.out.println("---------------");

        //去重操作
        Arrays.asList(1,2,3,3,3,4,5,2).stream().distinct().forEach(x -> System.out.print(x + " "));
        System.out.println();
        System.out.println("---------------");
        Arrays.asList(1,2,3,3,3,4,5,2).stream().collect(Collectors.toSet()).forEach(x -> System.out.print(x + " "));
        System.out.println();
        System.out.println("-------------------");

        //打印(20-30]这样的集合数据
        Stream.iterate(1,x->x+1).limit(50).skip(20).limit(10).forEach(x -> System.out.print(x + " "));
        System.out.println();

        String str ="11,22,33,44,55";
        System.out.println(Stream.of(str.split(",")).mapToInt(x -> Integer.valueOf(x)).sum());
        System.out.println(Stream.of(str.split(",")).mapToInt(Integer::valueOf).sum());
        System.out.println(Stream.of(str.split(",")).map(x -> Integer.valueOf(x)).mapToInt(x -> x).sum());
        System.out.println(Stream.of(str.split(",")).map(Integer::valueOf).mapToInt(x -> x).sum());

        //创建一组自定义对象
        String str2 = "java,scala,python";
        Stream.of(str2.split(",")).map(x->new Person(x)).forEach(System.out::println);
        Stream.of(str2.split(",")).map(Person::new).forEach(System.out::println);
        Stream.of(str2.split(",")).map(x->Person.build(x)).forEach(System.out::println);
        Stream.of(str2.split(",")).map(Person::build).forEach(System.out::println);

        //将str中的每一个数值都打印出来，同时算出最终的求和结果
        System.out.println(Stream.of(str.split(",")).peek(System.out::println).mapToInt(Integer::valueOf).sum());

        System.out.println(list.stream().allMatch(x -> x>=0));
    }
}
~~~
结果：
~~~
246
偶数之和：12
最大值：6
最大值：6
最小值：1
最小值：1
2
6
运行代码第1次
运行代码第2次
2
---------------
1
6
----------------
c# 
java 
python 
scala 
c# 
java 
scala 
python 
---------------
2 4 6 
---------------
1 2 3 4 5 
---------------
1 2 3 4 5 
-------------------
21 22 23 24 25 26 27 28 29 30 
165
165
165
165
Person{name='java'}
Person{name='scala'}
Person{name='python'}
Person{name='java'}
Person{name='scala'}
Person{name='python'}
Person{name='java'}
Person{name='scala'}
Person{name='python'}
Person{name='java'}
Person{name='scala'}
Person{name='python'}
11
22
33
44
55
165
true
~~~

# 5、自定义注解
## 5.1、介绍
Java注解又称Java标注，是JDK5.0版本开始支持加入源代码的特殊语法元数据。  
用于提供一种 **==安全的，类似注释的机制==** ，用于将任何的信息或元数据与程序元素（类、方法、变量等）进行关联。  
Java语言中的类、方法、变量、参数和包等都可以被标注。  
和Javadoc不同，Java标注可以通过反射获取标注内容。在编译器生成类文件时，标注可以被嵌入到字节码中。Java虚拟机可以保留标注内容，在运行时可以获取到标注内容。  
当然它也支持自定义Java标注。

## 5.2、注解体系图
![](https://gitee.com/jumyoright/blog-images/raw/master/images/youdaoNote/java/20210710160645.png)  

## 5.3、注解的作用
- 生成文档。 比如@param、@return等
- 跟踪代码依赖性，实现替代配置文件功能
- 编译时进行格式检查。比如@Override放在方法之前，如果这个方法并没有覆盖超类方法，那么就能检测出来并报错

## 5.4、常用注解
- @Traget： 定义注解类型，代表注解可注释在类、接口、方法、方法形参、构造器、包等哪些具体位置。
- @Retention：定义注解有效范围，source、class、runtime分别表示java源文件期间、编译为class文件期间、运行期间。**==原理就是通过反射获取==** 。
- @Documented注解表明这个注释是由javadoc记录的，在默认情况下也有类似的记录工具。如果一个类型声明被注释了文档化，它的注释成为公共API的一部分
- @Inherited具备继承性。此注解被作为类注解使用时，表示子类可以继承此注解。

## 5.5、应用场景
1. 自定义注解+拦截器 实现登录校验

2. 自定义注解+AOP 实现日志打印

## 5.6、自己实现自定义注解
自定义注解
~~~
@Documented
@Target(value = {ElementType.TYPE, ElementType.METHOD, ElementType.FIELD})
@Retention(value = RetentionPolicy.RUNTIME)

public @interface MyAnnotation {
    String name();

    String value();

    String path();
}
~~~
自定义类
~~~
@MyAnnotation(name = "类名称", value = "类值", path = "类路径")
public class UseMyAnnotation {

    @MyAnnotation(name = "姓名", value = "张三", path = "属性路径")
    private String name;

    @MyAnnotation(name = "年龄", value = "18", path = "/user")
    private String age;

    @MyAnnotation(name="方法名0",value="方法值0",path="方法访问路径1")
    public String testAnno(){
        return "successs!!!";
    }

    @MyAnnotation(name="方法名1",value="方法值1",path="方法访问路径1")
    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getAge() {
        return age;
    }

    public void setAge(String age) {
        this.age = age;
    }
}
~~~
反射获取注解测试
~~~
public class TestMyAnnotation {
    public static void main(String[] args) throws ClassNotFoundException {
        //使用类加载器获取类，你也可以直接通过Class.forName直接获取
        ClassLoader classLoader = TestMyAnnotation.class.getClassLoader();
        Class cls = classLoader.loadClass("cn.zy.test.features.annotation.UseMyAnnotation");
        //类加载器获取
        //Class clazz = Class.forName("cn.zy.test.features.annotation.UseMyAnnotation");

        //获取类的注解信息
        MyAnnotation ma = (MyAnnotation) cls.getAnnotation(MyAnnotation.class);
        System.out.println(ma.name() + "-----" + ma.value() + "-----" + ma.path());
        //获取方法的注解信息
        Method[] methods = cls.getMethods();
        for (int i = 0; i < methods.length; i++) {
            if(methods[i].isAnnotationPresent(MyAnnotation.class)) {
                MyAnnotation methodAno = methods[i].getAnnotation(MyAnnotation.class);
                System.out.println("遍历->当前方法名为：" + methods[i].getName() + "的注解信息：->"
                        + methodAno.name() + "->" + methodAno.value() + "->" + methodAno.path());
            }
        }
        //获取属性的注解信息
        Field[] fields = cls.getDeclaredFields();
        for (int i = 0; i < fields.length; i++) {
            MyAnnotation fieldAno = fields[i].getAnnotation(MyAnnotation.class);
            System.out.println("遍历->当前属性名为：" + fields[i].getName() + "的注解信息：->"
                    + fieldAno.name() + "->" + fieldAno.value() + "->" + fieldAno.path());
        }

    }
}
~~~
输出结果
~~~
类名称-----类值-----类路径
遍历->当前方法名为：getName的注解信息：->方法名1->方法值1->方法访问路径1
遍历->当前方法名为：testAnno的注解信息：->方法名0->方法值0->方法访问路径1
遍历->当前属性名为：name的注解信息：->姓名->张三->属性路径
遍历->当前属性名为：age的注解信息：->年龄->18->/user
~~~

# 6、待添加：可选类，datetime api， Base64