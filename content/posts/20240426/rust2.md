+++
title = '所有权规则，内存与分配'
date = 2024-04-26T23:52:39+08:00
draft = false
+++

# String类型
- String比其他标量数据类型都复杂。
- 字符串字面值：程序里手写的字符串的值，他们是不可变的。
- string在heap上分配，能够储存编译时未知数量的文本

# 创建string类型的值
- 可以通过from函数从字符串字面值创建string类型
example：
    let s = String::from("hello);
    "::"表示from是String类型下的函数
- 这类字符串可以修改

```rust
fn main() {
    let mut s = String::from("hello");

    s.push_str(", world");

    println!("{}",s)
}
```
运行结果：hello，world


- 为什么String类型的值可以修改，而字符串字面量不可以修改？

# 内存和分配

- 字符串字面值，在编译是就已知它的内容，其文本内容直接被硬编码到可执行文件里

![](/image/String1.png)

- rust采用了不同的方式，对于某个值来说，当拥有它的变量走出作用域范围时，内存会立即自动交还给操作系统，自动调用drop函数


# 变量与数据交互的方式：移动（Move）

- 多个变量可以与同一个数据使用一种独特的方式交互

```rust
let x = 5;
let y = x;
```

- 由于整数是已知的固定大小的简单的值，这两个5被压到了Stack中

## String版本

```rust
let s1 = String::from("hello");
let s2 = s1;
```

![string的组成](/image/string2.png)

## string的交互方式

![危险](/image/string3.png)

![rust的处理方法](/image/string4.png)

- 如果创s2之后再使用s1会怎样呢

```rust
let s1 = String::from("hello");
let s2 = s1;

println!("{}",s1);
```

运行结果：
![运行结果](/image/string5.png)


# 交互方式：克隆
```rust
let s1 = String::from("hello");
let s2 = s1.clone();

println!("{}",s1);
```

clone属于深拷贝

## stack上的数据：复制

```rust
fn main() {
    let x = 5;
    let y = x;

    println!("{},{}",x,y);
}
```

- copy trait,可以用于像整数一样完全存放在stack上的类型
- 如果一个类型实现了copy这个trait，那么旧的变量在赋值后依然可用
- 如果一个类型或者该类型的一部分实现了drop trait，那么rust不再允许它实现Copy trait了

# 一些拥有copy trait的类型

- 任何简单的标量组合类型都可以是copy的
- 任何需要分配内存或某种资源的都不是Copy的

一些拥有Copt trait的类型：  
    所有的整数类型，例如u32  
    bool  
    char  
    所有的浮点类型，例如f64  
    Tuple（元组），如果其所有字段都是Copy的  
