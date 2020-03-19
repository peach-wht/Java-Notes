## 根据前序遍历和中序遍历还原树：

### 思路：

先通过pre[0]==in[3]找到对应的rootIndex=3，那么得知前序集合里的前三个是左子树，后四个是右子树，且每一次的rootIndex 的左边是左子树，右边是右子树

还有记得每次使用其它类要导入，比如下面的Arrays.copyOfRange（）

```java
import java.util.Arrays; 
public  static TreeNode reConstructBinaryTree(int [] pre,int [] in) {
        if(pre.length==0){
            return null;
        }
        int rootVal=pre[0];
        if(pre.length==1){
            return new TreeNode(rootVal);
        }
        TreeNode treeNode=new TreeNode(rootVal);
        int rootIndex=0;
        for (int i = 0; i <in.length ; i++) {
            if(rootVal==in[i]){
                rootIndex=i;
                break;
            }
        }
        treeNode.left=reConstructBinaryTree(Arrays.copyOfRange(pre,1,rootIndex+1),Arrays.copyOfRange(in,0,rootIndex));
        treeNode.right=reConstructBinaryTree(Arrays.copyOfRange(pre,rootIndex+1,pre.length),Arrays.copyOfRange(in,rootIndex+1,in.length));
        return treeNode;
    }
```

## 合并两个递增链表

学会变通，带头结点的，可以返回next，这样就不会带头节点了

```java
public ListNode Merge(ListNode list1,ListNode list2) {
         ListNode listNode=new ListNode(-1);
        ListNode root=listNode;
        while (list1!=null&&list2!=null){
            if(list1.val<list2.val){
                listNode.next=list1;
                listNode=list1;
                list1=list1.next;
            }else {
                listNode.next=list2;
                listNode=list2;
                list2=list2.next;
            }
        }
            if(list1==null){
                listNode.next=list2;
            }
            if (list2==null){
                listNode.next=list1;
            }
        return  root.next;
    }
```

## 二叉树镜像

可以通过前序遍历实现

````java
public void Mirror(TreeNode root) {
        exchangePre(root);
    } 
    public void exchangePre(TreeNode root){
        if(root == null)
            return;
        if(root.left == null && root.right == null)
            return;
        TreeNode treeNode;
        treeNode=root.left;
        root.left=root.right;
        root.right=treeNode;
        exchangePre(root.left);
        exchangePre(root.right);
    }

````

## 顺时针打印矩阵

关键得建立一个坐标系，`arr[x][y]`其中x向下，y向右递增

![image-20200305150135262](D:\学习笔记\数据结构和算法\images\image-20200305150135262.png)

定义一个方向参数flag，和记录以经过的点的二维数组

```java
 int flag=1;//1向右，2向下，3向左，4向上
 boolean [][]vis=new boolean[matrix.length][matrix[0].length];
```

```java
 public ArrayList<Integer> printMatrix(int [][] matrix) {
        ArrayList<Integer> list = new ArrayList<>();
        int x=0;
        int y=0;
        int flag=1;//1向右，2向下，3向左，4向上
        boolean [][]vis=new boolean[matrix.length][matrix[0].length];
        while (list.size()<matrix.length*matrix[0].length){
            if(x<0||x>=matrix.length||y<0||y>=matrix[0].length||vis[x][y]){
                if(flag==1){
                    y--;
                    x++;
                    flag=2;
                }else if (flag==2){
                    x--;
                    y--;
                    flag=3;
                }else if(flag==3){
                    y++;
                    x--;
                    flag=4;
                }else {
                    x++;
                    y++;
                    flag=1;
                }
            }else {
                list.add(matrix[x][y]);
                vis[x][y]=true;
                if(flag==1){
                    y++;
                }else if (flag==2){
                    x++;
                }else if (flag==3){
                    y--;
                }else {
                    x--;
                }
            }

        }
        return list;
    }

```



栈的压入弹出序列

````java
import java.util.Stack;

public class StackCheck {
    public static void main(String[] args) {

        System.out.println(IsPopOrder(new int[]{1, 2, 3, 4, 5}, new int[]{4, 5, 3, 2, 1}));
    }

