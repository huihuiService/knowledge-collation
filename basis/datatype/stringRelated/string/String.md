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
不可变就意味着对象值不会被改动。
1、提高了对象的效率（拷贝时只需要给地址即可）。
2、提高了安全性（并发时不用担心被其他线程修改了），反射等机制时也不担心所需要的String不安全。
3、常量池也不用多次变化了。如上代码就是指向了堆中的常量池。
4、**hash效率更高**了，String比如在HashMap中被频繁的调用.hashCode()（扩展:如果Object做HashMap的key则需要重新hashCode和equals方法）。

### 思考:String 常量池的清理机制?查阅后补充!

### <span id="stringpool">3、什么是StringPool(StringTable) </span>
TODO