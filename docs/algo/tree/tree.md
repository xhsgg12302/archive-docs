* ## Intro(Tree)

    ?> 1. 树有很多种，每个节点最多只有两个子节点的一种形式称为`二叉树`。
    <br>2. 二叉树的子节点分为左节点和右节点
    <br>3. 如果该二叉树的所有叶子结点都在`最后一层`，并且节点总数=2^n-1,n为层数，则我们称为`满二叉树`。
    <br>4. 如果该二叉树的所有叶子结点都在最后一层或者倒数第二层，而且最后一层的叶子结点在左边连续，倒数第二层的叶子结点在右边连续，我们称为`完全二叉树`。

    ![](/.images/algo/tree/tree-binary-01.png ':size=20%')
    ![](/.images/algo/tree/tree-binary-02.png ':size=32%')
    ![](/.images/algo/tree/tree-binary-03.png ':size=33%')

* ## Implement

    - ### Node
    ```java
    class Node {
        public int value;
        public Node left,right;

        public Node(int value) { this.value = value;}

        public Node(int value, Node left, Node right) {
            this.value = value;
            this.left = left;
            this.right = right;
        }
    }
    ```

    - ### 遍历
    <!-- tabs:start -->
    #### **java**
    ```java
    // 前序遍历
    public static void recursionPreOrderTraversal(Node tree){
        if(tree!= null){
            System.out.print(tree.value + ",");
            recursionPreOrderTraversal(tree.left);
            recursionPreOrderTraversal(tree.right);
        }
    }
    // 中序遍历
    public static void recursionInOrderTraversal(Node tree){
        if(tree!= null){
            recursionInOrderTraversal(tree.left);
            System.out.print(tree.value + ",");
            recursionInOrderTraversal(tree.right);
        }
    }
    // 后序遍历
    public static void recursionPostOrderTraversal(Node tree){
        if(tree!= null){
            recursionPostOrderTraversal(tree.left);
            recursionPostOrderTraversal(tree.right);
            System.out.print(tree.value + ",");
        }
    }
    ```
    <!-- tabs:end -->

    - ### 后序和中序构建
    <!-- tabs:start -->
    #### **java**
    ```java
    public class BuildBinaryTree {

        private  Map<Integer,Integer> inMap = new HashMap<>();
        private int index_root = 0;
        private int [] postOrder = null;
        
        public void init(int[] inOrder,int[] postOrder){
            for (int i = 0; i < inOrder.length; i++) {
                inMap.put(inOrder[i],i);
            }
            this.postOrder = postOrder;
            index_root = postOrder.length - 1;
        }

        public Node build(int index_left,int index_right){
            if(index_left > index_right) return null;

            int root_value = postOrder[index_root--];
            Node root = new Node(root_value);
            int _left = inMap.get(root_value);
            root.right = build(_left + 1,index_right);

            root.left = build(index_left,_left - 1);
            return root;
        }

        @Test
        public void test(){
            int[] postOrder = {9,15,7,20,3};
            int[] inOrder = {9,3,15,20,7};

            init(inOrder,postOrder);
            Node root = build(0,inOrder.length - 1);
        }
    }
    ```
    <!-- tabs:end -->
    

## Reference
* https://dprebyl.github.io/syntree
* https://www.bilibili.com/video/BV1E4411H73v?p=91
* https://github.com/12302-bak/idea-test-project/tree/learning/_0_base-learning/src/main/java/_algorithm/tree/demo
* https://leetcode.cn/problems/construct-binary-tree-from-preorder-and-postorder-traversal/description/