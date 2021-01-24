---
title: java刷题记录
tags: 2020.12.28
notebook: Java
---

有效的括号：
* 思路：括号的正确组织方式要与出栈的思维对应，这主要考察对栈的理解。
* 代码：
     ```java
    class Solution {
    public static char match(char ch) {
        char ans='0';
        switch (ch) {
            case '(' : ans = ')';break;
            case '[' : ans = ']';break;
            case '{' : ans = '}';break;
        }
        return ans;

    }
    public static boolean isValid(String s) {
        char[] ch=s.toCharArray();
        int len=ch.length;
        if (len == 0)
        return true;
        if(len %2 != 0)
        return false;

        Stack st = new Stack();
        for (int i = 0;i < len;i++)
        {
            if (ch[i] == '(' || ch[i]=='[' || ch[i] == '{')
            {
                st.push(ch[i]);
            }
            else if (i != 0 && !st.empty() &&match((Character) st.peek()) == ch[i] ) 
            {
                st.pop();
            }
            else 
            return false;

            
        }

        if (st.empty())return true;
        else return false;

    }

}
    
    ```

合并链表：
* 递归思想：
    * 何时用递归？
* 本题思路：
    * 链表初值比较，找出小的一个，寻找下一个，构成递归结构。
    * 终止条件：当一个值与空指针比较，说明该值为最大值，返回
    ```java
        public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        if (l1 == null)
        return l2;
        if (l2 == null)
        return l1;
        if (l1.val < l2.val) {
            l1.next = mergeTwoLists(l1.next,l2);
            return l1;
        }
        else {
            l2.next = mergeTwoLists(l1,l2.next);
            return l2;
        }
    }
    ```


删除重复元素：
* 思路：一个额外空间用于移动获取非重复的元素值，依次将获取到的非重复元素填充到数组，同时数组长度加1.注意的是最后提交时的数组长度要加1.
```java
class Solution {
    public int removeDuplicates(int[] nums) {
        int temp;
        int len = nums.length;
        int res = 0;
        for (int i = 0;i < len-1;) {
            temp = nums[i];
            i++;
            if (temp != nums[i]) {
                res++;
                nums[res] = nums[i];
            }
        }
        return res+1;

    }
}

```

27.移除元素
* 思路：有了上一道题的经验，这一道题就很简单了。重要的是理解题目的意思，发现解题的关键。
```java
class Solution {
    public int removeElement(int[] nums, int val) {
        int len = nums.length;
        int res = 0;
        for (int i =0 ;i < len;i++) {
            if(nums[i] != val) {
                nums[res] = nums[i];
                res++;
            }
        }
        return res;

    }
}
```

28.实现strStr()
* 思路：如果库函数可以一步完成的，进能量不要采用库函数。本题注意取值边缘的问题。
```java
public class strStr {
    public static void main(String[] args) {
        String haystack = "abc";
        String needle = "c";
        int pos = strStr(haystack,needle);
        System.out.println(pos);
    }
    public static int strStr(String haystack, String needle) {
        if (needle == "")
            return 0;
        int L = needle.length();
        int len = haystack.length();
        for (int start = 0;start <= len - L ;++start )  {
            System.out.println(haystack.substring(start,start+L));
            if(haystack.substring(start,start+L).equals(needle))  {
                return start;
            }
        }
        return -1;

    }
}


```

