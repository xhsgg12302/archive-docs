## insertion

### visu-algo
![](/.images/algo/sort/insertion-sort-01.gif)

### implement
<!-- tabs:start -->

#### ** JAVA **
```java
import java.util.Arrays;

public class InsertionSort{
    public static void sort(int[] raw){
        for (int i = 1; i < raw.length; i++) {
            int preIndex = i - 1;
            int currentValue = raw[i];
            while(preIndex >= 0 && currentValue < raw[preIndex]){
                // 覆盖循环
                raw[preIndex + 1] = raw[preIndex];
                preIndex--;
            }
            raw[preIndex + 1] = currentValue;
        }
    }

    public static void main(String[] args){
        int[] arr = { 3, 44, 38, 5, 47, 15, 36, 26, 27, 2, 46, 4, 19, 50, 48 };
        sort(arr);
        System.out.println(Arrays.toString(arr));
    }
}
```

#### ** C **
```c
```

#### ** Python **
```python
def insertionSort(arr):
    for i in range(len(arr)):
        preIndex = i-1
        current = arr[i]
        while preIndex >= 0 and arr[preIndex] > current:
            arr[preIndex+1] = arr[preIndex]
            preIndex-=1
        arr[preIndex+1] = current
    return arr
```
<!-- tabs:end -->


## Reference
* https://www.runoob.com/w3cnote/selection-sort.html