---
title: Java--泛型
tags:
  - java
  - 泛型
  - notes
abbrlink: 8b379c6c
date: 2020-05-29 19:38:50
---


###### 泛型引入

集合：存放多个不同类型对象，容易出现转型错误 `ClassCastException`

泛型：Generic Programming JDK1.5推出的特性

编写的代码可以被很多不同类型的对象所重用

`List <String> list = new ArrayList<String>();`

`List <String> list = new ArrayList<>();`Java 7菱形语法

- 泛型类：`ArrayList`，`HashSet`，`HashMap`等
- 泛型方法：`Collections.binarySearch()`，`Arrays.sort()`等
- 泛型接口：`List`，`Iterator`等

泛型的本质：参数化类型，避免类型转换，代码可复用

同类：

- C++的模板(Template)
- C#的泛型

ArrayList源码

`public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable`

```java
public E get(int index) {
    rangeCheck(index);	//检查是否越界

    return elementData(index);	//返回对应的元素值
}
```

<!--more-->

###### 自定义泛型设计

泛型类

- 具有泛型变量的类
- 在类名后用代表引入类型
  - 多个字母表示多个引入类型
    如`<T,U>`等 
  - 引入类型可以修饰成员变量 /局部变量/参数/返回值
  - 没有专门的`template`关键字

```java
public class Interval<T> {
    private T lower;
    private T upper;

    public Interval(T lower, T upper) {
        this.lower = lower;
        this.upper = upper;
    }

    public T getLower() {
        return this.lower;
    }
}
```

泛型类调用

- 传入具体的类

  ```java
  Interval<Integer\> v1 = new Interval<Integer\>(1,2); 
  Interval<Integer\> v1 = new Interval<\>(1,2);
  ```

泛型方法

- 具有泛型参数的方法
- 该方法可在普通类/泛型类中
- `<T>`在修饰符后，返回类型前

```java
public class ArrayUtils {
    public static <T> T getMiddle(T... a) {
        return a == null ? null : a[a.length / 2];
    }
}
```

泛型接口

- 和泛型类相似，在类名后加`<T>`
- T用来指定方法返回值和参数
- 实现接口时，指定类型
- T也可以再是一个泛型类，可嵌套

```java
public interface Calculator<T> {
    public T add(T t1, T t2);
}
```

总结

- 泛型类：整个类都被泛化，包括变量和方法
- 泛型方法：方法被泛化，包括返回值和参数
- 泛型接口：泛化子类方法

###### 泛型类型限定

泛型

- 编写的代码可以被很多不同类型的对象所重用
- 特定场合下，需要对类型进行限定(使用某些特定方法)

泛型限定

- `<T extends Comparable>`约定T必须是Comparable的子类
- extends固定，后面可以多个，以&拼接，如`<T extends Comparable & Serializable>`
- extends限定可以有多个接口，但只能一个类，且类必须排第一位 –逗号隔参数，如`<T extends File &Cloneable, U extends Serializable>`

泛型类之间的继承

- `Pair<S>`和`Pair<T>`没有任何关系，无论S和T之间是什么关系
- 泛型类可以扩展或实现其他的类，如`ArrayList<T> implemetsList<T>`

------

上限界定符

Pair<? extends S>

Pair能接收的参数类型，是S自身或子类

eg `Pair<? extends Fruit>`代表`Pair<Fruit>`, `Pair<Apple>`, `Pair<ornage>` 等

> Apple extends Fruit
> Orange extends Fruit

<font color="red">只能get/不能set，编译器只能保证出来的类型，但不保证放入的 对象是什么类型</font>

`? extends Fruit getFirst();`	//肯定可以转型到Fruit

`void setFirst(? extend Fruit)` //未知具体的类型[错误]

------

下限界定符

Pair<？super S>

Pair能接收的类型参数，是S的自身或超类

eg `Pair<? super Apple>` 代表 `Pair<Object>`,`Pair<Fruit>`,`Pair<Apple>`等

<font color="red">只能set/不能get，编译器保证放入的是S本身或超类，但不保证 出来是什么具体类型</font>

void setFirst(? super Apple)	//可以放入Apple及子类对象

? super Apple getFirst()			//无法得知出来的对象类型，只能是Object

---

泛型PECS原则