35 搜索插入位置
* 思路： 采用二分法查找。分别设置两个边界left和right,设置mid=(left+right)/2,当目标值大于nums[mid]值，表示mid值偏小，通过放大left来调整，当目标值小于nums[mid]，表示mid偏大，缩小right来调整。
```java
class Solution {
    public int searchInsert(int[] nums, int target) {
        int len = nums.length;
        int left = 0;
        int right = len-1;
        if (target > nums[right])
        return len;
        while(left < right) {
            int mid = (left+right)/2;
            if (target == nums[mid]) {
                return mid;
            }
            else if (target > nums[mid]) {
                left++;
            }
            else {
                right--;
            }
        }
        return left;
    }
}


```
38 外观数列
* 解题思路：每一层都是上一层的描述，因此很直观地看出使用递归地方法。重要的描述方法的定义。
```java
class Solution {
    public String countAndSay(int n) {
        if (n == 1)
        return "1";
        String s = countAndSay(n-1);
        char[] str = s.toCharArray();
        String res = "";
        int len = str.length;
        char temp = str[0];
        for (int i = 0;i < len; ) {
            int count = 0;
            while (i < len && temp == str[i]  ) {
                count++;
                i++;
            }
            res = res + String.valueOf(count) + temp;
            if (i < len)temp =str[i];
        }
        return res;

    }
}


```
53.最大子和 
* 思路：如果前一个数小于零则不加，大于零则加到当前元素，最后取数组的最大值为返回值。该方法为动态规划，做法是先算出所有可能的正值，然后做比较。
```java
import java.util.Arrays;
class Solution {
    public int maxSubArray(int[] nums) {
        int len = nums.length;
        int pre = 0;
        for (int i = 1;i < len;i++) {
            if (nums[i-1] > 0) {
                nums[i] += nums[i-1];
            }

        }
        Arrays.sort(nums);
        return nums[len - 1];

    }
}
```
* 贪心算法：为了保证数组内的值最大，边缘的负值要去掉，因而要有一个变量维护前值的和，当前值和为负数，则该值舍去。同时为了获取最大和，在累加过程中要加一次比较一次，因而要有一个维护该最大和的变量，不断与之后的加和比较选出较大的那一个。
```java
class Solution {
    public int maxSubArray(int[] nums) {
        int len = nums.length;
        int pre = 0 > nums[0] ? nums[0]:0;
        int sum = pre;
        int max = pre;
        for (int i = 0; i < len; i++) {

            if (pre < 0) {
                pre = 0;
            }
            sum = max;
            pre += nums[i];
            max = Math.max(pre, sum);
        }
        return max;


    }
}

```

58.最后一个单词长度
* 思路：简单方法使用字符串分割函数以"  "为分割符，之后取最后一个字符串的长度即可。更快的方式直接从字符串末尾第一个非空字符开始回退到下一个空字符为止。



54.二叉搜索树的第二大节点
* 解题思路:改变二叉树遍历顺序，使用变量记录递归遍历次数，输出即可。不知道为什么递归函数不能定义为有返回的类型，否则就会无法持续递归。

```java
class Solution {
    int time = 0,res = 0;
    public int kthLargest(TreeNode root, int k) {
        time = k;
        traveser(root);
        return res;
    }
    public void traveser (TreeNode root) {
        if (root.right != null)    traveser(root.right);
        time--;
            if(time == 0) {
            res = root.val;
            return ;
        }
        if (root.left != null)traveser(root.left);

    }
}



```
66.加一
* 解题思路：主要考虑进位和边缘情况两种。进位设置一个变量来计算每一位相加是否大于10，边缘情况要考虑扩充数组位数，因此要判断是否需要重新设置。

```java
class Solution {
    public int[] plusOne(int[] digits) {
        int add =1;
        int len = digits.length;
        int end = digits[len - 1];
        for (int i = len-1;i >=0;i--) {
            digits[i] += add;
            add=0;
            if (digits[i] == 10) {
                digits[i] = 0;
                add = 1;
            }
        }

```
67.二进制求和
* 思路：要想到问题的两个关键：
1.进位问题
2.对位长度不一致
本解答使用ASCII码避开了字符串转换数值的操作，另外对位补齐应该是这种题的一种常规思考方向。
```java
class Solution {
    public String addBinary(String a, String b) {
        StringBuilder ans = new StringBuilder("");
        int sum = 0;
        int ca = 0;
        for (int i = a.length()-1,j = b.length()-1;i >= 0 || j>= 0;i--,j--) {
            sum += i>= 0 ? a.charAt(i) - '0' : 0;
            sum += j>= 0 ? b.charAt(j) - '0' : 0;
            sum += ca;
            ca = sum/2;
            sum = sum %2;
            ans.append(sum);
            sum = 0;
        }
        if (ca == 1) ans.append(ca);
        return ans.reverse().toString();

    }
}
```

