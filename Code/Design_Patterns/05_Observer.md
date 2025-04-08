## 觀察者模式 Observer Pattern

一個物件（Subject）狀態改變時，自動通知所有依賴它的物件（Observers）。
也就是「你變了，我就要知道」。

<br/>

<br/>

## 舉例
一個氣象站（WeatherStation），會不定時更新氣溫。
有三個螢幕（觀察者）想要即時看到最新溫度：

- 手機
- 電視
- 網頁

只要氣象站更新資料，所有觀察者都會同步收到通知

<br/>

<br/>

1. 定義 Observer 和 Subject 介面

    ```java
    // 觀察者
    public interface Observer {
        void update(float temperature);
    }
    ```

    ```java
    // 被觀察者
    public interface Subject {
        void registerObserver(Observer o);
        void removeObserver(Observer o);
        void notifyObservers();
    }
    ```

2. 實作被觀察者

    ```java
    public class WeatherStation implements Subject {
        private List<Observer> observers = new ArrayList<>();
        private float temperature;

        @Override
        public void registerObserver(Observer o) {
            observers.add(o);
        }

        @Override
        public void removeObserver(Observer o) {
            observers.remove(o);
        }

        public void notifyObservers() {
            for (Observer o : observers) {
                o.update(temperature);
            }
        }

        // 當溫度變化時的邏輯
        public void setTemperature(float newTemp) {
            System.out.println("Station 問度更新成: " + newTemp);
            this.temperature = newTemp;
            notifyObservers();
        }
    }
    ```

3. 實作觀察者

    ```java
    public class Phone implements Observer {
        @Override
        public void update(float temperature) {
            System.out.println("Phone:" + temperature);
        }
    }

    public class TV implements Observer {
        @Override
        public void update(float temperature) {
            System.out.println("TV:" + temperature);
        }
    }

    public class Web implements Observer {
        @Override
        public void update(float temperature) {
            System.out.println("Web:" + temperature);
        }
    }
    ```

4. 使用
```java
public class Start {

    public static void main(String[] args) {
        WeatherStation station = new WeatherStation();

        Observer phone = new Phone();
        Observer tv = new TV();
        Observer web = new Web();

        // 註冊觀察者
        station.registerObserver(phone);
        station.registerObserver(tv);
        station.registerObserver(web);

        // 模擬溫度變化
        station.setTemperature(28.5f);
    }
}
```