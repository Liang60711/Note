# PHP 8種基本資料型別，3種偽型別
### 標量型別  

1. 布林值(boolean)
2. 整數(integer)
3. 浮點數(float,double)
4. 字串(string)  

### 複合型別  
5. 物件(object)  
6. 陣列(array)

### 特別型別
7. 資源(resource)
8. 空值(null))

### 偽型別 (pseudo-types)
1. 數值(number)
2. 混合(mixed)
3. 回撥函數(callback)

<br/>

# 型別檢查
###  var_dump() 函式
### is_XXX() 函式，回傳 1 或 0
```php
$a = 123;
$b = "456";

// way 1 
var_dump($a);           // int(123)

// way 2  與 var_dump() 一起用
var_dump(is_int($b));   // bool(false)
```
### gettype() 函式
```php
echo gettype(123);      // integer
```

<br/>

# 型別轉換
* 自動轉換
    * 不太嚴謹的作法，通常還是會將變數做勤至轉換。
* 強制轉換
    * 臨時轉換  
        ```php
        // 除了 source 型別，其他都可以轉換
        $a = 123;
        $a = (string)$a;
        $a = (float)$a;
        ```
    * 使用函數臨時轉換
        ```php
        $a = 123;
        $a = intval($a);
        $a = string($a);
        ```
    * 永久轉換
        ```php
        $a = 123;
        settype($a, 'string');
        settype($a, 'array');
        ```



<br/>

# 變數
* 以 dollar sign 為開頭
* 可以用字母開頭，但數字不行
```php
<?php
    $variable = "abcd";
    echo $variable;
?>
```
可以用**底線**開頭
```php
<?php
    $_value = "value";
    echo $_value;
?>
```
### 雙引號 單引號
* 單引號執行效率比較快
* 單引號不解析 \，並把所有範圍內的當作字串
* 除了一個例外，內外都是雙引號或單引號，則可以使用跳脫字元 \ 。

```php
$number = 1;

// 雙引號才能解析變數
echo "$number";      // 1
echo "'$number'";    // '1'

// 單引號只能輸出字串
echo '$number';      // $number
echo '"$number"';    // "$number"


// 宣告跳脫字元只能用雙
$name1 = "abc\nabc";
$name2 = 'abc\nabc';
echo $name1;         // abc abc
echo $name2;         // abc\nabc

// 例外，內外都是雙引號，內外都是單引號
echo "\"aaa\"";      // "aaa"
echo '\'aaa\'';      // 'aaa'
echo "\'aaa\'";      // \'aaa'\
echo '\"aaa\"';      // \"aaa\"
```

## 命名法
1. camelCase；字首單字小寫，第二字首大寫，js, php
2. snake_case；底線區隔，Ruby
3. Pascal；單字字首皆大寫，C#

<hr>
<br/>


# 字串
* 英文字母為 1字元
* 中文為 3字元

<br/>

### 串聯運算子
. 可把字串或變數相加
```php
$string = "Hello" . " " . "World";

echo $string;                   // Hello world
echo $string . " " . "php";     // Hello world php
```

### html li 列表
```php
$listTitle = "List";
echo "<p>" . $listTitle . "</p>";
echo "<ul>" ;
echo "<li>" . "Item1" . "</li>";
echo "<li>" . "Item2" . "</li>";
echo "</ul>"
```

### var_dump 計算字串長度
```php
$string = "abc";
var_dump($string);      // 輸出 string(3) "abc"
```

### & 符號 不同的名字訪問同一個記憶體位址
```php
$a = 10;

// 將 $b 指向 $a 的記憶體
// 更改 $b 時，$a 也會一起更動
$b = &$a;

// 可以將變數刪除
unset($a);  // 只剩下 $b = 15;
```


<br/>

# 數字
### 整數型態
```php
$integer = 10;
var_dump($integer);     // int(10)


// 16進制
$integer = 0xff;
var_dump($integer);     // int(255)
```

### 浮點數操作
```php
$pi = 3.14159;

// 四捨五入 到小數第二位
$f1 = round($pi, 2);
echo $f1;               // 3.14
```

### int 和 float 轉換
```php
// intval()
$int1 = intval(3.14159);
var_dump($int1);             // int(3)


// floatval()
$float1 = floatval(3);
var_dump($float1);           // float(3)
```
### 字串 數字 浮點數 串聯
```php
// 型別不同但都可以串聯
echo "string" . 1 . 3.14;    // string13.14
```

### 無條件進位 無條件捨去 到整數位
雖然無小數點，但型別依然是 float
```php
// 無條件進位

var_dump(ceil(3.14));        // float(4)

//無條件捨去
var_dump(floor(3.14));       // float(3)
```


