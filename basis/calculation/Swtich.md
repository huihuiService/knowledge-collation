# Switch

## Swtich都支持什么类型?
+ byte & Byte
+ char & Character
+ short & Short
+ int & Integer
+ String
+ enum
**注意事项:从JAVA7开始才能使用char(Character)和String**

##### 使用包装类型DEMO
```
// 使用包装类型
Integer value = 5;
switch (value) {
    case 3:
        System.out.println("3");
        break;
    case 5:
        System.out.println("5");
        break;
    default:
        System.out.println("default");
}
```

##### 使用枚举类型DEMO
```
// 使用枚举类型
Status status = Status.PROCESSING;
switch (status) {
    case OPEN:
        System.out.println("open");
        break;
    case PROCESSING:
        System.out.println("processing");
        break;
    case CLOSE:
        System.out.println("close");
        break;
    default:
        System.out.println("default");
}
```

**其实说Switch支持包装类是不完全正确的，它本身是支持的基础类型，通过字节码编译我们发现再使用Integer等包装类时，会调用initvalue()方法进行自动拆箱。所以间接支持了几种类型的包装类**

### 使用 switch case 语句也有以下几点需要注意。
1. case 里面必须跟 break，不然程序会一个个 case 执行下去，直到最后一个 break 的 case 或者 default 出现。
2. case 条件里面只能是常量或者字面常量。
3. default 语句可有可无，最多只能有一个。

#### 常用形式
1. 标准型(case后面都有break语句，case后的值都是整数)
```
public static void main(String[] args) {
    int i = 3;
    
    switch (i) {
        case 1:
            System.out.println(1);
            break;
        case 2:
            System.out.println(2);
            break;
        default:
            System.out.println("default");
            break;
    }
}
```
输出:
```
default
```

2. 常量型(case后面都有break语句，case后的值都是常量)
```
public static void main(String[] args) {
    int i = 3;
    final int NUM1=1;
    final int NUM2=2;
    switch (i) {
        case NUM1:
            System.out.println(1);
            break;
        case NUM2:
            System.out.println(2);
            break;
        default:
            System.out.println("default");
            break;

    }
}
```
输出:
```
default
```
3. 没有break时候的样子
```
public static void main(String[] args) {
    int i = 2;

    switch (i) {
        case 1:
            System.out.println(1);
        case 2:
            System.out.println(2);
        case 3:
            System.out.println(3);
        default:
            System.out.println("default");
    }
}
```
输出:
```
2
3
default
```

```
public static void main(String[] args) {
    
}
```
输出:
```

```
#### 没有break的案例
1、deafult在命中前
```
public static void main(String[] args) {
    int i = 2;

    switch (i) {
        case 1:
            System.out.println(1);
        default:
            System.out.println("default");
        case 2:
            System.out.println(2);
        case 3:
            System.out.println(3);
        case '4':
            System.out.println(4);

    }
}
```
输出:
```
2
3
4
```
2、deafult在命中后
```
public static void main(String[] args) {
    int i = 2;

    switch (i) {
        case 1:
            System.out.println(1);
        case 2:
            System.out.println(2);
        default:
            System.out.println("default");
        case 3:
            System.out.println(3);
        case '4':
            System.out.println(4);

    }
}
```
输出:
```
2
default
3
4
```
没有break的案例分析结论:**当没有break时，命中后会进入后续所有的case。**