## linked list 與 array 比較
* array適合查找、遍歷、固定長度
* linked list適合插入、刪除；不宜過長，否則會導致遍歷性能下降。


<br/>

<br/>

## 實作 單向linked list
```java
// 調用
public static void main(String[] args){
    NodeManager nm = new NodeManager();
    nm.add(5);
}
```

```java
class NodeManager{
    private Node root;//根結點(第一個)
    private int currentIndex=0;//節點index，每次操作從0開始
    // 提供方法給外部類使用
    public void add(int data){
        if(root==null){
            root = new Node(data);//若沒有根結點則新增一個
        }else{
            root.addNode(data);//若已有根結點則，調用根結點的addNode()
        }
    }
    public void del(int data){// 刪除第一個搜尋到的節點
        if(root == null)return;
        if(root.data == data){
            root = root.next;//若要刪除的為root，則將next作為root
        }else{
            root.delNode(data);
        }
    }
    public void print(){
        if(root != null){
            System.out.print(root.getData() + "->");//先印出root的值
            root.printNode();//再印出其他節點
            System.out.println();
        }
    }
    public boolean find(int data){
        if(root == null)return false;
        if(root.data == data){//若根節點為要查找的數
            return true;
        }else{
            return root.findNode(data);//非根結點查找，調用內部類方法
        }  
    }
    public boolean update(int oldData, int newData){
        if(root == null) return false;//若無根結點
        if(root.data == oldData){//若要改的數為根結點
            root.setData(newData);
            return true;
        }else{
            return root.updateNode(oldData, newData);//非根結點更新，調用內部類方法
        }
    }
    public void insert(int index, int data){
        // 若將3插入1,2,4,5中，2個步驟
        // 3.next=2.next
        // 2.next=3
        if(index < 0)return;
        currentIndex = 0;
        if(index == currentIndex){//若要插在根節點位置
            Node newNode = new Node(data);
            newNode.next = root;//向currentIndex之前插入
            root = newNode;
        }else{
            root.insertNode(root, index, data);
        }
    }


    // 使用內部類封裝
    private class Node{
        private int data;
        private Node next;//使用當前類作為屬性
        // 建構子
        public Node(int data){
            this.data = data;
        }
        // setter and getter
        public void setData(int data){
            this.data = data;
        }
        public int getData(){
            return data;
        }
        // 以下為6種提供外部類的方法
        // 新增節點
        public void addNode(int data){
            if (this.next == null){
                this.next = new Node(data);//找到最末端節點後，並在最末端後再新增一個節點
            }else{
                this.next.addNode(data);//遞迴再找下一個節點，直到next為null
            }
        }
        // 刪除節點
        public void delNode(int data){
            if(this.next != null){
                if(this.next.data == data){//若下個節點為要刪除的，則將下下個節點取代下個節點
                    this.next = this.next.next;
                }else{
                    this.next.delNode(data);//用遞迴找下一個要刪除的節點
                }
            }
        }
        // 輸出所有節點
        public void printNode(){
            if(this.next != null){
                System.out.print(this.next.data + "->");
                this.next.printNode();
            }
        }
        // 查找節點
        public boolean findNode(int data){
            if(this.next != null){
                if(this.next.data == data){
                    return true;
                }else{
                    return this.next.findNode(data);
                }
            }
            return false;
        }
        // 修改節點
        public boolean updateNode(int oldData, int newData){
            if(this.next != null){
                if(this.next.data == oldData){
                    this.next.data = newData;
                    return true;
                }else{
                    return this.next.updateNode(oldData,newData);
                }
            }
            return false;
        }
        // 插入節點
        public void insertNode(Node prev, int index, int data){
            currentIndex++;//當前已經不是root了
            if(index == currentIndex){
                Node newNode = new Node(data);
                newNode.next = prev.next;
                prev.next = newNode;
            }else{
                this.insertNode(prev.next, index, data);
            }
        }
    }
}
```