# 异常

## 概念

在一些早期语言中，对错误的处理是建立在一些约定俗成的基础上，例如某些函数的返回值和标志位等，这些并不属于**语言特性**的一部分。通过这些返回值和标志位，程序员可以确定某些错误发生的位置和详细信息等。这种类型的错误检查，如果程序员对每一个错误都要进行判断的话，那么代码量会成倍增加，并且难以阅读。因此，程序员们通常倾向于不去检查错误。

对于这种现象，有效的解决办法是，强制程序员以一种规范的形式统一检查错误（异常），即将检查错误这种行为写入语言特性。在Java中，类Exception就是这个解决办法。

“Exception”这个词的含义是，某件事并不在一般情况之外或者并不遵循某一个规则。这和程序中出现错误的情况比较符合。“异常”出现了，也许你并不知道应该怎么处理，也或许当前环境下的信息不足以对它进行解决。但是你一定知道，这个异常**必须**要得到重视。也许别人会处理这个异常，也许更上一层环境就能解决这个异常。

使用异常还有一个好处：统一的错误处理会降低代码的复杂度，让代码结构更整洁规范易读。把实现功能的地方和处理错误的地方相分隔，会让你的工作更加井井有条。

## 基本异常

有一点十分重要，就是区分普通问题和异常：普通问题是指，在当前环境下，你可以得到足够的信息，去解决这个问题，不需要将这个问题交给其他环境；异常则是在当前环境得不到足够的信息，只能将这个异常**抛给**其他的环境中去解决。例：

```java
import java.util.*;
class sample{
    public static void main(String[] args){
        int a,b;
		Scanner s = new Scanner(System.in);
		a = s.nextInt();
		if(s.hasNextInt())
            b = s.nextInt();
        else 
            b = (int) s.nextDouble();
		System.out.println(a/b);
    }
}
```

这个小程序读取a和b的输入，并且输出a/b的值。在这个程序中，普通问题指读取的数是不是一个整数，我们可以有足够的信息去解决这个问题，即类型的强制转换。假如我们不知道会有人将b的值输入0，那么这个0的输入就得不到足够的信息去解决，这就是异常。当运行这个程序并将b赋值为0后，将出现下面的结果：

```java
Exception in thread "main" java.lang.ArithmeticException: / by zero
	at sample.main(sample.java:11)
```

java报出了一个Exception，指出除数为0。

异常和其他对象一样，都是一个**类型**，需要进行创建。在抛出异常之后，通过**new**关键字在堆上创建一个异常对象。然后从当前执行流程中跳出，由异常处理机制接管程序，并开始查找一个恰当的位置继续执行，即从**异常处理程序**继续执行。

异常最重要的一个方面是，如果程序出了问题，那么异常处理将**不允许**程序按照既定路线执行下去，并且**强制**程序终止运行并报告错误，或者强制程序处理错误，返回至稳定状态。这么做将会很大程度减少程序出错造成的损失。

### 异常参数

所有的异常标准类都会包含两个构造器：默认构造器和参数为字符串的构造器。

所有异常类的根类是Throwable。通常要根据具体异常类型来抛出相应的异常。错误信息可以用异常类来暗示。

## 捕获异常

在代码中，如果想去**尝试**一些操作，但是这些操作可能导致某些异常被抛出，就可以使用try块包裹这些操作：

```java
try{
    f();
}
```

如果你想“抓住”这些抛出的异常，就应该在try后面紧跟catch：

```java
try{
    f();
}catch(SomeException e){
    e.printStackTrace();
}catch(OtherException oe){
    oe.printStackTrace();
}catch(Exception exception){
    exception.printStackTrace();
}

```

在一段代码中，try和catch的数量可以有许多个。

当try中的异常被抛出后，异常处理程序就会在所有的异常列表中顺序寻找**第一个**恰当的异常，然后进入catch块内进行操作，当catch内语句执行完毕，则处理程序查找结束。

### 终止和恢复

异常处理理论分为两种模型：终止模型和恢复模型。Java支持终止模型。终止模型是指，程序假设每一次错误都是**不可挽回的**，一旦错误被抛出，就不能继续执行下去，也不能恢复到之前的地方。另一种恢复模型，虽然可以通过while使try不断执行，得到想要的结果，但是这会增加程序代码的耦合性，降低复用率，使得程序变得难以开发和维护。

## 自定义异常

我们可以继承Exception甚至是Throwable类来创建属于自己的异常：

```java
class MyException extends Exception{
    public MyException(){
        super();
    }
    public MyException(String s){
        super(s);
    }
}
```

在输出异常信息是，我们往往不会使用System.out，而是使用System.err标准错误流。

通常在异常处理中，使用printStackTrace()方法来输出错误信息。在默认参数下，是输出到标准错误流中，但是可以通过更改参数来输出到其他不同的地方：

```java
printStackTrace();
printStackTrace(PrintStream);
printStackTrace(java.io.PrintWriter);
```

### 日志记录

我们可以使用java.util.logging工具输出记录到日志中：

```java
class LoggingOut{
    private static Logger logger = Logger.getLogger("printLog");
    public static void printLog(Exception e){
        StringWriter trace = new StringWriter();
        e.printStackTrace(new PrintWriter(trace));
        logger.severe(trace.toString());
    }
}
```

## 异常说明

我们可以在方法后面加throws关键字来标记方法可能出现的异常：

```java
void f() throws Exception{}
```

代码必须和异常说明保持一致。如果方法产生异常但是你并没有说明，那么编译器将会强制你：要么处理异常，要么添加说明指出这个方法会产生异常。

你可以声明某个方法将抛出异常，但是实际上并不会真正的抛出。这样你可以在以后的编写中不需要修改已有的代码。

这种收到编译器强制检查的异常叫做**受检查的异常**。

