## 方法一
模拟题，将数字反转即可，注意负数。
```java
public boolean isPalindrome(int x) {  
    if (x < 0) {  
        return false;  
    }  
    int reverse = 0;  
    int base = x;  
    while (x != 0) {  
        reverse = reverse * 10 + x % 10;  
        x /= 10;  
    }  
    return reverse == base;  
}
```