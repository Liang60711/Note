# Mapping 屬性

## 概述
Elasticsearch 的 Mapping 是用來定義索引中，文件的結構，包括欄位的類型、格式及分析方式。類似於關聯數據庫中的 `schema` 定義。

<br/>

<br/>

## mapping 常見屬性(非所有)

`type`，欄位的數據類型

* 字串類

    * `text` 可分詞的字串。

    * `keyword` 精確值，不可分割(品牌、國家、email、ip)。

* 數值類

    * `long`

    * `integer`
    * `short`
    * `byte`
    * `double`
    * `float`

* 布林值

    * `boolean`

* 日期類

    * `date`

* 物件

    * `object`

特別注意，沒有特別一個類型叫陣列類型，但有`double陣列`、`text陣列`。



範例如下
```json
{
    "age": 21, // integer
    "weight": 52.1, //double
    "isMarried": false, // boolean
    "info": "我好帥我愛Java", // test
    "email": "test@hpicorp", // keyword
    "score": [99.1, 99.5, 98.9], // double[]
    "name": { // object
        "firstName": "王",
        "lastName": "老"
    }
}
```

<br/>

`index`，是否建立索引(倒排索引)，預設為 true。

* 預設是 true，代表上表的json中，每一個欄位(age, weight, isMarried...)，所以當不需要查詢的欄位，就可以設為 false。


<br/>

`analyzer`，使用哪種分詞器。

* 只有 type = `text`，才會使用分詞器，其他所有類型都不需要。

<br/>

`properties`，一個欄位的子欄位。

* 例如一個 object 類型，就有2個 properties。

    ```json
    {
        // object
        "name": { 
            "firstName": "王", // properties
            "lastName": "老" // properties
        }
    }

    ```
