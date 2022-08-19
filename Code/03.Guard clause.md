## Guard clause
翻譯是防衛語句
1. Guard clause 的好處是提高程式的可讀性，透過預先排除不符規格的內容可減少巢狀`if else`的發生或過長的`if else`區塊。
2. Guard clause 除了預先檢驗輸入的參數，也可應用在預先返回各種可能的結果。讓可讀性提高。

```php
// 一般寫法
public function sendSms(Sms $sms) {
    if (!empty($sms) && !empty($sms->content)) {
        // 發簡訊邏輯
    }
}



// Guard clause 防衛語句
public function sendSms(Sms $sms) {
    // 先將例外排除，以免寫出太多if, else
    if (empty($sms) || empty($sms->content)) {
        return 
    }

    // 發簡訊邏輯
}
```