> 前言：Core Java应该算是在Java领域内最有价值和影响力的书之一了，特别是卷1的基础，无论是做Android开发还是做Java开发个人觉得都应该看一遍，以下是笔者在阅读时笔记，不能说是总结，只能算是觉得哪里有需要就记一下，基本尊重原文（毕竟原文已经总结的很好了），部分加上了自己的理解，如有问题欢迎指正。   ——七月

#### [1.Java的基本程序设计结构](#Java的基本程序设计结构)
#### [2.对象与类](#对象与类)
#### [3.继承](#继承)
#### [4.反射](#反射) TODO
#### [5.泛型程序设计](#泛型程序设计)
#### [6.集合](#集合) TODO
#### [7.接口、lambda表达式与内部类](#接口、lambda表达式与内部类)

## Java的基本程序设计结构
### 变量
#### 关于const
const是Java保留的关键字，但目前并没有被使用。在Java中，必须使用 ***final*** 定义常量。

#### strictfp关键字
对于使用strictp关键字标记的方法必须使用严格的浮点计算来生成可再生的结果。例如，可以把main方法标记为

```
public static strictfp void main(String[] args)
```
如果这么标记，则main方法中的所有指令都会使用严格的浮点计算，同理，如果一个类标记为strictfp的话这个类所有的方法也都会使用这个方法。

#### 强制类型转换要注意的
如果将一个数值从一个类型强制转化成另一种类型，而又超出了该类型转化的范畴，结果就会被截断成一个完全不同的值。例如(byte)300 = 44。

所以，在使用强转的时候要考虑接收类型，大多数情况下，例如int强转成long或者子类强转成父类等。

### 运算符
#### 自增与自减运算符（面试常会问a++ 和 ++a的区别）
前缀（++ a）会先自增再引用，后缀（a ++ ）会先引用自增
```
int n = 7;
int m = 7;
int a = 2 * ++m; //a=16 m=8
int b = 2 * n++; //b=14 n=8
```

#### 运算符，三元操作符?:（优雅你的代码）
```
x<y ? x:y //返回x和y中较小的一个
```
如果第一个条件为true，返回第一个值（x）,如果为false则返回第二个（y）

### 字符串
#### 优雅的实现多拼接（例如","分割）
多个字符串拼接在一起，用一个定界符分隔的话，可以使用join方法：

```
String str = String.join(",","1","2","3");
//输出结果为 1，2，3
System.out.println(str);
```

#### 字符串相关
**字符串是否相等**

使用*s.equal(t)*可以判断字符串是否相等，判断字符串（不区分大小写）是否相等可以使用*str.equalsIgnoreCase(t)*

**空串与Null串**

空串判断：

```
if(str.length() == 0)
```
或

```
if(str.equals(""))
```
Null判断：

```
if(str==null)
```
所以一般严格判断的话：

```
if(str!=null && str.length()!=0)
```

#### 字符串构造器 StringBuilder
当复杂字符串拼接的话，可以使用StringBuilder提升拼接效率，如下：
```
    StringBuilder stringBuilder = new StringBuilder();
    stringBuilder.append("wang");
    stringBuilder.append("yuan");
    stringBuilder.append("dong");
    //输出结果：wangyuandong
    System.out.println(stringBuilder.toString());
```

### 控制流（条件语句）
#### switch语句使用慎重
如果忘记增加break语句的话会执行其他的case。所以不太建议使用，虽然会使代码简洁，但是同样会伴随风险。

### 大数值
如果基本的整数和浮点数精度不能满足需求的话，可以使用java.math包里的两个类**BigInteger**和**BigDecimal**，这两个类可以处理任意长度的数值。如+和*可以使用BigInteger里的add()和multiply()。

### 数组
#### 数组初始化及匿名数组
Java中提供了一种创建数组并赋予初始化值的简易写法

```
int[] smallPrimes = {2, 3, 4, 5, 6};
```
Java打印数组（当然，你也可以选择for循环）：

```
System.out.print(Arrays.toString(a));
```

#### 数组拷贝
在Java中，允许将一个数组变量拷贝给另一个数组变量，这时，两个数组变量将引用同一个数组。如果将一个数组拷贝到另一个数组的话，可以使用copyOf方法：

