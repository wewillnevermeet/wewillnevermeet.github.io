+++
title = 'Rust3'
date = 2024-05-01T03:10:07+08:00
draft = false
+++

# 所有权与函数

- 在语义上，将值传递给函数和把值赋值给变量是类似的，要么发生移动，要么发生复制

```rust
let s = String::from("hello,world");

take_ownership(s);

let x = 5;

makes_copy(x);

println!("X:{}",x);

fn take_ownership(some_string:String){
    println!("{}",some_string);
}

fn make_copy(some_number: i32){
    println!("{}",some_number);
}
```

s的值被移动到函数内，作用域移动到函数，而x由于实现了copy_trait，x仍然有效

# 返回值与作用域

- 函数在返回值的过程中也会发生同样的所有权转移

```rust
fn main() {
    let s1 = gives_ownership();

    let s2 = String::from("hello");

    let s3 = takes_and_gives_back(s2);
}

fn gives_ownership() -> String {
    let some_string = String::from("hello");
    somestring
}

fn takes_and_gives_back(a_string: String) -> String {
    a_string
}
```

细细品味所有权的移动！

![所有权移动规则](/image/string6.jpg)

# 如何让函数使用某个值但不获得其所有权？

```rust
fn main() {
    let s1 = String::from("hello");

    let (s2, len) = calculate_length(s1);

    println!("The length of '{}' is {}",s2,len);
}

fn calculate_length(s: String) -> (String, usize){
    let length = s.len();

    (s, length)
}
```

这样的方法太笨了，像C++一样，为了方便，rust也引入了“引用（reference）”

（待更新）