69.x的平方根
* 思路：要确定一个范围，可以想到的办法就是不断缩小边界，因而采用折半法。每次的数值在折半值的左边或者右边，如果在折半值的左边，就把右边界替换为折半值，而折半值在现有的边界基础上，取两个边界的中间部分，即l+(r-l)/2。这样当满足条件时，就会返回正确值。
```java
class Solution {
    public int mySqrt(int x) {
        int l = 0,r = x, ans = -1;
        while(l <= r) {
            int mid = l +(r - l)/2;
            if ((long) mid * mid <= x) {
                l = mid +1;
                ans = mid;
            } else {
                r = mid -1;
            }
        }
        return ans;

    }
}

```

* 牛顿迭代法：根据需求可以把问题写成y=x^2-C,其结果为所求根值。取某一点作为起始点，采用该点的斜率做直线y-y0=k(x-x0)，并求解该直线的零点，以该零点作为下一个x值，取其在函数y上的斜率再次做直线，求解零点。这样做的结果是，直线越靠近y函数的零点，其取值越趋近于目标值。因而只要将前后两次的x值迭代，当x^2与C的差小于1时，取到答案。
```java
class Solution {
    public int mySqrt(int x) {
        double ans = x;
        while ( Math.abs(x - ans * ans) > 0.5) {
            ans = ans/2 + x/(2*ans) ;
        }

        return  (int)ans;


    }
}

```

70 爬楼梯
* 思路：关键是要找对起点。重点在于到达某一状态与它之前的状态之间的关系。有了这种关联思维之后，就可以使用递推出答案。
```java
class Solution {
    public int climbStairs(int n) {
        int p =0,q=1,r=0;
        for (int i = 1;i<=n;i++) {
            r=p+q;
            p=q;
            q=r;
        }
        return r;

    }
}

```
83.删除列表中的重复元素
* 思路：这类题比较常见而且较为简单，去重就是选出不重复的元素，因而只要有一个空间记录不重复的值，并将该值一一放入新的答案空间即可。要注意参数为空的情况。
```java
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        if (head == null)
        return null;
        int temp = head.val;
        ListNode res = new ListNode(head.val);
        ListNode node = res;
        while(head != null ) {

            if ( temp != head.val ) {
                res.next = new ListNode(head.val);
                res = res.next;
                temp = head.val;
            }
            head = head.next;
        }
        return node;
    }
}


```

88.合并两个有序数组
* 思路：两个有序数组整合，数组的每个元素要对位比较，核心的思想在于取值的方式，每取走一个数组里的值，下一次的取值就要对应后移一位，如果没取走，则不移动。需要注意的是边界条件，当nums2为空的时候，直接返回。另外也要注意元素超出访问范围，这种情况说明一个数组的值已经取完，直接将剩下的数组赋值即可。
```java
class Solution {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        if (n==0)return;
        int[] ans = nums1.clone();
        for (int i =0,j=0;i+j < m+n ;) {
            if (i < m ) {
                if (j < n) {
                nums1[i + j] = ans[i] < nums2[j] ? ans[i++] : nums2[j++];
                } else {
                    nums1[i + j] = ans[i++];
                }
            }
            else {
                nums1[i+j] = nums2[j++];
            }
        }
    }
}

```
100.相同的树
* 思路：判断树是否相同，要判断的部分包括结构和值，由于结构是层层递进的，因而初步的思路就是使用递归来遍历树，只有层层遍历之后才能返回true，因而代码如下。节点为空的情况是要考虑的，不然会影响结构的判断。
```java
class Solution {
    public boolean isSameTree(TreeNode p, TreeNode q) {
        if (p == null || q==null) {
            if (p == q)
            return true;
            else
            return false;
        }
        if (p != null && q!= null) {
          if (p.val == q.val) {
              if (isSameTree(p.left,q.left)) {
                  if (isSameTree(p.right,q.right)) 
                  return true;
                  else return false;
              }
              else return false;
          }
          else return false;
        
        }
        return false;
    }
}

```
101.对称二叉树
* 思路：首先要确定判断条件是什么：根据镜像条件来看，对于某一级的两个对称节点p和q，若想要该二叉树镜像，需要满足：
    1. p.val = q.val
    2. p的左节点等于q的右节点，p的右节点等于q的左节点。
    以此作为递归的条件，满足则返回true.
