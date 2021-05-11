# function
* 宣告方式和 js 相同
* return 可以不放變數
* 全域變數在 function 中需要宣告 global(和 js 差別)
```php
function printHelloWorld(){
    echo "Hello World<br/>";
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

<br/>

<br/>

# 陣列函式
### 排序類
```php
$arr1 = [1,6,3,8,9];
$arr2 = [
    'a' => '7',
    'c' => '2',
    'b' => '5'
];

// 索引陣列 小>>大
sort($arr1);        // [1,3,6,8,9]

// 索引陣列 大>>小
rsort($arr1);       // [9,8,6,3,1]

// 結合陣列  value 小>>大
asort($arr2);      

// 結合陣列  value 大>>小
arsort($arr2);

// 結合陣列  key 小>>大
ksort($arr2);      

// 結合陣列  key 大>>小
krsort($arr2);

// 檢查 value 是否在 array
in_array(7, $arr2);     // return 1
```

<br/>

<br/>

# 字串函式
```php
// string 長度
strlen();

// 回傳小寫
strtolower();

// 回傳大寫
strtoupper();

// 刪除前後空白
trim();

// 只刪除左右空白
ltrim();
rtrim();

// 切割字元(類似 python strip() )
$str = 'aaa|bbb|ccc';
explode('|', $str); // 回傳 array

// 字串陣列 轉 字串
// 預設第一參數是空字串
$str = ['aaa', 'bbb', 'ccc'];
join(':', $str)     // 回傳 aaa:bbb:ccc
```

<br/>

<br/>

# 匿名函式 (Closure)
function 沒有名稱，但仍可傳入參數
```php
// 沒有參數
$str1 = function(){
    echo 'Anonymous functions';
};      // 這邊要分號
$str1();

// 有參數
$str2 = function(String $para1){        // 參數可指定型別
    echo 'Anonymous functions: ';
    echo $para1;
};
$str2("parameter");
```

<br/>

# 可變參數函式
可以透過函式來檢查傳入的參數數量
* func_num_args()
* func_get_arg($index)
* func_get_args()
```php
// 例如 username, password, address, phone_number，如果後兩個參數為選填時，一般參數寫法會出問題

function text(){
    echo func_num_args();           // 回傳參數數量
    echo "<br/>";

    echo func_get_arg(0);           // 輸入 index 可取參數數值(arg沒加s)
    echo "<br/>";

    print_r(func_get_args());       // 回傳參數陣列
}



text("username", "password", "taipei");
// 3
// username
// Array ( [0] => username [1] => password [2] => taipei )
```

<br/>

<br/>

# 打印
```php
// 輸出 array
print_r

// 輸出 format
printf

$a = 1;
$b = "Taipei";
printf("%s is no. %d", $b, $a);  // Taipei is no. 1
```

<br/>

<br/>

# 全域變數、區域變數 函數
### $GLOBALS()
回傳 array
```php
// 用字串取 $GLOBALS() 內容
print_r($GLOBALS()['_GET']);
```

### get_defined_vars()
```php
// 只能在區域內使用
function test(){
    $a = 1;
    $b = 2;
    return (get_defined_vars());
}

print_r(test()['b']);   // 2
```

<br/>

<br/>

# 時間函數

```php
// 回傳 timestamp
time();

// 回傳 Format 時間
// 05/11/2021 09:50:38 *** Tuesday May
date('m/j/Y h:i:s *** l F');

// 調整時區
date_default_timezone_set('Asia/Taipei');
```