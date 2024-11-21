## selection

### visualization
![](/.images/algo/sort/selection-sort-01.gif)

### implement
<!-- tabs:start -->

#### **JAVA**
```java
import java.util.Arrays;

public class SelectionSort{
    public static void sort(int[] raw){
        for (int i = 0; i < raw.length - 1 ; i++) {
            int temp = i;
            for (int j = i + 1; j < raw.length ; j++) {
                if(raw[temp] > raw[j])
                    temp = j;
            }
            switchNumberByXor(raw,i,temp);
        }
    }

    public static void switchNumberByXor(int[] raw, int x, int y ){
        if(raw[x] == raw[y]) return;
        raw[x] = raw[x] ^ raw[y];
        raw[y] = raw[x] ^ raw[y];
        raw[x] = raw[x] ^ raw[y];
    }

    public static void main(String[] args){
        int[] arr = { 3, 44, 38, 5, 47, 15, 36, 26, 27, 2, 46, 4, 19, 50, 48 };
        sort(arr);
        System.out.println(Arrays.toString(arr));
    }
}
```

#### **C**
```c
```
<!-- tabs:end -->


## Reference
* https://www.runoob.com/w3cnote/selection-sort.html