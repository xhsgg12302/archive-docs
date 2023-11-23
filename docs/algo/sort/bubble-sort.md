## bubble

### visu-algo
![](/.images/algo/sort/bubble-sort-01.gif)

### implement
<!-- tabs:start -->

#### ** JAVA **
```java
import java.util.Arrays;

public class BubbleSort{
    public static void sort(int[] raw){
        for (int i = 1; i < raw.length ; i++) {
            for (int j = 0; j < raw.length - i ; j++) {
                if(raw[j] > raw[j+1])
                    switchNumberByTemp(raw,j,j+1);
            }
        }
    }

    public static void switchNumberByTemp(int[] raw,int x, int y){
        int temp = raw[x];
        raw[x] = raw[y];
        raw[y] = temp;
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
<!-- tabs:end -->


## Reference
* https://www.runoob.com/w3cnote/bubble-sort.html