# 集合 collection 

<img src="https://ithelp.ithome.com.tw/upload/images/20200913/201195698StPc8wLH1.jpg">

<br/>

<br/>


# List
1. 會依序存放物件 (代表有index)
2. 可以存放重複的物件
3. 種類有
    * ArrayList
    * Vector
    * LinkedList

4. 如何選擇list?
    1. 考慮安全性問題，是否執行多線程。
    2. 是否頻繁進行插入、刪除(LinkedList)。
    3. 儲存後是否需要遍歷。


### ArrayList
1. 為動態陣列，可以儲存不同類型的物件，但不建議，故常使用泛型去限制ArrayList。
2. 第一次添加元素，擴充容量為10，之後的擴充算法為`原來陣列大小+原來陣列長度的一半`(源碼)。
3. 不適合進行刪除或插入，因為index會變。
4. 為了防止陣列動態擴充次數過多影響效能，建立ArrayList時，給定初始容量。
    ```java
    // initialCapacity 初始容量
    List<String> list = new ArrayList<>(20);
    ```

5. `線程不安全`，適合在單線程使用，效率較高。

```java
List<String> list = new ArrayList<>();//JDK1.7後可省略String
String str = "str";

// 新增
list.add(str);

// 取值
list.get(2); // index

// 刪除
list.remove(str);   // object
list.remove(2);     // index

// 取長度
list.size();

// 遍歷集合
int size = list.size();
for(int i=0; i<size; i++){
    System.out.print(list.get(i));
}

// 包含
list.contains("str");

// 更新元素
list.set(2, "str2");//參數 (int index, E element)

// 轉換成Array
String[] array = list.toArray(new String[]{});// toArray()參數加上泛型，需new一個陣列
```

<br/>

<br/>

### Vector
1. 也是採用動態陣列實現，預設的建構子，會建立一個長度為10的陣列。
2. 擴充算法為，當增加的量為0時，擴充為原來大小的2倍，當增量大於0時，擴充為原來大小+增加的量。
3. 不適合進行刪除或插入。
4. 為了防止陣列動態擴充次數過多影響效能，建立Vector時，給定初始容量。
5. `線程安全`，適合多線程，在單線程下使用效率較低。

```java
Vector<String> v = new Vector<>();
```

<br/>

<br/>

### LinkedList
1. 實現原理，採用雙向LinkedList結構。
2. 適合插入、刪除，性能高。

```java
LinkedList<String> list = new LinkedList<>();
```

<br/>

<br/>

# set
1. 元素為無序的(不保證順序)
2. 不允許重複元素。
3. 種類有
    * HashSet
    * TreeSet
    * LinkedHashSet
    

### HashSet
1. 實現原理，使用`HashMap`實現，新增元素時，會先計算元素的hashCode值，取餘後，決定將物件存在雜湊表的哪個位置中。
2. 不允許重複，可以有一個null元素。
3. 不保證順序恆久不變。
4. 排除重複元素是透過`equals`檢查，若多new一個相同的元素，則還是可以添加進HashSet。
    ```java
    Cat c1 = new Cat("A");
    Cat c2 = new Cat("B");
    Cat c3 = new Cat("C");
    Cat c4 = new Cat("A");

    Set<String> set = new HashSet<>();
    set.add(c1);
    set.add(c2);
    set.add(c3);
    set.add(c4);//可以添加
    set.add(c1);//無法添加
    ```
5. `Object.hashCode()` 用來支援HashMap，HashSet等需要計算該物件的hash值做為存放位置的索引(bucket number)的方法。
6. 在HashSet中，判斷兩個物件是否相同會做以下判斷(基於不能存放重複元素的原則，新增元素時都會做以下篩選)
    * 第一關會先判斷兩個物件的HashCode是否相同(兩個物件的HashCode相同，不一定是同個物件)
    * 第二關再使用`equals`判斷其他屬性是否相同。
    * 若第一第二關判斷完，結果都是true，則新元素不加入倒hashSet中。

    ```java
    Cat c1 = new Cat("AAA", 1,1);
    Cat c2 = new Cat("BBB", 2,2);
    Cat c3 = new Cat("AAA", 1,1);

    // 在沒有複寫equals() 和 hashCode()時，c1和c3的hashCode不相等
    System.out.println(c1.hashCode() == c2.hashCode());//false
    System.out.println(c1.hashCode() == c3.hashCode());//false

    // 複寫過，只要屬性相同，hashCode就會相同，故c3無法進hashSet
    System.out.println(c1.hashCode() == c2.hashCode());//false
    System.out.println(c1.hashCode() == c3.hashCode());//true

    /////////////////////////////////
    class Cat{
        private String name;
        private int age;
        private int id;

        @Override
        public boolean equals(Object o) {
            if (this == o) return true;
            if (!(o instanceof Cat)) return false;
            Cat cat = (Cat) o;
            return age == cat.age && id == cat.id && Objects.equals(name, cat.name);
        }

        @Override
        public int hashCode() {
            return Objects.hash(name, age, id);
        }

        public Cat(String name, int age, int id) {
            this.name = name;
            this.age = age;
            this.id = id;
        }

    }
    ```
7. 呈上，自定義物件(Cat)需要判斷各屬性都相同時為同一個物件(c1和c3)，若有這樣的需求時，需要重寫物件的`hashCode()`和`equals()`方法。


HashSet方法
```java
Set<String> set = new HashSet<>();
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

### TreeSet

<br/>

<br/>


# map
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