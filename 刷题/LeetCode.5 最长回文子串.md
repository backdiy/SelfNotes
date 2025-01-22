## 方法一
中心扩散法，需要分奇偶判断
* 时间复杂度：$O(n^{2})$
```java
public String longestPalindrome(String s) {  
    if (s == null || s.isEmpty() || s.length() == 1) {  
        return s;  
    }  
    int n = s.length();  
    int max = 0;  
    int start = 0;  
    int end = 0;  
    int i = 0;  
    while (i < n) {  
        int odd = i;  
        int even = i - 1;  
        int lenOdd = 0;  
        int lenEven = 1;  
        int j = i + 1;  
        boolean falseOdd = true;  
        boolean falseEven = true;  
        for (; j < n; j++) {  
            if (!falseOdd && !falseEven) {  
                break;  
            }  
            if (odd >= 0 && s.charAt(odd) == s.charAt(j)) {  
                odd--;  
                lenOdd += 2;  
            } else {  
                falseOdd = false;  
            }  
            if (even >= 0 && s.charAt(even) == s.charAt(j)) {  
                even--;  
                lenEven += 2;  
            } else {  
                falseEven = false;  
            }  
        }  
        if (lenOdd > max) {  
            start = odd + 1;  
            end = start + lenOdd;  
            max = lenOdd;  
        }  
        if (lenEven > max) {  
            start = even + 1;  
            end = start + lenEven;  
            max = lenEven;  
        }  
        i++;  
    }  
    return s.substring(start, end);  
}
```
## 方法二

## 方法三
