---
title: aviator表达式
date: 2019/9/24
---
#### 算术表达式
```
```java
Long result = (Long)AviatorEvaluator.execute("1+2+3");
System.out.println(result);//6
```
> note：Aviator的数值类型仅支持Long和Double,任何整数都将转换成Long,任何浮点数都将转换为Double,包括用户传入的变量数值。

#### 逻辑表达式
```java
Boolean result2 = (Boolean)AviatorEvaluator.execute("3>1 && 2!=4 || true");
System.out.println(result2);//true
```

#### 变量和字符串相加
```java
Map env = newHashMap();
env.put("yourname","aviator");
String result3 = (String)AviatorEvaluator.execute(" 'hello ' + yourname ", env);
System.out.println(result3);
```
> 上面的例子演示了怎么向表达式传入变量值，表达式中的yourname是一个变量，默认为null，通过传入Map的变量绑定环境，将yourname设置为你输入的名称。env的key是变量名，value是变量的值。

Aviator 2.2开始新增加一个exec方法,可以更方便地传入变量并执行,而不需要构造env这个map了:
```java
String result4= (String) AviatorEvaluator.exec(" 'hello ' + yourname ","aviator2");
System.out.println(result4);
```

#### 三元表达式
```java
String result5=(String)AviatorEvaluator.execute("3>0? 'yes':'no'");
System.out.println(result5);
```

#### 变量语法糖
Aviator 有个方便用户使用变量的语法糖, 当你要访问变量a中的某个属性b, 那么你可以通过a.b访问到, 更进一步, a.b.c将访问变量a的b属性中的c属性值, 推广开来也就是说 Aviator 可以将变量声明为嵌套访问的形式。
TestAviator类符合JavaBean规范, 并且是 public 的，我们就可以使用语法糖:
```java
public class TestAviator {
    int i;
    float f;
    Date date;
    // 构造方法
    public TestAviator(int i, float f, Date date) {
        this.i = i;
        this.f = f;
        this.date = date;
    }
    // getter and setter

    public static void main(String[] args) {
        TestAviator foo = new TestAviator(100, 3.14f, new Date());
        Map<String, Object> env = new HashMap<String, Object>();
        env.put("foo", foo);
        System.out.println(AviatorEvaluator.execute("'foo.i = '+foo.i", env));   // foo.i = 100
        System.out.println(AviatorEvaluator.execute("'foo.f = '+foo.f", env));   // foo.f = 3.14
        System.out.println(AviatorEvaluator.execute("'foo.date.year = '+(foo.date.year+1990)", env));  // foo.date.year = 2106
    }
}
```