    public  static boolean IsPopOrder(int [] pushA,int [] popA) {
        Stack<Integer> stack = new Stack<>();
        if(popA.length==0||pushA.length==0){
            return false;
        }else {
            boolean flag=false;//记录压栈是否完成
            for (int i = 0,j=0;;) {
                if (!flag){
                    stack.push(pushA[i]);
                    if(popA[j]==pushA[i]){
                        stack.pop();
                        j++;
                    }
                    if(i==pushA.length-1){
                        flag=true;
                    }
                    i++;
                }else {
                    if(j>=popA.length){
                        break;
                    }
                    if(stack.peek()==popA[j]){
                        stack.pop();
                        j++;
                    }else {
                        break;
                    }
                }
            }
        }
        return stack.empty();
    }
}

````

## 从上到下打印二叉树

主要是通过一个队列实现广度遍历，队列能做到

```java
import java.util.ArrayList;
import java.util.LinkedList;
import java.util.Queue;
public class PrintTreeLToR {
    public ArrayList<Integer> PrintFromTopToBottom(TreeNode root) {
        ArrayList<Integer> list = new ArrayList<>();
        Queue<TreeNode>queue= new LinkedList<>();
        if(root!=null){
            queue.add(root);
        }
        while (!queue.isEmpty()){
            TreeNode peek = queue.peek();
            list.add(peek.val);
            if(peek.left!=null){
                queue.add(peek.left);
            }
            if(peek.right!=null){
                queue.add(peek.right);
            }
            queue.poll();
        }
        return list;
    }
}

```

## 二叉排序树和双向链表

 输入一棵二叉搜索树，将该二叉搜索树转换成一个排序的双向链表。要求不能创建任何新的结点，只能调整树中结点指针的指向。 

### 只要关乎树的就使用递归

```java
    public TreeNode Convert(TreeNode root) {
        if(root==null)return null;
        if(root.left==null&&root.right==null)return root;
        TreeNode left=Convert(root.left);
        TreeNode p=left;
        while(p!=null&&p.right!=null)
        {
            p=p.right;
        }
        if(left!=null)
        {
            p.right=root;
            root.left=p;
        }
        TreeNode right=Convert(root.right);
        if(right!=null)
        {
            root.right=right;
            right.left=root;
        }

        return  left!=null?left:root;
    }

```

## 字符串排列

 输入一个字符串,按字典序打印出该字符串中字符的所有排列。例如输入字符串abc,则打印出由字符a,b,c所能排列出来的所有字符串abc,acb,bac,bca,cab和cba。 

思路：

先固定一个字符，再求后面字符的排列顺序，其中递归出口是，还剩一个未交换字符即i==arr.length

![image-20200307122958091](D:\学习笔记\面试专题\缺少的东西\images\image-20200307122958091.png)

````java
 public ArrayList<String> Permutation(String str) {
        List<String> res = new ArrayList<>();
        if (str != null && str.length() > 0) {
            PermutationHelper(str.toCharArray(), 0, res);
            Collections.sort(res);
        }
        return (ArrayList)res;
    }
 
    public void PermutationHelper(char[] cs, int i, List<String> list) {
        if (i == cs.length - 1) {
            String val = String.valueOf(cs);
            if (!list.contains(val))
                list.add(val);
        } else {
            for (int j = i; j < cs.length; j++) {
                swap(cs, i, j);
                PermutationHelper(cs, i+1, list);
                swap(cs, i, j);
            }
        }
    }
 
    public void swap(char[] cs, int i, int j) {
        char temp = cs[i];
        cs[i] = cs[j];
        cs[j] = temp;
    }
````

## 数组中出现次数超过数组长度一半的数字

思路：用一个数组来记录个数

````java
 public static void main(String[] args) {

        System.out.println(MoreThanHalfNum_Solution(new int[]{1, 3,3,3,2}));

    }
    public static int MoreThanHalfNum_Solution(int [] array) {
       int []vis=new int[array.length];//经过就为1
       for(int i=0;i<array.length;i++){
           if(vis[i]==0)
               vis[i]=1;
           for (int j=0;j<array.length;j++){
               if(array[i]==array[j]&&vis[j]==0&&i!=j){
                   vis[j]=vis[i]+1;
                   vis[i]=vis[j];//记得同步
               }
           }
       }
       int maxIndex=0;
       for(int i=0;i<vis.length;i++){
           if(vis[maxIndex]<vis[i]){
               maxIndex=i;
           }
       }
       return vis[maxIndex]>array.length/2?array[maxIndex]:0;
    }
