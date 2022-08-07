# redirect 重新導向

## status code 302 暫時轉移
1. laravel 預設是 `302`
2. `302` 每次都會先到 `/old` 再導向到 `/new`，導向前可以做一些其他事，例如點閱計算。
```php
// routes/web.php
Route::redirect('/old', '/new');
```


<br/>

<br/>

##  status code 301 永久轉移
1. 使用 `301` 會被瀏覽器 cache，第一次會先到 `/old`，再導向到`/new`
2. 第二次開始使用瀏覽器 cache，直接連線至 `/new`，就算把`/old` 改掉也是會連線至`/new`。
3. 需要將瀏覽器 `Disable cache` 打勾。
4. 若要網站要永久轉移，需使用`301`。
```php
// routes/web.php
Route::redirect('/old', '/new', 301);
```

<br/>

<br/>

* 使用者不管使用什麼方法，redirect 之後也都會用相同方法。所以如果使用者一開始用 POST，被 307 redirect 後，也應該要使用 POST。