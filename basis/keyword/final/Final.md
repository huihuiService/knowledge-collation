# 关键字final

## final是什么
final是个关键字，可以用于修饰类、成员变量、成员方法。


### 可以声明的位置
+ [类](#class)
+ [方法](#function)
+ [数据](#data)
    - [基本类型](#databasic)
    - [引用类型](#dataquote)

### final关键字用来修饰类、方法、成员变量、局部变量
表示最终是不可变的
|修饰对象|解释说明|
|:--|:--|
|类|无子类，不可以被继承，更不可能被重写|
|方法|方法不能在子类方法中被覆盖|
|b变量|称为常量，初始化以后不能改变值|
test master

#### <span id="class">final修饰类</span>
final修饰的类，表示该类不能被继承。如果代码中试图继承final修饰的类，则编译代码时会报错。

#### <span id="function">final修饰方法</span>

#### <span id="data">final修饰数据</span>

##### <span id="databasic">基本类型</span>

##### <span id="dataquote">引用类型</span>

### final修饰的变量也可以被更改