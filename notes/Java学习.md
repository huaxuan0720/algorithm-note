# Java学习

## Day1

### Java配置

- 下载安装对应jdk
- 配置环境变量JAVA_HOME和JAVA_HOME\bin和JAVA_HOME\jre\bin
- 不需要配置class_path
- 如果没有jre目录，可以使用管理员身份打开cmd，进入jdk目录，运行以下命令：

```cmd
bin\jlink.exe --module-path jmods --add-modules java.desktop --output jre
```

### 第一个Java程序

- HelloWorld.java

```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello World");
    }
}

```



注意事项：

- java文件的名称必须和public class的名称保持一致
- 一个java文件中可以包含多个class，但是public class只能有一个
- public static void main(String[] args)是所有java程序的入口，如果想执行对应的java代码，则必须添加如下的方法，且格式是固定的。
- main方法中的参数列表可以支持队中写法，String[] args，String [] args，String args[]
- main方法中参数名称无所谓，一般写成args
- java代码在编写时候，每行语句的结尾需要使用英文分号*;*结束
- java代码使用英文大括号*{}*进行代码块的分割

### Java注释

```java
//这是一个单行注释
/*
这是一个多行注释1
这是一个多行注释2
*/
/**
* 这是一个多行文档注释1
* 这是一个多行文档注释2
* 文档注释可以通过javadoc命令进行读取，生成程序的API文档
*/
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello World");
    }
}
```

### Java反编译

### Java数据类型和运算符

#### 标识符

- 必须以字母*a-z，A-Z*，下划线*_*，美元符号*$*开头，不可以用数字进行开头
- 其他部分可以是字母*a-z，A-Z*，下划线*_*，美元符号*$*和数字的任意组合
- 大小写敏感，长度无限制
- 不可以是Java关键字

#### 命名规范

- 类名，接口名称在命名的时候要首字母大写
- 方法，变量命名的时候首字母要小写
- 多个单词拼接表示一个标识符的时候，每个单词的首字符要大写
- 见名知义

## Day2

### Java关键字/保留字



## Java基本数据类型

&emsp;&emsp;Java是一种强类型的语言。强类型语言表示在定义的时候必须显式地生命变量的数据类型，弱类型语言表示系统会根据值自己去推断数据类型，不需要指定具体的数据类型是什么。

&emsp;&emsp;Java的基本数据类型

- 整数类型： byte，short，int，long
  - byte： 使用一个字节存储，范围是 $-128$~$127$ 
  - short：使用两个字节存储，范围是 $-32768$~$32767$ 
  - int：使用四个字节存储，范围 $-2^{31}$~$2^{31}-1$ 
  - long：使用四个字节存储，范围是$-2^{63}$~$2^{63}-1$ 
    - 1、在使用整型类型的时候，默认都是int类型，参见程序中的$3100000000$，超过了int类型的范围，因此会报错。
    - 2、如果需要使用long类型的话，必须要在数字的后面添加L（建议使用大写，小写容易和数字1混淆），参见程序中的$3100000000L$，可以正确的转换成long类型。
    - 3、Java会对超出数据范围的数据报错，而不是像C或者C++一样进行截断处理

```java
public class HelloWorld {
    public static void main(String[] args) {
        int i = 128;
        int ii = 3100000000; // 错误
        long l = 3100000000; // 错误
        long ll = 3100000000L;
    }
    public static String getType(Object o){ //获取变量类型方法

        return o.getClass().toString(); //使用int类型的getClass()方法

    }
}

```



- 浮点数类型：float，double
  - float：单精度类型，四个字节，尾数可以精确到7位有效数字
  - double：双精度类型，八个字节
    - 浮点常量默认是double类型，需要使用float类型是，需要在后面增加F/f，如：3.14F
    - 浮点数的舍入误差。如果需要进行不产生舍入误差的精确数字计算，需要使用BigDemical

```java
public class HelloWorld {
    public static void main(String[] args) {
        float f = 12.13213; //错误
        float ff = 12.13213f;
        double d = 12.321321;
        double dd = 12.13213f;
    }
}
```
- 字符类型： char
  - 和C语言类似，每一个字符对应一个具体的数值。char累心那个用来表示在Unicode编码表中的字符。
  - 转义字符
  - char类型在内存中存储的是该字符的Unicode编码值，所以char类型可以当作int类型来处理。
  - Java中的char数组和C语言和C++中的char数组表示含义不同。Java中仅表示一个数组，无法通过添加*'\\0'*来转换成字符串。
- 布尔类型：boolean
  - true：
  - false
  - 逻辑控制

## Java引用数据类型

- 类

## Java常量和变量

- 常量

- 变量

- 声明和赋值

- 变量名，变量类型，作用域

  - 在内类，方法外定义的变量叫成员变量，会存在默认值（静态static）

  - 在方法内定义的变量不会被赋予默认值，必须进行初始化操作。

```java
import static java.lang.Character.getType;
public class HelloWorld {
    int a;
    static int sa;
    public static void main(String[] args) {
        System.out.println(a); //错误
        System.out.println(sa); // 输出0
    }
}

```



- final关键字：
  - 和C++中的const关键字类似，使用final关键字修饰的变量称之为常量或者最终变量，不可以进行修改。

```java
public class HelloWorld {
    public static void main(String[] args) {
        final int i = 10;
        System.out.println(i);
        i = 20; // 错误,final关键字修饰的变量不能被修改
        System.out.println(i); // 错误
    }
}

```



## Java运算符

- +、-、*、/：加减乘除
- %：取余数
- ++、--：自增/自减运算符
- 赋值运算符：=
- 扩展赋值运算符：+=、-=、*=、/=
- 关系运算符：
- 逻辑运算符：&&、||、！分别表示与、或、非
- 位运算符：
  - &：与
  - |：或
  - ^：异或
  - ~：取反
  - \>>、\<<：移位运算符
- 三目运算符



```java
public class HelloWorld {
    public static void main(String[] args) {
        byte b = 10;
        System.out.println(getType(b + 1)); // 输出int类型class java.lang.Integer
        
//        b = b + 1;  // 错误
//        这里涉及到类型转换的问题，b是byte类型的数据，b + 1整体被转换为int类型的数据，
//        重新赋值给b的时候又将int类型转换成了byte类型，编译器判断可能会存在精度损失，因此会报错。

        b += 1;
        System.out.println(b);

    }
    public static String getType(Object o){ //获取变量类型方法

        return o.getClass().toString(); //使用int类型的getClass()方法

    }
}

```

## Java基本数据类型的转换

&emsp;&emsp;在赋值运算和算术运算是，要求数据类型相同，否则需要进行类型转换

- 自动转换
- 强制转换
- 在运算过程中，如果两个值的类型不一致，会自动将小的数据类型转换为大的数据类型。byte < short < int < long
- 在运算过程中，可以手动强制转换，将大的数据类型转换为小的数据类型。

```java
public class HelloWorld {
    public static void main(String[] args) {
        byte b = 10;
        int a = 200;
        byte cc = b + a; // 错误，可能会存在数据溢出
        byte dd = (byte)(a + b);
        System.out.println(cc); // 错误
        System.out.println(dd); //输出-46，表示二进制截断之后的数值结果
    }
}

```

## Java流程控制语句：分支语句

### if

### if……else……

### if……else if……else if……else

### switch语句

- 每个case模块要添加break语句，防止多次匹配。
- 如果多个case中处理的逻辑代码块的功能一致，可以考虑只在最后添加一次处理。
- default分支表示默认选线，当所有的case不匹配的时候执行此选项。
- default不是必须项，可以有也可以没有

```java
import java.util.Scanner;

public class HelloWorld {
    public static void main(String[] args) {
        int random = (int)(Math.random() * 26);
        char ch = (char)('a' + random);

        switch (ch)
        {
            case 'a':
            case 'e':
            case 'i':
            case 'o':
            case 'u':
                System.out.println("元音： " + ch);
                break;
            case 'w':
            case 'y':
                System.out.println("半元音： " + ch);
                break;
            default:
                System.out.println("辅音： "+ ch);
        }


    }
}

```

