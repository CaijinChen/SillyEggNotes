# Java知识点补充
###### 总结自java核心技术卷1
---
###### 1.代码点与代码单元
编码字符集（Coded Character Set）：Unicode就是编码字符集的一种，它所做的就是将一个字符集合映射到一个整数集合。

字符编码表（Character）：将CCS中的字符对应的整数转换成特定长度的比特值所制定的规则，比如UTF-8，UTF-16，UTF-32等。使用字符编码表可以帮助计算机使用一串二进制序列存储一个字符，考虑到转换的效率、大小端问题、存储空间等，出现了诸多的编码方式。

代码点：代码点就是字符在字符集中所映射的数字

代码单元：也就是一类变长编码方式中能够编码一个字符所需的最小字节数，比如UTF-8编码一个字符最少仅需一个字节，而UTF-16编码一个字符，最少都需要两个字节。以UTF-16为例，有时候编码一个字符需要两个代码单元，也就是四个字节。

###### 2.传参：按值传递与按引用传递
Java程序设计语言总是采用按值传递的方式（C++语言会采取按值传递与按引用传递两种方式），包括基本数据类型及对象引用。

###### 3.默认初始化、显示域初始化与初始化块
- Java会默认地给类中定义的属性赋予初值：数值为0，布尔值为false，对象引用为null

- 可以在类定义中直接给属性赋值，这个赋值的行为在构造器初始化属性之前。
	#
		public Test{
			private String i = "init value";  	
		}

- 初始化块
	#
		public Test{
			private String i；
			{
				i = "init value";
			}
		}

###### 5.初始化的顺序

1. 默认初始化
1. 按照在类声明中出现的顺序，依次执行所有域初始化语句和初始化块
1. 如果构造器中使用了其他构造器，先执行其他的构造器主体
2. 执行本构造器中的代码

###### 6.使用静态代码块初始化静态域
在类第一次加载时会进行静态域的初始化

	static int i;
	static int j;
	static{
		i = 0；
		j = 1;
	}
###### 7.使用this(...)在本构造器中调用其他的构造器

###### 8.静态导入
导入静态方法和静态域

	//导入java.lang.System包下的静态方法和静态域
	import static java.lang.System.*;
	//执行java.lang.System包下的静态方法和静态域
	out.println("Hello worls");

###### 9.jar文件
JAR文件使用ZIP格式组织文件和目录，任何支持ZIP解压的工具都能对JAR文件进行解压。

一个Jar文件既可以包含类文件，也可以包含诸如图像和声音这些其他类型的文件。除此外，还包含一个描述归档特征的清单文件

###### 10.javadoc

###### 11.反射（见第18条）
反射是指在程序运行期间发现更多的类及其属性的能力

###### 12.Object类是所有Java类的基类，在Java中所有类都由Object类扩展而来

###### 13.散列码
hashCode()方法可以获取一个对象对应的散列码，散列码是一个没有规律的整数值。hashCode()方法定义在Object对象中，每一个对象都有，其值为对象的存储地址。String对象的散列值是由字串的内容计算得到的，它重载了Object对象的hashCode()方法。

###### 14.toString方法
toString方法定义在Object对象中，用于返回表示对象值的字符串，没有重载的情况下，返回“类名@对象地址”这样形式的字串

###### 15.对象包装器与自动装（拆）箱
每一个基本类型都有与之对应的包装类，也称为包装器。例如：Integer，Long，Short，Double，Byte，Float，Character，Void以及Boolean（前六个派生于公共的超类Number），它们均修饰为final。

自动装箱，某些时候基本类型数据会被自动转换成对应的包装类对象。

    //因为泛型不能使用基本值类型的数据，需要转换成对应的对象类型
    list.add(3) =list.add(Integer.valueOf(3))

当Integer类型的数据转换为Int类型数据时，会对应地自动拆箱。

####### 16.可变数量参数
这类参数必须出现在参数列表中的最后位置

	public static void main(String... args){
		
	}
	//等同于
	public static void main(String[] args){
	
	}

###### 17.枚举类
比较两个枚举类型的值时，直接使用“==”即可，不需要使用equals

ordinal()方法返回枚举常量的在enum中的位置，位置从0开始计数

compareTo(E other)如果枚举常量出现在other之前返回负值，this == other返回0，否则返回正值。枚举常量的出现次序在声明中给出

###### 18.反射
反射可以用来：

