# String

## 目录
+ [1、改变](#change)
    1) [JDK8: char[]](#changejdk8)
    2) [JDK9: byte[]](#changejdk9)
+ [2、String为什么被声明为final(声明为final的好处)](#stringfinal)
+ [3、什么是StringPool(StringTable)](#stringpool)

## 详情解释:

## <span id="change">1、改变</span>
### <span id="changejdk8">在JDK8中String使用char数组存储数据</span>
char占16位既2字节, 因为JAVA内部使用UTF-16。
```
JDK8: private final char value[];
```
举例来说，如过存储一个中文则2字节占满，如果存储一个英文字符则前8位都是0，因为一个ASCII字符都能被单个字节表示。
早年JDK 6 引入的可选的 Compressed String 功能，现在 JDK 9 最新引入的 Compact String ，它们两者的设计目的都是优化字符串在 JVM 中的内存占用
JDK6中的可选虚拟机参数:
```
-XX:+UseCompressedStrings
```
当此选项启用时，字符串将以 byte[] 的形式存储，代替原来的 char[]，可以节省一些内存(多出来的那一个字节)。然而，此功能最终在 JDK7 中被移除，主要原因在于它将带来一些无法预料的性能问题。

### <span id="changejdk9">在JDK9中String使用byte数组存储字符串，并且同时使用coder来标识是使用了哪种编码</span>
Java 9 重新采纳字符串压缩这一概念:CompactString。（虽然JDK9、10被oracle官网不推荐了）我们就看JDK11的源码吧
JDK9后 修改为了byte数组存储
```
private final byte[] value;  
```
String中引入了final修饰的成员变量 coder
```
private final byte coder;
```
coder 可以被赋值的常量
```
static final byte LATIN1 = 0;
static final byte UTF16 = 1;
```
大多数操作都判断了coder，如我们熟悉的indexOf()
```
public int indexOf(int ch, int fromIndex) {
        return isLatin1() ? StringLatin1.indexOf(value, ch, fromIndex)
                          : StringUTF16.indexOf(value, ch, fromIndex);
    }
```
如上代码，对应了不同的处理StringLatin1, StringUTF16
虚拟机参数 CompactString 默认是被启用的，如果你需要关闭可以如下参数，注意：关闭后value依然是byte[]，只是编码格式没有了Latin1,默认为了UTF16
```
+XX:-CompactStrings
```

计算字符串长度的变化
```
public int length() {
    return value.length >> coder;
}
```
备注: >> 右移运算符相当于 除以 2的N次方。
eg:
```
int intTest = 20 >> 3;
double doubleTest = 20 >> 3;
// 2
System.out.println(intTest);
// 2.0 
System.out.println(doubleTest);
```

### 扩展
关于占用字节问题，测试环境为JDK8，JAVA默认使用unicode来表示字符（utf-8 16 32都是unicode），见如下代码
```
public static void main(String[] args) {
    try {
        String a = "我";
        String b = "w";
        // 3
        System.out.println(a.getBytes("UTF-8").length);
        // 1
        System.out.println(b.getBytes("UTF-8").length);

        char ac = '我';
        char bc = 'w';

        // 2
        System.out.println(charToByte(ac).length);
        // 2
        System.out.println(charToByte(bc).length);

    } catch (Exception e) {
        e.printStackTrace();
    }

}

public static byte[] charToByte(char c) {
    byte[] b = new byte[2];
    b[0] = (byte) ((c & 0xFF00) >> 8);
    b[1] = (byte) (c & 0xFF);
    return b;
}
```
针对String来说 不同的编码格式存储的不一样，英文和空格是1个字节，中文是3-4个字节（Unicode扩展区的一些汉字存储需要4个字节）。
而char永远都是2个字节，所以会有 JDK9以后的优化，避免char存储的英文时候的前8位都是0，浪费了空间。
**同样的代码在JDK11上执行结果是  3 1 2 2**，可以看出来， JDK11中 char 依然是两个字节不变，但字符串英文字符是1个字节了。

### <span id="stringfinal">2、String为什么是final,如此设计有什么好处</span>
如a = "abcd"; 如果改变属性 a = "abc" 则指向的是新地址。
前面已经提到过了，String的属性中存储为
```
private final byte[] value;
```
问题: 以下代码输出什么？
```
String s2 = "abcd"; 
String s3 = "ab"+"cd"; 
System.out.println("s2==s3 ? : " + (s2 == s3));
```
其实是等于的。为什么 不是ab cd 两个对象呢。
**Final意味着不可变的。与可变有什么优势？**
1、对象不可变定义 
不可变对象是指对象的状态在被初始化以后，在整个对象的生命周期内，不可改变。 

2、如何不可变 
通常情况下，在java中通过以下步骤实现不可变
1. 对于属性不提供设值方法
2. 所有的属性定义为private final
3. 类声明为final不允许继承
```
注意：不用final关键字也可以实现对象不可变，使用final只是显示的声明，提示开发者和编译器为不可变。
```

首先搞懂final, final的出现就是为了不想改变。
final修饰的类是不能被继承的，所以final修饰类是不能被篡改的。
不可变就意味着对象值不会被改动。
1、提高了对象的效率（拷贝时只需要给地址即可）。
2、提高了安全性（并发时不用担心被其他线程修改了）。
3、需要放在常量池，因为不可变所以可以共享。
**那么String真的不可以改变么？**
答案是：**不是，可以改**，通过反射。
答案从源码中找
```
private final char[] value;
```
final value 意味着String不能指向其他value了。 但是通过反射我们可以获取value的指针，从而修改value数组的结构，以下是代码。
```
public static void testReflection() throws Exception {
    //创建字符串"Hello World"， 并赋给引用s
    String s = "Hello World";
    System.out.println("s = " + s); //Hello World
    //获取String类中的value字段
    Field valueFieldOfString = String.class.getDeclaredField("value");
     
    //改变value属性的访问权限
    valueFieldOfString.setAccessible(true);
     
    //获取s对象上的value属性的值
    char[] value = (char[]) valueFieldOfString.get(s);
     
    //改变value所引用的数组中的第5个字符
    value[5] = '_';
     
    System.out.println("s = " + s);  //Hello_World
}
```
4、**hash效率更高**了，String比如在HashMap中被频繁的调用.hashCode()（扩展:如果Object做HashMap的key则需要重新hashCode和equals方法）。

### 思考:String 常量池的清理机制?查阅后补充!
看个代码
```
String str1 = “abc”;
String str2 = “abc”;
String str3 = “abc”;
String str4 = new String(“abc”);
String str5 = new String(“abc”);
String str6 = new String(“abc”);
```
<img src="https://segmentfault.com/img/bVPR6M?w=553&h=322/view">
对于str1、str2、str3来说 引用直接指向常量池。引用在栈，是同一个引用
对于str4、str5、str6来说，各自引用不同，除了常量池的对象外，new String("abc") 在堆里也是各自的，再堆里存的是 char数组/jdk9: byte数组。构造器里相当于引用传参。

---
面试题:
String str4 = new String(“abc”) 创建多少个对象？

1. 在常量池中查找是否有“abc”对象
+ 有则返回对应的引用实例
+ 没有则创建对应的实例对象
2. 在堆中 new 一个 String("abc") 对象
3. 将对象地址赋值给str4,创建一个引用

结论:常量池中没有“abc”字面量则创建两个对象，否则创建一个对象，以及创建一个引用

引申题目:
**String str1 = new String("A"+"B") ; 会创建多少个对象?**
**String str2 = new String("ABC") + "ABC" ; 会创建多少个对象?**
查看JDK8 字节码 得出的答案如下
str1:
常量池: "AB" 1个
堆: char[] 表示的 new String("AB")
栈: str1引用
共创建了3个

str2:
常量池: "ABC" 1个(第一次使用"ABC"时推入常量池)
堆: Method new StringBuilder(), new String("ABC"), StringBuilder.append(new String("ABC")) 但对象没增多，只是数据变了， 
StringBuilder.append("ABC") 但对象没增多，只是数据变了， 
StringBuilder.toString() 新增了一个 "ABCABC"的char[],
"ABCABC"  未入常量池
栈: str2引用
共创建了 常量池1个，堆中 1个StringBuilder, 1个 new String("ABC"), 一个 new String("ABCABC") 由SB.toString()时新增。 引用1个。

### <span id="stringpool">3、什么是StringPool(StringTable) </span>
TODO

### 扩展： String.intern() 方法
简单解释:如果字符串池中没有此值，则放入此值，并返回值地址。