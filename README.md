# rust_memo
这个是我学习rust的笔记。

# derive(Copy, Clone, Debug) and drop trait
在变量赋值的时候，比如：
```rust
    let m = 1_i32;
    let mut n = m;
    println!("{}", n);
    n = 2;
    let mut p = m;
    println!("{}", p);
    p = 3;
    println!("{}, {}, {}", m, n, p);
```
 1. 所有内建的简单类型都是在做COPY语义；
 
 2. 如struct（即使内部都是简单类型），和String，他们都是MOVE语义，即内存的所有权转移。这样才会有下面的编译错误：
```rust
    let x = String::from("111");
    let y = x;
    let z = x;
use of moved value: `x`

value used here after move
```

 3. 如果想让struct也做COPY语义，就要加上Copy和Clone的装饰器。如：
```rust
#[derive(Copy, Clone, Debug)]
struct X (i32);
```
注意，如果是复杂一点的struct的话，内部所有类型，都必须是拥有“Copy 和 Clone”的能力。

 4. 有关trait drop（析构函数）。这个trait是和Copy冲突的。
drop的设计初衷是为了使用者可以自定义“析构函数”，释放些资源，如文件句柄，或者发送消息等。
但是Copy以后，释放资源的时候就相当于double free了。
如果非要抬杠，加上flag+mutex来避免double free，那这样不就又违背了rust的设计初衷或者说原则嘛。
[stackoverflow上的一些解释](https://stackoverflow.com/questions/51704063/why-does-rust-not-allow-the-copy-and-drop-traits-on-one-type)