````

还有一种解法：

先排序，排序后的数组的中间值肯定是要找的数字，否则不会出现在中间，记录和它相等的值个数就可以了

````java
 		Arrays.sort(array);
        int mid=array.length/2;
        int count=0;
        for (int i=0;i<array.length;i++){
            if(array[mid]==array[i])
                count++;
        }
       return count>array.length/2?array[mid]:0;
````

## 连续子数组的最大值

```java
int res = array[0]; //记录当前所有子数组的和的最大值
        int max=array[0];   //包含array[i]的连续数组最大值
        for (int i = 1; i < array.length; i++) {
            max=Math.max(max+array[i], array[i]);
            res=Math.max(max, res);
        }
        return res;
```

## 整数1出现的次数：

关键是有没有想到数字转化为字符数组

````java
public static int NumberOf1Between1AndN_Solution(int n) {
        int sum=0;
        while (n>0){
            String s = String.valueOf(n);
            char[] chars = s.toCharArray();
            for (int i=0;i<chars.length;i++){
                if(chars[i]=='1'){
                    sum++;
                }
            }
            n--;
        }
        return sum;
    }
````

## 把数组排成最小的数：

 输入一个正整数数组，把数组里所有数字拼接起来排成一个数，打印能拼接出的所有数字中最小的一个。例如输入数组{3，32，321}，则打印出这三个数字能排成的最小数字为321323

思路：使用Collections的定制排序

````java
 public static String PrintMinNumber(int [] numbers) {
        ArrayList<Integer> list = new ArrayList<>();
        String s = "";

        for (int i = 0; i < numbers.length; i++) {
            list.add(numbers[i]);
        }
        Collections.sort(list, new Comparator<Integer>() {
            @Override
            public int compare(Integer o1, Integer o2) {
                String s1 = o1 + "" + o2;
                String s2 = o2 + "" + o1;
                return s1.compareTo(s2);
            }
        });

        for (int i : list) {
            s = s + i;
        }
        return s;
    }
````

## 丑数

思路：可以通过穷举法来进行，关键点就在每个质因子构成的最小值

```java
 public static int GetUglyNumber_Solution(int index) {
        if(index<=0)
            return 0;
        int i2=0,i3=0,i5=0;
        int []result=new int[index];
        int temp;
        result[0]=1;
        int count=0;
        while(count<index-1){
            temp=Math.min(result[i2]*2,Math.min(result[i3]*3,result[i5]*5));
            if(temp==result[i2]*2)
                i2++;
            if(temp==result[i3]*3)
                i3++;
            if(temp==result[i5]*5)
                i5++;
            result[++count]=temp;
        }
        return result[index-1];
    }
```

## 第一只出现一次的字符位置

在一个字符串(0<=字符串长度<=10000，全部由字母组成)中找到第一个只出现一次的字符,
并返回它的位置, 如果没有则返回 -1（需要区分大小写）.

```java
 public static int FirstNotRepeatingChar(String str) {
        boolean flag=false;
        char[] chars = str.toCharArray();
        int i = 0;
        for (; i <chars.length ; i++) {
            flag=true;
            for(int j=0;j<chars.length;j++){
                if(chars[i]==chars[j]&&i!=j){
                    flag=false;
                    break;
                }
            }
            if(flag){
                break;
            }
        }
        return flag?i:-1;
    }
```

## 两个链表的第一个公共节点(没搞懂)

 输入两个链表，找出它们的第一个公共结点。（注意因为传入数据是链表，所以错误测试数据的提示是用其他方式显示的，保证传入数据是正确的） 



```java
public ListNode FindFirstCommonNode(ListNode pHead1, ListNode pHead2) {
        ListNode p1=pHead1;
        ListNode p2=pHead2;
        while(p1!=p2){
            p1 = (p1==null ? pHead2 : p1.next);
            p2 = (p2==null ? pHead1 : p2.next);
        }
        return p1;
    }
```

##  统计一个数字在排序数组中出现的次数。 

不难

````java
    public int GetNumberOfK(int [] array , int k) {
        int sum=0;
        boolean flag=false;
        int index=0;
        for (int i=0;i<array.length&&!flag;i++){
            if(array[i]==k){
                sum++;
                index=i;
                flag=true;
            }
        }
        if(flag){
            for(int i=index+1;i<array.length;i++){
                if(array[i]==k){
                    sum++;
                }else {
                    break;
                }
            }
        }
        return sum;

    }
