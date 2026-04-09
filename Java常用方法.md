# Java常用方法

## 字符串转换为字符串数组

在 Java 中，将 **字符串（String）转换为数组** 有几种常见方式，取决于你想要的“数组类型”是什么👇

------

### ✅ 一、转为字符数组（`char[]`）

最常用、最直接的方式：

```java
String str = "hello";
char[] arr = str.toCharArray();

for (char c : arr) {
    System.out.println(c);
}
```

📘 输出：

```
h
e
l
l
o
```

------

### ✅ 二、按特定分隔符拆分为字符串数组（`String[]`）

如果字符串里有分隔符（例如空格、逗号等）：

```java
String str = "apple,banana,orange";
String[] arr = str.split(",");

for (String s : arr) {
    System.out.println(s);
}
```

📘 输出：

```
apple
banana
orange
```

> 🔹 `split()` 方法接收一个 **正则表达式** 参数，比如：
>
> - `split(" ")` 按空格切分
> - `split(",")` 按逗号切分
> - `split("")` 按每个字符切分（效果类似于 `toCharArray()`）

------

### ✅ 三、转为数字数组（例如 int[]）

如果字符串是数字组成的，比如 `"12345"`：

```java
String str = "12345";
int[] arr = new int[str.length()];

for (int i = 0; i < str.length(); i++) {
    arr[i] = str.charAt(i) - '0'; // 字符转数字
}

for (int num : arr) {
    System.out.println(num);
}
```

📘 输出：

```
1
2
3
4
5
```

------

### ✅ 四、混合示例：输入字符串，用 Map 做数字相加

假设你有：

```java
String input = "abbcd";
Map<String, Integer> map = new HashMap<>();
map.put("b", 2);
map.put("bc", 1);
```

你可以先把字符串转数组，再遍历做相加。

### ✅ 五、把字符串数组中的数字转换为整数

~~~java
String [] operations={1,"C"}
int sum=0; 
sum += Integer.parseInt(operations[0]);
~~~

在 Java 中，如果你想判断字符串数组中的某个元素（例如 `a[0]`）是否可以被解析为一个整数（即它是否是一个合法的整型常数），可以使用 `Integer.parseInt()` 并结合 `try...catch` 来实现。

------

## 判断字符串中数值是否为整数

### ✅ 方法一：使用 `try...catch`（最常见、最可靠）

```java
public class Main {
    public static void main(String[] args) {
        String[] a = {"123", "abc", "45.6", "-78", "001"};

        for (String s : a) {
            if (isInteger(s)) {
                System.out.println(s + " 是一个整数");
            } else {
                System.out.println(s + " 不是一个整数");
            }
        }
    }

    public static boolean isInteger(String str) {
        try {
            Integer.parseInt(str);  // 尝试解析为 int
            return true;
        } catch (NumberFormatException e) {
            return false;  // 解析失败就不是整数
        }
    }
}
```

#### 输出结果：

```
123 是一个整数
abc 不是一个整数
45.6 不是一个整数
-78 是一个整数
001 是一个整数
```

------

### ✅ 方法二：使用正则表达式（不抛异常，更高效）

如果你不想用异常处理，可以用正则表达式判断：

```java
public static boolean isInteger(String str) {
    return str.matches("-?\\d+");
}
```

说明：

- `-?` 表示可以有一个负号；
- `\\d+` 表示至少一个数字。

------

### ✅ 方法三：判断是否为整数后再取值

你还可以结合两种方法，例如：

```java
if (isInteger(a[0])) {
    int value = Integer.parseInt(a[0]);
    System.out.println("a[0] 的整数值为: " + value);
} else {
    System.out.println("a[0] 不是整数");
}
```



