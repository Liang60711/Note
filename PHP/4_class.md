# class
## class 組成

1. 成員(member)
    * 屬性(property)
    * 函式(function)
    * 屬性必須加上可視性(visibility)；方法則不一定要加，預設為 public。

2. 可視性(visibility)
    * 分為3種 public protected private。
    * 可視性用來決定有關成員(member)的存取權限。
    * public 可以使用在原 class、繼承的 class、建立的物件中。
	* protected 可以使用在原 class、繼承的 class 中。
    * private 只可以使用在原 class 中。

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
* 舉例，A、B、C三個網站都在賣同一場演唱會的票，因此票數需要統一個變數 (static) 讓各網站去扣，不能分開計算。

```php
class Person {
    // 屬性
    public static $age = 29;

    // 方法
    public static function getAge(){
        return self::$age;
    }
}

// static 呼叫 (可以不用建立實體)
echo Person::$age; 
echo Person::getAge();
```

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


## final
* 使用在 class 或 class中的方法(method)。
* 若使用在 class 中，該 class 無法被繼承。
* 若使用在方法中，該方法無法被 Override。

```php
// 方法無法被 override

class Base {
   final public function testing() {
       echo "Base";
   }
}

class Child extends Base{
   public function testing() {
       echo "Child";
   }
}
// 會報錯
```
```php
// class 無法被繼承

final class BaseClass {
   public function test() {
       echo "Base";
   }
}

class ChildClass extends BaseClass {
}
// 會報錯
```

<br/>

## 常見 class 函式
### is_a() 檢查類別
```php
class A{

}
class B extends A{

}
$a = new A();
$b = new B();

// is_a($object, class)
var_dump(is_a($b, "A"));        // bool(true)
var_dump(is_a($b, "B"));        // bool(true)
```

### get_class() 返回類別名稱
```php
class A{

}
class B extends A{

}
$a = new A();
$b = new B();


// get_class($object)
echo get_class($a);             // A
```

### get_parent_class() 返回父類別名稱
```php
class A{

}
class B extends A{

}
$a = new A();
$b = new B();


// get_parent_class($object)
var_dump(get_parent_class($a));      // bool(false)
echo get_parent_class($b);           // A
```