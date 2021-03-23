# 數值運算
### 四則運算範例
```php
// 秒 換算成 小時:分鐘:秒

$total_seconds = 1783;

$hour = intval($total_seconds / (60 * 60));       // floor() 也可以
$minute = intval($total_seconds / 60) % 60;
$second = $total_seconds % 60;

echo $total_seconds . "秒" . " 等於 " . $hour. "小時" .$minute. "分" .$second. "秒";
```

### 字串相加
會自動轉型，然後相加(可能會混淆)
```php
var_dump("1" + "2");        // int(3)
```

<br>

# 比較運算 邏輯運算

### if elseif else
```php
$number = 11;

if ($number > 10 ){
    echo "greater than " . 10;
}elseif ($number < 10){
    echo "less than " . 10;
}else{
    echo "equals to " . 10;
}

// greater than 10
```

### 比較 == 以及 ===
```php
$string = "10";
$number = 10;

// == 為比較兩者的值(即使型別不同)
if ($string == $number){
    echo "true";
}else{
    echo "false";
}
// 輸出 true



// === 為比較兩者的值、型別
if ($string === $number){
    echo "true";
}else{
    echo "false";
}
// 輸出 false
```

### 不等於
有兩種寫法 != 和 <>
```php
$number = 10;

if ($number != 0){
    echo "not 0";
}


if ($number <> 0){
    echo "not 0";
}
```
連型別一起比較
```php
$number = 10;

if ($number !== 0){
    echo "not 0";
}
```

### and (&&) or (||)
and or 需要 小括號
```php
$number = 10;

if (($number !=0) && ($number >=5)){
    echo "true";
}else{
    echo "false";
}
```

### 空字串 ; 0 ; null
```php
if ( "" ){
    echo "true";
}else{
    echo "false";
}
// false




if ( 0 ){
    echo "true";
}else{
    echo "false";
}
// false
// 非0的正負數(int, float)則是回傳true



if ( null ){
    echo "true";
}else{
    echo "false";
}
// false
```
null 會沒辦法進入區塊，所以多半會賦值給null
```php
$result = null;

if (null == $result){       //  等價 $result == null
    $result = 0;
}
echo $result;
// 0
```

<br>

### 三元運算子
```php
// 一般寫法
$result = true;

if ($result == true){
    echo "true";
}else{
    echo "false";
}


// 三元運算子
// 省略 if else
echo ($result == true) ?  "true" :  "false";
```

<br>

### switch case
1. 記得每個 case 都要 break，否則會繼續執行程式到下一個 break
2. switch 可以放條件或變數
3. default 與 else 意思相同
4. switch case 會把 "1" 和 1 判定相等
```php
$number = 1;

switch($number){
    case 0:
        echo 0;
        break;
    case 1:
        echo 1;
        break;
    case 2:
        echo 2;
        break;
    default:
        echo "none";
}
```
string 和 int 會判定相等 == 
```php
$number = 1;
switch($number){
    case "1":
        echo "1";
        break;
    default:
        echo "none";
}
```

空 _GET, 空 _POST 和 Array() 會判定完全相等 ===
```php
$a = Array();

switch($a){
    case $_GET:
        echo "_GET";
        break;
    case $_POST:
        echo "_POST";
        break;
}
```


### while, do while
```php
// 印出10個 HI
$count = 0;
$flag = true;

while($flag){
    echo "HI";
    echo "<br>";

    $count += 1;
    $flag = ($count != 10);
    /* 省略 if 的寫法
        if($count == 10){
        $flag = false;
        };
    */
};



// 簡化不用 flag
$count = 0;
while($count != 10){
    echo "HI";
    echo "<br>";
    $count --;      // -- 為減1
}


// 簡化 將 -- 丟進 while 條件
// ++ -- 會在加減到 0 時跳出
$count = 10;
while($count --){
    echo "HI";
    echo "<br>";    
};


// ++ -- 可放在變數前面
++$count        // 先 +1 再回傳 $count
$count++        // 先回傳 $count 再 +1
```

用 while 印出 array
```php
$array = ["A", "B", "C"];

$count = count($array);         // count() 可回傳 $array 長度
while($count--){
    echo $array[$count];
    echo "<br>";    
};

// 會印相反
// C
// B
// A


// 需要多加一個 變數 
$array = ["A", "B", "C"];
$count = count($array);

$index = 0;

while($count --){
    echo $array[$index];        // 改成 index
    echo "<br>";
    $index += 1;
}


// 所以 while 迴圈比較擅長 倒著 印出
// for 迴圈比較擅長 正著 印出
```
### do while
確保會至少跑一次迴圈
```php
$array = ["A", "B", "C"];
$count = count($array);

$index = 0;

do{
    echo $array[$index];
    echo "<br>";
    $index += 1;
}while(--$count)        // --放前面

```


### for, foreach 迴圈
* for 迴圈為 while 迴圈的語法糖
* 通常會搭配 array 使用
```php
// while 寫法
$array = ["A", "B", "C", "D", "E", "F", "G"];
$count = count($array);
$index = 0;

while($index < $count){
    echo $array[$index] . "<br>";
    $index ++;
}


// for 寫法
// for( $i=0 ;  ;  )   第一個區塊為變數宣告
// for(  ; true ;  )   第二個區塊放條件判斷，如果為 true 則會無窮迴圈
// for(  ; true ;  )   第三個區塊為每個迴圈都會做的事

for($i=0; $i<count($array); $i++){
    echo $array[$i] . "<br>";
};


// foreach 寫法
foreach($array as $a){
    echo $a . "<br>";
};

// foreach 寫法
// $key $value
foreach($array as $key => $value){
    echo $key . "=>" . $value . "<br>";
}
```

