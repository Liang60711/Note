## Quick Sort 

分治法概念，不穩定排序(相同元素相對順序可能改變)。



1. 時間複雜度: 
    * 平均 O(n log n)

    * 最差 O(n^2) ； 當資料以排序且總是選

2. 空間複雜度:

    * O(log n)


<br/>

<br/>

## 實作

```java
public class QuickSort {

    public static void quickSort(int[] arr, int left, int right) { 
        if (left < right) { // 制遞迴終止條件，只在子陣列有兩個或以上的元素時才遞迴呼叫 quickSort
            int pi = partition(arr, left, right);
            quickSort(arr, left, pi - 1);
            quickSort(arr, pi + 1, right);
        }
    }

    private static int partition(int[] arr, int left, int right) {
        int pivot = arr[right]; // 以最右邊元素作為 pivot
        int i = left;

        // 將比 pivot 小的數放在最左邊，依序往右放
        // i 是已排序好的元素的 index
        for (int j = left; j < right; j++) {
            if (arr[j] < pivot) {
                swap(arr, i, j);
                i++;
            }
        }

        // 最後將 pivot 放在正確的位置
        swap(arr, i, right);
        return i; // 回傳 pivot 元素 index
    }

    private static void swap(int[] arr, int i, int j) {
        int temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }
}
```

執行程式

```java
public static void main(String[] args) {
    int[] arr = {5, 2, 9, 1, 5, 6};
    quickSort(arr, 0, arr.length - 1);

    for (int i : arr) {
        System.out.println(i);
    }
}
``



---


```java

public class QuickSort {

    public static void quickSort(int[] arr, int low, int high) {
        if (low < high) {
            int pi = partition(arr, low, high);
            quickSort(arr, low, pi - 1);
            quickSort(arr, pi + 1, high);
        }
    }

    private static int partition(int[] arr, int low, int high) {

        int pivot = arr[high]; //使用最右邊的元素作為 pivot
        int i = low; //排序好的元素的index

        for (int j = low; j < high; j++) {
            if (arr[j] < pivot) {
                swap(arr, i, j);
                i++;
            }
        }
        swap(arr, i, high);
        return i;
    }

    private static void swap(int[] arr, int i, int j) {
        int temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }
}

```