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