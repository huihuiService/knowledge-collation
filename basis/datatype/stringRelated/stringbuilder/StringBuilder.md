# StringBuilder

StringBuiler构造方法和API与StrinBuffer类似，但**StringBuilder是线程非安全的**，因此性能较高。
### StrinBuilder常用方法

#### 构造函数
##### StringBuilder有四个构造函数：
1. StringBuilder() value内容为空，并设置容量为16个字节；
2. StringBuilder(CharSequece seq)  使用seq初始化，容量在此基础上加16；
3. StringBuilder(int capacity) 设置特定容量；
3. StringBuilder(String str)  使用str初始化，容量str大小的基础上加

#### append方法，由于继承了Appendable接口，所以要实现append方法，StringBuilder类几乎对所有的类型都重载了append方法
1. append(boolean b)
2. append(char c)
3. append(char[] str)
4. append(char[] str,int offset,int len)
5. append(CharSequence s)
6. append(CharSequence s,int start,int end)
7. append(double d)
8. append(float f)
9. append(int i)
10. append(long lng)
11. append(Object obj)
12. append(String str)
13. append(StringBuffer sb)

#### insert方法，insert可以控制插入的起始位置，也几乎对所有的基本类型都重载了insert方法
1. insert(int offser,boolean b)
2. insert(int offset,char c)
3. insert(int offset,char[] str)
4. insert(int index,char[] str,int offset,int len)
5. insert(int dsfOffset,CharSequence s)
6. insert(int dsfOffset,CharSequence s,int start,int end)
7. insert(int offset,double d)
8. insert(int offset,float f)
9. insert(int offset,int i)
10. insert(int offset,long l)
11. insert(int offset,Object obj)
12. insert(int offset,String str)
**注意:insert 没有 StringBuffer 的入参**

#### 其它会改变内容的方法，上面的那些方法会增加StringBuilder的内容，还有一些方法可以改变StringBuilder的内容：
1. StringBuilder delete(int start,int end) 删除从start到end（不包含）之间的内容；
2. StringBuilder deleteCharAt(int index) 删除index位置的字符；
3. StringBuilder replace(int start,int end,String str) 用str中的字符替换value中从start到end位置的子序列；
4. StringBuilder reverse() 反转；
5. void setCharAt(int index,char ch) 使用ch替换位置index处的字符；
6. void setLength(int newLength) 可能会改变内容（添加'\0'）；

#### 下面这些方法不会改变内容：
1. int capacity() 返回value的大小即容量；
2. int length() 返回内容的大小，即count；
3. char charAt(int index) 返回位置index处的字符；
4. void ensureCapacity(int minimumCapacity) 确保容量至少是minimumCapacity；
5. void getChars(int srcBegin,int srcEnd,char[] dst,int dstBegin) 返回srcBegin到srcEnd的字符到dst；
6. int indexOf(String str) 返回str第一次出现的位置；
7. int indexOf(String str,int fromIndex) 返回从fromIndex开始str第一次出现的位置；
8. int lastIndexOf(String str) 返回str最后出现的位置；
9. int lastIndexOf(String str,int fromIndex) 返回从fromIndex开始最后一次出现str的位置；
10. CharSequence subSequence(int start,int end) 返回字符子序列；
11. String substring(int start) 返回子串；
12. String substring(int start,int end) 返回子串；
13. String toString() 返回value形成的字符串；
14. void trimToSize() 缩小value的容量到真实内容大小；