````

## 二叉树的深度

 输入一棵二叉树，求该树的深度。从根结点到叶结点依次经过的结点（含根、叶结点）形成树的一条路径，最长路径的长度为树的深度。 

### 非递归：

思路：没搞懂，但点睛只笔在最后一个判断语句

```java
 public int TreeDepth(TreeNode root) {
        if(root==null){
            return 0;
        }
        Queue<TreeNode> queue= new LinkedList<>();
        int count=0;//记录当前节点数
        int nextC=1;//记录下一次到达下一层的节点数
        int deep=0;//记录深度
        queue.add(root);
        while (!queue.isEmpty()){
            TreeNode poll = queue.poll();
            count++;
            if(poll.left!=null){
                queue.add(poll.left);
            }
            if (poll.right!=null){
                queue.add(poll.right);
            }
            if(count==nextC){
                nextC=queue.size();
                count=0;
                deep++;
            }
        }
        return deep;
    }
```

### 递归：

`````java
	public int TreeDepth(TreeNode node){
        if (node==null){
            return 0;
        }
        int leftDepth=treeDepth(node.left);
        int rightDepth=treeDepth(node.right);
        return leftDepth>rightDepth?leftDepth+1:rightDepth+1;
    }
`````



## 平衡二叉树

 输入一棵二叉树，判断该二叉树是否是平衡二叉树。 

```java
public boolean IsBalanced_Solution(TreeNode root) {
        if (root==null)
            return true;
        int leftDepth=treeDepth(root.left);
        int rightDepth=treeDepth(root.right);
        return Math.abs(leftDepth-rightDepth)<=1;
    }

    public int treeDepth(TreeNode node){
        if (node==null){
            return 0;
        }
        int leftDepth=treeDepth(node.left);
        int rightDepth=treeDepth(node.right);
        return leftDepth>rightDepth?leftDepth+1:rightDepth+1;
    }
```

## 数组中只出现过一次的数字（破罐子破摔，没技巧）

 一个整型数组里除了两个数字之外，其他的数字都出现了两次。请写程序找出这两个只出现一次的数字。 

````java
public static void FindNumsAppearOnce(int [] array,int num1[] , int num2[]) {
        int i = FindNumHelper(array,0,false);
        num1[0]=array[i];
        int j = FindNumHelper(array,num1[0],true);
        num2[0]=array[j];
    }
    public static int FindNumHelper(int num[],int s,boolean f){
        int index=0;
        boolean flag=true;
        for (int i = 0; i <num.length ; i++) {
            flag=true;
            for (int j=0;j<num.length;j++){
                if(i!=j&&num[i]==num[j]){
                    flag=false;
                    break;
                }
            }
            if (f&&num[i]==s){
                flag=false;
            }
            if(flag){
                index=i;
                break;
            }
        }
        return index;
    }
````

## 和为S的连续正序列

穷举法

````java
 public static ArrayList<ArrayList<Integer>> FindContinuousSequence(int sum) {
        ArrayList<ArrayList<Integer>> arrayLists = new ArrayList<>();
        boolean flag;
        int count=0;
        for(int i=1;i<=sum/2+1;i++){
            flag=false;
            count=0;
            ArrayList<Integer> arrayList = new ArrayList<>();
            for(int j=i;j<=sum/2+1;j++){
                count+=j;
                arrayList.add(j);
                if(count==sum){
                    flag=true;
                    break;
                }
            }
            if(flag&&arrayList.size()>1){
                arrayLists.add(arrayList);
            }
        }
        return arrayLists;
    }

````





## 和为S的数组中两个数字

```java
 public  static ArrayList<Integer> FindNumbersWithSum(int [] array, int sum) {
        ArrayList<Integer> arrayList = new ArrayList<>();
        int count=0;
        while (count<array.length){
            if(sum<array[count]){
                break;
            }
            count++;
        }
        boolean flag;
        for(int i=0;i<count;i++){
            arrayList.clear();
            flag=false;
            for(int j=i+1;j<count;j++){
                if((array[i]+array[j])==sum){
                    arrayList.add(array[i]);
                    arrayList.add(array[j]);
                    flag=true;
                    break;
                }
            }
            if (flag){
                break;
            }
        }
        return arrayList;
    }
```

