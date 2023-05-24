## 二元樹 Binary Tree

> https://ithelp.ithome.com.tw/articles/10242571
1. 二元樹可以為空集合
2. 每 1 個節點最多只有 2 個子節點，左節點與右節點
3. 有次序關係，左節點會排在右節點之前，不能顛倒

<br/>

<br/>

實作二元樹，打印使用`中序遍歷 (Inorder Traversal)`
>https://ithelp.ithome.com.tw/articles/10205571
```java
public class BinaryTree{

    // 根節點
    private Node root;

    // 新增節點
    public void add(int data){
        if(root == null){
            root = new Node(data);//若無根結點
        }else{
            root.addNode(data);
        }
    }

    // 印出所有節點
    public void print(){
        root.printNode();
    }
    


    // 使用內部類
    private class Node{
        private int data;
        private Node left;//左節點
        private Node right;//右節點

        public Node(int data){
            this.data = data;
        }

        public void addNode(int data){
            // root和data的值需比較大小決定左/右節點
            if(this.data > data){//若根節點比data大，則data為左節點
                if(this.left==null){
                    this.left = new Node(data);
                }else{
                    this.left.addNode(data);//若左節點已滿，則將左節點作為根節點
                }
            }else{
                if(this.right==null){
                    this.right = new Node(data);
                }else{
                    this.right.addNode(data);
                }
            }
        }

        public void printNode(){
            // 中序遍歷，打印順序是左節點->根節點->右節點
            if(this.left != null){
                this.left.printNode();//遞迴找最左邊(最小)的節點
            }
            System.out.print(this.data + "->");
            if(this.right != null){
                this.right.printNode();
            }
        }
        //      8
        //     / \
        //    3   10
        //   / \    \
        //  1   6    14
        //     / \   /
        //    4  7  13
    }
}
```

```java
public static void main(String[] args){

    BinaryTree bt = new BinaryTree();

    bt.add(8);
    bt.add(3);
    bt.add(10);
    bt.add(1);
    bt.add(6);
    bt.add(14);
    bt.add(4);
    bt.add(7);
    bt.add(13);
    bt.print();
}
```