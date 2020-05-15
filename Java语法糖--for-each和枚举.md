---
title: Java语法糖for-each和枚举
tags:
  - Java
  - 语法糖
  - for-each
  - 枚举
  - notes
abbrlink: 6d197383
date: 2020-04-19 22:56:58
---


###### for-each

- `for-each`从JDK5.0开始引入
- 避免越界错误

```java
for(Object object:Set){
	object	//元素
}
```

`for`与 `for-each`对比

- for可以删除元素，`for-each`不可以删除/替换元素
- `for-each`遍历的时候，是不知道当前元素的具体位置索引
- `for-each`只能正向遍历，不能反向遍历
- `for-each`不能同时遍历2个或以上的集合
- `for`和`for-each`性能接近

###### 枚举

- 枚举变量：变量的取值只在一个有限的集合内
- Java5推出`enum`类型
  - `enum`关键字声明枚举类，且都是`Enum`的子类（不需要加`extends`）
  - `enum`内部有多个值，就有多少个实例对象
  - 不能直接`new`枚举类对象

```java
public enum State {
    SUCCESS, LOADING, NETWORK_ERROR, EMPTY, NONE;
}
```

- Java的enum类型
  - 除了枚举的内容，还可以添加属性/构造函数/方法
  - 处构造函数只能是package-private(default)或者private，内部调用

|  关键字   | 同一个类 | 同一个包 | 不同包的子类 | 不同包的非子类 |
| :-------: | :------: | :------: | :----------: | :------------: |
|  private  |    √     |    -     |      -       |       -        |
|  default  |    √     |    √     |      -       |       -        |
| protected |    √     |    √     |      √       |       -        |
|  public   |    √     |    √     |      √       |       √        |

```java
enum Fruit {
    APPLE(10), ORANGE(8);
    private int price;	//属性

    Fruit(int price) {
        this.price = price;
    }

    public int getPrice() {
        return this.price;
    }
}
```

Java的`enum`类型：所有的`enum`类型属于`Enum`的子类，也继承了相应的方法

- `ordinal()` 返回枚举值所在的索引位置，从0开始
- `compareTo()` 比较两个枚举值的索引位置大小
- `toString()` 返回枚举值的字符串表示
- `Enum.valueOf()` 将字符串初始化为枚举对象
- `values()` 返回所有的枚举值

枚举类型限定取值集合，减少实例对象