- 在运行中分析类
- 在运行中查看对象
- 实现通用的数组操作代码
- 利用method对象（类似于C++中的函数指针）

Class类

程序运行期间，Java运行时系统始终会为所有的对象维护一个被称为运行时的类型标志，这个信息跟踪每个对象所属的类，虚拟机利用运行时类型信息选择相应的方法执行。Java语言中可以通过专门的Java类来访问这类信息，保存这些信息的类也即Class类。

	Employee e = new Employee()；
	Class c = e.getClass();
	//也可以通过类名获取对应的Class对象
	Class c = Class.forName("com.java.xxx.Employee")
	//如果T是任意的java类型，T.class将代表匹配的类对象。
	Class c1 = Employee.class;
	Class c2 = int.class;
	Class c3 = Double[].class;

**注意：一个Class对象代表的是一个类型，而这个类型又不一定是一种类**

虚拟机会为每一个类型管理一个Class对象，因此可以利用“==”实现类对象之间的比较

	//判断是否为同一类型
	if（e.geClass() == Employee.class） ...
	Employee e1 = new Employee("name", 1);
    Employee e2 = new Employee("name", 2);
    System.out.println(e1.getClass() == e2.getClass());//true
    Integer k = 45;
    Integer j = 89;
    System.out.println(k.getClass() == j.getClass() && k.getClass() == Integer.class);//true

newInstance()方法可以调用默认构造函数（即没有参数的构造器）初始化新创建的对象，如果没有默认构造函数，则抛出异常

java.lang.reflect包中有三个类Field、Method和Constructor，分别用于描述类的域、方法和构造器这三个类，还有一个Modifier类可以使用静态方法分析getModifiers（前面所述的三个类都有这个方法）的方法返回的整数值。
Class类中的getFields、getMethods、getConstructors方法将分别返回类提供的public域、方法和构造器组，其中包括超类的公有成员。Class类的getDeclaredFields、getDeclaredMethods、getDeclaredConstructors方法可以分别返回类中声明的全部域、方法和构造器，其中包括私有和受保护成员，但不包括超类的成员

Java反射机制的默认行为受限于Java的访问控制，然而，如果一个Java程序没有受到安全管理器的控制，就可以覆盖访问控制。可以使用AccessibleObject的静态方法setAccessible获取访问，这个特性是为调试、持久存储和相似机制提供的。AccessObject类是Field、Method、Constructor类的超类

调用方法

Method  invoke方法

	Object invoke(Object obj, Object... args);
第一个参数是隐式参数，其余对象提供显示参数，如果没有显示参数就需要传递一个null值。对于静态方法，第一个参数可忽略，即传递一个null值即可。如果返回的是基本类型，那么这个方法会自动装箱

获取方法

	Method getMethod(String name, Class... parameterType)
	Method getDeclaredMethod(String name, Class... parameterType)

**注意：invoke的参数和返回值必须是Object类型的，使用invoke方法调用时速度会比直接调用更慢**

###### 19.继承设计的技巧

- 将公共操作和域放在超类
- 不要使用protected
- 使用继承实现‘is-a’操作
- 除非所有继承的方法都有意义，否则不要使用继承
- 在覆盖方法时不要改变预期行为
- 使用多态而非类型信息
- 不要过多地使用反射

###### 20.接口与内部类

内部类技术主要用于设计具有相互协作关系的类集合，特别是在编写处理GUI事件的代码。

代理，这是一种实现任意接口的对象，代理是一种非常专业的构造工具，它可以用来构建系统及的工具。

###### 21.接口

接口不能实例化，但是在声明的时候却可以声明接口的变量

	Comparable x;//OK

可以使用instanceof方法查看类是否实现了某一个接口

接口中的属性会被自动设为public static final

###### 22.克隆对象

clone方法可以为对象创建一个不影响原来对象的拷贝对象。clone方法是Object类的protected方法，默认的克隆操作是浅拷贝，也就是说，在只涉及到数值或基本类型时拷贝是没有问题的，但是当对象含有子对象的依赖时，拷贝的结果会使得新对象与就对象的子域指向同一个子对象，使得原始对象与克隆对象共享这部分信息。

clone方法访问规则：子类只能调用受保护的clone方法克隆他自己。为此必须重新定义clone方法，并将其声明为public，这样才能够让所有的方法克隆对象

Cloneable接口是Java提供的标记接口之一，它并没有抽象方法需要被实现，仅仅说明该对象是可克隆的。即使clone的默认实现（浅拷贝）能够满足需求，也应该实现Cloneable接口

