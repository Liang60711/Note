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
2. HashSet底層是HashMap，故在儲存資料時，是使用HashMap的put()方法
    
    ```java
    // java.util.HashSet
    private static final Object PRESENT = new Object();

    // 將新元素(e)儲存進key
    // 會將一個填充物PRESENT儲存進value
    public boolean add(E e){
        return map.put(e, PRESENT)==NULL;
    }
    ```

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
    * 若第一第二關判斷完，結果都是true，則新元素不加入hashSet中。

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
    // 自定義類中，可以Override這兩個方法，讓相同屬性的物件不會被重複加入到HashSet中。
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
7. 呈上，自定義物件(Cat)需要判斷各屬性都相同時為同一個物件(c1和c3)，若有這樣的需求時，需要重寫物件的`hashCode()`和`equals()`方法，或是加入 lombok 中的註解 `@EqualsAndHashCode`。


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
    Comparator<Cat> com = (Cat o1, Cat o2) -> (o1.id - o2.id);//只有一行可以省略大括號，加上小括號；也可以不加小括號
    TreeSet<Cat> tree = new TreeSet<>(com);
    ```
3. 因為使用了`Comparator`，可實現兩個功能:
    1. 可以比較順序。
    2. 可以用來排除重複的元素(因為有進行比較，如果元素相同，則不會add進TreeSet)。

4. 若自定義的類沒有implements Comparator，則無法添加到TreeSet中(TreeSet必須是有序的)。

<br/>

<br/>

### LinkedHashSet
1. 使用`array` + `LinkedList` 實作成 `HashTable`；陣列用來表示元素的位置，鍊表用來保證元素的插入順序。

<br/>

<br/>


# map
1. 將物件以 key, value 方式儲存
2. key不能重複，value可以重複。
3. 具體實現類

    * HashMap
    * TreeMap
    * HashTable
    * LinkedHashMap

4. 總結:
    * 不要求元素排序: 
        * 單線程: `HashMap`
        * 多線程: `HashTable`

    * 要求元素排序
        * 按照插入(新增)順序排序: `LinkedHashMap`
        * 按照key值排序: `TreeMap`

<br/>

<br/>


### HashMap
1. 實現原理是使用`array` + `LinkedList` + `紅黑樹`，java8加入紅黑樹是為了防止LinkedList過長。

    * 補充: 紅黑樹相較於一般二元樹是確保樹的某些分支左右平衡，遍歷時效能比較好。

2. 儲存過程: 將`key`值取hashCode，並使用陣列長度(初始值為16)對其取餘數，碰撞時則使用LinkedList串聯，串聯過多(超過8)則改成紅黑樹；當陣列儲存達75%時，陣列會進行擴充，擴充算法為`<<1，相當於*2` (`resize()方法`)，每次擴充表示HashTable會重新`散列`(重新取餘，計算每個物件的儲存位置)，所以須盡量減少擴充次數。

3. `default initial capacity` 預設初始容量為 `16`，元素超過此數則會重新建構。
4. `loadFactor` 為 `0.75`，此載入因子表示`Hash表中元素的填滿的程度`，至於為什麼為這個數，因需在`雜湊衝突`和`空間利用率`取折衷。
5. HashTable查找速度快是因為不用遍歷，所以當其中某一元素的LinkedList過長時(碰撞過多)，則需花費時間在LinkedList的遍歷上造成速度變慢，所以當LinkedList的長度大於等於`TREEIFY_THRESHOLD = 8`這個長度時，則會將此元素的LinkedList結構改變成紅黑樹 (class `TreeNode`)。

6. 線程不安全，適合在單線程使用。


```java
// 宣告
Map<Integer, String> map = new HashMap<>();

// 新增
map.put(1, "aaa");

// 取值
map.get(1);

// 長度
map.size();
```
遍歷方式
```java
Map<Integer, String> map = new HashMap<>();

// 1.map.entrySet()
// 一個 entry 就是一個 key + 一個 value
// entrySet() 就是返回許多 entry，即 Map.Entry 類組成的 Set
Set<Map.Entry<Integer,String>> entrySet = map.entrySet();
for(Map.Entry e: entrySet){
    System.out.println(e.getKey()+ "->" + e.getValue());
}

// 2.map.keySet()，取所有key
Set<Integer> keys = map.keySet();
for(Integer i: keys){
    System.out.println(i + "->" + map.get(i));
}

// 3.map.value()直接返回Collection，沒有key
Collection<String> values = map.values();

