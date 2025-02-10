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
动态规划，设`dp[i][j]`表示i与j之间是否为回文字符串，如果`s[i]==s[j]`且`dp[i+1][j-1]=true`，则`dp[i][j]=true`。`dp[i][i]`始终为true，如果`s[i]==s[i+1]`则`dp[i][i+1]=true`。
* 时间复杂度：$O(n^{2})$
```java
public String longestPalindrome(String s) {  
    if (s == null || s.isEmpty() || s.length() == 1) {  
        return s;  
    }  
    boolean[][] dp = new boolean[s.length()][s.length()];  
    for (int i = 0; i < s.length(); i++) {  
        dp[i][i] = true;  
    }  
    int maxLen = 0;  
    int maxStart = 0;  
    for (int len = 1; len < s.length(); len++) {  
        for (int start = 0; start < s.length() - len; start++) {  
            int end = start + len;  
            if (s.charAt(start) == s.charAt(end) && (start == end - 1 || dp[start + 1][end - 1])) {  
                dp[start][end] = true;  
                if (len > maxLen) {  
                    maxLen = len;  
                    maxStart = start;  
                }  
            }  
        }  
    }  
    return s.substring(maxStart, maxStart + maxLen + 1);  
}
```
## 方法三
Manacher算法，算是中心扩散的优化，利用回文字符串对称性的特点求出以每个字符串为中心的回文串的半径，找到最大半径的点位，就可以求出最长的回文字符串。
- 对字符串进行预处理，在每个字符串之间与字符串开头结尾添加一个占位符`A`，在字符串开头再次添加一个不同的占位符`B`。
- 维护一个当前已知回文字符串的最右边界`R`和对应的中心`C`。
- 对于每个位置`i`，计算其对称位置`mirror = 2 * C - i`。
- 对于每个位置`i`，如果 `i` 在`R`的范围内，则可以利用对称性初始化`p[i]`，也就是`p[i]=min(p[mirror],R-i)`，然后尝试扩展回文子串，更新`p[i]`、最右边界`R`和中心`C` ，找到`p[i]`最大的`i`。
```java
public String longestPalindrome(String s) {  
    if (s == null || s.isEmpty() || s.length() == 1) {  
        return s;  
    }  
    StringBuilder sb = new StringBuilder();  
    sb.append("$");  
    for (char c : s.toCharArray()) {  
        sb.append("#").append(c);  
    }  
    sb.append("#");  
    int[] r = new int[sb.length()];  
    int maxR = 0;  
    int maxCenter = 0;  
    int center = 0;  
    int right = 0;  
    int i = 1;  
    while (i < sb.length()) {  
        r[i] = right > i ? Math.min(r[2 * center - i], right - i) : 1;  
        while (i + r[i] < sb.length() && i - r[i] > 0 && sb.charAt(i + r[i]) == sb.charAt(i - r[i])) {  
            r[i]++;  
        }  
        if (i + r[i] > right) {  
            center = i;  
            right = i + r[i];  
        }  
        if (r[i] > maxR) {  
            maxR = r[i];  
            maxCenter = i;  
        }  
        i++;  
    }  
    int start = (maxCenter - maxR) / 2;  
    return s.substring(start, start + maxR - 1);  
}
```