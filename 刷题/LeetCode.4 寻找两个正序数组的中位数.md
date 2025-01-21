## 方法一
利用中位数的含义列出二分查找的条件，确定两个数组各自的切割点
- 时间复杂度：$O(log(min(n,m)))$
```java
public double findMedianSortedArrays(int[] nums1, int[] nums2) {  
    if (nums1.length > nums2.length) {  
        return findMedianSortedArrays(nums2, nums1);  
    }  
    int len1 = nums1.length;  
    int len2 = nums2.length;  
    int left = 0;  
    int right = len1;  
    int i;  
    int j;  
    int maxl;  
    int minr;  
    while (left <= right) {  
        i = (left + right) / 2;  
        j = (len1 + len2 + 1) / 2 - i;  
        if (i != len1 && j > 0 && nums1[i] < nums2[j - 1]) {  
            left = i + 1;  
        } else if (i > 0 && j != len2 && nums1[i - 1] > nums2[j]) {  
            right = i - 1;  
        } else {  
            if (i == 0) {  
                maxl = nums2[j - 1];  
            } else if (j == 0) {  
                maxl = nums1[i - 1];  
            } else {  
                maxl = Math.max(nums1[i - 1], nums2[j - 1]);  
            }  
            if ((len1 + len2) % 2 == 1) {  
                return maxl;  
            }  
            if (i == len1) {  
                minr = nums2[j];  
            } else if (j == len2) {  
                minr = nums1[i];  
            } else {  
                minr = Math.min(nums1[i], nums2[j]);  
            }  
            return (maxl + minr) / 2.0;  
        }  
    }  
    return 0.0;  
}
```
## 方法二
对于n个长度为m的数组求第k大的数，则可以用最小堆或优先队列获取第k大的数
- 时间复杂度：$O(k*log(n))$