# function
* 宣告方式和 js 相同
* return 可以不放變數
* 全域變數在 function 中需要宣告 global(和 js 差別)
```php
function printHelloWorld(){
    echo "Hello World<br>";
    return;
}

printHelloWorld();
```

### 範例 秒轉換時間
```php
// $totalSecond 為總秒數
function secondToTime($totalSecond){
    $hour = intval($totalSecond / (60*60));
    $minute = intval($totalSecond / 60) % 60;
    $second = $totalSecond % 60;

    return array($hour. "小時", $minute. "分", $second. "秒", );

}

// list() 函式可將 array 中的元素宣告成變數
list ($hour, $minute, $second) = secondToTime(32583);
echo "$hour";
```

### function 預設值
```php
function mathFunction($x, $y=1){        // $y 預設為 1
    return $x*2 + $y*3;
}

echo mathFunction(1);
```

### const 宣告 常數(常數為全域變數)
* 常數宣告後，不可再進行變動
* 與全域變數差別是，全域變數可以更動值，但常數不行
* 可以宣告為 int, float, string, array
```php
// 宣告常數 通常為大寫
const ABC = "ABCABCABC<br>";

// 常數無法放在字串中使用
echo "{ABC}";       // {ABC}
echo ABC;           // ABCABCABC
```

### define()
* 參數1 要放字串，參數2 可以為 int, float, string, array
```php
// 宣告常數
define("CONSTANT", "Hello world.");
echo CONSTANT;      // Hello world.


// 可以宣告為 array，只能用在 PHP 7 以上
define('ANIMALS', array(
    'dog',
    'cat',
    'bird'
));

echo ANIMALS[1];    // cat
```

### const 和 define()
* PHP 兩者作用很類似，常界定模糊
* const 用在 class 中設定常數
* define 用在設定檔中


<br>

# 巢狀迴圈
* 在維護巢狀迴圈時，標籤會很亂，解決方法為使用 function

```php
// 原本的寫法
$students = [
    [
      "姓名" => "小明",
      "數學成績" => 30,
      "英文成績" => 20,
      "歷史成績" => 91,
    ], 
    [
      "姓名" => "老王",
      "數學成績" => 89,
      "英文成績" => 82,
      "歷史成績" => 83,
    ], 
    [
      "姓名" => "小美",
      "數學成績" => 65,
      "英文成績" => 99,
      "歷史成績" => 87,
    ], 
  ];

// 維護上需要嵌入很多標籤
echo "<table border=1>";
echo "<tbody>";
foreach($students as $student){
    echo "<tr>";
    foreach($student as $key => $value){
        echo "<td>";
        echo "{$key}: {$value}";
        echo "</td>";
    }
    echo "</tr>";
}
echo "</tbody>";
echo "</table>";
```
改成以下方式
```php
// 宣告 兩個 function
function createTr($data){
    echo "<tr>";
    foreach($data as $key => $value){
        echo "<td>";
        echo "{$value}";
        echo "</td>";
    }
    echo "</tr>";
}

function createTable($datas){
    echo "<table border=1>";
    // thead
    echo "<thead>";
    echo "<tr>";
    foreach($datas[0] as $key => $value){
        echo "<th>";
        echo $key;
        echo "</th>";
    }
    echo "</tr>";
    echo "</thead>";

    // tbody
    echo "<tbody>";
    foreach($datas as $data){
        createTr($data);
    }
    echo "</tbody>";
    echo "</table>";
}

// 執行
createTable($students);
```