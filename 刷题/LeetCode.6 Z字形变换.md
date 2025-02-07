## 方法一
模拟构造字符串即可，注意边界条件以及字符串间隔规律。
```java
public String convert(String s, int numRows) {  
    StringBuilder sb = new StringBuilder();  
    if (s.length() <= numRows || numRows == 1) {  
        return s;  
    }  
    for (int i = 1; i <= numRows; i++) {  
        int now = i - 1;  
        if (i == 1 || i == numRows) {  
            while (now < s.length()) {  
                sb.append(s.charAt(now));  
                now += (numRows - 1) * 2;  
            }  
        } else {  
            int next = (numRows - i) * 2 + i - 1;  
            while (now < s.length() || next < s.length()) {  
                if (now < s.length()) {  
                    sb.append(s.charAt(now));  
                }  
                if (next < s.length()) {  
                    sb.append(s.charAt(next));  
                }  
                now += (numRows - 1) * 2;  
                next += (numRows - 1) * 2;  
            }  
        }  
    }  
    return sb.toString();  
}
```