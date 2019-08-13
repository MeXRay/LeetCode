[1.minimum-depth-of-binary-tree](#1.minimum-depth-of-binary-tree)

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

> 别人用栈做辅助空间，易于理解，但空间复杂度比我多，代码行数却差不多

# 3.reorder-list
> 不断头插尾，想用递归去处理链表，就算是全局变量，指针也一直指向第一个，不可行。换了种复制反转链表再逐个加到原先链表，一半的时候结束，但是时间复杂度不过关

> 他的思想和我差不多，但是不用复制，它拆分反转直接把我的复制浪费的空间和一半得停止的额外计算给完成了

```java
 public void reorderList(ListNode head) {
        if(head == null || head.next==null) return;
        ListNode fast,slow;
        fast=slow=head;
        while(fast.next!=null&&fast.next.next!=null){
            fast=fast.next.next;
            slow=slow.next;
        }
        //拆分
        ListNode after=slow.next;
        slow.next=null;
        //后半部分反转 前插法
        ListNode nhead=null;
        while(after!=null){
            ListNode next=after.next;
            after.next=nhead;
            nhead=after;
            after=next;
        }
        //相隔插入
        ListNode pre=head;
        while(nhead!=null){
            ListNode anext=nhead.next;
            ListNode fnext=pre.next;
            pre.next=nhead;
            nhead.next=fnext;
            pre=fnext;
            nhead=anext;
        }
    }
   ```

# 4.word-break
> 根据字典单词去用空格划分出所有可能。  我采用了split，但因为string的不可变性，我用了StringBuilder,想到了可以让不同字典里的单词做词首，但没去实现
回溯以便于不会后面还有另一种可能但是停在前面了。

> 他用动态规划背包问题，10行顶我至少30+代码。。  不过他只是判断是否能将字符串全都分割，即判断布尔值，而没有存放到ArrayList队列里。即便这样，他用了substring动态地去比较哪个范围的值的是否相等，好过我的split。而且他用一个一维数组来记录能否划分，其实是动态思想来记录解决了我停在了后面还有可能性的
地方。
```java
只判断
public boolean wordBreak(String s, Set<String> dict) {
        //动态规划 一次遍历 记录到最后
        int n=s.length();
        boolean[] res=new boolean[n+1];
        res[0]=true;
        for(int i=1;i<=n;i++)//把s当做背包？
            for(String word : dict){
                int len=word.length();
                if(len<=i && word.equals(s.substring(i-len,i))){//len<= 把一些可能的小分子计算在内了
                    res[i]=res[i]||res[i-len];//这也限制了是否第一个单词前面有无剩余
                }
            }
        return res[n];
    }
```
```java
不止判断
 public ArrayList<String> wordBreak(String s, Set<String> dict) {
        //42分钟
        //递归思想+遍历字典，每个可行的字典单词就有机会进入递归
        //还用了Map来记录，增加复用率
        Map<String,ArrayList<String>> map =new HashMap<>();
        return add(s,dict,map);
    }
    public ArrayList<String> add(String s,Set<String> dict,Map<String,ArrayList<String>> map){
        //利用s.startsWith来判断开头，之后s.substring(单词长度)来进入递归
        //它在每次递归都新建了一个ArrayList,其实无可避免，只要记住最简单模型，其余的消耗都是必须的
        if(map.containsKey(s)) return map.get(s);
        ArrayList<String> res=new ArrayList<>();
        if("".equals(s)){
            res.add("");
            return res;
        }
        for(String word : dict){
            if(s.startsWith(word)){
                for(String str:add(s.substring(word.length()),dict,map))
                    //其实每次只有一个str吧
                    res.add(word+(str==""?"":" ")+str);
            }
        }
        map.put(s,res);//而不是word,word，他解决的是catsanddoganddog,后面的anddog直接拿
        return res;
        
    }
 ```
# 5.single-number-ii
//最多出现3次，找出只出现一次的