```java
class Solution {
    public boolean isSymmetric(TreeNode root) {
        if (root == null)
        return true;
        return dfs(root.left,root.right);

    }
    public boolean dfs(TreeNode p, TreeNode q) {
        if (p == null || q == null) {
            if (p == q) return true;
            else return false;
        }
        if (p.val == q.val) {
            if(dfs(p.left,q.right)&& dfs(p.right,q.left))
            return true;
            else return false;
        }
        else return false;
    }
}

```
* 迭代：迭代是将递归展开，逐级检验并返回结果。使用迭代法要将节点入队，判断时出队。每一级节点都要将左右子节点入队，这样相当于将树展开，然后依次出队判断。
```java
    public boolean isSymmetric(TreeNode root) {
        return check(root,root);
    }
    public boolean check(TreeNode u,TreeNode v) {
        Queue<TreeNode> q = new LinkedList<TreeNode>();
        q.offer(u);
        q.offer(v);
        while(!q.isEmpty()) {
            u=q.poll();
            v=q.poll();
            if (u==null && v==null) {
                continue;
            }
            if ((u== null ||v== null) && u != v) {
                return false;
            }
            if (u.val != v.val) return false;

            q.offer(u.left);
            q.offer(v.right);

            q.offer(u.right);
            q.offer(v.left);
        }
        return true;
    }

```
# 错题
104.返回二叉树的最大深度
* 思路：涉及到递归问题，应该有一个全局思想就是本函数的功能在整体问题的解决有什么作用，是否可以利用。函数的功能是返回二叉树的最大深度，那么这种返回值对问题的解决肯定有作用。
```java
class Solution {

    public int maxDepth(TreeNode root) {
        if (root == null)
        return 0;
        else {
            int l = maxDepth(root.left);
            int r = maxDepth(root.right);
            return Math.max(l,r)+1;
        }

    }
}
```
* 广度搜索：将节点入队，入队的顺序为每一层从左到右的顺序。然后依序出队。出完一层就加一个深度。出队的同时完成下一层的入队。
```java


```

104.二叉树的层序遍历II
* 思路：本题可以借用“二叉树的深度”这道题的迭代法结题，即使用队列将树展开，展开的顺序刚好就是自顶向下每一层的顺序。之后再将生成的答案列表反转即可获得自底向上的列表。
```java
 import java.util.Arrays;
class Solution {
    public List<List<Integer>> levelOrderBottom(TreeNode root) {
        if (root == null) {
            List<List<Integer>> res = new ArrayList<>();
            return res;
        }
        int size = 1;
        Queue<TreeNode> q = new LinkedList<TreeNode>();
        q.offer(root);
        List<List<Integer>> res = new ArrayList<>();
        while(!q.isEmpty()) {
            List<Integer> ans = new ArrayList();
            res.add(ans);
            size = q.size();
            while(size != 0 ) {
                TreeNode u = q.poll();
                ans.add(u.val);
                // ans = ans.next;
                if (u.left != null) {
                    q.offer(u.left);
                }
                if(u.right != null) {
                    q.offer(u.right);
                }
                size--;
            }
        }
        Collections.reverse(res);
        return res;
    }
}

```
108.有序数组转换平衡二叉搜索树
* 思路：还是递归思想，首先看函数的功能是返回给定有序数组的平衡二叉树根节点，这样就可以看出这是一个递归问题，对于每一级的节点，都利用函数返回其平衡二叉搜索树即可。问题分为两步：
1. 将数组的中间节点作为根节点
2. 数组分为两部分，分别调用函数获取上述根节点的左右子节点。
如此递归即可。
```java
class Solution {
    public TreeNode sortedArrayToBST(int[] nums) {
        int len = nums.length;
        if (len == 1) {
            TreeNode r = new TreeNode(nums[0]);
            return r;
        }
        if( len == 0)return null;

        TreeNode r = new TreeNode(nums[len/2]);
        int[] num1 = new int[len/2];
        int[] num2 = new int[len -len/2 -1];
        for (int i = 0;i<len/2;i++) {
            num1[i] = nums[i];
            if(i < (len - len/2 -1))num2[i] = nums[len/2 + i +1];
        }
        r.left = sortedArrayToBST(num1);
        r.right = sortedArrayToBST(num2);

        return r;

    }
}

```
110.平衡二叉树
* 思路：这题还是要递归的思路，但是要考虑到末级递归要判断是否平衡，第一级要判断两边是否平衡，因此每一级的递归需要两个参数：
1. 当前节点是否平衡
2. 上一级节点的两边是否平衡
```java
class Solution {
    public boolean isBalanced(TreeNode root) {
        if (root == null) return true;
        if (root.left == null || root.right == null) {
            if (Math.abs(deep(root.left)-deep(root.right)) <= 1)
            return true;
            else return false;
        }
        if(isBalanced(root.left) && isBalanced(root.right)) {
            if (Math.abs(deep(root.left)-deep(root.right)) <= 1)
            return true;
            else return false;
        }
        return false;


    }
    public int deep (TreeNode root) {
        if(root == null)
        return 0;
        int l = 1+deep(root.left);
        int r = 1+deep(root.right);
        return Math.max(l,r);
    }
}

```
111.二叉树最小深度
* 思路：最小深度是比较简单的只要取递归过程中的最小值就可以，但是要注意根节点的左子树为空的情况，因为最小深度是到叶子节点，所以根节点是不算的，故而要排除这种情况。本题待优化。
```java
class Solution {
    public int minDepth(TreeNode root) {
        if (root == null) {
            return 0;
        }

        if (root.left == null && root.right == null) {
            return 1;
        }

        int min_depth = Integer.MAX_VALUE;
        if (root.left != null) {
            min_depth = Math.min(minDepth(root.left), min_depth);
        }
        if (root.right != null) {
            min_depth = Math.min(minDepth(root.right), min_depth);
        }

        return min_depth + 1;
    }
}

```