## Java流程控制语句：循环语句

### while循环

### do……while……循环

### for循环

## Scanner类

1. 首先创建Scanner类实例，传入参数表示数据输入流。输入流包括标准控制台输入，文件输入等。
2. 根据使用情况使用nextLine，nextInt等方法获取数据输入。

```java
import java.util.Scanner;

public class HelloWorld {
    public static void main(String[] args) {
        //创建Scanner输入类，从标准控制台System.in输入数据
        Scanner sc = new Scanner(System.in);

        System.out.println("Please Input a Integer number:");
        int ii = sc.nextInt();
        System.out.println("Your Input Integer is: " + ii);

        System.out.println("Please Input a Double number:");
        double dd = sc.nextDouble();
        System.out.println("Your Input Double Number is: " + dd);

        System.out.println("Please Input a String:");
        String ss = sc.nextLine(); //此处读取前一次输入最后输入的Enter符号
        ss = sc.nextLine();
        System.out.println("Your Input String is: " + ss);

    }
}

```



## Java方法（函数）

- 修饰符
  - public
  - static

## Java递归调用

```java
import java.util.Scanner;

public class HelloWorld {
    public static void main(String[] args) {
       System.out.println(method(5));
    }
    public static int method(int a)
    {
        if (a == 1)
        {
            return 1;
        }
        else
        {
            return method(a - 1) * a;
        }
    }
}
```



## Java面向对象编程

### 对象和类的概念和关系

### Java类的定义

```java
class Person
{
    private int id;
    private int age;
    
    public void setAge(int a)
    {
        
    }
    public int getAge()
    {
        
    }
}
```

- 属性（成员变量）
  - 成员变量可以是Java语言中的任何一种数据类型（包括基本数据类型和引用类型）。
  - 在定义成员变量的时候可以对其初始化，如果不对其初始化，Java使用默认的值对其初始化。
  - 成员变量的作用范围是整个类体。
- 方法（成员函数）

1. 必须使用new关键字创建对象。
2. 使用对象（引用）运算符.来获取或设置对象的成员变量。
3. 必须使用……成员函数（方法）。
4. 同一个类的每个对象都有不同的成员变量存储空间。
5. 同一个类的每一个对象共享该类的方法。



## Java引用

- Java语言中除了基本数据类型之外的变量类型都称之为引用类型。
- Java语言中的队形都是通过引用对其进行操作。

## Java构造函数

- 使用new关键字创建一个新的对象。
- 构造函数是定义在Java类中的一个用来初始化对象的函数。
- **构造函数与类同名且没有返回值。**

- 当没有指定构造函数时，编译器为类自动添加形如下的构造函数。一旦存在自定义的构造函数，则编译器不再为类添加空的构造函数。

```Java
类名()
{
    
}
```

- 创建对象时，使用构造函数初始化对象的成员变量。

## Java函数重载Overload

- 方法的重载是指一个类中可以定义有相同名称，但具有不同的参数的多个方法。调用时，会根据额不同的参数列表选择对应的方法。

1.  **重载方法参数必须不同：**

参数个数不同，如method(int x)与method(int x,int y)不同

参数类型不同，如method(int x)与method(double x)不同

参数顺序不同，如method(int x,double y)与method(double x,int y)不同

2. **重载只与方法名与参数类型相关与返回值无关**

如void method(int x)与int method(int y)不是方法重载，不能同时存在

3. **重载与具体的变量标识符无关**

如method(int x)与method(int y)不是方法重载，不能同时存在。

4. **构造函数也可以被重载。**

```java
import java.util.Scanner;

public class HelloWorld {
    double method(int a, int b)
    {
        System.out.println("method 01");
        return a * b * 1.0;
    }
    double method(double a, double b)
    {
        System.out.println("method 02");
        return a * b;
    }
    double method(int a, double b)
    {
        System.out.println("method 03");
        return a * b;
    }
    double method(double a, int b)
    {
        System.out.println("method 04");
        return a * b;
    }
    public static void main(String[] args)
    {
        HelloWorld t=new HelloWorld();
        System.out.println(t.method(1, 2));
        System.out.println(t.method(1.0, 2.0));
        System.out.println(t.method(1, 2.3));
        System.out.println(t.method(2.4, 3));
    }
}
/*
method 01
2.0
method 02
2.0
method 03
2.3
method 04
7.199999999999999
*/
```

## this关键字

- 在类的方法定义中使用的this关键字代表使用该方法的对象的引用。
- 当必须指出当前使用方法的对象是谁时需要使用this
- 有时使用this可以处理方法中成员变量和参数重名的情况
- this可以看作是一个变量，它的值是当前对象的引用

```java
class ThisRef{
    private int i;
    ThisRef(int i)
    {
        this.i = i;
    }
    void print()
    {
        System.out.println(i);
    }
    ThisRef increase()
    {
        this.i ++;
        return this;
    }
}
public class HelloWorld {
    public static void main(String[] args) {
        ThisRef a = new ThisRef(100);
        a.increase().increase().increase().print();
    }
}
```

## static关键字

- 在类中，用static生命的成员变量为静态成员变量，它为该类的公用变量，在第一次使用时被初始化。对于该类的所有的对象来说，static成员变量只有一份。
- 用static声明的方法为静态方法，在调用该方法时，不会将对象的引用传递给它，所以在static方法中不可以访问非static的成员。
  - 静态方法不再是针对于某个对象调用，所以不能访问非静态成员。
- 可以通过对象引用或者类名（不需要初始化）访问静态成员。

```java
import java.util.Scanner;

class Cat{
    public static int sid=0;
    private String name;
    int id;

    Cat(String name)
    {
        this.name = name;
        this.id = this.sid ++;
    }
    public void info()
    {
        System.out.println("My name is " + this.name + " No. "+this.id);
    }

}
public class HelloWorld {
    public static void main(String[] args) {
        Cat.sid = 100;
        Cat mimi = new Cat("mimi");
        Cat pipi = new Cat("pipi");
        mimi.info();
        pipi.info();
        mimi.sid = 2000;
		mimi.info();
        pipi.info();
    }
}
```

## package和import语句

为了方便于大型软件系统中数目众多的类，解决类的命名冲突问题，Java引入包（package）机制，提供类的多重类命名空间。

- package语句作为Java源文件的第一条语句，指明该文件所在的包。（若缺省该语句，则指定为无名包）
  - 格式是**package pkg1[.pkg2[.pkg3...]]**
- Java编译器把包对应于文件系统的目录管理，package语句中，用**'.'**来指明包（目录）的层次，例如使用语句
  - package com.hx，则该文件中所有的类位于**'.\com\hx'**目录下。
- 如果将一个类打包，则使用该类的时候，必须使用该类的全名（例如:**com.hx.Cat**），Java编译器才会找到该类。
- 也可以使用import语句在文件的开头引入需要使用的类，例如

```java
import com.hx.MyClass;
import java.util.*; //引入java.util包中所有的类

........;
MyClass myClass = new MyClass(); //可以直接使用类名
........;

```

- 可以不需要用import语句直接使用java.lang包中的类。

### package和import语句总结

- 如果想将一个类放入包中，在这个类的源文件第一句话写package语句。
- 必须保证该类的class文件位于正确的目录下。
  - 该类的源码可能会产生影响。
    - 删除或者转移源码到另外的目录
- 另外的类想访问的话
  - 写全名
  - import语句引入
    - \*：星号表示引入当前包中的所有类
    - 具体的类名
  - 访问位于同一个包中的类不需要引入。
- 必须class文件的最上层包的父目录位于classpath下。
- 执行一个类需要写完整包名。

### Java的SDK常见的包

