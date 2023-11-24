## java 中异常返回时机

### example
<!-- tabs:start -->
#### ** 1 **
```java
// finally语句在return语句执行之后return返回之前执行的
public int exp1(){
    int b = 20;
    try {
        System.out.println("try block");
        return b += 80;
    }
    catch (Exception e) {
        System.out.println("catch block");
    }
    finally {
        System.out.println("finally block");
        if (b > 25) {
            System.out.println("b>25, b = " + b);
        }
    }
    return b;
}

output:
try block
finally block
b>25, b = 100
100
```

#### ** 2 **
```java
// finally块中的return语句会覆盖try块中的return返回
public int exp2() {
    int b = 20;
    try {
        System.out.println("try block");
        return b += 80;
    } catch (Exception e) {
        System.out.println("catch block");
    } finally {
        System.out.println("finally block");
        if (b > 25) {
            System.out.println("b>25, b = " + b);
        }
        return 200;
    }
    // return b;
}

output:
try block
finally block
b>25, b = 100
200
```

#### ** 3 **
```java
// 如果finally语句中没有return语句覆盖返回值，那么原来的返回值可能因为finally里的修改而改变也可能不变
public int exp3() {
    int b = 20;
    try {
        System.out.println("try block");
        return b += 80;
    } catch (Exception e) {
        System.out.println("catch block");
    } finally {
        System.out.println("finally block");
        if (b > 25) {
            System.out.println("b>25, b = " + b);
        }
        b = 150;
    }
    return 2000;
}

output:
try block
finally block
b>25, b = 100
100
```

```java
public Map<String, String> exp3_1() {
    Map<String, String> map = new HashMap();
    map.put("KEY", "INIT");
    try {
        map.put("KEY", "TRY");
        return map;
    }
    catch (Exception e) {
        map.put("KEY", "CATCH");
    }
    finally {
        map.put("KEY", "FINALLY");
        map = null;
    }
    return map;
}

output:
FINALLY
```

#### ** 4 **
```java
// try块里的return语句在异常的情况下不会被执行(参考中的例子有错误)
public int exp4() {
    int b = 20;
    try {
        System.out.println("try block");
        b = b / 0;
        return b += 80;
    } catch (Exception e) {
        b += 15;
        System.out.println("catch block");
    } finally {
        System.out.println("finally block");
        if (b > 25) {
            System.out.println("b>25, b = " + b);
        }
        b += 50;
    }
    return 204;
}

output:
try block
catch block
finally block
b>25, b = 35
204
```

#### ** 5 **
```java
// 当发生异常后，catch中的return执行情况与未发生异常时try中return的执行情况完全一样
public int exp5() {
    int b = 20;
    try {
        System.out.println("try block");
        b = b /0;
        return b += 80;
    } catch (Exception e) {
        System.out.println("catch block");
        return b += 15;
    } finally {
        System.out.println("finally block");
        if (b > 25) {
            System.out.println("b>25, b = " + b);
        }
        b += 50;
    }
    //return b;
}

output:
try block
catch block
finally block
b>25, b = 35
35
```

#### ** 6 **
```java
// catch中的return先计算然后传值到finally中最后finally直接返回，也符合情况1。
public int exp6() {
    int b = 20;
    try {
        System.out.println("try block");
        b = b /0;
        return b += 80;
    } catch (Exception e) {
        System.out.println("catch block");
        return b += 15;
    } finally {
        System.out.println("finally block");
        if (b > 25) {
            System.out.println("b>25, b = " + b);
        }
        return b += 50;
    }
    //return b;
}

output:
try block
catch block
finally block
b>25, b = 35
85
```
<!-- tabs:end -->

## 字节码查看异常执行顺序


## reference
* [Java finally语句到底是在return之前还是之后执行?](https://www.cnblogs.com/lanxuezaipiao/p/3440471.html)
* [java关键字finally底层原理](https://blog.csdn.net/qq644266189/article/details/123034999)
