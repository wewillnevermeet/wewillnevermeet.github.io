+++
title = 'Reference'
date = 2024-05-08T22:15:01+08:00
draft = false
+++

# 引用和借用

```rust
fn main(){
    let s1 = String::from("hello");
    let len = calculate_length(&s1);

    println!("The length of '{}' is {}",s1,len);
}

fn calculate_length(s: &String) -> usize {
    s.len
}
```

在这个例子中，参数类型是&String 而不是 String  
&表示引用，引用只获得某些值而不获得所有权  
![图解引用](/image/reference.png)  
  
我们把引用作为函数参数这个行为叫做借用  
那我们可以修改借用的东西吗  

```rust
fn main(){
    let s1 = String::from("hello");
    let len = calculate_length(&s1);

    println!("The length of '{}' is {}",s1,len);
}

fn calculate_length(s: &String) -> usize {
    s.push_str(",world")
    s.len
}
```

运行会报错，如下：
![报错](/image/reference1.png)
所以不可以修改引借用的东西  
和变量一样，引用默认不可变  

## 可变引用
```rust
fn main(){
    let mut s1 = String::from("hello");
    let len = calculate_length(&mut s1);

    println!("The length of '{}' is {}",s1,len);
}

fn calculate_length(s: &mut String) -> usize {
    s.push_str(",world")
    s.len
}
```

引用所引用的内容也要是可变的  
同时一个变量只能有一个可变引用  

- error[E0499]:cannot borrow 's' as mutable more than once at a time

这样做可以防止编译时数据竞争。  
以下三种行为会发生数据竞争：

- 两个或多个指针同时访问同一个数据
- 至少有一个指针用于写入数据
- 没有使用任何机制同步对数据的访问

可以通过创建新的作用域，来允许非同时的创建多个可变引用  
```rust
fn main() {
    let mut s = String::from("hello");
    {
        let s1 = &mut s;
    }
    let s2 = &mut s;
}
```

但是还有另一个限制：

- 不可以同时拥有一个不可变引用和可变引用
- 允许拥有多个不可变引用

```rust
fn main() {
    let mut s = String::from("hello");
    let r1 = &s;
    let r2 = &s;
    let s1 = &mut s;

    println!("{},{},{}",r1,r2,s1);
}
```

## 悬空引用Dangling references

悬空指针（Dangling Pointer）：一个指针引用了内存中的某个地址，而这块内存可能已经被释放并分配给其他人使用了。  
  
但是在rust中，编译器可以永远保证都不是悬空引用  

如果你使用了某些数据，编译器将保证在离开作用域之前数据不会离开作用域

```rust
fn main() {
    let r = dangle();
}

fn dangle() -> &String {
    let s = String::from("hello");
    &s
}
```