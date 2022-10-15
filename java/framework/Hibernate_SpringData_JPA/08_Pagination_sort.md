## 分頁功能
使用 findAll() 方法時，帶入參數 `PageRequest(int page, int size, Sort sort)`。
```java
// 返回的是 Page 類
Page<Student> result = studentRepository.findAll(
    Pageable pageable = PageRequest.of(0, 10)   // page 的 index 是從 0 開始算， 每頁10筆資料
);


result.getNumberOfElements(); // 本頁筆數
result.getSize();             // 每頁筆數 
result.getTotalElements();    // 全部筆數
result.getTotalPages();       // 全部頁數


// 轉成 List
List<Student> studentList = result.getContent();
```
<br/>

<br/>

## 排序功能
與上述相同，再帶入 Sort 參數

```java
// 使用 Sort.by() 選擇依照什麼欄位排序，並選擇由大到小
Page<Student> result = studentRepository.findAll(
    Pageable pageable = PageRequest.of(0, 10, Sort.by("updateTime").descending())
);
```