```
int[] copiedLuckyNumbers = Arrays.copyOf(luckyNumbers, luckyNumbers.length);
```
排序：

```
Arrays.sort();
```

## 对象与类
### 定义类
#### 类之间的关系
类之间常见的关系有以下三种：
- 依赖 ("uses-a") 一个类的方法操纵另一个类的对象
- 聚合 ("has-a") 类A对象包含类B对象
- 继承 ("is-a") 父子类

#### 构造器
其实就是初始化（赋值），类似oc里的init方法。
- 构造器与类同名
- 每个类可以有一个以上的构造器
- 构造器可以有0、1或者多个参数
- 构造器没有返回值
- 构造器总是伴着new操作一起调用

所有的Java对象都是在堆中构造的，构造器总是伴随着new操作符一起使用。

*注意*不要再构造器中定义与示例域重名的局部变量。（嗯，虽然是一句废话，但是经常会有人习惯性文档里就直接定义了）

```
// 这肯定不行！
public User(String name, int age)
{
    String name = name;
    int age = age;
}
```

#### 隐式参数与显式参数
出现在方法名前的类对象即为隐式参数，位于方法名后括号中的参数为显式参数。

在每个方法中，关键字this表示隐式参数。

*PS：在Java中，所有的方法都必须在类的内部定义，但并不表示他们是内联方法。*

#### 老生常谈的实例域值
- 一个私有的数据域；
- 一个公有的域访问器方法；
- 一个公有的域更改器方法。

```
public class User {
    
    private String name;

    public void setName(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }
}
```

### 静态域和静态方法
用static修饰符标记

**静态域**
静态域属于类，不属于实例，所有这个类的实例共享一个静态域，例如实例化了1000个这个类的对象，那么这1000个对象共享一个静态域。更直观的说明如下代码：

```
public class User {
//只是为了更直观的说明，所以请忽略这个public - -
    public static String desc = "1231231231231";
}

System.out.println(User.desc);
```
**静态常量**

静态变量相对用的比较少，更多的还是静态常量。例如配置一些第三方服务的APPKEY这种的。

```
public static final String APPKEY = "APP_KEY";
```
*PS：可以声明public是因为final声明后为不可变，所以也就没有其他类进行乱修改的问题了。*

**静态方法**

静态方法是一种不能向对象实施操作的方法。（类似OC里的类方法）

**工厂方法**

静态方法还有另一种常见的用途，就是构造对象。可以通过工厂方法构造不同风格的格式化对象。
那么为什么不能用构造器来生成对象：
- 无法命名构造器，构造器的名字必须与类名相同。
- 使用构造器的话，无法改变所构造的对象类型。

### 对象构造
#### 重载

如果多个方法有相同的名字，不同的参数，这种特征就叫重载。

#### 默认域初始化

如果在构造器没有显式的给域赋予初值，那么就会被自动的赋为默认值：数值为0、布尔值为false、对象引用为null。（如果不明确的对域初始化的话，其实会影响代码的可读性）

#### 无参数的构造器

很多类都包含一个无参数构造器，这个构造器会将所有实例域都设成默认值，同时要注意的是，仅当类没有提供任何构造器的时候，系统才会统一提供一个默认的构造器。

#### 显式域初始化
通过重载类的构造器方法，可以采用多种形式设置类的实例域的初始状态。

#### 初始化块
虽然很少用，但是Java存在这种初始化机制，叫做初始化块，只要构造类的对象，这些块就会被执行。例如：

```
public class User {

    private String name = "123";
    //初始化块
    {
        name = "初始化块";
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }

    public static final String APPKEY = "APP_KEY";
}
```

#### finalize方法
可以为任何一个类添加finalize方法，finalize方法将在垃圾回收器清除对象之前调用，但是不要依赖他！不要依赖他！不要依赖他！(重要的事情说三遍)，因为你不知道他什么时候才会被调用。

### 静态导入
import语句不仅可以导入类，还增加导入静态 方法和静态域的功能。这样的好处是不用加类名前缀了，但是坏处也很明显，代码的阅读维护会很难受。

### 类设计技巧(设计会单开一篇笔记记录)
1. 一定要保证数据私有
2. 一定要对数据初始化（实例域初始化）
3. 不要在类中使用过多的基本类型，如果基本类型过多的话，可以看一下是否可以生成一个新的类去替换他的一部分实例域
4. 不是所有的域都需要独立的访问器和域更改器
5. 将职责过多的类进行分解
6. 类名和方法名要能体现他们的职责
7. 优先使用不可变的类

