# 04 Java 只有值传递

> 本文为个人学习摘要笔记。  
> 原文地址：[为什么说 Java 中只有值传递](https://github.com/hollischuang/toBeTopJavaer/blob/master/basics/java-basic/java-pass-by.md)

Java 中的参数传递是值传递还是引用传递的错误理解：

* 错误理解一：值传递和引用传递，区分的条件是传递的内容，如果是个值，就是值传递。如果是个引用，就是引用传递。
* 错误理解二：Java 是引用传递。
* 错误理解三：传递的参数如果是普通类型，那就是值传递，如果是对象，那就是引用传递。

## 实参与形参

参数在程序语言中分为形式参数和实际参数。

* 形式参数：是在定义函数名和函数体的时候使用的参数，目的是用来接收调用该函数时传入的参数。
* 实际参数：在调用有参函数时，主调函数和被调函数之间有数据传递关系。在主调函数中调用一个函数时，函数名后面括号中的参数称为“实际参数”。

举个栗子：

```java
public static void main(String[] args) {
  ParamTest pt = new ParamTest();
  pt.sout("Hollis"); // 实际参数为 Hollis
}

public void sout(String name) { // 形式参数为 name
  System.out.println(name);
}
```

**实际参数是调用有参方法的时候真正传递的内容，而形式参数是用于接收实参内容的参数**。

## 值传递与引用传递

当我们调用一个有参函数的时候，会把实际参数传递给形式参数。在程序语言中，这个传递过程中有两种情况，即值传递和引用传递。

* 值传递（pass by value）是指在调用函数时将实际参数复制一份传递到函数中，这样在函数中如果对参数进行修改，将不会影响到实际参数。
* 引用传递（pass by reference）是指在调用函数时将实际参数的地址直接传递到函数中，那么在函数中对参数所进行的修改，将影响到实际参数。

这里通过三个栗子来说明 java 中是按值传递还是引用传递的。

### 栗子一

```java
public static void main(String[] args) {
  ParamTest pt = new ParamTest();

  int i = 10;
  pt.pass(i);
  System.out.println("print in main , i is " + i);
}

public void pass(int j) {
  j = 20;
  System.out.println("print in pass , j is " + j);
}
```

上面的代码中，我们在 pass 方法中修改了参数 j 的值，然后分别在 pass 方法和 main 方法中打印参数的值。输出结果如下：

```text
print in pass , j is 20
print in main , i is 10
```

可见，pass 方法内部对 name 的值的修改并没有改变实际参数 i 的值。那么，按照上面的定义，有人得到结论：Java 的方法传递是值传递。

### 栗子二

```java
public static void main(String[] args) {
  ParamTest pt = new ParamTest();

  User hollis = new User();
  hollis.setName("Hollis");
  hollis.setGender("Male");
  pt.pass(hollis);
  System.out.println("print in main , user is " + hollis);
}

public void pass(User user) {
  user.setName("hollischuang");
  System.out.println("print in pass , user is " + user);
}
```

同样是一个 pass 方法，同样是在 pass 方法内修改参数的值。输出结果如下：

```text
print in pass , user is User{name='hollischuang', gender='Male'}
print in main , user is User{name='hollischuang', gender='Male'}
```

经过 pass 方法执行后，实参的值竟然被改变了，那按照上面的引用传递的定义，实际参数的值被改变了，这不就是引用传递了么。于是，根据上面的两段代码，有人得出一个新的结论：Java 的方法中，在传递普通类型的时候是值传递，在传递对象类型的时候是引用传递（错误理解三）。

### 栗子三

```java
public static void main(String[] args) {
  ParamTest pt = new ParamTest();

  String name = "Hollis";
  pt.pass(name);
  System.out.println("print in main , name is " + name);
}

public void pass(String name) {
  name = "hollischuang";
  System.out.println("print in pass , name is " + name);
}
```

上面的代码输出结果为：

```text
print in pass , name is hollischuang
print in main , name is Hollis
```

这又作何解释呢？同样传递了一个对象，但是原始参数的值并没有被修改，难道传递对象又变成值传递了？

## Java 中的值传递

上面的三个栗子，表现的结果不一样，导致大家对 Java 中传递类型产生困惑。

其实，上面的概念没有错，只是代码的例子有问题。这里再复习下值传递和引用传递的概念，然后再举几个真正恰当的例子。

* 值传递（pass by value）是指在调用函数时将实际参数复制一份传递到函数中，这样在函数中如果对参数进行修改，将不会影响到实际参数。
* 引用传递（pass by reference）是指在调用函数时将实际参数的地址直接传递到函数中，那么在函数中对参数所进行的修改，将影响到实际参数。

总结值传递和引用传递之前的区别的重点：

|  | 值传递 | 引用传递 |
| :--- | :--- | :--- |
| 根本区别 | 会创建副本 | 不创建副本 |
| 所以 | 函数中无法改变原始对象 | 函数中可以改变原始对象 |

上面几个错误的例子中，都只关注了实际参数内容是否有改变。如传递的是 User 对象，我们试着改变他的 name 属性的值，然后检查是否有改变。其实，在实验方法上就错了，当然得到的结论也就有问题了。

为什么说实验方法错了呢？这里我们来举一个形象的栗子：

* 场景一：你有一把钥匙，当你的朋友想要去你家的时候，如果你**直接**把你的钥匙给他了，这就是引用传递。这种情况下，如果他对这把钥匙做了什么事情，比如他在钥匙上刻下了自己名字，那么这把钥匙还给你的时候，你自己的钥匙上也会多出他刻的名字。
* 场景二：你有一把钥匙，当你的朋友想要去你家的时候，你**复刻**了一把新钥匙给他，自己的还在自己手里，这就是值传递。这种情况下，他对这把钥匙做什么都不会影响你手里的这把钥匙。

但是，不管上面那种情况，你的朋友拿着你给他的钥匙，进到你的家里，把你家的电视砸了。那你说你会不会受到影响？而我们在上面的例子中，改变 user 对象的 name 属性的值的时候，不就是在“砸电视”么。

```java
public static void main(String[] args) {
  ParamTest pt = new ParamTest();

  User hollis = new User();
  hollis.setName("Hollis");
  hollis.setGender("Male");
  pt.pass(hollis);
  System.out.println("print in main , user is " + hollis);
}

public void pass(User user) {
  user = new User();
  user.setName("hollischuang");
  user.setGender("Male");
  System.out.println("print in pass , user is " + user);
}
```

上面的代码中，我们在 pass 方法中，改变了 user 对象，输出结果如下：

```text
print in pass , user is User{name='hollischuang', gender='Male'}
print in main , user is User{name='Hollis', gender='Male'}
```

图解上面的过程：

![Java &#x503C;&#x4F20;&#x9012;](https://raw.githubusercontent.com/chanshiyucx/yoi/master/2019/Java-只有值传递/Java-值传递.png)

当我们在 main 中创建一个 User 对象的时候，在堆中开辟一块内存，其中保存了 name 和 gender 等数据。然后 hollis 持有该内存的地址 `0x123456`（图 1）。当尝试调用 pass 方法，hollis 作为实际参数传递给形式参数 user 的时候，会把这个地址 `0x123456` 交给 user，这时，user 也指向了这个地址（图 2）。然后在 pass 方法内对参数进行修改的时候，即 `user = new User();`，会重新开辟一块 `0X456789` 的内存，赋值给 user。后面对 user 的任何修改都不会改变内存 `0X123456` 的内容。

通过概念我们也能知道，这里是把实际参数的引用的地址复制了一份，传递给了形式参数。所以，**上面的参数其实是值传递，把实参对象引用的地址当做值传递给了形式参数**。

这时我们再回顾上面的栗子二，在参数传递的过程中，实际参数的地址被拷贝给了形参，只是，在这个方法中，并没有对形参本身进行修改，而是修改的形参持有的地址中存储的内容。

所以，值传递和引用传递的区别并不是传递的内容。而是实参到底有没有被复制一份给形参。在判断实参内容有没有受影响的时候，要看传的的是什么，如果你传递的是个地址，那么就看这个地址的变化会不会有影响，而不是看地址指向的对象的变化。就像钥匙和房子的关系。

那么，既然这样，为啥上面同样是传递对象，传递的 String 对象和 User 对象的表现结果不一样呢？我们在 pass 方法中使用 `name = "hollischuang";` 试着去更改 name 的值，阴差阳错的直接改变了 name 的引用的地址。因为这段代码，会 new 一个 String，在把引用交给 name，即等价于 `name = new String("hollischuang");`。而原来的那个"Hollis"字符串还是由实参持有着的，所以，并没有修改到实际参数的值。

**所以说，Java 中其实还是值传递的，只不过对于对象参数，值的内容是对象的引用**。

## 总结

无论是值传递还是引用传递，其实都是一种求值策略（Evaluation strategy）。在求值策略中，还有一种叫做按共享传递（call by sharing）。其实 Java 中的参数传递严格意义上说应该是按共享传递。

> 按共享传递，是指在调用函数时，传递给函数的是实参的地址的拷贝（如果实参在栈中，则直接拷贝该值）。在函数内部对参数进行操作时，需要先拷贝的地址寻找到具体的值，再进行操作。如果该值在栈中，那么因为是直接拷贝的值，所以函数内部对参数进行操作不会对外部变量产生影响。如果原来拷贝的是原值在堆中的地址，那么需要先根据该地址找到堆中对应的位置，再进行操作。因为传递的是地址的拷贝所以函数内对值的操作对外部变量是可见的。

简单点说，Java 中的传递，是值传递，而这个值，实际上是对象的引用。而按共享传递其实只是按值传递的一个特例罢了。所以我们可以说 Java 的传递是按共享传递，或者说 Java 中的传递是值传递。
