+++
title = 'First try of RUST'
date = 2024-04-12T18:03:35+08:00
draft = false
+++

# First try of rust
It's security is really excellent!!!!!
```rust
use std::io;//prelude
use rand::Rng;//trait
use std::cmp::Ordering;
fn main() {
    let secret_number = rand::thread_rng().gen_range(1, 101);//i32,u32,i64
    loop{
        println!("guess a number");


        //let foo = 1;
        //let bar = foo;
        //foo = 2;  It's immutable in common sense
        let mut guess = String::new();//UTF-8 //:: means new is a union function
        //key word 'mut' means that the 'guess' is mutable
        io::stdin().read_line(&mut guess).expect("can't readline");
        //The type of the return value of function "stdin" is Stdin
        //The type of the return value of method "read_line" is io::Result<usize>(enums)
        //io::Result OK,Err

        //shadow
        let guess: u32 = match guess.trim().parse(){
            Ok(num) => num,
            Err(_) => continue
        };//Usually we use match to deal with problem
        //u32:unsigned
        match guess.cmp(&secret_number){//seems to be switch
            Ordering::Less => println!("It is too small!"),//3 arms
            Ordering::Equal => {
                println!("Wiiiiiiiiiiiiiiiiiiiiiiin!");
                break;// scape from loop
            },
            Ordering::Greater => println!("It is too big"),//larger than
        }

        println!("your answer is {}", guess);
        //{} is a placeholder that is replaced with a variable value when out put.
    }
}

```