- Producer Extends, Consumer Super
- 要从泛型类读取类型T的数据，并且不能写入，可以使用 ? extends 通配符；(Producer Extends，泛型类是生产者，往外输出 东西)
- 如果要向泛型类写入类型T的数据，并且不需要读取，可以使用 ? super 通配符；(Consumer Super，泛型类是消费者，往内增加东西)
- 如果既想写入又想读出，那就不用通配符

---

无限定类型的泛型

- `Pair<T>`，原始类型
- `Pair<?>` ，无限定通配符，表示任意类型
  - 如`Pair<Obejct>`, `Pair<Apple>`, `Pair<Orange>`
  - ? getFirst() //不确定出来是什么类型，只能赋值给Object
  - void setFirst() //无法放入任何对象，甚至是Object

###### 泛型实现的本质和约束

JDK的版本是向后兼容的:即低版本的class文件可以在高版本的JDK上运行 
因此，JVM里面没有泛型对象，而是采用<font color="red">类型擦除</font>技术， 只有普通的类和方法

类型擦除

- 擦除泛型变量，替换为原始类型(raw type)，无限定为Object

- 擦除泛型变量，替换为原始类型(raw type)，有限定则为第 一个类型

- 擦除泛型变量后，为了保证类型的安全性，需要自动进行类型转换

  泛型表达式翻译

  `Fruit f=fruits.getFirst();`
  `Object o=fruits.getFirst();` `Fruit f=(Fruit) o;`

- 擦除泛型变量后，为了保证类型的安全性，需要自动进行 类型转换

- 重载泛型方法翻译(自动桥方法)

泛型的约束

- 不能用基本类型(8种)来实例化泛型
- 运行时类型查询只适用于原始类型
- 不能创建参数化类型的数组
- 可变参数警告
- 不能实例化类型变量
- 不能构造泛型数组
- 泛型类的静态上下文中类型变量无效
- 不能抛出或捕获泛型类的异常实例
- 可以消除对受查异常(checked exception)的检查
- 类型擦除后引发的方法冲突

###### Java类型协变和逆变

面向对象语言，支持子类型(Subtyping) 

- 类型变化关系(type variance)

  更复杂类型中的子类型关系，与子类型之间的关系相关联。

Type Variance形式化定义: 

- A、B是类型，f(·)表示类型转换，≤表示继承关系，如A ≤ B， 表示A继承于B
- f(·)是<font color="red">协变(covariant)</font>的，如果A ≤ B，有f(A) ≤f(B)
- f(·)是<font color="red">逆变(contravariant)</font>的，如果A ≤ B，有f(B) ≤f(A)
- f(·)是<font color="red">不变(invariant)</font>的，当上述两种都不成立，即f(A)和f(B)没有关系 
- f(·)是<font color="red">双变(bivariant)</font>的，如果A ≤ B，有f(B) ≤f(A) 和f(A) ≤f(B) 同时成立

---

Java数组是协变的

String是Object的子类，`String[]`是`Object[]`的子类

---

Java的(原始的)泛型是不变的

String是Object的子类，`List<String>`和`List<Object>`没有关系

---

泛型可采用通配符，支持协变和逆变(PECS原则)

假设 B是A的子类

`ArrayList<? extends A> list3 = new ArrayList<B>();`  协变
`ArrayList<? super B> list4 = new ArrayList<A>();`  逆变

---

复合情况：数组协变，泛型不变

假设 B是A的子类，C是B的子类

```java
public static void testArrayAndList(){
	B[] r1=test(new ArrayList<B>();	// 编译错误
	A[] r2=test(new ArrayList<B>();	// 编译错误
	Object[] r3=test(new ArrayList<Object>());	// 编译错误
	
	A[] r4=test(new ArrayList<A>());
	Object[] r5=test(new ArrayList<A>());	
}
public static A[] test(ArratList<A> list){
	return new A[1];
}
```

---

方法情况

- JDK 1.4 重写的方法参数和返回值要求一样
- JDK 1.5 +重写的方法，参数要求一样的，返回值是协变的

```java
class Father{
	public B f1(B obj){
		System.out.println("Father.f1()");
		return new B();
	}
}
class Son extends Father{
	public B f1(B obj){
	//public C f1(B obj){}	返回值是C，也是对的
		System.out.println("Son.f1()");
		return new C();
	}
}
// 调用
Father foo=new Son();
foo.f1(new B());
```

---

总结Java类型变化(type variance)

- 数组是协变的(covariant)
- 原始的泛型是不变的(invariant)
- `<? extends A>`支持协变(covariant)
- `<? super B>`支持逆变(contravariant)

