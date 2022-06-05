## File I/O
建立檔案
```java
import java.io.File;

File file = new File(fileName);

// 建立目錄
file.mkdir();

// 讀取目錄
file.list();

// 刪除檔案
file.delete();
```
讀取檔案
```java
// 以 Byte 方式讀取 (影音、圖片檔)
FileInputStream(fileName);

// 以 Char, String 方式讀取
FileReader(fileName);
```
寫入檔案
```java
// 以 Byte 方式寫入
FileOutputStream(fileName);

// 以 Char, String 方式寫入
FileWriter(fileName);
```
範例
```java
// 新增目錄
public static void createFolder(String fileName) {
    File file = new File(fileName);
    file.mkdirs();
}
// 讀取目錄
public static String[] readFolder(String fileName) {
    File file = new File(fileName);
    return file.list();
}
// 刪除檔案
public static void deleteFolder(String fileName) {
    File file = new File(fileName);
    file.delete();
}
```
讀取檔案範例，先使用`FileReader`，再用`BufferedReader`，最後跑回圈，並關閉檔案。
```java
public static void readTxtFile(String fileName) {
    FileReader fr = null;
    try {
        fr = new FileReader(fileName);
    } catch (FileNotFoundException e) {
        throw new RuntimeException(e);
    }

    BufferedReader br = new BufferedReader(fr);
    String tmp = null;
    try{
        while((tmp = br.readLine()) != null) {
            System.out.println(tmp);
        }
    }catch (IOException e){
        e.printStackTrace();
    }finally {
        try{
            br.close();
        }catch (IOException e){
            e.printStackTrace();
        }
    }
}
```

寫入範例，先使用`FileWriter`，在使用`BufferedWriter`，最後使用`flush`將資料寫入檔案中。
```java
public static void writeTxtFile(String fileName) {
    // 建立寫入資料
    List<String> list = new ArrayList<String>();
    list.add("test1");
    list.add("test2");
    list.add("test3");

    try{
        FileWriter fw = new FileWriter(fileName);
        BufferedWriter bw = new BufferedWriter(fw);

        for(String str : list) {
            bw.write(str);// 寫入至記憶體
            bw.newLine(); // 換行 
        }

        bw.flush(); // 從記憶體中寫入到檔案

    }catch (IOException e){
        e.printStackTrace();
    }
}
```