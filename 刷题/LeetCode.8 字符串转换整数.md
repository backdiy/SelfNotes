## 方法一
模拟题，根据要求编写程序，需注意题目给出的边界条件
```java
public int myAtoi(String s) {  
    // 当前遍历的索引  
    int index = 0;  
    // 符号标志，默认为正  
    int sign = 1;  
    // 最终结果  
    int result = 0;  
    // 1. 跳过前导空格  
    while (index < s.length() && s.charAt(index) == ' ') {  
        index++;  
    }  
    // 2. 处理符号位  
    if (index < s.length() && (s.charAt(index) == '+' || s.charAt(index) == '-')) {  
        sign = (s.charAt(index) == '-') ? -1 : 1;  
        index++;  
    }  
    // 3. 构造结果  
    while (index < s.length() && Character.isDigit(s.charAt(index))) {  
        int digit = s.charAt(index) - '0';  
  
        // 检查是否溢出  
        if (result > Integer.MAX_VALUE / 10 || (result == Integer.MAX_VALUE / 10 && digit > Integer.MAX_VALUE % 10)) {  
            return (sign == 1) ? Integer.MAX_VALUE : Integer.MIN_VALUE;  
        }  
        result = result * 10 + digit;  
        index++;  
    }  
    return result * sign;  
}
```