# String

## 改变时
### 在JDK8中String使用char数组存储数据
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

https://reionchan.github.io/2017/09/25/java-9-compact-string/#compressed-string---java-6

### 在JDK9中String使用byte数组存储字符串，并且同时使用coder来标识是使用了哪种编码
String俨然已经成为java里最常用的类。