# Boolean
布林值 true false 大寫小寫均可以過
```php
// 列印 boolean 變數
$a = true;
echo $a;                    // 1


$b = false;
echo $b;                    // false 不會有返回值
```

<br/>

# $_GET, $_POST
* 為全域變數，在 function 內外都可以呼叫
* 此兩者變數為 PHP built-in 預設變數
* $_GET 以 Query String 格式，並用URL來傳遞參數
    
    ```php
    // 傳遞參數以 ?開頭，中間以 key=value 作為格式

    127.0.0.1:8000/test.php?key=value


    // php
    <?php
        echo $_GET["key"];           // 輸出 value
    
    ?>
    ```


* $_POST form-data 可以查看傳遞參數

    ```php
    <?php
        echo $_POST["key"];          // 此處 key 即是 form 表單 input 的 name 屬性

    ?>
    ```
# $GLOBALS
* 為 PHP 中 built in 變數
* 資料型別為 array
* 全域變數都會儲存在此 array


<br/>

# Array 
### 陣列宣告
1. PHP 的 Array 和 [key-value store 鍵值存儲] 是相同的
2. 若沒定義 Key，則會自動從 0 開始自動排列
```php
// Array()宣告
$array1 = Array("1", "2", "3");


// 中括號宣告
$array1 = ["1", "2", "3"];


// 索引值自定義
// 因為是 dict 型態，所以 index 順序沒差
// key 可以是 int 或 string
$array1 = Array(
    "key1" => "A",
    "key2" => "B",
    "key3" => "C",
);

// 等價
$array1 = [
    "key1" => "A",
    "key2" => "B",
    "key3" => "C",    
];

```

### 陣列取值
```php
// index 取值
$array1 = ["1", "2", "3"];

echo $array1[0];            // 1
```

### 陣列新增刪除 函數
```php
$array1 = ["1", "2", "3",];

// 新增一個值在最後
array_push($array1, "4");

// 刪除最後一個值
array_pop($array1);
```
### array_splice 函數
array_splice(array1, start, length, array2)
```php
// array1 要新增或修改的陣列
// start 要執行動作的 index，可支援 -1 (最後一項)
// length 從 start index 開始取代到幾個 index；例如: 6 為從 start 開始往後取代 6 個值；-3 為從 start 開始取代到 index 為 -3(倒數第3個)；0 為不取代，直接插入新值；不輸入則從 start 開始取代所有值。
// array2 只有一個 可以用 string 取代
// return 值為提取的元素(array)，比較特別。


$array1 = ["1", "2", "3", "4", "5", "6", "7", "8", "9", "10"];
$array2 = array_splice($array1, 7, -1, ["7.5"]);
print_r($array1);       // ["1", "2", "3", "4", "5", "6", "7", "7.5", "10"]
print_r($array2);       // ["8", "9"] return 提取的元素
```
### 陣列刪除
```php
// unset(array[index]);
// index 不會重新排序，拿掉1就會缺1
$array1 = [1, 2, 3, 4, 5];
unset($array1[0]);          // [2, 3, 4, 5]
```

### 陣列 轉為 JSON 格式(key: value)
```php
$array1 = [1, 2, 3, 4, 5];

json_encode($array1);                           // [1, 2, 3, 4, 5]
json_encode($array1, JSON_FORCE_OBJECT);        // {"0":1, "1":2, "2":3, "3":4, "4":5}
```

### 陣列 index 重新排序
```php
$array1 = [1, 2, 3, 4, 5];
unset($array1[1]);               // 把 index 1 刪除

// array_splice() 重新排序 index 
array_splice($array1, 1, 0, );

// array_values() 重新排序 index 
// 需要回傳
$array1 = array_values($array1);
```

<br/>

# 常數 const
* 以全大寫命名
* 常數宣告後，不可再進行變動
* 與全域變數差別是，全域變數可以更動值，但常數不行
* 可以宣告為 int, float, string, array
* 只能透過 class::const 來呼叫
## const 宣告 常數(常數為全域變數)
```php
// 宣告常數 通常為大寫
const ABC = "ABCABCABC<br/>";

// 常數無法放在字串中使用
echo "{ABC}";       // {ABC}
echo ABC;           // ABCABCABC
```

## define()
* 參數1 要放字串，參數2 型別可以為 int, float, string, array
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

<br/>

## 檢查常數是否被使用
```php
const A = "APPLE";

var_dump(defined("A"));     // bool(true)
```

## 魔術常數 (magic constants)
```php
// 當前行數
__LINE__

// 文件完整路徑
__FILE__

// 當前目錄
__DIR__

// 當前函式
__FUNCTION__

// 當前類別
__CLASS__
```