118.杨辉三角
* 思路：在给定规则的基础上实现双循环遍历就可以得到数组。边界值的处理可以直接赋值。
```java
    public List<List<Integer>> generate(int numRows) {
        List<List<Integer>> ans = new ArrayList<>();
        if (numRows == 0)
        return ans;
        for (int i = 0;i< numRows;i++) {
            List<Integer> row = new ArrayList();
            for (int j=0;j<=i;j++) {
                if ((j==0 ||j==i)) {
                    row.add(1);
                }
                else {
                    row.add(ans.get(i-1).get(j)+ans.get(i-1).get(j-1));
                }
            }
            ans.add(row);
        }
        return ans;
    }

```

118.杨辉三角II
* 思路：杨辉三角每一行的元素数值符合组合规律C(m,n)，获取某一行的元素只需要依次计算即可，这样做的优点是算法前面每一行的值不用计算，复杂度为k。C(m,n)与C(m,n-1)之间相差一个(m-n+1)/n.
```java
class Solution {
    public List<Integer> getRow(int rowIndex) {
        List<Integer> ans = new ArrayList<>();
        for (int i = 0;i<= rowIndex;i++) {
            if(i== 0 || i== rowIndex) ans.add(1);
            else ans.add(ans.get(i-1)*(rowIndex-i+1)/i);
        }
        return ans;

    }
}

```

121.买卖股票的最佳时机
* 思路：要寻找最大差值，就要向后寻找买入最小值，同时计算后面元素与前面元素的差值，并计算该值与前值的最大值。要记录两个数字：
1. 买入的最小值
2. 目前可得的最大利润
```java
    public int maxProfit(int[] prices) {
        if (prices.length == 0) return 0;
        int min = prices[0],pre = 0;
        for (int i = 0;i<prices.length;i++) {
            min = Math.min(min,prices[i]);
            int val = prices[i] - min > 0? prices[i] -min:0;
            pre = Math.max(pre,val); 
        }
        return pre;
    }

```
122.买入股票最佳时机II
* 思路：要想使多次买卖的值最大，只要在买入的过程中累加每一次有差值的收益即可。
```java
    public int maxProfit(int[] prices) {
        if (prices.length == 0) return 0;
        int min = prices[0],pre = 0;

        for (int i = 0;i< prices.length;i++) {
            min = Math.min(min,prices[i]);
            int val = prices[i] - min >0 ? prices[i]-min:0;
            pre+=val;
            min=prices[i];
        }
        return pre;
    }

```
环形链表
* 思路：快慢指针。当链表成环，慢指针一定会与快指针重合，这时可以判断链表环路。
```java
public class Solution {
    public boolean hasCycle(ListNode head) {
        if (head == null || head.next == null) {
            return false;
        }
        ListNode slow = head;
        ListNode fast = head.next;
        while (slow != fast) {
            if (fast == null || fast.next == null) {
                return false;
            }
            slow = slow.next;
            fast = fast.next.next;
        }
        return true;
    }
}

```
* 思路2：最大值不重复：遍历链表的同时将遍历过的位置赋值为最大值。当未遍历过的节点出现最大值，说明链表成环。
```java
public class Solution {
    public boolean hasCycle(ListNode head) {
        int val =Integer.MAX_VALUE;
        while (head != null) {
            head.val = val;
            head = head.next;
            if(head!=null && head.val == val) return true ;
        }
        return false;

    }
}

```
* 思路3：Set集合添加每个节点，当有重合时，会返回旧值，以此作为判断该节点是否被遍历过，从而判断出结果。
```java
public class Solution {
    public ListNode detectCycle(ListNode head) {
        Set<ListNode> seen = new HashSet<ListNode>();
        while (head != null) {
            if (!seen.add(head)) {
                return head;
            }
            head = head.next;
        }
        return null;
    }
}

```