## 继承
### 类、超类、子类
#### 覆盖方法
这个在继承中应该经常会遇到，因为很多时候父类的方法并不适用于子类，所以会提供一个新的方法来覆盖父类中的这个方法。

#### 子类构造器

```
public class Teacher extends User {
    private int mAge;

    public Teacher(String name, int age){
        super(name);
        this.mAge = age;
    }
}
```
super关键字也是有两个用途，一个是调用超类的方法，另一个是调用超类的构造器。

#### 概念——继承层次
继承并不仅限于一个层次，由一个超类派生出来的所有类的集合被称为*继承层次*。在继承层次中，从某个特定的类到其祖先的路径被称为该类的*继承链*。

*PS:Java里不支持多继承，一般要实现类似多继承的话可以使用接口来实现*

#### 多态
每个子类的对象也是超类的对象，也就是程序中出现超类对象的任何地方都可以用子类对象来替换，例如：

```
User user;
user = new User();
user = new Student();
```
*其实说白了就是所有的男人都是人，但是所有的人不都是男人*

#### 理解方法的调用（划重点）
例如调用x.f(args)
1. 编译器查看对象的声明类型和方法名。因为可能会存在多个名字为f但是参数类行不一样的方法，所以编译器会一一列举所有这个类当中名为f的方法和他的父类当中访问属性非私有且名为f的方法，这样编译器就获得了所有可能被调用的候选方法。（原文是public，但是protected子类也可以访问）
2. 接下来编译器将查看调用方法时提供的参数类型，如果所有名为f的方法中存在一个与提供的参数类型完全匹配，就选择这个方法。这个过程被称为*重载解析*。如果编译器没有找到参数类型与之匹配的方法，或者发现经过类型转换后有多个方法与之匹配，则会报错。至此，编译器已经获得要调用的方法名及参数类型。
3. 若果是private方法、static方法、final方法或者构造器，那么编译器可以准确的知道使用的是哪个方法，这种调用方法称为*静态绑定*。相对的，调用的方法依赖于隐式参数的实际类型，在运行时实现动态绑定。
4. 当程序运行时，并且采用动态绑定的调用方法时，虚拟机一定调用与x所引用对象的实际类型最合适的那个类的方法。也就是假设x的实际类型是D，它是C类的子类。如果D类定义了方法f(String)就直接调用他，否则就去C类续寻找f(String)，以此类推。

*PS:再覆盖一个方法的时候，子类方法不能低于超类方法的可见性，例如超类方法声明为public，子类一定也是public*

#### 阻止继承：final类和方法
不允许扩展的类被称为final类（也就是阻止这个类定义子类），如果在定义类的时候用了final修饰符就表明这个类是final类，如下：

```
public final class Teacher extends User {
    ...
}
```
则这个Teacher类不会再被定义子类。

类中的特定方法也可以声明成final，这样的话，子类就不能覆盖这个方法。（例如有的方法是需要外部调用public，但是不想让子类可以覆盖，那么就可以使用final）

#### 抽象类
包含一个或多个抽象方法的类必须被声明为抽象的。
所谓抽象方法即为只声明没有实现，具体实现在子类中。（大多数认为，在抽象类中不能包含具体方法）

一个类即使不包含抽象方法，也可以声明成抽象类。

抽象类不能被实例化，也就是说如果一个类是抽象类，那么他就不能创建出这个类的对象，但是可以创建一个具体子类的对象。需要注意的是，可以定义一个抽象类的对象变量，但它只能引用非抽象子类的实例，如：

```
Person p = new Student("qiyue", 18);
```
p是抽象类Person的变量，引用了非抽象子类Student的实例。

#### 受访问保护
类中最好的标记方式是域标记为private，方法标记为public。如果仅限于子类访问的话，就是protected，不过即使是子类，还是不建议声明这个方法，因为其他协作开发同学即可通过派生去访问这个超类，这就违背了封装原则，所以protected也是要慎用的。

### Object：所有类的超类
Object是Java中所有类的爸爸。

在Java中只有基本类型不是对象，例如：数值、字符和布尔值。

