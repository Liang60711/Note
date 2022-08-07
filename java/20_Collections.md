## Collections
可對集合操作的工具類、靜態方法，可分為以下幾種
1. 排序類
2. 查找/替換
3. 同步Synchronized
4. 不可變集合

<br/>

<br/>


## 排序類(針對List interface)
`reverse()` 反轉 List
```java
List<String> list = new ArrayList<>();

list.add("aaa");
list.add("bbb");
list.add("ccc");

Collections.reverse(list);
```
`shuffle()` 隨機排序 List
```java
Collections.shuffle(list);
```
`sort()` 按照ASC排序，可以多加`Comparator`
```java
Collections.sort(list);//ASC

Collections.sort(list, Collections.reverseOrder());//DESC

Collections.sort(list, new Comparator<String>() {
    @Override
    public int compare(String o1, String o2) {
        return -1;
        //-1代表o2>o1
        // 0代表o1=o2
        // 1代表o1>o2
    }
});//DESC
```
`swap()` 交換元素位置
```java
Collections.swap(list, 0, 2); //index=0和2交換位置
```
`rotate()` 使List元素向右移幾位，最右邊元素會回到第一個
```java
// 1,2,3,4,5
Collections.rotate(list, 2);
// 4,5,1,2,3
```

<br/>

<br/>

## 查找/替換
`binarySearch()` 使用value找index，沒找到回傳-1
```java
list.add("aaa");
list.add("b");
list.add("c");

Collections.binarySearch(list, "c"); // return 2
```
`max()` 返回最大值
```java
Collections.max(List list, Comparator<> c);
```
`fill()` 全部元素填充為某個值
```java
// ["aa", "b", "c"]
Collections.fill(list, "bin");// ["bin", "bin", "bin"]
```
`frequency()` 找出元素重複次數
```java
Collections.frequency(list, "a");
```
`replaceAll()` 全部替換
```java
Collections.replaceAll(list, "aaa", "apple");
```

<br/>

<br/>

## 同步類
針對線程不安全的Collecion，包含`HashSet`, `ArrayList`,  `HashMap`

<br/>

`synchronizedList()` 重新產生一個線程安全的List，需要回存
```java
List<String> list = Collections.synchronizedList(new ArrayList<String>());
```
synchronizedSet()、synchronizedMap() 同理。


<br/>

<br/>


## 不可變集合
有以下三個方法: 

1. emptyXxx() : 返回一個`空的且不可修改的集合物件`。
2. singletonXxx() : 返回一個`只包含指定物件，不可修改的集合物件`。
3. unmodifiableXxx() : 返回一個`指定物件的不可變視圖`。

<br/>

`emptyList()`，通常用在拋轉資料時，回傳空集合時就不用再做判斷。
```java
public List<String> query(){
    if (notfound()){
        return Collections.emptyList();//此空List不能做任何改動
    }
    ...
}
```

<br/>

<br/>

## 其他類
`reverseOrder()` 返回一個比較器，但規則全部反轉。
```java
Collections.sort(list, Collections.reverseOrder());//DESC
```
