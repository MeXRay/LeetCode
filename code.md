# 1.minimum-depth-of-binary-tree
要求：二叉树的最小深度，有叶子结点的话不能认为根节点是最短路径

我的解法:
```java
public int run(TreeNode root) {
        if(root == null) return 0;
        if(root.left == null && root.right == null) return 1;
        int l=run(root.left)+1;// 深度遍历可以不怕一直左，所以允许用计算式可行
        int r=run(root.right)+1;
        if(root.right == null) r=Integer.MAX_VALUE;//每个空都会比较小，但不可取
        if(root.left == null) l=Integer.MAX_VALUE;
        return  l<r?l:r;
    }
```

还可以再较少两行代码：
```java
public int minDepth(TreeNode root) {
    if (root == null) return 0;
    int left = minDepth(root.left);
    int right = minDepth(root.right);
    if (left == 0 || right == 0) return left + right + 1;
    return Math.min(left, right) + 1;
}
```
思想区别在于我用计算式递归在计算深度而他，直接把深度一层一层返回，他的才是正宗思路，他实现了我所想；这也成为了他可以left+right+1，来节省代码，我的left
不会老是为0

实现区别在于 if (left == 0 || right == 0) return left + right + 1;  我在遇到空时把它们赋值为很大的数，他则是直接返回非空的那个数，以加的形式可以不管
哪个为空都保证是非空+1！省了代码行   他的这种返回方法不包括加自己，所以在返回值里+1了；而我是以1返回算上了自己

# 2.binary-tree-postorder-traversal
> 重点是遇到空怎么不让插入list,我返回值是int,钻了漏洞-1. 而且我把只有一个数的情况单独拿出来，因为不符合递归规律

# 3.reorder-list
> 不断头插尾，想用递归去处理链表，就算是全局变量，指针也一直指向第一个，不可行。