## 链表与有环链表

如何判断一个单链表有环
**使用快慢指针法，即设置两个指针，一个为slow，一个为fast，从头开始对链表进行扫描。**

指针slow每次走一步，指针fast每次走两步。
如果存在环，则这两个指针一定会相遇。
如果不存在环，则fast指针会最先到达NULL而退出。
为何说二者一定相遇？因为fast会先进入环，在slow进入之后，如果把slow看做在前面，则fast在后面每次循环都会想slow靠近1，所以一定会相遇，而不会出现fast直接跳过slow的情况。

如何求环中单链表的环长
当快慢指针在环上相遇后，记录第一次相遇点为Pos，之后指针slow继续每次走一步，指针fast每次走两步。
![](D:\学习笔记\数据结构和算法\images\20190328205107433.png)

**在下次相遇时，fast比slow正好多走了一圈，即多走的距离正好等于环长**。

设从第一次相遇到第二次相遇，设slow走了**len**步，则fast走了**2\*len**步，相遇时多走了一圈：

- 环长 = 2*len - len

## 二叉树的下一个节点

 给定一个二叉树和其中的一个结点，请找出中序遍历顺序的下一个结点并且返回。注意，树中的结点不仅包含左右子结点，同时包含指向父结点的指针。 

分析：

下一个节点肯定是右子树或前一个节点的左子树，
    有右子树：遍历到它的左子树末端
    没有右子树：
                看父节点的左子树是否是自身
                        是，那么返回父节点
                        不是，那么跳到父节点重复上面过程，直到指针为空



````java
public TreeLinkNode GetNext(TreeLinkNode pNode){
        if(pNode==null){
            return pNode;
        }
        if(pNode.right!=null){
            pNode=pNode.right;
            while (pNode.left!=null){
                pNode=pNode.left;
            }
            return pNode;

        }
        while (pNode.next!=null) {
            if(pNode.next.left==pNode){
                return pNode.next;
            }
            pNode=pNode.next;

        }
        return null;
    }
````

## 对称二叉树

 请实现一个函数，用来判断一颗二叉树是不是对称的。注意，如果一个二叉树同此二叉树的镜像是同样的，定义其为对称的。 

思路：

只要判断根节点下的left和right是否相等，之后递归判断left.right和right.left，left.left和right.right是否相等就行

`````java
boolean isSymmetrical(TreeNode pRoot) {
        if(pRoot==null){
            return true;
        }
        return isSymmetricalHelper(pRoot.left,pRoot.right);

    }

    public boolean isSymmetricalHelper(TreeNode left,TreeNode right){
        if(left==null){
            return right==null;
        }
        if(right==null){
            return false;
        }
        if(left.val!=right.val){
            return false;
        }
        return isSymmetricalHelper(left.right,right.left)&&isSymmetricalHelper(left.left,right.right);

    }
`````

## 将二叉树之字打印

 请实现一个函数按照之字形打印二叉树，即第一行按照从左到右的顺序打印，第二层按照从右至左的顺序打印，第三行按照从左到右的顺序打印，其他行以此类推。 

思路：

需要用flag控制是否反转

```java
public static ArrayList<ArrayList<Integer>> Print(TreeNode pRoot) {
        if(pRoot==null){
            return new ArrayList<ArrayList<Integer>>();
        }
        Queue<TreeNode>queue=new LinkedList<>();
        ArrayList<ArrayList<Integer>> lists = new ArrayList<>();
        ArrayList<Integer> arrayList = new ArrayList<>();
        int flag=0;//0为从左到右，1为从右到左
        int nextCount=1;
        int count=0;
        queue.add(pRoot);
        while (!queue.isEmpty()){
            count++;
            TreeNode node = queue.poll();
            arrayList.add(node.val);
            System.out.println(node.val);
            if(node.left!=null){
                queue.add(node.left);
            }
            if(node.right!=null){
                queue.add(node.right);
            }
            if(count==nextCount){
                nextCount=queue.size();
                count=0;
                ArrayList<Integer> integers = new ArrayList<>();
                integers.addAll(arrayList);
                if(flag==1){
                    Collections.reverse(integers);
                }
                lists.add(integers);
                arrayList.clear();
                if(flag==0){
                    flag=1;
                }else {
                    flag=0;
                }
            }
        }
        return lists;
    }
```

