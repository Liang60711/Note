## 集合 collection 

## List
1. 會依序存放物件 (代表有index)
2. 可以存放重複的物件

    ```java
    // List是一個interface，以下有許多實作的類，其中一個是ArrayList
    List<String> list = new ArrayList<String>();
    ```
3. 方法

    ```java
    List<String> list = new ArrayList<String>();
    String str = "str";

    // 新增
    list.add(str);

    // 取值
    list.get(2); // index

    // 刪除
    list.remove(str);   // object
    list.remove(2);     // int

    // 取長度
    list.size();
    ```

<br/>

<br/>

## set
1. 不會依序存放物件
2. 存放物件不會重複

    ```java
    // Set為interface，HashSet為實作的類
    Set<String> set = new HashSet<String>();
    ```

3. crud

    ```java
    Set<String> set = new HashSet<String>();
    String str = "str";

    // 新增
    set.add(str);

    // 取值，iterable

    // 刪除(不能輸入index，set沒有順序)
    set.remove(str);

    // 取長度
    set.size();
    ```

<br/>

<br/>


## map
1. 將物件以 key, value 方式儲存
2. 使用 key 取得存放物件

    ```java
    Map<String, Integer> map = new HashMap<String, Integer>();
    ```

3. 方法

    ```java
    Map<String, Integer> map = new HashMap<String, Integer>();
    String str = "str";

    // 新增
    map.put("key1", 1);  // 參數為 (key, value)

    // 取value值
    map.get("key1")     // 1

    // 取所有值(先取所有的keySet)，再用for取值
    // map.keySet() 為取所有的key
    for(String key : map.keySet()){
        System.out.println(map.get(key));
    }

    // 刪除
    map.remove(str);
    ```


<br/>

<br/>

## package
collection 都是存放在 `java.util` package下