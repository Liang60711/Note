# 檔案上傳
從 Form 接收資料需將檔案型態更改
```html
<form action="#" method="POST" enctype="multipart/form-data">
            @csrf
            <!-- image -->
            <div class="form-group">
                <input type="file" name="image">
            </div>

            <!-- submit -->
            <div class="form-group">
                <button class="btn-primary" type="submit">SUBMIT</button>
            </div>
        </form>
```

### 若圖片名稱為 <code>car.jpg</code>，可以使用以下函數 (類似regex) 方法，來驗證檔案
```php
// __function() 帶入函數
// image 為 form 的 input name
$request->image->__function()
```
<br/>

|函數|抓取|回傳值|
|--|--|--|
|getClientOriginalName()|上傳前檔名|'car.jpg'|
|getFilename()|暫存檔的名稱|'php5946.tmp'|
|guessExtension()|上傳前副檔名|'jpg'|
|getClientExtension()|(沒有檔名、副檔名時)副檔名|'jpg'|
|getMimeType()|上傳後檔案類型|'image/jpeg'|
|getClientMimeType()|上傳後檔案類型|'image/jpeg'|
|getSize()|檔案大小 (Bytes)|149186 |
|getError()|是否錯誤|成功 1<br/>失敗 0|
|isValid()|是否有效|有效 true<br/>無效 false|
|store()|||
|asStore()|||
|storePublicly()|||
|move()|移動檔案||

<br/>

<br/>





### 存入資料庫: controller 使用 <code>$request</code> 去抓檔案
```php
public function store(Request $request)
{
    // 先驗證
    $request->validate([
            'name' => 'required',
            'founded' => 'required|integer|min:0|max:2021',
            'description' => 'required',
            'image' => 'required|mimes:jpg,png,jpeg|max:5048'
    ]);

    // 整理 image 檔名
    $newImageName = time() . '-' . $request->name . '.' . $request->image->extension();

    // 將上傳圖片移入 public
    // move(存放目錄, 存放檔案)
    $request->image->move(public_path('images'), $newImageName);

    // 存入資料庫 (存網址而已，檔案本身在 public/images 裡面)
    $car = Car::create([
            'name' => $request->input('name'),
            'founded' => $request->input('founded'),
            'description' => $request->input('description'),
            'image_path' => $newImageName
        ]);

    return redirect()->route('cars.index');
}
```
記得在 Car model 中增加可更改的欄位
```php
protected $fillable = ['name', 'founded', 'description', 'image_path'];
```
最後在 cars.show 中顯示
```html
<img src="{{ asset('images/' . $car->image_path) }}">
```