children() 會遍歷取所有子元素(第1層)，但不會取所有後代元素
```js
// 取第2個子元素
$('tr').children('td').eq(2);

// 抓取所有子元素
$('#tr').children('td.child');
```