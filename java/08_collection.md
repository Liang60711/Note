# 集合 collection 

<img src="https://ithelp.ithome.com.tw/upload/images/20200913/201195698StPc8wLH1.jpg">

<br/>

<br/>

## package
collection 都是存放在 `java.util` package下

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

4. 總結:
    * 要求排序選`TreeSet`
    * 不要求排序選`HashSet`
    * 不要求排序但是要保持插入順序選`LinkedHashSet`
    

### HashSet
1. 實現原理，使用`HashMap`實現，新增元素時，會先計算元素的hashCode值，取餘後，決定將物件存在雜湊表的哪個位置中。
2. 不允許重複，可以有一個null元素。
3. 不保證順序。
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
1. TreeSet是基於TreeMap去實現，TreeMap是一個有序的二元樹，故TreeSet必須也要是有序的，這點與HashSet不同。
2. 如果是自定義的物件加入TreeSet，需要new `comparator`，來比較順序。

    ```java
    // 若為Interger則會使用已寫好的comparator比較
    TreeSet<Integer> tree = new TreeSet<>();
    tree.add(1);
    tree.add(2);
    tree.add(3);
    // 可以自定義 Integer 的順序
    TreeSet<Integer> tree = new TreeSet<>(new Comparator<Integer>(){
        @Override
        public int compare(Integer o1, Integer o2){
            return o1 - o2; //此時順序會是1,2,3，這是默認寫法
        }
    });
    ```
    自定義的物件，有3種寫法

    ```java
    // 1.直接複寫
    TreeSet<Cat> tree = new TreeSet<>(new Comparator<Cat>(){
        @Override
        public int compare(Cat o1, Cat o2){
            return o1.id - o2.id;   //依照id排順序
        }
    });
    

    // 2.使用繼承的類當參數
    TreeSet<Cat> tree = new TreeSet<>(new CatComparator());
    
    class CatComparator implements Comparator<Cat>{
        @Override
        public int compare(Cat o1, Cat o2){
            return o1.id - o2.id;   //依照id排順序
        }
    }

    // 3.使用lambda
    Comparator<Cat> com = (Cat o1, Cat o2) -> (o1.id - o2.id);//只有一行可以省略大括號，加上小括號；也可以不佳小括號
    TreeSet<Cat> tree = new TreeSet<>(com);
    ```
3. 因為使用了`Comparator`，可實現兩個功能:
    1. 可以比較順序。
    2. 可以用來排除重複的元素(因為有進行比較，如果元素相同，則不會add進TreeSet)。

4. 若自定義的類沒有implements Comparator，則無法添加到TreeSet中(TreeSet必須是有序的)。

<br/>

<br/>

### LinkedHashSet
1. 使用`Hash table` + `LinkedList` 實作；雜湊表用來表示元素的唯一，鍊表用來保證元素的插入順序。

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

## 集合輸出
通常集合輸出分為以下幾種: 
1. **Iterator**
2. ListIterator
3. Enumeration
4. **foreach**

<br/>

### Iterator 迭代器
jdk1.5前都用此方式
```java
// 第一次使用next()是返回第一個元素
private static void iterator(Collection<Cat> c){
    Iterator<Cat> iter = c.iterator();
    while(iter.hasNext()){
        System.out.println(iter.next());
    }
}
```


### ListIterator
只有`list`可以使用。可以向前/後進行迭代，使用hasPrevious() / hasNext()
```java
private static void listIterator(List<Cat> c){
    ListIterator<Cat> li = c.listIterator();
    while(li.hasNext()){
        System.out.println(li.next());
    }
}
```


### Enumeration 枚舉
jdk1.0，被認為是過時代碼
```java
private static void enumeration1(){
    Vector<Cat> vs = new Vector<>();
    vs.add(new Cat("A", 1,1));
    vs.add(new Cat("b", 2,2));
    vs.add(new Cat("c", 2,2));
    vs.add(new Cat("d", 2,2));

    Enumeration e = vs.elements();

    while(e.hasMoreElements()){
        System.out.println(e.nextElement());
    }
}
```


### foreach
jdk1.5，最簡單的用法
```java
private static void foreach(Collection<Cat> c){
    foreach(Cat cat : c){
        System.out.println(cat);
    }
}
```
jdk1.8，Lambda Collection forEach()
```java
List<Cat> list = new ArrayList<>();

// 1. forEach + lambda
list.forEach(s -> System.out.println(s));// 參數只有一個省略小括號

// 2. forEach + lambda + Method References
list.forEach(System.out::println);//調用靜態方法，並將省略的參數丟進println方法內

// 3. 其實原本forEach寫法是這樣
list.forEach(new Consumer<Cat>() {
    @Override
    public void accept(Cat cat) {
        System.out.println(cat);
    }
});
```
Iterable介面新增了forEach()方法，參數為Consumer介面的實作(action)，而Consumer是只有一個抽象方法(accept())的Functional Interface，所以可以用Lambda語法改寫。

```java
// java.lang.Iterable
public interface Iterable{

    default void forEach(Consumer<? super T> action) {
        Objects.requireNonNull(action);
        for (T t : this) {
            action.accept(t);//每次foreach會執行accept這個方法
        }
    }
}
```
```java
// java.util.function.Consumer
public interface Consumer<T> {
    void accept(T t);//Consumer類只有一個accept方法，故可以用lambda複寫。
}
```