查看某一类是否实现了Cloneable接口可以：

	if(obj instanceof Cloneable) ...

实现深拷贝：

	class Employee implements Cloneable{
		...
		public Employee clone() CloneSupportedException{
			//call Objects.clone
			Employee e = (Employee)super.clone();
			//clone mutable fields
			e.obj = (Object)this.obj.clone();
			return e;
		}
	}

所有的数组类型均包含一个clone方法，这个方法被设为public，而不是protected，可以利用这个方法创建一个包含所有数据元素拷贝的一个新数组

	int[] numbers = {1，2，3，4，5，6}；
	int[] clonedNumbers = numbers.clone()

###### 23.接口与回调

回调指的是在发生某类事件的时候需要采取的动作

###### 24.内部类

内部类是定义在另一个类中的类，内部类的使用原因：

- 可以访问该类定义的数据，包括私有的数据
- 可以对同一个包中的其他类隐藏起来
- 定义回调函数时可以使用匿名内部类实现

一些语法规则：

在内部类中引用外部类：OuterClass.this

在外部类中构造内部类：outerObject.new InnerClass(construction parameters)

外部类引用内部类：OuterClass.InnerClass

内部类可以是私有类

内部类是编译器层出现的，在虚拟机环境下并没有内部类这一概念，这是因为编译器处理内部类的时候会把内部类翻译成用‘$’符号分隔外部类名与内部类名的常规类文件，而虚拟机则对内部类不可见。

局部内部类（它不能由public、private修饰，它的作用于仅局限于声明这个内部类的块中），它可以实现完美的隐藏。

	public void method(){
		class InnerClass{
			...
		}
		InnerClass innerClass = new InnerClasss();
		...
	}

局部内部类可以访问局部变量，前提是局部变量必须声明为final

	public void method(final boolean flag){
		class InnerClass{
			...//此处可以使用局部变量flag
		}
	}

匿名内部类

匿名内部类是局部内部类的深入，假如只创建这个类的一个对象就不需要命名了。

	public void method(){
		Listener listener = new Listener(){
			...//方法
		}
	}
	//此处Listener是一个接口，语法的含义：创建一个实现了Listener接口的类的新对象，需要实现的该接口的方法放置在{}中

匿名类不能有构造器，因为构造器的名字需要同类名一致，而匿名类没有类名。

匿名类如果有构造参数则需要将其传递给超类的构造器

通过匿名类实现接口时不允许传参

	//定义一个Employee类对象
	Employee employee = new Employee("name", 123);
	//定义一个继承自Employee类的匿名内部类对象
	Employee employee = new Employee("name", 123){
        public String getName(){
            System.out.println(super.getName());
            return "This is a String, not a name";
        }
    };
    System.out.println(employee.getName());
	//如果在构造参数的闭括号跟上一个花括号，则说明正在定义一个匿名内部类
	//an object of an inner class extends Person
	Person person = new Person(){ ... }
#
	//在静态方法中，下面的语句可以返回包含静态方法的类名
	System.out.println(new Employee(){}.getClass().getEnclosingClass());

静态内部类

有时候内部类不需要外部类的引用，这时可以声明静态内部类来断绝这类引用。

	public class Outer{
		public static class Inner{
			...
		}
	}

静态内部类的对象除了没有对生成它的外围类对象没有引用特权外，与其他内部类完全一样

声明在接口中的内部类自动成为static和public类

###### 25.代理

利用代理可以在运行时创建一个实现了一组给定接口的新类

代理类是在程序运行过程中创建的，然而一旦被创建就变成了常规类，与虚拟机中的任何其他类没有什么区别

所有的代理类都扩展与Proxy，代理类一定是public和final的

###### 26.异常

所有的异常对象都是派生于Throwable类的一个实例

Throwable<br/>
|<br/>
|————Error<br/>
|_______Exception<br/>

Throwable被继承后有两个分支：Error和Exception

Error类层次结构描述了Java运行时系统的内部错误和资源耗尽的错误。应用程序不应抛出这种类型的对象。如果出现了这样的内部错误，应尽快通知用户

设计Java时，需要多关注Exception类及其继承类，它又派生出两个分支：RuntimeException、IOException。

RuntimeException出现在由程序错误的时候；IOException出现在I/O错误的情况

