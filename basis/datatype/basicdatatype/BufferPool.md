# 缓冲池

## 什么是缓冲池?

Java虚拟机为了优化8种基础数据类型的包装对象，为它们提供了缓存池，缓存池的大小为一个字节。（8个bit位）长度2^8位，范围是-128~127。超过这个范围，包装类就在自己缓冲池外面赋值。***缓冲池里面的值是共享的***。

### 代码解释
先看一下我们的测试代码
```
public static void main(String[] args) {
    Integer a = new Integer(127);
    Integer b = new Integer(127);

    Integer c = Integer.valueOf(127);
    Integer d = Integer.valueOf(127);


    System.out.println("a==b : " + (a==b) );

    System.out.println("a == c : " + (a == c) );

    System.out.println("b == c : " + (b == c) );

    System.out.println("c == d : " + (c == d) );

    Integer e = Integer.valueOf(128);
    Integer f = Integer.valueOf(128);

    System.out.println("e == f : " + (e == f) );

    int g = 127;
    System.out.println("c == g : " + (c == g) );
    System.out.println("a == g : " + (a == g) );
    System.out.println("b == g : " + (b == g) );

    int h = 128;
    System.out.println("e == h : " + (e == h) );
    System.out.println("f == h : " + (f == h) );

    Integer f1 = Integer.valueOf(128);
    int h1 = 128;
    System.out.println("f1 == h1 : " + (f1 == h1) );

}
```
输出结果:
```
a==b : false
a == c : false
b == c : false
c == d : true
e == f : false
c == g : true
a == g : true
b == g : true
e == h : true
f == h : true
f1 == h1 : true
```

可以看到:
https://blog.csdn.net/stdio_54456153/article/details/98783049

https://blog.csdn.net/QYHuiiQ/article/details/80148835

## 哪些包具有缓冲池类型?
+ Byte
+ Short
+ Character
+ Integer
+ Long