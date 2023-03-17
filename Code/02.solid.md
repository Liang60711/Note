## solid 或 七大原則 


1. `Single Responsibility Principle`，單一責任原則
    * 定義: A class should have only one reason to change. 一個模組應只對唯一的一個角色負責。
    * 目的: 限制改變程式碼所帶來的影響力。
    * 例如: 電商後台，刷卡功能被許多等級的使用者所共用，每次不需要更改功能。

    ```php
    interface creditCard()
    {
        public function caculate();
    }

    class userCreditCard implements creditCard
    {
        public function caculate(){
            // 商業邏輯
        }
    }

    class vipUserCreditCard implements creditCard
    {
        public function caculate(){
            // 商業邏輯
        }
    }
    ```

2. `Open/Closed Principle`，開放封閉原則
    * 定義: 程式在面對擴充時是開放的，且擴充時不應修改到原有的程式。
    * 目的: 新增功能時，不要影響舊有已經穩定的部分。
    * 例如: goole chrome, vscode 皆使用 `extension` 來擴充功能，不影響舊有功能。

3. `Liskov Substitution Principle`，里氏替換原則  
    > https://ithelp.ithome.com.tw/articles/10235629
    * 定義: 子型態必須遵從父型態的行為進行設計。
    * 簡單一句話就是，任何父類出現的地方，子類一定可以替換，且不會產生不良影響。

        ```java
        // List 可以替換成 ArrayList，且不會有問題
        List<String> list = new ArrayList()<>;
        ```


4. `Interface Segregation Principle`，介面隔離原則  
    > https://medium.com/%E7%A8%8B%E5%BC%8F%E6%84%9B%E5%A5%BD%E8%80%85/%E4%BD%BF%E4%BA%BA%E7%98%8B%E7%8B%82%E7%9A%84-solid-%E5%8E%9F%E5%89%87-%E4%BB%8B%E9%9D%A2%E9%9A%94%E9%9B%A2%E5%8E%9F%E5%89%87-interface-segregation-principle-50f54473c79e

    * 模組與模組之間的依賴，不應有用不到的功能可以被對方呼叫，應簡化接口至最小。



5. `Dependency Inversion Principle`，依賴反轉原則
    > https://ithelp.ithome.com.tw/articles/10236359
    * 定義: 高階模組不應該依賴於低階模組，兩者都該依賴抽象；抽象不應該依賴於具體實作方式。具體實作方式則應該依賴抽象。
    * 高層低層是相對關係，也就是呼叫者與被呼叫者。

    * 舉例: 

        ```java
        // Payment是高階模組，CreditCard是低階模組
        class Payment {
            public void pay(CreditCard creditCard) {
                creditCard.pay();
            }
        }

        class CreditCard {
            public void pay() {
                System.out.println("信用卡付款");
            }
        }

        public class Bank {
            public static void main(String args[]) {
                Payment p = new Payment();
                p.pay(new CreditCard());
            }
        }
        ```
        需將付款行為抽出
        ```java
        interface IPayment{
            public void pay();
        }


        class CreditCard implements IPayment{
            public void pay(){
                System.out.println("信用卡付款");
            }
        }

        class QRCode implements IPayment{
            public void pay(){
                System.out.println("QRCode付款");
            }
        }

        class Payment{
            public void pay(IPayment iPayment){
                iPayment.pay();
            }
        }

        public class Bank {
            public static void main(String args[]) {
                Payment p = new Payment();
                p.pay(new CreditCard());
                p.pay(new QRCode());
            }
        }
        ```

        <br/>

        <br/>


6. `Law of Demeter`，迪米特法則
    > https://medium.com/@ckpattern35/ck-patt-%E8%A8%AD%E8%A8%88%E6%A8%A1%E5%BC%8F-11-%E8%BF%AA%E7%B1%B3%E7%89%B9%E6%B3%95%E5%89%87-demeter-law-931fefc4abda
    * 降低類別之間的耦合度。當兩個類別不必彼此直接通信時，可以透過第三方來幫忙類別方法的調用。對自己依賴的類別，知道越少越好。
    * 原則: 
        1. 類別之間，應該要互相保持最低程度的了解(做好 private)。
        2. 各自的工作盡可能封裝在自己的類別中，再用 public 的方式讓外部使用。
        3. 迪米特法則的一個英文解釋是：Only talk to your immediate friends.
        (只與直接朋友溝通。如果是陌生的朋友，就透過第三方來傳話。)
    
    <br/>

    <br/>

7. `Composite/Aggregate Reuse Principle`，合成/聚合複用原則
    > https://ithelp.ithome.com.tw/articles/10236782
    * 盡量使用組合(contains-a)/聚合(has-a)方式來代替繼承(is-a)來達到重複使用的目的，而如果要使用繼承的話，則須符合里氏替換原則(LSP)。
    * **合成**：部分對象以及整體對象的生命週期是一致的(器官與人的關係，器官不在了人就不在了)。
    * **聚合**：部分對象的生命週期與整體對象沒有關聯，甚至可能更長(學生與班級的關係，班級畢業了，學生可能存在另一個整體中)。
    * 與繼承比較: 
        * **封裝性**: 此原則維持了類別的封裝性，相較於繼承，封裝性更佳。
        * **耦合度**: 
            1. 繼承時父類別與子類別的耦合度高。若父類別做任何更動都會影響到子類別，降低了類別的擴充及維護。
            2. 合成/聚合時新舊類別的耦合度及依賴程度低，新類別只能透過複用類別的接口取得內容。