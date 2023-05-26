## 策略模式

> https://ianjustin39.github.io/ianlife/design-pattern/strategy-pattern/

策略模式是屬於物件導向程式設計 (Object-oriented Programming, OOP) 結構，是行為模式 (behavioral pattern) 的一種，也被稱為 Policy Pattern。
此模式的邏輯是，定義一系列的算法，把它們一個個封裝起來，並且使它們可相互替換。

`解決問題`: 在有多種算法相似的情況下，使用 if...else 所帶來的複雜和難以維護。


```java
// 定義 Strategy interface
public interface Strategy {
    int doOperation(int num1, int num2); // 此處依照 intellij 提示省略 public abstract
}
```
```java
// 實作加法減法
public class StrategyAdd implements Strategy {
    @Override
    public int doOperation(int num1, int num2) {
        return num1 + num2;
    }
}
public class StrategySubtract implements Strategy {
    @Override
    public int doOperation(int num1, int num2) {
        return num1 - num2;
    }
}
```
```java
// 建立 Context 類
public class Context {
    
    private Strategy strategy;
    
    public Context(Strategy strategy) {
        this.strategy = strategy;
    }
    
    // 依據傳入的 Strategy，執行相對應的方法
    public int executeStrategy(int num1, int num2) {
        return strategy.doOperation(num1, num2);
    }
}
```
使用方式
```java
// 加法策略
Context context1 = new Context(new StrategyAdd());
int r1 = context1.executeStrategy(10, 8);

// 抽換成減法策略
Context context2 = new Context(new StrategySubtract());
int r2 = context2.executeStrategy(10, 8);
```