创建的异常类应该包含两个构造器，一个是默认的无参构造器，一个是带有详细信息的构造器（getMessage和toString方法可以得到这些详细信息）

catch可以捕获多个异常

	catch(Exception1 | Exception2 e){ ... }

使用异常机制的技巧：

- 异常不能代替简单的测试
- 不要过分地细化异常
- 利用异常层次结构：
	- 不要只抛出RuntimeException异常，应当寻找更加适合的自己子类或异常类
	- 不要只捕获Throwable异常，否则会使代码更难维护
- 不要压制异常
- 在检测错误时，“苛刻”要比放任更好
- 不要羞于传递异常

###### 27.断言
断言会对对应的条件代码进行检测，如果结果为false，则抛出AssertionError异常

形式：

- assert 条件；
- assert 条件：表达式；//断言失败时，表达式可以产生一个消息字符串并打印出来

默认情况下断言是禁用的，断言能够在测试期间像代码插入检查语句，但在打包发布时能够被自动地移除

启用断言

java -ea|-enableassertions XXX

使用断言：

- 断崖失败是致命的、不可恢复的错误
- 断言检查只用于开发和检测阶段

###### 28.日志

基本日志（java.util.logging）

	//记录日志信息
	Logger.getGlobal().info("messages here")
	//关闭日志记录
	Logger.getGlobal().setLevel(Level.OFF)
	//激活日志记录器
	Logger.getGlobal().setLevel(Level.INFO)

日子记录器类似于包的结构，含有结构层析

	Looger.getLogger("com.imooc.appName ")

日志级别：

- SEVERE
- WALNING
- INFO
- CONFIG
- FINE
- FINER
- FINEST

默认情况下，只记录前三个级别，但也可以设置其他级别

logger.setLevel(Level.FINE)

可以使用Level.ALL开启所有的级别的日志记录，使用Level.OFF关闭所有级别的记录。

	logger.warning(message)
	logger.fine(message)
	logger.log(Level.FINE, message)

###### 30.Java中的错误处理机制
- 抛出异常
- 日志
- 使用断言

###### 31.泛型

类型参数使得程序具有更好的可读性和安全性

泛型类型可以有多个，一般使用大写字母代表泛型类型，比如：变量E表示集合的元素类型，K和V分别表示关键字与值得类型。有时还会使用U、S等等字母表示“任意类型”

泛型类型必须是引用类型，不能是基本值类型

	class Pair<T, U>{
	    private T first;
	    private U second;
	    public Pair(T first, U second){
	        this.first = first;
	        this.second = second;
	    }
	    public T getFirst() {
	        return first;
	    }
	    public void setFirst(T first) {
	        this.first = first;
	    }
	    public U getSecond() {
	        return second;
	    }
	    public void setSecond(U second) {
	        this.second = second;
	    }
	}
	//Pair<String, Integer> pair = new Pair<>("key", 123);

泛型方法可以定义在一个普通类中（不必是一个泛型类）

	class Tclass{
	
	    public <T> T getValue(T arg){
	        return arg;
	    }
	    
	    public static <T> T getKey(T arg){
	        return arg;
	    }
	}

	System.out.println(t.<String>getValue("This is a string"));
    System.out.println(Tclass.<String>getKey("This is another string"));
	//使用时，尖括号内的类型大部分时候可以不用声明，因为此时编译器可以从传入的参数得到泛型类型
	System.out.println(t.getValue("This is a string"));
    System.out.println(Tclass.getKey("This is another string"));

类型变量的限定

有时需要类型变量需要继承了某一类或实现了某一接口

	class TestClass<T extends Comparable>
	//Comparable是一个接口，意味着泛型T必须实现了Comparable接口，这里使用extends而不是implements，其实是为了说明T应该是绑定类型的子类型，T
	和绑定类型可以是类也可以是接口，使用extends关键字是更接近子类的概念。
	//这类限定可以有多个，多个限定类型之间用‘&’分隔
	T extends Comparable & Serializable

虚拟机没有泛型类型对象，所有的对象均属于普通类，所以泛型只存在于编译阶段，无论何时定义了泛型对象均会自动提供一个对应的原始类型，擦除类型变量，并替换为限定类型（无限定时为Object）

例如Pair\<T>的原始类型为：

	public class Pair<T>{
		private T first;
		public Pair(T first){
			this.first = first;
		}
	}
	
	public class Pair{
		private Object first;
		public Pair(Object first){
			this.first = first;
		}
	}