155.最小栈
* 思路：本题的难点在于常数之间返回栈中最小元素。在取最小值时，采用辅助栈来记录最小元素。同时要记得当原栈弹出最小值时，辅助栈也要对应弹出。判断辅助栈是否压栈的时候，等号也要取，否则可能会有访问空指针的情况发生。
```java
class MinStack {
    Stack<Integer> stack;
    Stack<Integer> xStack;
    /** initialize your data structure here. */
    public MinStack() {
        stack = new Stack<>();
        xStack = new Stack<>();
    }
    
    public void push(int x) {
        stack.push(x);
        if(xStack.isEmpty() || x <= xStack.peek())
        xStack.push(x);
    }
    
    public void pop() {
        if(stack.pop().equals(xStack.peek()))
        xStack.pop();
    }
    
    public int top() {
        return stack.peek();
    }
    
    public int getMin() {
        return xStack.peek();
    }
}

```
160.相交链表
* 哈希遍历：使用哈希表先对其中一条链遍历，然后再对第二条遍历，当遇到重复的节点，哈希表判断值。
* 双指针法：定义两个指针p1,p2，分别指向headA和headB，当p1指针到达终点时，返回headB的位置，p2到达末尾时返回headA的位置。设两个节点到相交点的距离为a，b,相交点到末尾的长度为c，则以该方式遍历，在相交点会有a+c+b=b+c+a，因此当两个指针相遇时就是所求节点。这类题需要灵活使用指针，观察两者之间的关系，寻求解决。
```java
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        if (headA == null || headB== null)
        return null;
        ListNode p1 = headA,p2 = headB;
        while(p1 != p2) {
            p1 = (p1 == null)?headB:p1.next;
            p2 =( p2 == null)?headA:p2.next;
        }
        return p2;
    }
}

```
167.两数之和2
* 首先想到的是双指针，接着要缩短查找空间，右边界大于target肯定不行，于是计算好右边界才正式开始移动指针。移动的时候左右同时移动，这样是比较省时间，但是也会漏掉左值和右值比较接近的情况，因此不能设置左值小于右值，在计算结束后排序即可。
```java
class Solution {
    public int[] twoSum(int[] numbers, int target) {
        int i=0,j=numbers.length-1; 
        while(numbers[j]>target)j--;
        while(numbers[i]+numbers[j] != target) {
            if(numbers[i]+numbers[j]<target)i++;
            if(numbers[i]+numbers[j]>target)j--;
        }
        int[] ans = {i+1,j+1};
        Arrays.sort(ans);
        return ans;
        
    }
}

```
168.Excel表达的名称
* 一道小题花了很久的时间，反思了一下做题的不足。上来做题就应该把最核心的部分思考好，之后再动手，难的不是框架，而是这最关键的部分。边界条件的处理很重要，当n为26的整数倍的时候，一边是添加字符，另一边数字的处理要减去26，再除以26。想清楚这两点，问题就解决了。
```java
class Solution {
    public String convertToTitle(int n) {
        StringBuilder res = new StringBuilder();
        while(n != 0) {
            if (n % 26 == 0) {
                res.append('Z');
                n -= 26;
                n/=26;
            }
            else if(n% 26 != 0) {
                res.append((char)(n%26 +'A'-1));
                n/=26;
            }
        }
        return res.reverse().toString();
    }
}

```
169.多数元素
* 排序整理：排序后相同的元素放在一起，使用一个变量做计数，每次数字改变时，做一次比较，当次数大于n/2时，为答案。时间复杂度为nlogn，主要的开销在开始的排序上。
```java
    public int majorityElement(int[] nums) {
        Arrays.sort(nums);
        int time = 0,temp = nums[0];
        for(int i = 0;i<nums.length;i++) {
            if(temp == nums[i])
            time++;
            if(temp != nums[i]) {
                if(time >nums.length/2) break;
                temp = nums[i];
                time=1;
            }
        }
        return temp;
    }

```
172.阶乘后的零
* 阶乘的时间复杂度很高，而本题要求logn复杂度，因此不能就算阶乘再统计。考虑不计算阶乘，末尾的零是由因式里的10产生的，因此考虑因式中能分解多少个5。找到这个思路，后面的统计就好做了，累加除以5的结果，能得到因式中5的数量，即为答案。
```java
    public int trailingZeroes(int n) {
        int num =0;
        while(n != 0) {
            num += n/5;
            n /= 5;
        }
        return num;
    }

```
173.二叉搜索迭代器
* 思路：因为判断与返回最小值的时间都要求为O(1),因此参考前面返回最小栈的题目，做一个辅助栈，将搜索二叉树反中序遍历存储到辅助栈中，调用时弹出即可。
```java
class BSTIterator {
    Stack<Integer> st = new Stack<>();
    public BSTIterator(TreeNode root) {
        dfs(root);
    }
    public void dfs(TreeNode root) {
        if(root == null) return;
        if(root.right != null) dfs(root.right);
        st.add(root.val);
        if(root.left != null) dfs(root.left);
        
    }
    
    public int next() {
        return st.pop();
    }
    
    public boolean hasNext() {
        if(!st.isEmpty())
        return true;
        else 
        return false;
    }
}

```
* 迭代：由于上述思路使用空间为O(n),不满足题目要求的h，因此需要进行优化。所以考虑将遍历过程进行存储。将左节点全部入栈，右节点在根节点出栈的时候执行相同的操作。
```java
class BSTIterator {
    Stack<TreeNode> st;
    public BSTIterator(TreeNode root) {
        
        st = new Stack<>();
        TS(root);
    }
    public void TS(TreeNode root) {
        st.add(root);
        while(root.left != null) {
            st.add(root.left);
            root = root.left;
        }
        
    }
    public int next() {
        TreeNode ts = st.pop();
        if(ts.right != null)
        TS(ts.right);
        return ts.val;
    }
    public boolean hasNext() {
        return !st.isEmpty();

    }
}

```
5.最长回文子串
* 思路：一开始采用遍历的方式，但超时了。问题在于每当移动一个字符串，就要从当前位置重新左右遍历，因此时间复杂度很高，会超时。
```java
class Solution {
    public String longestPalindrome(String s) {
        StringBuilder str = new StringBuilder(s);
        int len = s.length();
        String pre="";
        for (int i = 0;i < len; i++) {
            for(int j =i;j<=len;j++) {
                StringBuilder str1 = new StringBuilder(str.substring(i,j));
                String re = str1.reverse().toString();
                if(str.substring(i,j).equals(re))
                pre = j-i>pre.length() ? str.substring(i,j) : pre;
            }
        }
        return pre;
    }
}

```
* 采用答案提示的方式，考虑缩减重复遍历的部分，采用布尔数组来存放两端值是否相同，这样即使窗口移动，前面的遍历记录仍然可以使用。
```java
    public String longestPalindrome(String s) {
        int n = s.length();
        boolean[][] dp = new boolean[n][n];
        String ans= "";
        for (int l = 0;l<n;l++) {
            for(int i =0 ;i+l<n;i++) {
                int j = i+l;
                if(l==0)
                {dp[i][j]=true;}
                else if(l==1) {
                    dp[i][j] = s.charAt(i)==s.charAt(j);
                }
                else {
                    dp[i][j] = (s.charAt(i) == s.charAt(j) && dp[i+1][j-1]);
                }
                if(dp[i][j] && 1+l >ans.length())
                ans = s.substring(i,i+l+1);
            }
        }
        return ans;
    }

```
* 使用中心扩展的方法：考虑两种情况，第一种中心字符是单个字符，这样就将字符串以单个字符为中心向两边扩展。另一种是中心字符是两个，以两个字符为中心向外扩展。根据扩展长度计算上下界即可。
```java
class Solution {
    public String longestPalindrome(String s) {
        int n = s.length();
        if(n==0 || n==1)
        return s;
        String ans = "";
        int start = 0,end = 0;
        for (int i = 0;i < n;i++) {
            int len1 = boll(s,i,i);
            int len2 = boll(s,i,i+1);
            int len = Math.max(len1,len2);
            if(len > end -start) {
                start = i - (len-1)/2;
                end = i+len/2;
                
            }
        }
        return  s.substring(start,end+1);
    }
    public int boll(String s,int left,int right) {
        while(left >=0 && right < s.length() && s.charAt(left) ==s.charAt(right) ) {
            --left;
            ++right;
        }
        return right - left -1;
    }
}

```
6.Z字变换
* 思路：按照顺序将字符串放入StringBuilder数组中，然后再拼接起来，在存放的过程中，行是变换的，因此可以添加一个移动行序的变量。思路上相当于真实地摆出Z字形，然后直接提取字符串即可。难点在于实现方式。
```java
class Solution {
    public String convert(String s, int numRows) {
        if (numRows == 1)
        return s;
        List<StringBuilder> rows = new ArrayList<StringBuilder>();
        for (int i =0;i< Math.min(numRows,s.length());i++) {
            rows.add(new StringBuilder());
        }
        int curRow = 0;
        boolean goDown = false;

        for(char c : s.toCharArray()) {
            rows.get(curRow).append(c);
            if(curRow == 0|| curRow == numRows-1) goDown = !goDown;
            curRow+= goDown?1:-1; 
        }
        StringBuilder ans = new StringBuilder();
        for(StringBuilder str : rows) {
            ans.append(str);
        }
        return ans.toString();
    }
}

```
* 直接按照字符的取值间隔计算，这样做的难点是字符的序列位置不好推算。
```java
class Solution {
    public String convert(String s, int numRows) {
        int n = s.length();
        if(numRows == 1)
        return s;
        int cycle = 2*numRows -2;
        StringBuilder ret = new StringBuilder();
        for(int i = 0;i< numRows;i++) {
            for(int j = 0;j + i <n;j += cycle) {
                ret.append(s.charAt(i+j));
                if(i != 0 && i != numRows -1 && j+cycle-i <n) 
                ret.append(s.charAt(j+cycle-i));
            }
        }
        return ret.toString();
    }
}

```