// 4.map.forEach()
map.forEach((k,v)->System.out.println(k+"->"+v));
```


<br/>

<br/>

## HashTable
* HashMap和HashTable的區別
 
    1. HashTable是jdk1.0開始，比較舊。
    2. 預設陣列大小: `11`，加載因子loadFactor: `0.75`。
    3. 擴充方式: 當前陣列容量 *2 + 1
    4. 沒有用到`紅黑樹`結構。
    5. 線程安全，用在多線程。

* 用法幾乎與HashMap相同

    ```java
    Map<String, String> table = new Hashtable<>();
    table.put("one", "aaa");
    table.put("two", "bbb");
    table.put("three", "ccc");

    table.forEach((k,v)->System.out.println(k+ "->" +v));
    ```

<br/>

<br/>


## LinkedHashMap
1. LinkedHashMap 是 HashMap 的子類。
2. 由於 HashMap 不能保證元素順序恆久不變，擴充時進行的`散列`可能造成元素順序改變。


<br/>

<br/>

## TreeMap
1. 實現原理基於`紅黑樹`，需要排順序，故加入元素時，需要進行比較。
2. 與`TreeSet`相同的是，自定義的類在加入元素時，需要實作`Comparator`。

    ```java
    // 1.重寫compare()
    public class Dog implements Comparator<Dog> {
        private int id;
        private int age;
        private String name;

        public int getId() {
            return id;
        }

        
        public Dog(int id, int age, String name) {
            this.id = id;
            this.age = age;
            this.name = name;
        }

        @Override
        public int compare(Dog o1, Dog o2) {
            return o1.id - o2.id;
        }
    }
    ```
    ```java
    // 2. lambda，TreeMap只有key才能做排序
    Comparator<Dog> com = (Dog o1, Dog o2) -> o1.getId()-o2.getId();

    Map<Dog, Integer> dogs = new TreeMap<>(com);
    ```
    ```java
    // 3. 順序顛倒 ReverseComparator
    Comparator<Dog> com = (Dog o1, Dog o2) -> o1.getId()-o2.getId();

    Map<Dog, Integer> dogs = new TreeMap<>(new ReverseComparator(com));
    ```


<br/>

<br/>

## MultiValueMap
1. 由 Map 和 List 組成，用途是一個 key 可以存多個 value
2. 實現原理

    ```java
    // map 的 value 使用 list 來代替，實現可以存放多個值
    public interface MultiValueMap<K, V> extends Map<K, List<V>> {
    }
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


<br/>

<br/>

## 集合方法
`getOrDefault()` 可以設定default值
```java
Map<Integer, String> map = new HashMap<>();

map.getOrDefault(87, "null");//default與value必須是同型
```
`putIfAbsent()` 檢查key值沒有重複才加入元素，`防止覆蓋原本的value`
```java
map.put(1,"apple");
map.put(2,"bb");
map.put(3,"cc");

map.putIfAbsent(3, "dd"); // 已有key=3，返回已有元素"cc"
map.putIfAbsent(4, "dd"); // 成功加入，返回null
```
`remove(key, value)` 比 remove(key) 更嚴謹，鍵值都相同才會刪除
```java
map.remove(1, "apple");
```
`replace(key, oldValue, newValue)` 比 put(k,v) 和 replace(k,v) 嚴謹，會先檢查舊值，確定有才替換
```java
map.replace(1, "cc", "dd");
```
`merge(key, value, remappingFunction)`

如果key不存在，執行 `put(key, value)`，如果key已經存在，則 remappingFunction可以選擇合併的方式，並將newValue賦給key。

```java
map.put(1,"apple");
map.put(2,"bb");
map.put(3,"cc");

map.merge(4, "dd", (oldVal, newVal)->oldVal.concat(newVal));//只會執行put(4,"dd");

map.merge(1, " good", (oldVal, newVal)->oldVal.concat(newVal));// (1, "apple good");
```
可以使用在物件
```java
Map<Integer, Dog> map = new HashMap<>();
map.put(1, new Dog(1,"dog1"));  // Dog(age, name);
map.put(2, new Dog(2,"dog2"));
map.put(3, new Dog(3,"dog3"));

Dog dog4 = new (4, "dog4");

map.merge(1, dog4, (oldVal, newVal) -> new Dog(oldVal.age, newVal.name));
// oldVal = Dog(1, "dog1");
// newVal = Dog(4, "dog4");
// key = 1，value = Dog(1, "Dog4");
```