对于Pair\<T extends Comparable>相应地会有

	public class Pair{
		private Comparable first;
		public Pair(Comparable first){
			this.first = first;
		}
	}

对于Pair\<T extends Comparable & Serializable>同于

对于Pair\<T extends Serializable & Comparable>会有

	public class Pair{
		private Serializable first;
		public Pair(Serializable first){
			this.first = first;
		}
	}

考量上面的两个的差异，应该尽可能将标签接口（类似于Serializable这样的没有方法的接口）放在列表的末尾，即使用Pair\<T extends Comparable & Serializable>的写法，这可以减少对Comparable的强制转换

泛型类的静态上下文中类型变量无效，以下写法错误

	class Pair<T>{
		//error, 错误写法
		public static T getT(T t){
			return t;
		}
	}

不能抛出或捕获泛型类的实例（未搞懂）

通配符类型

	Pair<? extends Employee>
	Pair<? super Employee>

无限通配符

	Pair<?>

反射和类型

Class类是泛型的，例如String.class实际上是Class<String>类的对象（事实上，是唯一的对象）

###### 32.集合

集合框架：接口与实现分离

集合类的基本接口：Collection，它有两个基本方法

	public interface Collection<E>
	{
		boolean add(E element);
		//用于返回实现了Iterator接口的对象，可以使用这个迭代器对象访问集合中的元素
		Iterator<E> iterator();
		...
	}

Iterator接口

	public interface Iterator<E>{
		E next();
		boolean hasNext();
		//remove方法会删除上次调用next方法时返回的元素
		void remove();
	}
	
	//常见用法
	Collection<String> c = ...
	Iterator<String> iter = c.iterator();
	while(iter.hasNext()){
		String element = iter.next();
		...
	}

	//Java5后可以使用foreach取代上面的写法
	for(String element : c){
		...
	}

**注意：实际上，foreach能够和任何实现了Iterator接口的对象一起工作，这个接口只包含一个方法： `Iterator<E> iterator();`Collection接口继承了Iterator接口**