24.两两交换链表中的节点
* 思路：节点两个一组，互换位置就可以了。使用递归是一个比较好的办法。
```java
class Solution {
    public ListNode swapPairs(ListNode head) {
        if(head ==null || head.next == null) {
            return head;
        }
        ListNode ls = head.next;
        head.next = swapPairs(ls.next);
        ls.next = head;
        return ls;
    }
}
```
29.两数相除
* 思路：除法的机制就是找出最大倍数的因子并累加。因此要经过一个倍数叠加的过程，再就是对边界条件的处理，讨论1和-1两种情况。
```java
class Solution {
    public int divide(int dividend, int divisor) {
        if(divisor == 1) {
            return dividend < Integer.MAX_VALUE ? dividend:Integer.MAX_VALUE;
        }
        if(divisor == -1) {
            if(dividend <= -2147483648)
            return Integer.MAX_VALUE;
            else return -dividend;
        }

        long a = dividend,b = divisor;
        int sgn = 1;
        if((a>0 && b<0) || (a<0 && b>0))
        sgn = -1;
        a = a>0?a:-a;
        b = b>0?b:-b;
        int ans = div(a,b);
        if(sgn >0)return ans ;
        else return -ans;
    }
    public int div(long a,long b) {
        if(a<b)return 0;
        long count = 1;
        long tb = b;
        while(tb+tb <= a) {
            count = 2*count;
            tb = tb + tb;
        }
        return (int)count+div(a-tb,b); 
    } 
}
```

31.下一个排列
* 思路：要注意的是下一个排列的值只比前一个排列大。这样的话除了要用大数替代反序位置的数，还要考虑是不是有更小的较大数放在高位，同时对尾列进行处理。
```java

```
