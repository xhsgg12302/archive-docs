## bubble

### gif
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
        int[] arr = { 22, 34, 3, 32, 82, 55, 89, 50, 37, 5, 64, 35, 9, 70 };
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