- java.lang
  - 包含一些Java语言的核心类，如String，Math，Integer，System和Thread
  - 使用该Java包中的类时，可以不使用import语句，其他的Java包均需要使用import语句引入。
- java.awt
  - 包含一些构成抽象窗口工具集（abstract window toolkits）的多个类，这些类被用来构建和管理应用程序的图形用户界面（GUI）。
- java.applet
  - 包含applet运行所需要的一些类。
- java.net
  - 包含执行和网络相关的操作的类。
- java.io
  - 包含能提供多种输入输出功能的类
- java.util
  - 包含一些实用工具类，如定义系统特性，使用与日期日历相关的函数。
- Java打包命令

```bash
jar -cvf xx.jar *.*
```

定义一个Java的包，注意构造函数也需要写成public，以便调用该类的方法可以正确进行构造函数的调用。

```java
package com.hx;

public class Cat {
    public static int sid=0;
    private String name;
    int id;

    public Cat(String name)
    {
        this.name = name;
        id = sid ++;
    }

    public void info()
    {
        System.out.println("My name is " + this.name + " No. "+this.id);
    }
}


```

调用上述包的主函数

```java
import java.util.Scanner;
import com.hx.Cat;

public class HelloWorld {
    public static void main(String[] args) {
        Cat.sid = 100;
        Cat mimi = new Cat("mimi");
        Cat pipi = new Cat("pipi");
        mimi.info();
        pipi.info();

    }
}

```



## Java类的继承

Java中使用extends关键字实现类的继承机制。

通过继承，子类自动拥有了基类（superclass）的所有成员（成员变量和成员方法）

**Java只支持单继承，不允许多继承**

（一个子类只能由一个基类，一个基类可以派生出多个子类。）

```java
import java.util.Scanner;
class Person
{
    private String name;
    private int age;

    public void setAge(int age) 
    {
        this.age = age;
    }

    public int getAge()
    {
        return this.age;
    }

    public void setName(String name)
    {
        this.name = name;
    }

    public String getName()
    {
        return this.name;
    }
}

class Student extends Person
{
    private String school;

    public void setSchool(String school) 
    {
        this.school = school;
    }

    public String getSchool()
    {
        return this.school;
    }
}

public class HelloWorld {
    public static void main(String[] args) {
        Student student = new Student();

        student.setName("John");
        student.setAge(20);
        student.setSchool("xinhua school");

        System.out.println(student.getName());
        System.out.println(student.getAge());
        System.out.println(student.getSchool());
    }
}

```



## Java访问控制

Java权限修饰符public，protected，private置于类的成员定义前，用来限定其他对象对该类对象成员的访问权限。

| 修饰符                | 类内部 | 同一个包 | 子类 | 任何地方 |
| --------------------- | ------ | -------- | ---- | -------- |
| private               | yes    |          |      |          |
| default（什么都不写） | yes    | yes      |      |          |
| protected             | yes    | yes      | yes  |          |
| public                | yes    | yes      | yes  | yes      |

对class的权限修饰只可以使用public和default

### public

### private

### protected

## Java方法重写

- **在子类中可以根据需要对从基类中继承来的方法进行重写。**
- **重写方法必须和被重写方法具有相同的方法名称，参数列表和返回类型。**
- **重写方法不能使用比被重写方法更严格的访问权限。**

```java
import java.util.Scanner;
class Person
{
    private String name;
    private int age;
    public void setAge(int age) {
        this.age = age;
    }

    public int getAge()
    {
        return this.age;
    }

    public void setName(String name)
    {
        this.name = name;
    }

    public String getName()
    {
        return this.name;
    }

    public String info()
    {
        return "name: "+this.getName()+" age: "+this.getAge()+"\n";
    }
}

class Student extends Person
{
    private String school;

    public void setSchool(String school) {
        this.school = school;
    }

    public String getSchool()
    {
        return this.school;
    }

    @Override
    public String info() {
        return "name: "+this.getName()+" age: "+this.getAge()+" school: "+ this.getSchool() + "\n";
    }
}

public class HelloWorld {
    public static void main(String[] args) {
        Student student = new Student();

        student.setName("John");
        student.setAge(20);
        student.setSchool("xinhua school");

        System.out.println(student.getName());
        System.out.println(student.getAge());
        System.out.println(student.getSchool());
        System.out.println(student.info());
    }
}
```

## super关键字

super关键字用于从子类调用父类的方法或者使用父类的变量。在Java类中使用super来引用基类的成分。

```java
class Father
{
    public int value;
    public void f()
    {
        value = 100;
        System.out.println("Father's value is " + this.value);
    }
}

class Child extends Father
{
    public int value;
    public void f()
    {
        super.f();
        value = 200;
        System.out.println("Child's value is " + value);
        System.out.println(this.value);
        System.out.println(super.value);
    }
}
public class HelloWorld {
    public static void main(String[] args) {
        Child c = new Child();
        c.f();
    }
}
```

上述代码输出为：

```txt
Father's value is 100
Child's value is 200
200
100
```

super关键字表示的是子类中的一个指针，该指针指向子类的父类（基类），因此通过super关键字可以实现从子类访问父类的效果。

**由此可见，父类和子类的变量是同时存在的，即使是同名。类中看到的是子类的变量，父类中看到的是父类中的变量。它们互相隐藏，而同名的方法则是实实在在的覆盖（重写）**

## Java继承中的构造方法

- 子类的构造的过程中**必须**调用基类的构造方法，
- 子类可以在自己的构造方法中使用以下的方式super(agr_list)调用基类的构造方法。
  - 使用this(arg_list)可以调用本类的另外的构造方法。
  - 如果调用super，必须写在子类构造方法的第一行。
- 如果子类的构造方法中没有显示地调用基类的构造方法，则系统默认调用基类无参数的构造方法。
- 如果子类构造方法中既没有显式调用基类构造方法，而基类中又没有无参数的构造方法，则编译出错。

```java
class Father
{
    public int fid;
    Father(int id)
    {
        this.fid = id;
    }

    Father()
    {
    this.fid = 0;
    }
}

class Child extends Father
{
    int cid;
    Child()
    {
        this.cid = 0;

    }
    Child(int cid)
    {
        this.cid = cid;
    }
    Child(int fid, int cid)
    {
        super(fid);
        this.cid = cid;
    }
    public void print()
    {
        System.out.println("Father: " + super.fid + " Child: " + cid);
    }
}
public class HelloWorld {
    public static void main(String[] args) {
        Child c1 = new Child();c1.print();
        Child c2 = new Child(100);c2.print();
        Child c3 = new Child(100, 200);c3.print();
    }
}
```

输出结果：

```txt
Father: 0 Child: 0
Father: 0 Child: 100
Father: 100 Child: 200
```

## Object类

- Object类是所有的Java类的根基类。
- 如果在类的声明中未使用extends关键字指明其基类，则默认基类为Object类。
- Object类的构造函数
  - Object()
- Object方法汇总

