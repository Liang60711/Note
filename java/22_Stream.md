> https://matthung0807.blogspot.com/2021/01/java-8-stream-pipeline-operations-intro.htm

## Stream
1. 可以對collection操作，處理數據的工具。
2. 屬於`lazy loading`，數據只能被使用一次。
3. 有兩種操作方法
    
    * 中間操作: 生成/返回一個stream，可以讓stream繼續處理。
    * 結束操作: 返回值不是stream，而是執行計算操作，例如`forEach`

4. 通常會搭配lambda一同使用。
5. 與laravel的collection類似功能。

<br/>

<br/>

## 建立stream
```java
// of()，建立一個stream
Stream<String> stream = Stream.of("aaa", "bbb", "ccc", "ddd");

// stream()，Collection介面下有預設一個stream()，將集合轉換為Stream
List<Integer> list = new ArrayList<>();
Stream<Integer> ss = 
```

## forEach
遍歷
```java
stream.forEach(System.out::println);
```

## sorted
依照元素進行排序
```java
// ASC
List<LedDeviceList> test = ledDeviceLists.stream()
    .sorted(Comparator.comparing(LedDeviceList::getLdlId))
    .collect(Collectors.toList());

// DESC
List<LedDeviceList> test = ledDeviceLists.stream()
        .sorted(Comparator.comparing(LedDeviceList::getLdlId).reversed())
        .collect(Collectors.toList());
```

## filter
```java
Stream<String> stream = Stream.of("aaaa", "bbbb", "ccc", "ddd");

// filter() 參數為 Predicate
stream.filter((str) -> str.length()>3 );//filter會遍歷元素並return條件符合的元素，最終還是返回stream

// 故可以filter() + forEach印出
stream.filter((str) -> str.length()>3).forEach(System.out::println);

// aaaa
// bbbb
```

## distinct
過濾掉相同元素
```java
// 同樣是中間操作
stream.distinct();

// 打印
stream.distinct().forEach(System.out::println);
```

## map
修改原stream後，並映射到新的stream後回傳
```java
stream.map((s)->{
    if(s.length() > 3){
        return s.toUpperCase();
    }
    return s;
}).forEach(System.out::println);
```

## flatMap
將多維stream降至一維
```java
// 泛型一定要加
Stream<List<Integer>> ss = Stream.of(Arrays.asList(1,2,3), Arrays.asList(4,5,6));

// stream() 是 Collection interface的default方法
ss.flatMap(list -> list.stream()).forEach(System.out::println);
```

## reduce
聚合方法，可以遍歷將每個元素，聚合成一個結果
```java
List<Integer> list = Arrays.asList(1,2,3,4,5,6);

// acc為聚合結果, n為遍歷的元素
// 若list有可能為0個元素時，reduce()就無法使用，所以必須用Optional去接返回值
Optional<Integer> opt = list
                        .stream()
                        .reduce((acc, n) -> acc + n);

if (opt.isPresent()) {
    System.out.println(opt.get());//21
}
```
獲取長度最長的字串
```java
Stream<String> stream = Stream.of("aaaaa", "bbbb", "ccc", "ddd");

// 同樣是遍歷每個元素，並返回聚合值，這邊返回的是每次比較較長的值
Optional<String> opt = stream
                        .reduce((s1, s2)-> s1.length() >= s2.length() ? s1: s2);

System.out.println(opt.get());
```

## collect
將Stream轉換成Collection
```java
Stream<String> stream = Stream.of("aaaaa", "bbbb", "ccc", "ddd");

List<String> list = stream.collect(Collectors.toList());
```

<br/>

<br/>

## Collectors.groupingBy
將 `List<Object>` 依照某元素分組，回傳 `Map<T, List<Object>>`，T的型別為某元素的型別
```java
// getStudentId 為 Integer，回傳的 Map key 也為 Integer
List<Student> list = new ArrayList<>();
Map<Integer, List<LedData>> groupMap = list.stream().collect(Collectors.groupingBy(Student::getStudentId));
```