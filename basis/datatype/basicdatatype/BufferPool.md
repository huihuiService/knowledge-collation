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

可以看到: Integer 在范围内 -128~127内是有缓存池的。当然如果是 new Integer(127) 这样不会走缓存池，反而会新建对象，所以指针地址并不相同。Integer.valueOf(int) 会在缓存池里取，多次调用会指向一个对象（此处的对象指int 127这个对象）。

# 扩展
### int 和 Integer 的存储位置，以及此处对比的几个true的原因?
int 没有引用的概念， 基础数据类型都是直接存储在内存中的内存**栈**上的，数据本身的值就是存储在栈空间里面的。
Integer是基础数据的包装类型，属于对象类，继承自Object类(此处继承是隐式的继承)，都是按照Java里面存储对象的内存模型来存储数据的。
存储对象的内存模型:分为两部分，引用、和数据本身。引用存储在**堆**内存中，（堆中存储方法区，局部变量等刷新缓慢，失去引用时不消失，需要垃圾回收器处理）堆内数据的地址存储在有序的内存**栈**上。

## 继续扩展
```
Integer a = new Integer(128);
int b = 128;
// 输出true
System.out.println(a == b);
```
Integer 和 int 对比的时候 Integer会自动拆箱(并无核心资料说明，测试对比得到答案) 
装箱调用的是  Integer.valueOf();
拆箱调用的是  intValue() return int;

## 哪些包具有缓冲池类型?
+ Byte
+ Short
+ Character
+ Integer
+ Long