所有的数组类型，不管是对象数组还是基本类型数组都继承了Object。

#### toString 方法

Object有一个重要的方法toString，用于返回表示对象值的字符串。所有的类都继承于Object，所以toString提供于所有子类调用。不过，最好还是自定义toString方法，并将对应的子类描述添加进去，这样无论是从日志处理还是调试都会更方便一些。因为toString打印出来的是所属类名和它的hashCode。例如数组直接调用toString输出的是类似：“[I@1a46e30”这种的。

#### Object 包含的一些方法
- Class getClass()

返回包含对象信息的类对象。

- boolean equals(Object otherObject)

返回比较两个对象是否相等，如果两个对象只想同一块存储区域，则返回true。在自定义类中，应该重写这个方法。

- String toString()

返回描述该对象值的字符串。在自定义的类中，应该覆盖这个方法。

- String getName()

返回这个类的名字

```
    ClassDemo cl = new ClassDemo();
    Class c1Class = cl.getClass();

    // returns the name of the class
    String name = c1Class.getName();
    System.out.println("Class Name = " + name);
```

- Class getSuperclass()

以Class对象的形式返回这个类的父类信息。

### 泛型数组列表（ArrayList）
ArrayList是一个采用类型参数的泛型类(自定义泛型类等，后面章节会有总结，这里只说一下ArrayList的简单用法)，构造可以如下：

```
ArrayList<User> userList = new ArrayList<>();
```
添加元素可以使用add方法：

```
userList.add(user);
```
没有泛型类时，原始的ArrayList类提供的get方法别无选择，只能返回Object，因此get方法的调用者必须对返回值进行类型转换，因此建议使用ArrayList<User>。

获取元素如下：

```
userList.get(index);
```

插入指定位置可以如下：

```
userList.add(index,user);
```
同样，可以从数组列表中间删除元素：

```
userList.remove(index);
```
设置指定位置值：

```
userList.set(index, user);
```

### 对象包装器与自动装箱
int、long等这些的基本类型都有一个对应的类Integer、Long等。因为比如ArrayList的类型参数是不允许是基本类型。所以，这些基本类型对应的类就称为包装类。

Java为了方便操作，有一个自动装箱的概念，例如list.add(3)，3是基本类型，但是会自动变成：list.add(Integer.valueOf(3));

- 对象包装器类是不可变的
- 对象包装器类是final

### 参数数量可变方法（变参）

```
public class PrintStream
{
    public PrintStream printf(String fmt, Object...args){
        return format(fmt, args);
    }
}
```
这里的...是Java代码的一部分，标明这个方法可以接收任意数量的对象（除fmt参数之外），这个我觉得直接上代码理解最直观：

```
public static double max(double...values){
    double largest = Double.NEGATIVE_INFINITY;
    for(double v: values) if (v > largest) largest = v;
    return largest;
}
```

### 继承的设计技巧
1. 将公共操作和域放在超类
2. 不要使用受保护的域
3. 使用继承实现“is-a”关系
4. 除非所有继承的方法都有意义，否则不要使用继承
5. 在覆盖方法时，不要改变预期的行为
6. 使用多态，而非类型消息
7. 不要过多的使用反射机制


## 反射
> 七月：怎么说呢，个人感觉反射更对的是对工具开发工程师，应用开发工程师可能很大时候都用不到，就类似于iOS的runtime，大多数的面试官都喜欢问这个并拿出来作为一个必考点，但是实际上公司的项目并不会用到（当然如果硬解释说用到xxx库里是runtime实现的也是用到了，那也无话可说了），笔者的所有项目里，只遇到了两次使用第三方封装的SDK不得不使用runtime去动态替换原有的SDK方法，但是其他的百分之八十项目在所以的过程中并没有用到。这里不是说不要去学它，对于研发来说就是一直行走在学习的路上，所以学肯定比不学要好，但是个人观点，舍本求末则是没必要的。（以上只是个人观点，不赞同的请无视掉，仅当是笔者的碎碎念）

反射库提供了一套丰富的工具集，以便于编写能够动态操纵Java代码的程序。特别是在设计或运行中添加新类时，能够快速的应用开发工具动态查询新添加类的能力。
> 能够分析类能力的程序称为反射。

反射机制可以用来：
- 在运行时分析类的能力。
- 在运行时查看对象，例如，编写一个toString的方法供所有类使用。
- 实现通用的数组操纵代码。
- 利用Method对象，这个对象很像C++中的函数指针。


## （TODO, 这个单独拿出来说）

## 泛型程序设计
### 为什么要使用泛型程序设计
泛型程序设计意味着编写的代码可以被很多不同类型的对象所重用，例如一个ArrayList类可以聚集任何类型的对象。

在Java中增加泛型类之前，泛型程序设计是用继承来实现的。但是这种实现方式必不可免的会遇到两个问题：
- 当获取一个值时必须进行强制的类型转换
- 没有错误检查，可以向ArrayList中添加任何类的对象（也就是其他地方get的时候，在类型强转时很有可能就出错了）。

### 定义简单的泛型类
一个泛型类就是具有一个或多个类型变量的类。不多说，代码最直观：

```
public class Pair<T> {
    private T first;
    private T second;

    public Pair(){
        this.first = null;
        this.second = null;
    }

    public Pair(T first, T second){
        this.first = first;
        this.second = second;
    }

    public T getFirst() {
        return first;
    }

    public void setFirst(T first) {
        this.first = first;
    }

    public T getSecond() {
        return second;
    }

    public void setSecond(T second) {
        this.second = second;
    }
}

public class Main {

    public static void main(String[] args) {

        Pair<String> pair = new Pair("first", "second");
        System.out.println(pair.getFirst());
        System.out.println(pair.getSecond());
    }

}
```
### 泛型方法
普通类中可以定义一个泛型方法，例如：

```
//返回数组中间的元素
public static <T> T getMiddle(T...a){
        return a[a.length/2];
}
```

### 关于T 和 Object 的一点小看法
理解泛型的时候可能会有一些小疑惑，就是为什么使用T而不是用Object。

(先跑个题，你也可以不用T，用W、X等非保留字也是可以的。。。)

首先，T类型无关，只是一种JDK1.5以后的新特性，事实是编译器支持可以在运行时将实际类型载入，class的二进制码里不含任何和类型有关的信息。

同时如果我们用了Object的话，那么Object是所有类型的父类，也就意味着他的范围很大覆盖所有的类，但是泛型的概念是限定某种类型，那么直接用Object的话就很不符。

这里可以用[这篇博客](http://www.cnblogs.com/yulinfeng/p/6056038.html)的总结：
> Object范围非常广，而T从一开始就会限定这个类型（包括它可以限定类型为Object）。
>
> Object由于它是所有类的父类，所以会强制类型转换，而T从一开始在编码时（注意是在写代码时）就限定了某种具体类型，所以它不用强制类型转换。（之所以要强调在写代码时是因为泛型在虚拟机中会被JVM擦除掉它的具体类型信息，这点可参考泛型，在这里不做引申）。

所以，这里个人最大的区别就是Object是个实际的类，而T是一个特性，类似于一个标签。

而且，类型擦除会将T改变成Object，这里其实也就明确为什么了。

### 类型擦除
无论何时定义一个泛型类型，都自动提供了一个相应的原始类型。原始类型的名字就是山区类型参数后的泛型类型名。擦除类型变量，并替换为限定类型（无限定变量的使用Object）


```
public class Pair {
    private Object first;
    private Object second;

    public Pair(){
        this.first = null;
        this.second = null;
    }

    public Pair(Object first, Object second){
        this.first = first;
        this.second = second;
    }

    public Object getFirst() {
        return first;
    }

    public void setFirst(Object first) {
        this.first = first;
    }

    public Object getSecond() {
        return second;
    }

    public void setSecond(Object second) {
        this.second = second;
    }
}
```

在程序中可以包含不同类型的Pair，例如，Pair<String>或Pair<LocalDate>。而擦除类型后就变成原始的Pair类型了。

原始类型用第一个限定的类型变量来替换，如果没有给定的限定就用Object替换。

### 约束与局限性
#### 不能用基本类型实例化类型参数
不能用类型参数代替基本类型，因此没有类似Pair<int>、Pair<double>。
> 原因就是擦除之后，Pair类含有Object类型的域，而Object不能存储double值（基本类型）
#### 运行时类型查询只是用于原始类型
>虚拟机中的对象总有一个特定的非泛型类型，因此，所有的类型查询只产生原始类型。
#### 不能创建参数化类型的数组，例如：

```
Pair<String>[] table = new Pair<String>[10];//error
```
原因是擦除之后，table类型是Pair[]。可以把它转换为Object[]，

```
Object[] objarray = table;
```

这样objarray[0]="hello"就会抛出异常。
#### 不能实例化类型变量
不能使用像new T(...)，new T[...]或T.class这样的表达式中的类型变量。

#### 不能构造泛型数组 
同上，就像不能实例化泛型示例也不能实例化泛型数组。泛型化数组T[]

#### 泛型类的静态上下文中类型的变量无效
不能在静态域或方法中引用类型变量，例如：

```
public class Singleton<T> {
    private static T singleton; //error
    public static T getSingleton() { //error
        return singleton;
    }
}
```
这是无法运行的(这里编译器也会直接报错)。

#### 不能抛出或捕获泛型类的示例，重点还是注意擦除后的冲突。

### 泛型类型的继承规则
比如父类Parents和子类Son，那么Pair<Son>不是Pair<Parents>的一个子类。

因为无论P<S>、P<T>里，S和T有什么样的联系，P<S>和P<T>没什么联系。

### 通配符类型
#### 通配符概念
通配符类型中，允许类型参数变化。例如：

```
Pair<? extends Emloyee>
```
这个表示任何泛型Pair类型，他的类型参数都是Emloyee的子类。

#### 通配符的超类型限定
通配符可以指定一个超类型限定，例如：

```
? super Manager
```
这表示这个通配符限制为Manager的所有超类型（也就是都是父类）。

#### 无限定通配符
还有一种无限定通配符，例如 Pair<?> ,这个看着基本上和Pair类型一样，但是为什么还要单独做一个呢？原因是，实际上类型 Pair<?> 有以下方法：

```
? getFirst()
void setFirst(?)
```
可以用任意Object对象调用原始Pair类的setObject。
注意：可以调用 setFirst(null)。


## 接口、lambda表达式与内部类
### 接口
在Java的程序设计中，接口不是类，而是对类的一组需求描述（类似oc的协议），这些类要遵从接口描述的统一格式进行定义。例如：

```
public interface Comparable {
    int compreTo(Object other);
}
//Java SE 5.0后
public interface Comparable<T> {
    int compreTo(T other);
}
```
接口中的所有方法自动属于public，因此不需要额外的声明。但是，在实现接口时必须把方法声明为public，否则编译器将认为这个方法的访问属性是包可见性，即类的默认访问属性，之后编译器会给出试图提供更严格的访问权限的警告信息⚠️。

为了让类实现一个接口，通常需要做下面两件事儿：
1. 将类声明为实现给定的接口
2. 对接口中所有的方法进行定义


```
public class User implements Comparable {
    @Override
    public int compreTo(Object other) {
        return 0;
    }
}
```

当然，更严谨的方式，可以给泛型Comparable接口提供一个参数类型

```
public class User implements Comparable<User> {
    @Override
    public int compreTo(User other) {
        return 0;
    }
}
```
#### 接口的特性
接口不是类，尤其是不能使用new运算符实例化一个接口：

```
x = new Comparable(...);//error
```
然而，尽管不能构造接口的对象，却能声明接口的变量：

```
Comparable x;//OK
```
接口变量必须引用实现了接口的类对象

```
x = new User(...);
```
和类可以被继承一样，接口也可以被扩展，例如一个叫Moveable的接口：

```
public interface Moveable {
    void move(double x, double y);
}
```
然后，一个叫Powered的接口可以继承他:

```
public interface Powered extends Moveable{
    double milesPerGallon();
}
```
虽然在接口中不能包含实例域或静态方法，但却可以包含常量：

```
double milesPerGallon();
double SPEED_LIMIT = 95;
```
不过再Java SE 8中，允许在接口中增加静态方法。

#### 默认方法
可以为接口方法提供一个默认实现，必须用default修饰符标记这样一个方法。

```
public interFace Comparable<T> {
    default int compareTo(T other){
        return 0;
    }
}
```

## 集合
#### Collection接口
Java中集合类的基本接口是Collection接口。这个接口有两个基本方法：

```
public interFace Collection<E>
{
    boolen add(E element);
    Iterator<E> iterator();
}
```