| Modifier and Type                                            | Return             | Method and Description                                       |
| ------------------------------------------------------------ | ------------------ | ------------------------------------------------------------ |
| [clone()](https://docs.oracle.com/javase/7/docs/api/java/lang/Object.html#clone()) | `protected Object` | Creates and returns a copy of this object.                   |
| [toString()](https://docs.oracle.com/javase/7/docs/api/java/lang/Object.html#toString()) | `String`           | Returns a string representation of the object.               |
| [equals(Object obj)](https://docs.oracle.com/javase/7/docs/api/java/lang/Object.html#equals(java.lang.Object)) | `boolean`          | Indicates whether some other object is "equal to" this one.  |
| [finalize()](https://docs.oracle.com/javase/7/docs/api/java/lang/Object.html#finalize()) | `protected void`   | Called by the garbage collector on an object when garbage collection determines that there are no more references to the object. |
| [hashcode()](https://docs.oracle.com/javase/7/docs/api/java/lang/Object.html#hashCode()) | `int`              | Returns a hash code value for the object.                    |
| [getClass()](https://docs.oracle.com/javase/7/docs/api/java/lang/Object.html#getClass()) | `Class<?>`         | Returns the runtime class of this `Object`.                  |
| [notify()](https://docs.oracle.com/javase/7/docs/api/java/lang/Object.html#notify()) | `void`             | Wakes up a single thread that is waiting on this object's monitor. |
| [notifyAll()](https://docs.oracle.com/javase/7/docs/api/java/lang/Object.html#notifyAll()) | `void`             | Wakes up all threads that are waiting on this object's monitor. |
| [wait()](https://docs.oracle.com/javase/7/docs/api/java/lang/Object.html#wait()) | `void`             | Causes the current thread to wait until another thread invokes the [`notify()`](https://docs.oracle.com/javase/7/docs/api/java/lang/Object.html#notify()) method or the [`notifyAll()`](https://docs.oracle.com/javase/7/docs/api/java/lang/Object.html#notifyAll()) method for this object. |
| [wait(long timeout)](https://docs.oracle.com/javase/7/docs/api/java/lang/Object.html#wait(long)) | `void`             | Causes the current thread to wait until either another thread invokes the [`notify()`](https://docs.oracle.com/javase/7/docs/api/java/lang/Object.html#notify()) method or the [`notifyAll()`](https://docs.oracle.com/javase/7/docs/api/java/lang/Object.html#notifyAll()) method for this object, or a specified amount of time has elapsed. |
| [wait(long timeout, int nanos)](https://docs.oracle.com/javase/7/docs/api/java/lang/Object.html#wait(long, int)) | `void`             | Causes the current thread to wait until another thread invokes the [`notify()`](https://docs.oracle.com/javase/7/docs/api/java/lang/Object.html#notify()) method or the [`notifyAll()`](https://docs.oracle.com/javase/7/docs/api/java/lang/Object.html#notifyAll()) method for this object, or some other thread interrupts the current thread, or a certain amount of real time has elapsed. |

### hashcode

### equals方法

[equals](https://blog.csdn.net/changshuchao/article/details/86714875)

- Object类中定义有
  - public boolean equals(Object obj)方法
    - 提供dinginess对象是否“相等”的逻辑。
  - Object的equals方法定义为：x.equals(y) 当x和y是同一个对象的引用时返回true，否则返回false
  - SDK提供的一些类，如String，Date等，重写了Object的equals方法，调用这些类的equals方法，x.equals(y)，当x和y所引用的对象时同一类对象且属性内容相等时（并不一定是相同对象），返回true，否则返回false。
  - 可以根据需要在用户自定义类型中重写equals方法。

```java
import java.util.Objects;

class Cat
{
    int color;
    int height;
    int weight;

    Cat()
    {

    }

    Cat(int color, int height, int weight)
    {
        this.color = color;
        this.height = height;
        this.weight = weight;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Cat cat = (Cat) o;
        return color == cat.color &&
                height == cat.height &&
                weight == cat.weight;
    }
}

public class HelloWorld {
    public static void main(String[] args) {
        Cat c1 = new Cat(1, 2, 3);
        Cat c2 = new Cat(1, 2, 3);
        System.out.println(c1 == c2);
        System.out.println(c1);
        System.out.println(c2);
        System.out.println(c1.equals(c2));
    }
}

```

输出结果：

```txt
false
Cat@b4c966a
Cat@2f4d3709
true
```

## Java对象转型（casting）[还需要研究]

- 一个基类的引用类型变量可以指向其子类的对象
- 一个基类的引用不可以访问其子类对象新增加的成员（属性和方法）
- 可以使用引用变量 instanceof 类名 来判断该引用变量所指向的对象是否属于该类或者该类的子类。
- 子类的对象可以当作基类的对象来使用称作向上转型（upcasting）反之称之为向下转型（downcasting）

## Java动态绑定和多态

动态绑定是指在执行期间（而不是在编译期间）判断所引用对象的实际类型，根据实际的类型调用其相应的方法。

```java
import java.util.Objects;

class Animal
{
    public String name;

    Animal(String name)
    {
        this.name = name;
    }

    public void enjoy()
    {
        System.out.println("yelling......");
    }
}

class Cat extends Animal
{
    public String eyeColor;

    Cat(String name, String color)
    {
        super(name);
        this.eyeColor = color;
    }

    @Override
    public void enjoy() {
        System.out.println("cat yelling......");
    }
}

class Dog extends Animal
{
    public String furColor;

    Dog(String name, String color)
    {
        super(name);
        this.furColor = color;
    }

    @Override
    public void enjoy() {
        System.out.println("dog yelling......");
    }
}


class Lady
{
    public String name;
    public Animal pet;
    Lady(String name, Animal pet)
    {
        this.name = name;
        this.pet = pet;
    }

    public void petEnjoy()
    {
        pet.enjoy();
    }
}
public class HelloWorld {
    public static void main(String[] args) {
        Lady l1 = new Lady("l1", new Cat("cat11", "blue"));
        l1.petEnjoy();
        Lady l2 = new Lady("l2", new Dog("dog11", "yellow"));
        l2.petEnjoy();
    }
}

```

输出结果为：

```txt
cat yelling......
dog yelling......
```

在上面的程序中，Animal是一个父类（基类），Cat类和Dog类都继承自Animal类，是Animal类的两个不同的子类。在Animal类型中定义了一个enjoy函数，同时在继承自Animal的Cat和Dog类中对该函数进行了重写（override），自己定义了不同的enjoy函数。Lady类包含两个属性，其中最重要的一个属性是Animal类型的pet变量。同时，Lady类型中还定义有一个函数用以调用pet中的enjoy函数。

Lady类中原始定义的pet类型是Animal类，但是在真正使用构造函数构造一个Lady类的实例时，传递给pet变量的类型是Cat类和Dog类。当我们构造完成一个Cat实例，并将其传递给它的父类类型Animal的pet变量时，就是父类指向子类类型。此时调用父类的enjoy函数，Java会根据动态绑定确定具体应该调用哪一个enjoy函数。当传递给pet的是一个Cat类型的变量时，Java会根据传递过来的实际的引用的类型来调用相应的函数。在这里程序中，传递给pet变量的是一个Cat类型的数据，则Java会自动地调用Cat类型中的相应的函数，即Cat类型中的enjoy函数。

Java动态绑定需要满足三个条件

- 要有继承
- 要有重写
- 父类引用指向子类对象

## Java抽象类

- 用abstract关键字来修饰一个类时，这个类叫做抽象类，用abstract来修饰一个方法时，该方法叫做抽象方法
- 含有抽象方法的类必须被声明称抽象类，抽象类必须要摆继承，抽象方法必须被重写
- 抽象类不能被实例化
- 抽象方法只需声明，不需要实现
- 抽象类可以继续派生出抽象类。

```java
import java.util.Objects;

abstract class Animal
{
    public String name;

    Animal(String name)
    {
        this.name = name;
    }

    public abstract void enjoy();

}

class Cat extends Animal
{
    public String eyeColor;

    Cat(String name, String color)
    {
        super(name);
        this.eyeColor = color;
    }

    @Override
    public void enjoy() {
        System.out.println("cat yelling......");
    }
}

class Dog extends Animal
{
    public String furColor;

    Dog(String name, String color)
    {
        super(name);
        this.furColor = color;
    }

    @Override
    public void enjoy() {
        System.out.println("dog yelling......");
    }
}


class Lady
{
    public String name;
    public Animal pet;
    Lady(String name, Animal pet)
    {
        this.name = name;
        this.pet = pet;
    }

    public void petEnjoy()
    {
        pet.enjoy();
    }
}
public class HelloWorld {
    public static void main(String[] args) {
        Lady l1 = new Lady("l1", new Cat("cat11", "blue"));
        l1.petEnjoy();
        Lady l2 = new Lady("l2", new Dog("cat11", "yellow"));
        l2.petEnjoy();
    }
}

```

## final关键字

- final修饰的变量的值不能够被改变
  - final的成员变量
  - final的局部变量（形参）
- final修饰的方法不能被重写
- final修饰的类不能够被继承

final关键字修饰的是顶层数据，当final修饰的是一个引用数据时，final修饰的变量所指向的内存地址是无法改变的，但是内存地址里面的数据内容是可以被改变的。

```java
class Cat {
    public String name, color;

    Cat() {
    }

    Cat(String name, String color)
    {
        this.name = name;
        this.color = color;
    }

}


public class HelloWorld {
    public static void main(String[] args)
    {
        final Cat c = new Cat("cat1", "blue");
        print(c);
        c.name = "cat222";
        print(c);

        Cat c1 = new Cat("cat000", "yellow");
        func(c1);
        
    }
    public static void func(final Cat c)
    {
        print(c);
        c.color = "green";
        print(c);
    }
    public static void print(final Cat c)
    {
        System.out.println("name : " + c.name + " color : " + c.color);
    }
}

```

输出结果为：

```txt
name : cat1 color : blue
name : cat222 color : blue
name : cat000 color : yellow
name : cat000 color : green
```

可以看到变量被定义为final之后，所指向的内存区域的数据是可以被改变的，只是变量不可以被重新指定一个新的对象引用。

final修饰一个类时表示该类不可以被继承。例如java.lang.String和java.lang.Math等类。

## Java接口

- 多个无关的类可以实现同一个接口
- 一个类可以实现多个无关的接口
- 与继承关系类似，接口和实现类之间存在多态性。
- 接口（interface）是抽象方法和常量值的定义的集合。
- 从本质上讲，接口是一种特殊的抽象类，这种抽象类中只包含常量和方法的定义，而没有变量和方法的实现
- 接口定义举例

```java
public interface Runner
{
    public static final int id = 1;
    public void start();
    public void run();
    public void stop();
}
```



- 接口特性
  - 接口可以多重实现
  - 接口中声明的属性默认是public static final的，也只能是public static fianl的
  - 接口中只能定义抽象方法，而且这些方法默认是public的，也只能是public的
  - 接口可以继承其他的接口，并添加新的属性和抽象方法。

```java
import static javax.print.attribute.standard.MediaSizeName.C;

interface Singer
{
    public void sing();
    public void sleep();
}

interface Painter
{
    public void draw();
    public void eat();
}

class Teacher implements Singer, Painter
{
    private String name;
    Teacher(String name)
    {
        this.name = name;
    }

    @Override
    public void sing() {
        System.out.println(this.name + " is singing...");
    }

    @Override
    public void sleep() {
        System.out.println(this.name + " is sleeping...");
    }

    @Override
    public void draw() {
        System.out.println(this.name + " is drawing...");
    }

    @Override
    public void eat() {
        System.out.println(this.name + " is eating...");
    }
}
public class HelloWorld {
    public static void main(String[] args) {
       Teacher t = new Teacher("john");
       t.sing();
       t.draw();
       t.eat();
       t.sleep();
    }
}

```

输出结果：

```txt
john is singing...
john is drawing...
john is eating...
john is sleeping...
```

上述代码中使用了一个类实现了多个接口。

实际使用中需要注意避免同时实现含有两个相同方法的接口。

## Java异常（没看完）

### Java异常概念

Java程序运行期出现的错误。

观察错误的名字和行号最重要。

- Java异常时Java提供的用于处理程序中错误的一种机制。
- 所谓错误是指在程序运行的过程中发生的一些异常事件，如除0溢出，数组下标越界，所要读取的文件不存在，
- 设计良好的程序应该在异常发生的时候提供处理这些错误的方法，使得程序不会因为异常的发生而阻断或者产生不可遇见的后果。
- Java程序的执行过程中如果出现异常事件，可以生成一个异常类对象，该异常对象封装了异常事件的信息并将被提交给Java 的运行时系统，这个过程被称为抛出（Throw）异常。
- 当Java运行时系统接收到异常对象时，会寻找能处理这一异常的代码并把当前异常对象交给其处理，这一过程被称之为捕获（catch）异常。

```java
public class HelloWorld {
    public static void main(String[] args) {
       int[] a = {1, 2, 3};
       System.out.println(a[2]);
       
       try
       {
           System.out.println(a[3]);
       }
       catch (ArrayIndexOutOfBoundsException e)
       {
           System.out.println("Array Index Out of Boundary...");
           e.printStackTrace();
       }
    }
}

```

运行结果：

```ttx
3
Array Index Out of Boundary...
java.lang.ArrayIndexOutOfBoundsException: Index 3 out of bounds for length 3
	at HelloWorld.main(HelloWorld.java:11)
```

以下代码为手动抛出异常示例

```java



public class HelloWorld {
    public static void main(String[] args)
    {
        int[] array = {1, 2, 3, 4, 5};
        try
        {
            int r = get(array, 2);
            System.out.println("first try block: " + r);
        }
        catch (ArrayIndexOutOfBoundsException e)
        {
            System.out.println("first try block...");
            e.printStackTrace();
        }

        try
        {
            int r = get(array, 7);
            System.out.println("second try block: " + r);
        }
        catch (ArrayIndexOutOfBoundsException e)
        {
            System.out.println("second try block...");
            e.printStackTrace();
        }
    }
    public static int get(final int[] array, final int index)
    {
        if (index >= array.length || index < 0)
        {
            throw new ArrayIndexOutOfBoundsException("please set index right...");
        }
        return array[index];
    }
}

```

运行结果

```txt
first try block: 3
second try block...
java.lang.ArrayIndexOutOfBoundsException: please set index right...
	at HelloWorld.get(HelloWorld.java:34)
	at HelloWorld.main(HelloWorld.java:21)
```

### Java异常分类

- Throwable
  - Error
    - 。。。
  - Exception
    - RunTimeException
      - 。。。
    - 。。。

- Error：称为错误，由Java虚拟机生成并抛出，包括动态链接失败，虚拟机错误等，程序对其不做处理
- Exception：所有异常类的父类，其子类对应了各种各样可能出现的异常事件，一般需要用户显式地声明或捕获。
- Runtime Exception：一类特殊的异常，如除以0，数组下标越界等，其产生比较频繁，处理麻烦，如果显式的声明或捕获将会对程序的可读性和运行效率影响很大。因此有系统自动检测并将它们交给缺省的异常处理程序（用户可不必对其处理）。
- Exception(in java.lang)
  - ClassNotFoundException
  - IOException
  - InterruptedException
  - Runtime Exception
    - ArithmeticException，NullPointerException
    - IndexOutOfBoundsException
      - ArrayIndexOutOfBoundsException
      - StringIndexOutOfBoundsException



## 数组

### 一维数组

- 一维数组的声明方法：

```java
type var[];
或者
type[] var;
```

- 例如：

```java
int a1[];
int[] a2;
double b[];
Person[] p1;
String s1[];
```

- Java语言中声明数组时不能指定其长度（数组中元素的个数），例如以下是错误的

```java
int a[5]; //非法
```

- Java中使用关键字new创建数组对象，格式是

```java
数组名 = new 数组元素的类型[数组元素的个数]
```

- 元素为基本数据类型的数组

```java
public class HelloWorld {
    public static void main(String[] args)
    {
        int[] a;
        a = new int[5];
        for (int i =0;i<a.length;++i)
        {
            a[i] = i ;
        }
        for (int i=0;i<a.length;++i)
        {
            System.out.println(a[i]);
        }
    }
}
```

- 元素为引用类型的数组

```java
public class HelloWorld {
    public static void main(String[] args)
    {
        Date[] a;
        a = new Date[3];
        a[0] = new Date(1, 2, 3);
        a[1] = new Date(2, 3, 4);
        a[2] = new Date(3, 4, 5); 
    }

}

class Date
{
    int day, month, year;
    Date(int day, int month, int year)
    {
        this.day = day;
        this.month = month;
        this.year = year;
    }
}
```

### 数组初始化

- 动态初始化
  - 数组定义与为数组元素分配空间和赋值的操作分开进行
  - 必须先分配空间，再进行赋值

- 静态初始化
  - 在定义数组的同时九尾数组元素分配空间并赋值

```java
public class HelloWorld {
    public static void main(String[] args)
    {
        int[] a = {1, 2, 3};
        Date[] days = {
                new Date(1, 2, 3),
                new Date(2, 3, 4),
                new Date(3, 4, 5)
        };
    }

}

class Date
{
    int day, month, year;
    Date(int day, int month, int year)
    {
        this.day = day;
        this.month = month;
        this.year = year;
    }
}
```

- 数组元素的默认初始化
  - 数组是引用类型，他的元素相当于类的成员变量，因此数组分配空间后，每个元素也被按照成员变量的规则被隐式初始化

```java
public class HelloWorld {
    public static void main(String[] args)
    {
        int[] a = new int [5];
        Date[] days = new Date[3];
        System.out.println(a[0]);
        System.out.println(days[0]);
    }

}

class Date
{
    int day, month, year;
    Date(int day, int month, int year)
    {
        this.day = day;
        this.month = month;
        this.year = year;
    }
}
```

输出结果

```txt
0
null
```

### 数组元素的引用

- 定义并用运算符new为数组分配空间之后，才可以引用数组中的每个元素，数组元素的引用方式是
  - arrayName[index]
    - index是数组元素下表，可以是整型常量或整形表达式
  - 数组元素下表从0开始，长度为n的数组的合法下表取值范围是0~n-1

- 每一个数组都有一个属性length类指明它的长度
  - a.length 的值为数组a的长度（元素个数）

### 二维数组

二维数组可以看作以数组为元素的数组.

```java
int a[][] = {{1, 2, 3}, {2, 3, 4, 5}, {8}};
```

- Java中多维数组的声明和初始化应按照从高维到低维的顺序（从左到右）进行，

```java
int a[][] = new int[3][];
a[0] = new int[2];
a[1] = new int[3];
a[2] = new int[4];
```

以下数组声明非法

```java
int a[][] = new int[][3];
```

#### 二维数组的初始化

- 静态初始化

```java
int a[][] = {{1, 2}, {3, 4, 5}, {7, 8, 0, 9}};
//以下的初始化方式非法
int a[3][2] = {{1, 2}, {4, 5}, {0, 9}};
```

- 动态初始化

```java
int a[][] = new int[3][5];
int b[][] = new int[3][];
b[0] = new int[2];
b[1] = new int[3];
b[2] = new int[4];
```

### 数组的拷贝

- 使用java.lang.System类的静态方法

```java
public static void arraycopy(Object src, int srcPos, Object dest, int destPos, int length);
```

- 可以用于数组src从第srcPos项元素开始的length个元素拷贝到目标数组dest从destPos项开始的length个位置。
- 如果元数据数目超过了目标数组边界会抛出IndexOutOfBoundsException异常。

```java
public class HelloWorld {
    public static void main(String[] args)
    {
        String[] s= {"MS", "IBM", "Sun", "Oracle", "Apple"};
        String[] sDest = new String[6];
        System.arraycopy(s, 0, sDest, 0, s.length);

        for(int i=0;i<sDest.length;++i)
        {
            System.out.println(sDest[i]);
        }
        
    }

}
```

输出结果为：

```txt
MS
IBM
Sun
Oracle
Apple
null
```

```java
public class HelloWorld {
    public static void main(String[] args)
    {
        int[][] intArray = {{1, 2}, {1, 2, 3}, {1, 2, 3, 4}};
        int[][] intDest = new int[3][];

        System.arraycopy(intArray, 0, intDest, 0, intArray.length);
        intDest[2][1] = 1000;

        for (int i=0;i<intArray.length;++i)
        {
            for (int j=0;j<intArray[i].length;++j)
            {
                System.out.println(i + ", "+j+": "+intArray[i][j]);
            }
        }
    }
}
```

输出结果为：

```txt
0, 0: 1
0, 1: 2
1, 0: 1
1, 1: 2
1, 2: 3
2, 0: 1
2, 1: 1000
2, 2: 3
2, 3: 4
```

在上面的第二个数组拷贝的程序中，由于二维数组中第一个维度是一个引用类型，因此，拷贝之后的拷贝的也是对应的引用，拷贝之后，两个数组的相应位置指向的是同一个对象，因此，在intDest中修改对应的数据之后，修改之后的数据也可以在intArray数组中访问。

## Java常用类

### 字符串相关类（String，StringBuffer）

#### String类

- java.lang.String类代表**不可变**的字符序列。
- **String类不允许通过[]进行下标的索引获取对应的字符。**
- "xxxxx"为该类的一个对象。
- String类的常见的构造方法
  - String(String original)
    - 创建一个String对象，为original的拷贝。
  - String(char[] value)
    - 用一个字符数组创建一个String对象
  - String(char[] value, int offset, int count)
    - 用一个字符数组从offset项开始的count个字符序列创建一个String对象。

```java
public class HelloWorld {
    public static void main(String[] args)
    {
        String s1 = "hello";
        String s2 = "world";
        String s3 = "hello";
        System.out.println(s1 == s3);

        s1 = new String("hello");
        s2 = new String("hello");
        System.out.println(s1 == s2);
        System.out.println(s1.equals(s2));

        char[] c = {'s', 'u', 'n', ' ', 'j', 'a', 'v', 'a'};
        String s4 = new String(c);
        String s5 = new String(c, 4, 4);
        System.out.println(s4);
        System.out.println(s5);
    }
}
```

输出结果：

```txt
true
false
true
sun java
java
```

如果在利用char数组构造String的过程中，出现了下标越出char数组边界的情况，Java会报StringIndexOutOfBoundsException异常。

```java
public class HelloWorld {
    public static void main(String[] args)
    {
        char[] c = {'s', 'u', 'n', ' ', 'j', 'a', 'v', 'a'};
        String s4 = new String(c);
        String s5 = new String(c, 4, 6);
        System.out.println(s4);
        System.out.println(s5);
    }
}
```

结果如下：

```txt
Exception in thread "main" java.lang.StringIndexOutOfBoundsException: offset 4, count 6, length 8
	at java.base/java.lang.String.checkBoundsOffCount(String.java:3741)
	at java.base/java.lang.String.rangeCheck(String.java:298)
	at java.base/java.lang.String.<init>(String.java:294)
	at HelloWorld.main(HelloWorld.java:6)
```

String类常用方法

- public char charAt(int index)
  - 返回字符串中第index个字符
- public int length()
  - 返回字符串的长度
- public int indexOf(String str)
  - fan会字符串中出现str的第一个位置
- public int indexOf(String str, int fromIndex)
  - 返回字符串中从fromIndex开始出现str的第一个位置
- public boolean equals(String another)
  - 判断两个字符串是否相等
- public boolean equalsIgnoreCase(String another)
  - 比较字符串与another是否一样（忽略大小写）
- public String replace(char oldChar, char newChar)
  - 在字符串中用newChar字符（全部）替换oldChar字符。

```java
public class HelloWorld {
    public static void main(String[] args)
    {
        String s1 = "sun java";
        String s2 = "Sun Java";
        System.out.println(s1.charAt(1));
        System.out.println(s2.length());
        System.out.println(s1.indexOf("java"));
        System.out.println(s1.indexOf("Java"));
        System.out.println(s1.equals(s2));
        System.out.println(s1.equalsIgnoreCase(s2));

        String s = "我是程序员，我在学Java";
        String sr = s.replace('我','你');
        System.out.println(sr);

    }
}
```

结果如下：

```txt
u
8
4
-1
false
true
你是程序员，你在学Java

```

- public boolean startsWith(String prefix)
- 判断字符串是否以prefix字符串开头
- public boolean endsWith(String suffix)
  - 判断字符串是否以suffix字符串结尾
- public String toUpperCase()
  - 返回一个字符串，为该字符串的大写形式
- public String toLowerCase()
  - 返回一个字符串，为该字符串的小写形式
- public String substring(int beginIndex) 
  - 返回该字符串从beginIndex开始到结尾的子字符串
- public String substring(int beginIndex, int endIndex)
  - 返回该字符串从beginIndex开始到endIndex结尾的子字符串，注意该endIndex不被包含在内
- public String trim()
  - 返回将该字符串去掉开头个结尾的空格后的字符串

```java
public class HelloWorld {
    public static void main(String[] args)
    {
        String s = "Welcome to Java World!";
        String s1 = "   sum java   ";
        System.out.println(s.startsWith("Welcome"));
        System.out.println(s.endsWith("World"));

        String sL = s.toLowerCase();
        String sU = s.toUpperCase();

        System.out.println(sL);
        System.out.println(sU);

        String subs1 = s.substring(11);
        System.out.println(subs1);

        String subs2 = s.substring(11, 17);
        System.out.println(subs2);

        String sp = s1.trim();
        System.out.println(sp);


    }
}
```

结果如下：

```txt
true
false
welcome to java world!
WELCOME TO JAVA WORLD!
Java World!
Java W
sum java
```

- 静态重载方法
  - public static String valueOf()可以将基本数据类型转换为字符串
    - public static String valueOf(int i)
    - public static String valueOf(double d)
    - ……

```java
public class HelloWorld {
    public static void main(String[] args)
    {
        Date d = new Date(2004, 2, 19);
        System.out.println(String.valueOf(d));
    }
}

class Date extends Object
{
    int day, month, year;
    Date(int day, int month, int year)
    {
        this.day = day;
        this.month = month;
        this.year = year;
    }

    @Override
    public String toString() {
        return "Date{" +
                "day=" + day +
                ", month=" + month +
                ", year=" + year +
                '}';
    }
}
```

结果如下：

```txt
Date{day=2004, month=2, year=19}
```

查看String.valueOf(Object obj)的源码之后可以发现，该方法调用的是对象中的toString方法，如下：

```java
public static String valueOf(Object obj) {
    return (obj == null) ? "null" : obj.toString();
}
```

此处我们已经对该方法进行了重写，因此调用的就是重写之后的toString方法。

- 方法public String[] split(String regex)可以将一个字符串按照指定的分隔符分隔，返回分隔之后的字符串数组。

```java
public class HelloWorld {
    public static void main(String[] args)
    {
        String s = "hello,world,,java,,apple";
        String[] result = s.split(",");
        for(int i=0;i<result.length;++i)
        {
            System.out.println(result[i]);
        }
    }
}


```

输出结果为：

```txt
hello
world

java

apple
```

需要注意的是，**当两个分隔符联结在一起时，split会插入一个空值。**

#### StringBuffer类

- java.lang.StringBuffer代表可变的字符序列
- **StringBuffer类是线程安全的**
- StringBuffer和String类似，但是StringBuffer可以对其字符串进行改变。
- StringBuffer类的常见的构造方法。
  - StringBuffer()
    - 创建一个不包含任何字符序列的空的StringBuffer对象。
  - StringBuffer(String str)
    - 创建一个StringBuffer对象，包含与String对象str相同的字符序列
- StringBuffer常用方法
  - 重载方法public StringBuffer append(...)可以为该StringBuffer对象添加字符序列，返回添加后的该StringBuffer对象引用
    - public StringBuffer append(String str)
    - public StringBuffer append(StringBuffer sbuf)
    - public StringBuffer append(char[] str)
    - public StringBuffer append(char[] str, int offset, int len)
    - public StringBuffer append(double d)
    - public StringBuffer append(Object obj)
    - ……
  - 重载方法public StringBuffer insert(...)可以为该StringBuffer对象在指定位置插入字符序列，返回修改之后的该StringBuffer对象引用
    - public StringBuffer insert(int offset, String str)
    - public StringBuffer(int offset, double d)
    - ……
  - 方法public StringBuffer delete(int start, int end)可以删除从start开始到end-1为止的一段字符序列，返回修改之后的该StringBuffer对象引用。
  - pubilc int indexOf(String str)
  - public int indexOf(String str, int fromIndex)
  - public String substring(int start)
  - public String substring(int start, int end)
  - public int length()
  - 方法public StringBuffer reverse()用于将字符序列逆序，返回修改后的该StringBuffer对象引用。

```java
public class HelloWorld {
    public static void main(String[] args)
    {
        String s = "MS";
        char[] a = {'a', 'b', 'c'};

        StringBuffer sb1 = new StringBuffer(s);
        sb1.append('/').append("IBM")
                .append('/').append("Sun");

        System.out.println(sb1);

        StringBuffer sb2 = new StringBuffer("number");
        for (int i=0;i<9;++i)
        {
            sb2.append(i);
        }
        System.out.println(sb2);

        sb2.delete(8, sb2.length()).insert(0, a);
        System.out.println(sb2);

        System.out.println(sb2.reverse());
    }
}
```

输出结果为：

```txt
MS/IBM/Sun
number012345678
abcnumber01
10rebmuncba
```

### 基础数据类型包装类

- 包装类（如：Integer， Double等），这些类封装了一个相应的基本数据类型数值，并为其提供了一系列操作。
- 以java,lang.Integer类为例，构造方法：
  - Integer(int value)
  - Integer(String s)
- 以java,lang.Integer类为例，常见方法包括
  - public static final int MAX_VALUE
    - 最大的int型数（$2^{31} - 1$)
  - public static final int MIN_VALUE 
    - 最大的int型数（$ - 2^{31}$)
  - public long longValue()
    - 返回封装数据的long类型的数值
  - public double doubleValue()
    - 返回封装数据的double类型的数值
  - public int intValue()
    - 返回封装数据的int类型的数值
  - public static int parseInt(String s) throws NumberFormatException
    - 将字符串解析成int类型的数据，返回该数据
  - public static Integer valueOf(String s) throws NumberFormatException
    - 返回Integer对象，其中封装的整形数据为字符串s所表示

```java
public class HelloWorld {
    public static void main(String[] args)
    {
        Integer i = new Integer(100);
        Double d = new Double("123.456");

        int j = i.intValue() + d.intValue();
        float f = i.floatValue() + d.floatValue();

        System.out.println(j);
        System.out.println(f);

        double pi = Double.parseDouble("3.1415926");
        double r = Double.valueOf("2.0").doubleValue();
        double s = pi * r * r;

        System.out.println(s);

        try
        {
            int k = Integer.parseInt("1.25");
        }
        catch (NumberFormatException e)
        {
            System.out.println("Format error");
        }
        System.out.println(Integer.toBinaryString(123) + 'B');
        System.out.println(Integer.toHexString(123) + 'H');
        System.out.println(Integer.toOctalString(123) + 'O');

    }
}

```

输出结果为：

```txt
223
223.456
12.5663704
Format error
1111011B
7bH
173O
```

### Math类

- java.lang.Math提供了一系列静态方法用于是科学数学计算。
  - abs()
  - acos, asin, atan, sin, cos, tan
  - sqrt
  - pow
  - log
  - exp
  - max
  - min
  - random()
    - 返回0.0~1.0之间的随机数
  - long round(double a)
    - double类型的数据a转换成long类型（四舍五入）
  - toDegrees(double angrad)
    - 弧度->角度
  - toRadian(double angdeg)
    - 角度->弧度

### File类

- java.io.File类代表系统文件名（路径和文件名）
- File类的常见构造方法
  - public File(String pathname)
    - 以pathname为路径创建File对象，如果pathname是相对路径，则默认的当前路径在系统属性user.dir中存储
  - public File(String parent, String child)
    - 以parent为父路径，child为子路径创建File对象
- File的静态属性String separator存储了当前系统的路径分隔符

- 通过File对象可以访问文件的属性
  - public boolean canRead()
  - public boolean canWrite()
  - public boolean exists()
  - public boolean isDirectory()
  - public boolean  isFile()
  - public boolean isHidden()
  - public long lastModified()
    - 上次修改的时间
  - public long length()
    - 获取文件的大小（字节数），如果文件不存在则返回0L，如果是文件夹也返回0L。
  - public String getName()
  - public String getPath()
- 通过File对象创建空文件或目录（在该对象所指的文件或目录不存在的情况下）
  - public boolean createNewFile() throws IOException
  - public boolean delete()
  - public boolean mkdir()
  - public boolean mkdirs()
    - 创建在路径中的一系列目录

```java
import java.io.*;


public class HelloWorld {
    public static void main(String[] args)
    {
        String separator = File.separator;

        String filename = "myfile.txt";

        String directory = "mydir1" + separator + "mydir2";

        File f = new File(directory, filename);
        System.out.println(f.getAbsolutePath());
        System.out.println(f);

        if (f.exists())
        {
            System.out.println("file name: " + f.getAbsolutePath());
            System.out.println("file size: " + f.length());
        }
        else
        {
            f = f.getParentFile();
            System.out.println(f);
            f.mkdirs();

            try
            {
                f.createNewFile();
            }
            catch (IOException e)
            {
                e.printStackTrace();
            }
        }

    }
}
```

递归显示文件夹和子文件

```java
import java.io.*;


public class HelloWorld {
    public static void main(String[] args)
    {
        File f = new File("C:\\Users\\Bryan\\Desktop\\JavaLearning");
        listDir(f, 0);
    }
    private static void listDir(File f, int depth)
    {
        for (int i=0;i<depth;++i)
        {
            System.out.print('\t');
        }
        System.out.println(f);
        if (f.isDirectory()) {
            File[] children = f.listFiles();
            for (int i = 0; i < children.length; ++i) {
                listDir(children[i], depth + 1);
            }
        }
    }
}
```

运行结果如下：

```txt
C:\Users\Bryan\Desktop\JavaLearning
	C:\Users\Bryan\Desktop\JavaLearning\.idea
		C:\Users\Bryan\Desktop\JavaLearning\.idea\misc.xml
		C:\Users\Bryan\Desktop\JavaLearning\.idea\modules.xml
		C:\Users\Bryan\Desktop\JavaLearning\.idea\workspace.xml
	C:\Users\Bryan\Desktop\JavaLearning\JavaLearning.iml
	C:\Users\Bryan\Desktop\JavaLearning\out
		C:\Users\Bryan\Desktop\JavaLearning\out\production
			C:\Users\Bryan\Desktop\JavaLearning\out\production\JavaLearning
				C:\Users\Bryan\Desktop\JavaLearning\out\production\JavaLearning\com
					C:\Users\Bryan\Desktop\JavaLearning\out\production\JavaLearning\com\hx
						C:\Users\Bryan\Desktop\JavaLearning\out\production\JavaLearning\com\hx\Cat.class
				C:\Users\Bryan\Desktop\JavaLearning\out\production\JavaLearning\HelloWorld.class
	C:\Users\Bryan\Desktop\JavaLearning\src
		C:\Users\Bryan\Desktop\JavaLearning\src\com
			C:\Users\Bryan\Desktop\JavaLearning\src\com\hx
				C:\Users\Bryan\Desktop\JavaLearning\src\com\hx\Cat.java
		C:\Users\Bryan\Desktop\JavaLearning\src\HelloWorld.class
		C:\Users\Bryan\Desktop\JavaLearning\src\HelloWorld.java

```

### Enum枚举类型

- 枚举类型
  - 只能够取特定值中的一个
  - 使用enum关键字
  - 是java.lang.Enum类型

```java
public class HelloWorld {
    public enum MyColor{red, blue, green};

    public static void main(String[] args)
    {
        MyColor m = MyColor.red;

        switch (m)
        {
            case red:
            {
                System.out.println("red");
                break;
            }
            case blue:
            {
                System.out.println("blue");
                break;
            }
            case green:
            {
                System.out.println("green");
                break;
            }
            default:
            {
                System.out.println("default");
                break;
            }
        }

    }

}
```

输出结果

```txt
red
```

## Java容器

- JDK所提供的容器API位于java.util包内
- 容器API的类图结 构如下所示

![aaa][java-container]

- Collection接口——定义了存取一组对象的方法，其子接口Set和List分别定义了存储方式
  - Set中的数据对象没有顺序且不可以重复
  - List中的数据对象有顺序且可以重复
- Map接口定义了存储“键（key）——值（value）映射对”的方法
- Collection接口中所定义的方法
  - int size()
  - boolean isEmpty()
  - void clear()
  - boolean contains(Object element)
  - boolean add(Object element)
  - boolean remove(Object element)
  - Iterator iterator()
  - boolean containsAll(Collection c)
  - boolean addAll(Collection c)
  - boolean retainAll(Collection c)
  - boolean removeAll(Collection c)
  - Object[] toArray()

Java容器的简单使用

```java
import java.io.*;
import java.util.*;

public class HelloWorld {

    public static void main(String[] args)
    {
        Collection c = new ArrayList();

        //可以放入不同类型的对象
        c.add("hello");
        c.add(new Name("bob", "allan"));
        c.add(new Integer(100));
        System.out.println(c.size());
        System.out.println(c);
    }
}
class Name
{
    private String lastName;
    private String firstName;

    Name(String fn, String ln)
    {
        this.firstName = fn;
        this.lastName = ln;
    }

    @Override
    public String toString() {
        return "Name{" +
                "lastName='" + lastName + '\'' +
                ", firstName='" + firstName + '\'' +
                '}';
    }
}
```

输出结果为：

```txt
3
[hello, Name{lastName='allan', firstName='bob'}, 100]
```

### Collection方法举例

- 容器类对象在调用remove，contains等方法时需要比较对象是否相等，这会涉及到对象类型的equals方法和hashcode方法。对于自定义的类型，需要重写equals方法和hashcode方法，以实现自定义的对象相等规则。
  - 注意：**相等的对象应该具有相等的hash codes。**
- 举例：增加Name类的equals方法和hashcode方法如下：

```java
class Name
{
    private String lastName;
    private String firstName;

    Name(String fn, String ln)
    {
        this.firstName = fn;
        this.lastName = ln;
    }

    @Override
    public String toString() {
        return "Name{" +
                "lastName='" + lastName + '\'' +
                ", firstName='" + firstName + '\'' +
                '}';
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Name name = (Name) o;
        return Objects.equals(lastName, name.lastName) &&
                Objects.equals(firstName, name.firstName);
    }

    @Override
    public int hashCode() {
        return Objects.hash(lastName, firstName);
    }
}
```

[Java中hashcode的作用](https://blog.csdn.net/fenglibing/article/details/8905007)。主要是用于在容器中增加查找的便捷性，用一个具体的数字表示某一个特定的对象。

### Iterator接口

- 所有实现了Collection接口的容器类都有一个iterator方法，用以返回一个实现了Iterator接口的对象。
- Iterator对象称作迭代器，用以方便地实现对容器内元素的遍历操作。
- Iterator接口定义了如下的一些方法。
  - boolean hasNext()
    - 判断游标右边是否有元素
  - Object next()
    - 返回右边右边的元素并将游标移动到下一个位置。
  - boolean remove()
    - 从基础集合中移除此迭代器返回的最后一个元素（可选操作）。 每次调用next（）只能调用一次此方法。 如果在迭代进行过程中以其他方式（而不是通过调用此方法）修改了基础集合，则未指定迭代器的行为。(删除游标左边的元素，在执行完next之后，该操作只能执行一次。)
- Iterator对象的remove方法时在迭代过程中删除元素的唯一的安全方法。



















[java-container]:data:image/png;base64,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