## regex validation
> 可以用 regex101 查詢 regex 寫法。

<br/>

<br/>


### request validation
1.  request regex 是從原生 php `preg_match` 函式去寫的。

    ```php
    // Http\Request.php

    // 驗證使用者是否要登入狀態
    public function authorize()
    {
        return true;
    }

    // 驗證每個form送進的name
    public function rules()
    {
        return [
            'name1' => 'require|string|max:6',  // 順序會影響哪個會先被提醒
            'name2' => 'nullable|min:0|integer' // 是用explode('|', 'string')
        ];
    }
    ```

<br/>

<br/>

### regex validation

```php
    // 驗證每個form送進的name
    public function rules()
    {
        // 以 ^ 開頭 $ 結尾；i (insensitive) 不考慮大小寫；g (global) 遇到所有符合的都會篩
        // 如果 regex 有 | 符號，只能用 array 寫法
        return [
            'name1' => ['require', 'string', 'max:6'], 
            'name2' => ['nullable', 'regex:/^\w+@\w+\.\w+$/ig']
        ];
    }
```

<br/>

<br>

### rule 建立
```shell
php artisan make:rule EmailRule
```
在 `App\Rules\EmailRule.php`
```php
    // 先宣告屬性 (若為form post驗證，則attribute為name的名稱)
    class EmailRule implements Rule
    {
        public function __construct()
        {
            $this->$attribute = 
        }

        // 判斷函式
        // $attribute 為此 name 名稱
        // $value 為驗證的值
        public function passes($attribute, $value)
        {
            $this->name = $attribute;
            return preg_match('/^\w+@\w+\.\w+$/ig', $value);
        }

        // 若沒符合(passes 函式回傳 false)，則會顯示此 message
        public function message()
        {
            return "The validation $this->name error message.";
        }
    }
```
在 `request.php`中使用
```php
return [
            'name1' => ['require', 'string', 'max:6'], 
            'name2' => ['nullable', new EmailRule();]
        ];
```