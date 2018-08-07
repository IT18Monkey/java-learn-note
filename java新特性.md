 > 作者：[lkd_whh](https://www.jianshu.com/u/dad4d9675892)
 >
 >  转载请注明出处

  #### java5

  1. 泛型 (Generics)

     ```java
      List<Integer> list=new ArrayList<Integer>();
     ```

  2. 增强循环（Enhanced for Loop）

     ```java
      int[] array = {1, 2, 3, 4, 5};
      for (int i : array) {
         System.out.println(i);
      } 
     ```

  3. 自动封箱拆箱(Autoboxing/Unboxing )。八大基本类型和它们的包装类能够自动的相互转换。

  4. 枚举(Typesafe Enums)。

     ps:枚举是一种实现线程安全的单例模式的好方式。

     ```java
      enum TestEnum{
             one,
             two;
             TestEnum() {
             }
         }
     ```
  5. 可变参数 (Varargs)

     语法：`(type... arguments)`

     可变参数本质仍然是用一个数组存储参数，只是java隐藏了这一过程。需要注意的是如果一个方法声明中含有可变参数，那必须放在最后一个位置。

     ```java
      	/**
          * 可变参数测试
          *
          * @param args
          */
         public static void testVarargs(String... args) {
             //本质就是个数组
             System.out.println(args[1]);
             for (String arg : args) {
                 System.out.println(arg);
             }
         }
     ```
  6. 静态导入（Static Import）。通过import类来使用类里的静态变量或方法（直接通过名字，不需要加上`类名.`）,简化了代码的书写。

     ```java
     //单个导入
     import static java.lang.Math.PI;
     //批量导入
     import static java.lang.Math.*;
     ```

     ps:过去的版本中只能通过继承类或实现接口才能使用。

  7. 注解（Annotations）。关键字`@interface`。

     ```java
     //注释注解的注解又叫做元注解
     @Retention(RetentionPolicy.RUNTIME)
     @Target(ElementType.METHOD)
     public @interface MyAnnotations {
         //注解内可以添加方法，也可以不添加
         //注解方法的返回值只能是基本类型，String，Class类,枚举,注解,可以有默认值。
         int id();
         String name() default "whh";
         Class classType();
         TestEnum enumType();
     }

     ```
  8. 新的线程模型和并发库（`java.util.concurrent`)。

  #### java6

  1. 集合框架增强。

  - 为了更好的支持双向访问集合。添加了许多新的类和接口。
  - 新的数组拷贝方法。`Arrays.copyOf`和`Arrays.copyOfRange`

  ```java
  //以下为添加的新接口和类
  Deque,BlockingDeque,NavigableSet,NavigableMap,ConcurrentNavigableMap，ArrayDeque， ConcurrentSkipListSet ,ConcurrentSkipListMap,ConcurrentSkipListMap ,AbstractMap.SimpleEntry ,AbstractMap.SimpleImmutableEntry
  ```
  2. Scripting. 可以让其他语言在java平台上运行。 java6包含了一个基于Mozilla Rhino实现的javascript脚本引擎。
  3. 支持JDBC4.0规范。

  #### java7

  1. 二进制前缀`0b`或者`0B`。整型（byte, short, int, long）可以直接用二进制表示。    

     ```java
       //二进制字面值前缀0b 或0B
         int i = 0b010;//10进制值为2
         int j = 0B010;
     ```

  2. 字面常量数字的下划线。用下划线连接整数提升其可读性，自身无含义，不可用在数字的起始和末尾。

  ```java
  	//数字间的下划线不影响实际值
       int k = 1_1;//值为11
  ```

  3. switch 支持String类型。


  4. 泛型实例化类型自动推断。

     ```java
     Map<String, List<String>> myMap = new HashMap<String, List<String>>();    // 之前
     Map<String, List<String>> myMap = new HashMap<>();        // 现在
     ```

  5. try-with-resources语句。

  ```java
         /*
           * 声明在try括号中的对象称为资源，在方法执行完毕后会被自动关闭,相对与之前必须在finally关闭资源，这一特性大大提高了代码的简洁性。
           * 所有实现java.lang.AutoCloseable接口的类都作为资源被自动关闭。
          */
         try (BufferedReader reader=new BufferedReader(new FileReader("d:1.txt"))){
            return reader.readLine();
        }
  ```

  6. 单个catch中捕获多个异常类型（用`|` 分割）并通过改进的类型检查重新抛出异常。

  #### java8

  1. lambada表达式(Lambda Expressions)。Lambda允许把函数作为一个方法的参数（函数作为参数传递进方法中)。
  2. 方法引用（Method references）。方法引用提供了非常有用的语法，可以直接引用已有Java类或对象（实例）的方法或构造器。与lambda联合使用，可以使语言的构造更紧凑简洁，减少冗余代码。
  3. 默认方法（Default methods）。默认方法允许将新功能添加到库的接口中，并确保兼容实现老版本接口的旧有代码。
  4. 重复注解（Repeating Annotations）。重复注解提供了在同一声明或类型中多次应用相同注解类型的能力。
  5. 类型注解（Type Annotation）。在任何地方都能使用注解，而不是在声明的地方。
  6. 类型推断增强。
  7. 方法参数反射（Method Parameter Reflection）。
  8. Stream API 。新添加的Stream API（java.util.stream） 把真正的函数式编程风格引入到Java中。Stream API集成到了Collections API里。
  9. HashMap改进，在键值哈希冲突时能有更好表现。
  10. Date Time API。加强对日期和时间的处理。
  11. java.util 包下的改进，提供了几个实用的工具类。
      - 并行数组排序。
      - 标准的Base64编解码。
      - 支持无符号运算。
  12. java.util.concurrent 包下增加了新的类和方法。
      - `java.util.concurrent.ConcurrentHashMap` 类添加了新的方法以支持新的StreamApi和lambada表达式。
      - `java.util.concurrent.atomic` 包下新增了类以支持可伸缩可更新的变量。
      - `java.util.concurrent.ForkJoinPool`类新增了方法以支持 common pool。
      - 新增了`java.util.concurrent.locks.StampedLock`类，为控制读/写访问提供了一个基于性能的锁，且有三种模式可供选择。
  13. HotSpot
      - 删除了 永久代（PermGen）.
      - 方法调用的字节码指令支持默认方法。

  #### java9

  1. java模块系统 （Java Platform Module System）。
  2. 新的版本号格式。`$MAJOR.$MINOR.$SECURITY.$PATCH`
  3. java shell，交互式命令行控制台。
  4. 在`private instance methods`方法上可以使用`@SafeVarargs `注解。
  5. diamond语法与匿名内部类结合使用。
  6. 下划线不能用在变量名中。
  7. 支持私有接口方法(您可以使用diamond语法与匿名内部类结合使用)。
  8. Javadoc
     - 简化Doclet API。
     - 支持生成HTML5格式。
     - 加入了搜索框,使用这个搜索框可以查询程序元素、标记的单词和文档中的短语。
     - 支持新的模块系统。
  9. JVM
     - 增强了`Garbage-First(G1)`并用它替代`Parallel GC`成为默认的垃圾收集器。
     - 统一了JVM 日志，为所有组件引入了同一个日志系统。
     - 删除了JDK 8中弃用的GC组合。（DefNew + CMS，ParNew + SerialOld，Incremental CMS）。
  10. properties文件支持`UTF-8`编码,之前只支持`ISO-8859-1`。
  11. 支持`Unicode 8.0`，在JDK8中是`Unicode 6.2`。

  #### java10

  1. 局部变量类型推断（Local-Variable Type Inference）

     ```java
     //之前的代码格式
     URL url = new URL("http://www.oracle.com/"); 
     URLConnection conn = url.openConnection(); 
     Reader reader = new BufferedReader(
         new InputStreamReader(conn.getInputStream()))
     //java10中用var来声明变量
     var url = new URL("http://www.oracle.com/"); 
     var conn = url.openConnection(); 
     var reader = new BufferedReader(
         new InputStreamReader(conn.getInputStream()));
     ```

     `var`是一个保留类型名称，而不是关键字。所以之前使用`var`作为变量、方法名、包名的都没问题，但是如果作为类或接口名，那么这个类和接口就必须重命名了。

     `var`的使用场景主要有以下四种：

     - 本地变量初始化。
     - 增强for循环中。
     - 传统for循环中声明的索引变量。
     - Try-with-resources 变量。​

  #### 参考链接：

  - [JDK Release Notes](http://www.oracle.com/technetwork/java/javase/jdk-relnotes-index-2162236.html)
  - [What's New in JDK 8](http://www.oracle.com/technetwork/java/javase/8-whats-new-2157071.html)
  - [What’s New in JDK 9](https://docs.oracle.com/javase/9/whatsnew/toc.htm#JSNEW-GUID-C23AFD78-C777-460B-8ACE-58BE5EA681F6)
  - [JDK 10 Release Notes](http://www.oracle.com/technetwork/java/javase/10-relnote-issues-4108729.html#NewFeature)
