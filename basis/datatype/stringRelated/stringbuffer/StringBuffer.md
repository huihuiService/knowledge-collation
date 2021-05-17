# StringBuffer

##### 重点:StringBuffer是线程安全的！

##### StringBuffer vs StringBuilder:
StringBuffer和StringBuilder都是继承了AbstractStringBuilder抽象类。大部分操作中StringBuffer与StringBuilder基本相同，只是StringBuffer加上了**synchronized**关键字，所以StringBuffer是线程安全的，而StringBuilder是线程非安全的。

##### 使用场景
- 少量字符串操作时使用String
- 单线程时使用StringBuilder
- 多线程时使用StringBuffer

#### StringBuffer原理
StringBuffer继承自AbstractStringBuilder抽象类。类中有
```
char[] value;
int count;
```
**在JDK8后修改为了如下格式，与String变动类似**
```
byte[] value;
byte coder;
int count;
```
在此我们先不讨论char[] 与 byte[]的变动了，此变动类似String类的变动。详情可以看我String类相关的介绍。
看代码得知StringBuffer本质上是个数组。