![](https://i.imgur.com/80mtVst.png)

可见，除了Map，其他的集合类均实现了Collection接口

这里介绍一些不常见的集合类

集合类型|描述
:-|:-
ArrayDeque|用循环数组实现的双端序列
HashSet|没有重复元素的无序集合
TreeSet|一种有序集（红黑树）
EnumSet|包含枚举类型的集合
LinkedHashSet|可以记住元素插入次序的集
PriorityQueue|允许高效删除最小元素的集合
HashMap|键值关联的数据结构
TreeMap|键值有序排列的映射表（红黑树）
EnumMap|键值属于枚举类型的映射表
LinkedHashMap|可以记住键值添加次序的映射表
WeakHashMap|其值无用武之地后可以被垃圾回收器回收的映射表
IdentityHashMap|用“==”而不是equals比较键值的映射表

Vector类所有方法都是同步的，可以由两个线程安全地访问一个Vector对象，但是效率比较低。ArrayList方法不是同步的所有线程不安全，但效率较高

###### 33.多线程

并发：伪并行，CPU在一段时间按照调度算法不停地切换执行不同的程序，造成多个程序并行运行的假象

并行：多个CPU分别执行不同的任务，此时CPU不用去切换进程上下文，常见于多核CPU以及多处理器的情况下

多进程与多线程：本是上每个进程拥有自己的一整套变量，而线程则共享数据。线程间通信相比进程间通信效率要更高、更简易。在一些操作系统中，创建线程的开销相比创建进程的开销要小得多。

线程实现：

1.继承Runnable接口：

	public interface Runable{
		void run()
	}
#
	class Run implements Runnable{

	    @Override
	    public void run() {
	        System.out.println("3秒后我会输出一些东西");
	        try{
	            Thread.sleep(3000);
	        }catch(InterruptedException e){
	            e.printStackTrace();
	            Logger.getGlobal().info("线程中断");
	        }
	        System.out.println("这是我要输出的东西");
	    }
	}

	Runnable run = new Run();
	//由Runable创建Thread对象
    Thread t = new Thread(run);
	//启动线程
    t.start();

	//结果：
	3秒后我会输出一些东西
	这是我要输出的东西
	Process finished with exit code 0

2.继承Thread类：

这种方法已不再推荐，应该从运行机制上减少需要并行运行的任务数量，如果有很多任务，可以考虑建立线程池来处理

	class Run extends Thread{

	    @Override
	    public void run() {
	        super.run();
	        System.out.println("3秒后我会输出一些东西");
	        try{
	            Thread.sleep(3000);
	        }catch(InterruptedException e){
	            e.printStackTrace();
	            Logger.getGlobal().info("线程中断");
	        }
	        System.out.println("这是我要输出的东西");
	    }
	}

	//创建Run对象
    Thread t = new Run();
	//启动线程
    t.start();

	//结果：
	3秒后我会输出一些东西
	这是我要输出的东西
	Process finished with exit code 0

**注意：不要调用Thread或Runable对象的run方法。直接调用run方法只会执行同一个线程中的任务，而不会启动新线程，应该调用start方法**

线程中断

当线程的run方法执行方法体中的最后一句语句、或由return语句返回或者在方法中出现了没有捕获的异常，线程将终止

interrupt方法可以用来请求终止进程，当对一个线程调用interrupt，则线程对应的中断状态会被置位。

currentThread()返回当前执行线程的Thread对象

interrupted()它会测试当前线程是否被中断，调用它还会重置中断状态为false，即不中断

线程状态

- New
- Runable
- Blocked
- Waiting
- Timed waiting（计时等待）
- Terminated（被终止）

![](https://i.imgur.com/xJK5I7q.png)

在多处理器的机器中，每一个处理器运行一个线程，当线程数多于处理器的数量时，仍旧采用时间片轮转的机制

线程终止：

- 因为run方法正常退出而自然死亡 
- 因为一个没有捕获的异常而终止

一些方法：

- join()等待终止指定的线程
- getState()获取当前状态

线程属性

每个线程都有一个优先级，一个线程会继承它的父线程的优先级。

MIN_PRIORITY(1) --->  MAX_PRIORITY(10)

NORM_PRIORITY(5) //默认线程优先级

yield()方法导致当前线程处于让步状态，这是一个静态方法，如果有其他优先级高于或等于当前线程的线程存在，则它们会被调度。

Java的线程分为守护线程和用户线程

守护线程

setDaemon(true)方法设置一个线程成为守护线程。守护线程是为了给其他线程服务而存在的。若要设置则这一方法必须在线程启动之前调用。当用户线程运行完毕，只剩下守护线程时，JVM退出。

	public class Test08 {
	    public static void main(String[] args) {
	        Thread t1 = new Thread(new Runnable() {
	            @Override
	            public void run() {
	                try{
	                    while(true){
	                        Thread.sleep(500);
	                        System.out.println(">>> i'm daemon");
	                    }
	                }catch(InterruptedException e){
	                    e.printStackTrace();
	                }
	            }
	        });
	        t1.setDaemon(true);
	        t1.start();
	        for(int i = 0; i < 3; i ++){
	
	            new Thread(new Runnable() {
	                @Override
	                public void run() {
	                    for(int i = 0; i < 3; i++){
	                        try{
	                            Thread.sleep(1000);
	                        }catch(InterruptedException e){
	                            Logger.getGlobal().info("Interrupt accident");
	                        }
	                        System.out.println("This is a son thread");
	                    }
	                }
	            }).start();
	        }
	    }
	}
	//运行结果
	>>> i'm daemon
	>>> i'm daemon
	This is a son thread
	This is a son thread
	This is a son thread
	>>> i'm daemon
	This is a son thread
	This is a son thread
	>>> i'm daemon
	This is a son thread
	>>> i'm daemon
	This is a son thread
	This is a son thread
	This is a son thread
	>>> i'm daemon
	Process finished with exit code 0

线程的run方法不能抛出任何被检测的异常。但是，不需要任何catch语句处理可以被传播的异常。相反就在线程死亡之前，异常会被传送到一个用于未捕获异常的处理器。

处理器需要实现Thread.UncaughtExceptionHandler接口，它只有一个方法：`void uncaughtException(Thread t, Throwable e)`。可以使用setUncaughtExceptionHandler方法为任何线程安装一个处理器。也可以使用Thread类的静态方法setDefaultUncaughtExceptionHandler为所有线程安装一个默认的处理器。替换处理器可以使用日志API发送未捕获异常到日志文件中。如果不安装默认的处理器，默认的处理器为空。

线程同步

1.synchronized关键字

    public synchronized void method(){

        while(condition...){
            wait();
        }
        ...
        notifyAll();
    }
	//同步静态方法 synchronized(Class)同步类
	public static synchronized void method(){}
	//同步普通方法 synchronized(this)同步对象
	public synchronized void method(){}
	//同步代码块 synchronized(xxx)同步xxx
	synchronized(xxx) {
            
    }

notifyAll 解除那些在该对象锁上调用wait方法的线程的阻塞状态
notify 随机选择一个在该对象上调用wait方法的线程，解除其阻塞状态
wait 让线程进入等待状态
wait(long)、wait(long, int) 导致线程进入等待状态直到它被通知或经过指定的时间

2.ReentrantLock类(实现了Lock接口)

ReentrantLock 构建一个可以被用来保护临界区的可重入锁
ReentrantLock(boolean fair) 构建一个带有公平策略的锁，公平锁偏爱等待时间最长的线程，但此锁会降低性能

	ReentrantLock lock = new ReentrantLock();
	lock.lock();
	try{
		...
	}finally{
		//把解锁操作置于finally子句中很重要，如果在临界区的代码抛出异常，锁必须释放
		lock.unlock();
	}

3.条件变量

条件变量适用于已获得锁但是线程继续执行的条件不满足的时候，线程选择先行释放锁以供其他线程使用的情况，此时线程会被阻塞。

一个锁对象可以有一个或多个相关的条件对象。

	class Bank{

		...
	    private Lock bankLock = new ReentrantLock();
	    private Condition condition;
	
	    public Bank(int n, double initialBalance){
	        ...
	        condition = bankLock.newCondition();
	    }
	
	    public void transfer(int from, int to, double amount) throws InterruptedException{
	        bankLock.lock();
	        try{
	            while(accounts[from] < amount){
					//此时线程阻塞，锁被释放，线程进入该条件的等待集，锁可用时线程仍不能马上解除阻塞，它会一直阻塞直到另一个线程调用同一条件上的signalAll方法
	                condition.await();
	            }
	            ...
				//重新激活等待的所有线程
	            condition.signalAll();
	        }finally {
	            bankLock.unlock();
	        }
	   }

await() 将线程放到条件的等待集中
signalAll() 解除该条件的等待集中所有线程的阻塞状态
signal() 从等待集中随机选择一个线程解除其阻塞状态

锁和条件

- 锁用来保护代码片段，任何时刻只能有一个线程执行被保护的代码片段
- 锁可以管理试图进入被保护代码段的线程
- 锁可以拥有一个或多个条件对象
- 每个条件对象管理那些已经进入被保护代码段但还不能运行的线程

volatile 修饰的成员变量在每次被线程访问时，都强制从共享内存中重新读取该成员变量的值。而且，当成员变量发生变化时，会强制线程将变化值回写到共享内存。这样在任何时刻，两个不同的线程总是看到某个成员变量的同一个值。

声明final属性达到变量的共享

线程局部变量

读写锁

阻塞队列

ArrayBlockingQueue
LinkedBlokingQueue
LinkedBlokingDeque
DelayQueue
PriorityBlockingQueue
BlockingQueue
BlockingDeque

###### 34.线程安全的集合类

ConcurrentHashMap
ConcurrentSkipListMap
ConcurrentSkipListSet
ConcurrentLinkedQueue

###### 35.Callable Future

Runable封装一个异步运行的任务，它没有参数也没有返回值

Callable与Runable类似，但是有返回值，Callable接口是一个参数化的类型，只有一个call方法

	public interface Callable{
		V call() throws Exception();
	}

类型参数是返回值的类型，比如Callable<Integer>则返回值类型需要是Integer

Future保存异步计算的结果，可以启动一个计算，将Future对象交给某个线程，等到对象的所有者在计算完成好后就可以获得结果

	public interface Future<V>{
		//第一个get方法的调用被阻塞，直到计算完成
		V get() throws ...;
		//第二个get方法调用超时则抛出TimeoutException异常
		V get(long timeout, TimeUnit unit) throws ...;
		//取消计算（mayInterrupt为true时）
		void cancel(boolean mayInterrupt);
		boolean isCancelled();
		boolean isDone;
	}

FutureTask包装器是一种非常便利的机制，可将Callable转换成Future和Runnable，它同时实现二者的接口
	
	Callable<Integer> myComputation = ...;
	FutureTask<Integer> task = new FutureTask<Integer>(myComputation);
	Thread t = new Thread(task);
	t.start();
	...
	Integer result = task.get();

线程池

同步器

java.util.concurrent

java并发编程

