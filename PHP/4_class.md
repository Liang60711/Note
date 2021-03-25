# class
## class 組成

1. 成員(member)
    * 屬性(property)
    * 函式(function)
    * 屬性必須加上可視性(visibility)；方法則不一定要加，預設為 public。

2. 可視性(visibility)
    * 分為3種 public protect private。
    * 可視性用來決定有關成員(member)的存取權限。
    * public 可以從物件外部被存取，但是 protect、private 兩者都不行。
    * public 和 protect 的成員都會在繼承的時候繼承到子類別，而 private 則不會。

3. 靜態/非靜態(static/non-static)
    * 使用 static 關鍵字的為靜態，(除了const的成員以外)，其餘為非靜態。

4. 建構子/解構子(constructors/destructors)
    * 建構子為 new 一個物件時會自動跑的方法。
    * 當程式結束時或是已經沒有任何變數參照指向物件時，會自動跑的方法。

    ```php
    class Demo
    {
        // 建構子
        function __construct() {
        echo "construct<br/>";
        }

        // 解構子
        function __destruct() {
        echo "destructor<br/>";
        }
    }
    ```

<br>

## class 建立
```php
class SimpleClass
{
    // 屬性宣告
    public $var = 'a default value';
    // 方法宣告
    public function displayVar() {
        echo $this->var;
    }
}
```
## 建立實體物件
```php
// 建立物件
$simple = new SimpleClass();

// 使用屬性
echo $simple->var . "\n"; 

// 使用方法
$simple->displayVar();
```


## 宣告參數 型別
* 宣告 String 型別，輸入 int 會被自動轉型，故要使用嚴格模式

```php
// 嚴格模式開啟
declare(strict_types=1);

// 父類別
class Person
{   
    function __construct($name){     
        $this->name = $name;
    }
    function getName(Person $object){   // 指定傳入參數為 Person 類別
        echo $object->name;
    }
}

// 子類別
class Student extends Person
{
    // pass
}

$s = new Student("安安");
Person::getName($s);                    // 安安
```


<br/>

## static
* 通常在 new 一個物件的時候，會把整個 class 裡面的屬性和方法在記憶體中再建立一份，會造成記憶體使用上的浪費。
* staitc 所佔用的記憶體會在程式結束後釋放。
* 宣告 static 的 屬性、方法、變數都會共用記憶體。
* 一對冒號 :: 稱為 Scope Resolution Operator 用於呼叫 class 的 static 和 const；或是類別間的呼叫。

<br/>

## parent 和 self 和 $this 差別
* parent 代表父類別
* self 代表當前類名
* $this 代表當前物件
* 用 self::const 或 self::$static 呼叫常數或靜態變數
* 用 $this->$non_static 呼叫非靜態變數。
```php
class Demo
{
    const BR_TAG = "<br/>";
    function __construct(){
        echo "123:", self::BR_TAG;
    }
    }
}

// 在 class 外呼叫 const
echo Demo::BR_TAG;
```

<br/>

## Override
* 新 class 繼承舊 class 時，將舊有的方法(method)，以名稱相同的方式再寫一次。
* 好處是可以覆蓋舊方法，而不影響舊 class。

<br/>