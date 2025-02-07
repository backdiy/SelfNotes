## 方法一
模拟题，通过取模数再乘10累加，注意判断大小是否超过Integer范围
```java
public int reverse(int x) {  
    long ans = 0;  
    while (x != 0) {  
        ans = ans * 10 + x % 10;  
        x /= 10;  
    }  
    if (ans > Integer.MAX_VALUE || ans < Integer.MIN_VALUE) {  
        return 0;  
    }  
    return (int) ans;  
}
```