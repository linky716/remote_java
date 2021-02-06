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
* 思路：要注意的是下一个排列的值只比前一个排列大。这样的话除了要用大数替代反序位置的数，还要考虑是不是有更小的较大数放在高位，同时对尾列进行处理。按照替换的规律，首先要找到从尾部向前第一个降序，下一个大的数列要将这个逆序的较小的数和从尾部向前比他稍大的数互换，保证高位的替换尽可能小。替换之后，这个替换位后面的数字要被置为顺序排列，保证它是最小的。
```java
class Solution {
    public void nextPermutation(int[] nums) {
        if(nums.length <= 1) return ;
        int i= nums.length-2;
        while(i >=0 && nums[i] >= nums[i+1]) {
            i--;
        }
        // swap(nums,i,i-1);
        if(i >= 0){
        int j = nums.length-1;
        while(j>= 0 && nums[j] <= nums[i]) {
            j--;
        }
        swap(nums,j,i);
        }
        resort(nums,i+1);
    }
    public void swap(int[] nums,int a,int b) {
        int temp = nums[a];
        nums[a]= nums[b];
        nums[b]= temp;
    }
    public void resort(int[] nums,int pos) {
        int len = nums.length-1;
        while(pos < len) {
            swap(nums,pos,len);
            pos++;
            len--;
        }
    }
}
```

33.搜索旋转排序数组
* 思路： 本题要求时间复杂度logn,这就提示采用二分法来解决问题。要在有序数组旋转了的情况下找出目标值。将数组分为两半，判断target是否在有序的那一半中，如果在，使用二分法继续查找，如果不是，将区间指向无序的一边，再次二分查找，直到区间有序且目标值在区间内，找到该值。否则就返回-1.
```java
class Solution {
    public int search(int[] nums, int target) {
        if(nums.length ==0) return -1;
        if(nums.length == 1) return nums[0] == target ? 0:-1;
        int left = 0,right = nums.length-1;
        while(left <= right) {
            int mid = (right + left)/2;
            if(target == nums[mid]) return mid;
            if(nums[0] <= nums[mid]) {
                if(target < nums[mid] && target >= nums[0] ) {
                    right = mid-1;
                }
                else
                left = mid+1;
            }
            else if(nums[0] > nums[mid]) {
                if(target > nums[mid] && target <= nums[nums.length-1]) {
                    left = mid+1;
                }
                else 
                right = mid-1;
            }
        }
        return -1;
    }
}
```
34.在排序数组中查找元素的第一个和最后一个位置
* 思路： 要求logn的复杂度，自然考虑使用二分法。先查找到目标值，然后使用两个变量记录左右的边界值即可。题目本身并不难，细节的地方需要注意，left和right是否相等，数组是否会越界等问题要思考。
```java
class Solution {
    public int[] searchRange(int[] nums, int target) {
        if(nums.length == 0) return  new int[] {-1,-1};
        int left = 0,right = nums.length-1;
        int std =-1;
        while(left <= right) {
            int mid = left + (right - left)/2;
            if(nums[mid] == target) {
                std = mid;
                break;
            }
            else if(nums[mid] > target) {
                right = mid -1;
            }
            else
            left = mid+1;
        }
        if(std == -1) return new int[] {-1,-1};
        left = std;
        right = std;
        while(left >=0 &&nums[left] ==target) left--;
        while(right <= nums.length-1 && nums[right] == target) right++;
        return new int[] {left+1,right-1};  

    }
}
```

36.有效的数独
* 思路：满足行，列，子宫格内没有重复的元素。要想复杂度最低，最要一次遍历。很容易想到使用HASH表来记录出现的次数，问题在于如何一次性完整遍历三个条件。
```java
class Solution {
    public boolean isValidSudoku(char[][] board) {
        HashMap<Integer,Integer>[] row = new HashMap[9];
        HashMap<Integer,Integer>[] col = new HashMap[9];
        HashMap<Integer,Integer>[] box = new HashMap[9];
        for(int i=0;i<9;i++) {
            row[i] = new HashMap<>();
            col[i] = new HashMap<>();
            box[i] = new HashMap<>();
        }
        for(int i = 0;i<9;i++) {
            for(int j = 0;j<9;j++) {
                char num = board[i][j];
                if(num != '.') {
                    int n = (int)num;
                    int index= (i/3)*3 + j/3;
                    row[i].put(n,row[i].getOrDefault(n,0)+1);
                    col[j].put(n,col[j].getOrDefault(n,0)+1);
                    box[index].put(n,box[index].getOrDefault(n,0)+1);
                    if((row[i].get(n) > 1) || (col[j].get(n) > 1 ) || (box[index].get(n)>1)) return false;
                }
            }
        }
        return true;
    }
}
```
39.组合总和
* 思路： 使用递归搜寻下一个值，可以跳过，也可以不跳过。选择当前值之后，下一次递归的起始仍为当前位置，表示可以重复使用。注意每次回溯调用后删掉尾部元素是为了回到上一层的位置。这样才符合回溯的概念。
```java
class Solution {
    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        List<List<Integer>> ans = new ArrayList<>();
        List<Integer> combin = new ArrayList<>();
        dfs(candidates,target,0,combin,ans);
        return new ArrayList(new HashSet<>(ans));
    }
    public void dfs(int[] candidates,int target,int idx,List<Integer> combin,List<List<Integer>> ans) {
        if(idx == candidates.length) {
            return;
        }
        if( target == 0) {
            ans.add(new ArrayList<Integer>(combin));
        }
        dfs(candidates,target,idx+1,combin,ans);
        if(target - candidates[idx] >= 0) {
            combin.add(candidates[idx]);
            dfs(candidates,target-candidates[idx],idx,combin,ans);
            combin.remove(combin.size()-1);
        }
    }
}
```
40.组合总和II
* 思路： 在第一题的思路基础上，不可以使用重复的元素，只要下一次开始递归的顺序不包含当前即可。为了去除重复的集合，保证相同数字集合的组合顺序相同，可以在开始时对数组排序。
```java
class Solution {
    public List<List<Integer>> combinationSum2(int[] candidates, int target) {
        Arrays.sort(candidates);
        List<List<Integer>> ans = new ArrayList<>();
        List<Integer> combin = new ArrayList();
        dfs(candidates,combin,ans,0,target);
        return new ArrayList(new HashSet(ans)); 
    }
    public void dfs(int[] candidates,List<Integer> combin,List<List<Integer>> ans,int idx,int target) {
        if(target == 0) {
            ans.add(new ArrayList<>(combin));
            return;
        }
        if(idx == candidates.length) {
            return;
        }

        dfs(candidates,combin,ans,idx+1,target);
        if(target - candidates[idx] >= 0) {
            combin.add(candidates[idx]);
            dfs(candidates,combin,ans,idx+1,target-candidates[idx]);
            combin.remove(combin.size()-1);
        }
    }
}
``` 
43.字符串相乘
* 思路： 使用逐一相乘然后相加的办法，累积相加。
```java
class Solution {
    public static String multiply(String num1, String num2) {
        if(num2.equals("0") || num1.equals("0")) return "0";
        StringBuilder str = new StringBuilder();
        StringBuilder pre = new StringBuilder("0");
        for(int i = num1.length()-1;i>=0;i--) {
            int ce = 0;
            char mul = num1.charAt(i);
            for(int j = num2.length()-1;j >= 0;j--) {
                int sum = (mul -'0') * (num2.charAt(j)-'0');
                str.append(String.valueOf((sum + ce) %10));
                ce = (sum + ce)/10;
            }
            if(ce != 0) str.append(String.valueOf(ce));
            for(int l = num1.length()-i-1;l > 0;l--) {
                str.insert(0,0);
            }
            pre = sum(str,pre);
            str.setLength(0);
        }
        return pre.reverse().toString();
    }
    public static StringBuilder sum (StringBuilder a,StringBuilder b) {
        int ce = 0;
        StringBuilder re = new StringBuilder("");
        for(int i = 0;i < a.length() || i <b.length();i++) {
            char m = i < a.length() ? a.charAt(i):'0';
            char n = i < b.length() ? b.charAt(i):'0';
            int sum = m -'0' +n-'0';
            re.append((sum + ce) %10);
            ce = (sum+ce)/10;
        }
        if(ce != 0) re.append("1");
        return re;
    }
}
```
* 相乘：将两个字符串中任意两个字符位相乘的值保存到对应位置数组中，最后对应相加处理即可。
```java
    public static String multiply(String num1, String num2) {
        if(num1.equals("0") || num2.equals("0"))
            return "0";
        StringBuilder str = new StringBuilder();
        int m = num1.length(),n= num2.length();
        int[] arr = new int[m+n];
        for(int i = m-1;i>=0;i--) {
            for(int j = n-1;j>=0;j--) {
                arr[i+j+1] += (num1.charAt(i)-'0')*(num2.charAt(j)-'0');
            }
        }
        for(int i = m+n-1;i >0;i--) {
            arr[i-1] += arr[i]/10;
            arr[i] %=10;
        }
        int index = arr[0] == 0? 1:0;
        for(int i = index;i<= m+n-1;i++) {
            str.append(arr[i]);
        }
        return str.toString();
    }
```
46.全排列
* 思路： 全排列的种类很容易让人联想到阶乘，事实上全排列的方式就是如此。首先选取第一个值，再从剩下的数值中选取下一个值。如此反复直到获取最后一个值。因此，在递归时要做两件事，一是要从剩下的所有位置中获取下一个元素，二是对抽取元素后的数组进行组织，进行下一次抽取。代码如下：
```java
    public List<List<Integer>> permute(int[] nums) {
        List<List<Integer>> ans = new ArrayList<>();
        List<Integer> combin = new ArrayList<>();
        dfs(nums,0,combin,ans);
        return ans;
    }
    public void dfs(int[] nums,int pos,List<Integer> combin,List<List<Integer>> ans) {
        if(pos == nums.length) {
            ans.add(new ArrayList(combin));
            return;
        }
        for(int i = pos;i<nums.length;i++) {
            
            combin.add(nums[i]);
            swap(nums,pos,i);
            dfs(nums,pos+1,combin,ans);
            swap(nums,pos,i);
            combin.remove(combin.size()-1);
        }
        
    }
    public void swap(int[] nums,int a,int b) {
        int temp = nums[a];
        nums[a] = nums[b];
        nums[b] = temp;
        return; 
    }
```
48.旋转图像
* 思路： 题目要求原地翻转，因而首先考虑元素位置的变化关系，从而可以得出规律。在此基础上可以发现，一个图像可以分成4个可移动的块，他们的坐标有对应关系。从而可以实现变换。
```java
class Solution {
    public void rotate(int[][] matrix) {
        int n = matrix.length;
        
        for(int i = 0;i< n/2 ;i++) {
            for(int j = 0;j<(n+1)/2;j++) {
                int temp = matrix[i][j];
                matrix[i][j] = matrix[n-j-1][i];
                matrix[n-1-j][i]=matrix[n-1-i][n-1-j];
                matrix[n-1-i][n-1-j] = matrix[j][n-1-i];
                matrix[j][n-1-i] =temp;

            }
        }
    }
}
```
* 翻转替代：将原矩阵水平翻转后再按对角线折叠可以实现目标矩阵。因此需要做两次变化。
```java
class Solution {
    public void rotate(int[][] matrix) {
        int n = matrix.length;
        for(int i = 0;i< n/2;i++) {
            for(int j = 0;j< n;j++) {
                int temp = matrix[i][j];
                matrix[i][j] = matrix[n-1-i][j];
                matrix[n-1-i][j] = temp;
            }
        }
        for(int i = 0;i < n;i++) {
            for(int j = i;j<n;j++) {
                int temp = matrix[i][j];
                matrix[i][j] = matrix[j][i];
                matrix[j][i] = temp;
            }
        }
    }
}
```

49.字母异位词分组
* 思路： 使用哈希来确定是否一致，首先要考虑将符合条件的一组字符串使用统一的标识来区分，并统计。也就是Map的key值要一致。有两种方式来统一key值。一个是将字符串的字母排序，含有相同字母的数字将产生相同的字符串，作为key值。另一个方法是使用数字来统计字符串中出现的字符，并整合为key值。
```java
class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        Map<String,List<String>> map = new HashMap<>();
        for(String str:strs) {
            int[] count = new int[26];
            for(int j = 0;j < str.length();j++) {
                count[str.charAt(j)-'a']++;
            }
            StringBuilder sb = new StringBuilder();
            for(int k = 0;k <26 ;k++) {
                if(count[k] != 0) {
                    sb.append((char)(k+'a'));
                    sb.append(count[k]);
                }
            }
            String key = sb.toString();
            List<String> list = map.getOrDefault(key,new ArrayList<>());
            list.add(str);
            map.put(key,list);
        }
        return new ArrayList<List<String>>(map.values());
    }
}

```
```java

class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        Map<String,List<String>> map = new HashMap<>();
        for(String str:strs) {
            char[] arr = str.toCharArray();
            Arrays.sort(arr);
            String key = new String(arr);
            List<String> list = map.getOrDefault(key,new ArrayList<>());
            list.add(str);
            map.put(key,list);
        }
        return new ArrayList<List<String>>(map.values());
    }
}
```
50.pow(x,n)
* 思路： 做乘法和做除法的思路是一致的，不使用复杂度为n的循环，而是采用倍数叠加的方式增加每一次计算的倍数，从而更快地求解。
```java
class Solution {
    public double myPow(double x, int n) {
        if(n == 0) return 1;
        if(n == 1) return x;
        int time = n >0 ? n: -n;
        int count = 1;
        double multi = x >0 ? x:-x;
        int sgn = x > 0? 1:-1;
        if(n %2 == 0) sgn =1;
        while(count < time/2) {
            count = 2*count;
            multi = multi*multi;    
        }
        return n > 0 ? sgn*multi* myPow(Math.abs(x),time-count):sgn*1/(multi* myPow(Math.abs(x),time-count));
    }
}
```
54.螺旋矩阵
* 思路： 比较直观的是模拟螺旋的过程，涉及到四个方向的移动，之后只要列举移动条件就可以了。需要注意的地方是循环的终止，否则会出现多的部分。
```java
public static List<Integer> spiralOrder(int[][] matrix) {
        int row = matrix.length,col = matrix[0].length;
        List<Integer> list = new ArrayList<>();
        int left = 0,rigth = col,up =0,down = row;
        int n = col*row-1;
        while(left < rigth && up < down) {
            int i = up,j = left;
            while(j < rigth && n >= 0) {
                list.add(matrix[i][j]);
                n--;
                j++;
            }
            j--;
            up++;
            i = up;
            while(i < down && n >= 0) {
                list.add(matrix[i][j]);
                n--;
                i++;
            }
            i--;
            rigth--;
            j = rigth-1;
            while(j>= left && n>=0) {
                list.add(matrix[i][j]);
                j--;
                n--;
            }
            j++;
            down--;
            i = down-1;
            while(i >= up && n>=0) {
                list.add(matrix[i][j]);
                i--;
                n--;
            }
            j--;
            left++;
        }
        return list;
    }
```

## 55.跳跃游戏
* 思路： 使用贪心算法。假设有一个点可以到达目标点，那么无论从什么路径走到这个点，最终都可以到达目标。因此，每一次都在可以到达的范围内搜寻，并扩大可以搜寻的范围。
```java
class Solution {
    public boolean canJump(int[] nums) {
        int n= nums.length;
        int rightmost = 0;
        for(int i =0;i < n;i++) {
            if(i <= rightmost) {
                rightmost = Math.max(rightmost,i+nums[i]);
            }
            if(rightmost >= n-1)
            return true;
        }
        return false;
    }
}
```

190.颠倒二进制位
* 思路：要要将二进制位颠倒，可以使用位操作获取每一位的值。
```java
public class Solution {
    // you need treat n as an unsigned value
    public int reverseBits(int n) {
        int res = 0;
        for(int i = 0;i<32;++i) {
            res = res<<1;
            res = (n&1) | res;
            n = n >>1;
        }
        return res;
    }
}
```
192.位1的个数
* 思路：使用诸位计数的方式可以得到答案，在上一题中已经实现过。本题还可以使用一个小技巧：n&(n-1)可以将n的最后一个1置0.而其它位不改变。因此没进行一次计算就会统计一次1的个数，直到所有的1置为0，n也变为0.
```java
public class Solution {
    // you need to treat n as an unsigned value
    public int hammingWeight(int n) {
        int sum =0;
        while( n != 0) {
            sum++;
            n = n &(n-1);
        }
        return sum;
    }
}
```
202.快乐数
* 思路：判断是的条件很简单，即经过处理之后，数字最终会变为1.但是判断不是的判据是陷入无限循环，因而采用哈希表来记录判断的数字，如果出现重复值则直接返回false;
```Java
class Solution {
    Set<Integer> set = new HashSet<>();
    public boolean isHappy(int n) {
        if(!set.add(n)) {
            return false;
        }
        if(n == 1)
        return true;
        int sum = 0;
        while( n != 0) {
            sum += (n%10)*(n%10);
            n/=10;
        }
        
        return isHappy(sum);
    }
}
```
203.移除链表元素
* 思路： 在检测到当前节点的值是要删除的值时，还需要让当前节点的前一个节点指向当前节点的下一个节点。因此，最好使用前节点来做判断。要注意空节点和开头部分重复的情况。
```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode removeElements(ListNode head, int val) {
        if(head == null)
        return head;
        ListNode ans = head;
        while(ans != null && ans.val == val) {
            ans = ans.next;
        }
        ListNode last = head;
        head = head.next;
        while(head != null) {
            while(last.next!= null &&  last.next.val == val) {
                last.next = last.next.next;
            }
            last = head;
            head = head.next;
        }
        return ans;
    }
}
```
199.二叉树的右视图
* 思路： 采用广度搜索的方式可以保证层序遍历，再次基础之上，只要每一层的第一个值为右端值且加入答案列表即可。重点在于确认所在层，使用另一个队列，与节点保存队列一致即可。
```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public List<Integer> rightSideView(TreeNode root) {
        Queue<TreeNode> q = new LinkedList<TreeNode>();
        // Stack<Integer>  stack = new Stack<>();
        q.offer(root);
        List<Integer> list = new ArrayList<>();
        Queue<Integer> sdepth = new LinkedList<>(); 
        sdepth.offer(0);
        int deep =0;
        while(!q.isEmpty()) {
            int depth = sdepth.remove();
            TreeNode node = q.remove();
            if(node != null && depth == deep ) {
                list.add(node.val);
                deep++;
            }
            if(node != null) {
                q.offer(node.right);
                q.offer(node.left);
                sdepth.offer(depth+1);
                sdepth.offer(depth+1);
            }
        }
        return list;
    }
}
```
206.反转链表
* 思路：按照题目要求思考递归方式解决问题，如果使用当前函数做递归函数，那么就需要以把头节点放到末尾，着样做很耗时，因此考虑每次递归返回尾节点。
```java
class Solution {
    public ListNode reverseList(ListNode head) {
        if(head == null || head.next == null) return head;
        ListNode ans = lastnode(head);
        dfs(head);
        return ans;
        
    }
    public ListNode lastnode(ListNode head) {
        while(head.next != null) {
            head = head.next;
        }
        return head;
    }
    public ListNode dfs(ListNode head) {
        if(head.next.next == null) {
            ListNode temp = head.next;
            head.next = null;
            temp.next = head;
            return head;
        }
        ListNode temp = head.next;
        head.next = null;
        dfs(temp).next = head;
        return head;
    }
}
```
205.同构字符串
* 思路：使用哈希表记录s中的字符为key，对应位置的t的字符为value，如果s->t的映射发生了冲突（一个s字符对应两个t字符），则返回false。
```java
class Solution {
    public boolean isIsomorphic(String s, String t) {
        Map<Character,Character> map = new HashMap<>();
        for(int i = 0;i < s.length();i++) {
            if(map.containsKey(s.charAt(i))) {
                if(map.get(s.charAt(i)) != t.charAt(i) )
                return false;
            } else {
                if(map.containsValue(t.charAt(i)))
                return false;
            }
            map.put(s.charAt(i),t.charAt(i));
        }
        return true;
    }
}
```
222.完全二叉树的节点个数
* 思路：二分查找+位运算。由完全二叉树的性质可以知道，深度为h的完全二叉树节点个数为[2^(h-1),2^h],因此使用数与树结合。数字的二进制来表达节点的位置。根节点为1，根节点的左节点向后加0，根节点的右节点向后加1，使用二分查找的方法来查找最后一个节点的位置。
```Java
class Solution {
    public int countNodes(TreeNode root) {
        if(root == null)
        return 0;
        int level = 0;
        TreeNode node = root;
        while(node.left != null) {
            level++;
            node = node.left;
        }
        int left = 1 << level,right = (1<<(level+1))-1;
        while(left < right) {
            int mid = (right - left +1)/2+left;
            if(exits(root,level,mid)) {
                left = mid;
            }
            else {
                right = mid -1;
            }
        }
        return left;
    }
    public boolean exits(TreeNode root,int level,int k) {
        TreeNode node = root;
        int bits = 1 << (level-1);
        while(node != null && bits >0) {
            if((bits & k )== 0) {
                node = node.left;
            }
            else {
                node = node.right;
            }
            bits >>= 1;
        }
        return node != null;
    }
}
```
201.数字范围按位与
* 思路： 题目的本质是找出两数的最长公共前缀。因此使用移位法直到两数相等为止，只剩下公共前缀，再向右移即可获得答案。或者采用n&(n-1)消去该数字右边最后一位的思想，直到找到答案。
```java
class Solution {
    public int rangeBitwiseAnd(int m, int n) {
        while(m < n) {
            n = n & (n-1);
        }
        return n;
    }
}
```

226.翻转二叉树
* 思路： 函数的功能是将左右子树翻转并返回翻转后的根节点，使用递归可以解决此问题。从根节点出发，左节点翻转后的返回值作为右节点，右节点翻转后的返回值作为左节点即完成翻转。
```java
public TreeNode invertTree(TreeNode root) {
        if(root == null)
        return root;
        TreeNode right = invertTree(root.left);
        TreeNode left = invertTree(root.right);
        root.right = right;
        root.left = left;
        return root;
    }
```
257.二叉树所有路径
* 思路：二叉树遍历即可，但是要注意每一次传参时，如果传递的不是基本类型，会直接更改其引用值，对后续递归产生影响。
```java
class Solution {
    public List<String> binaryTreePaths(TreeNode root) {
        List<String> ans = new ArrayList<>();
        StringBuilder str = new StringBuilder("");
        traveser(root,ans,str);
        return ans;
    }
    public void traveser(TreeNode root,List<String> ans,StringBuilder str) {
        if(root == null)
        return;
        str.append(root.val);
        if(root.left == null && root.right == null) {
            ans.add(str.toString());
            return;
        } else {
        str.append("->");
        traveser(root.left,ans,new StringBuilder(str));
        traveser(root.right,ans,new StringBuilder(str));
        }
        return;
    }
    
}
```
258.各位相加
* 思路：各位数相加相当于每10个数字取1，也就是数字取余9之后的值。考虑当数字是9的时候，结果会为0，因此(num-1)%9+1,返回即可。
```java
class Solution {
    public int addDigits(int num) {
        return (num-1)%9+1;
    }
}
```
260.只出现一次的数字 III
* 思路：如果找出只出现一次的数字，只需要把所有的值都异或就可以。如果是查找两个数字，所有的值异或得到两个数字a和b的值。因此，如果将两个数字分到不同的组，相同的数字在同一组，就可以得到结果。分组依据是选取异或结果中的一位不为0的位，使用&运算把数分开。
```java
class Solution {
    public int[] singleNumber(int[] nums) {
        int res = 0;
        for(int n:nums) {
            res ^= n;
        }
        int div = 1;
        while((div & res) == 0) {
            div <<= 1;
        }
        int a = 0,b = 0;
        for(int n:nums) {
            if((n & div) != 0) {
                a ^= n;
            } else {
                b ^= n;
            }
        }
        return new int[]{a,b};
    }
}
```
263.丑数
* 思路：如果是丑数，除去所有2，3，5因子之后结果为1。
```java
class Solution {
    public boolean isUgly(int num) {
        if(num == 0)
        return false;
        if(num == 1)
        return true;
        while(num%2==0) {
            num /= 2;
        }
        while(num%3==0) {
            num /= 3;
        }
        while(num%5==0) {
            num /= 5;
        }
        return num==1?true:false;
    }
}
```
268.丢失的数字
* 思路：使用位运算，数组的下标是[0,n-1]，数组内容是[0,n]之间缺一个值。补充一个n给下标，然后异或，最后除了丢失的数字，所有的数字都出现了两次。
```java
class Solution {
    public int missingNumber(int[] nums) {
        int missing = nums.length;
        for (int i = 0; i < nums.length; i++) {
            missing ^= i ^ nums[i];
        }
        return missing;
    }
}
```
274.H 指数
* 思路：先对数组进行排序，根据H指数的意义，满足当前值小于等于后面值的个数时，该值可能为H指数。或者当前值大于后面值的个数时，后面值的个数为H指数。具体见下：
```java
class Solution {
    public int hIndex(int[] citations) {
        
        Arrays.sort(citations);
        int pre = 0;
        for(int i = 0; i< citations.length;i++) {
            if(citations[i] <= citations.length-i) {
                pre = Math.max(citations[i],pre);
            } else if(citations[i] > citations.length-i){
                pre = Math.max(citations.length-i,pre);
            }
        }
        return pre;
    }
}
```
* 计数法：使用数组对论文数组计数，从后向前论文的总量依次累加。找出满足k >s的最大值即为h指数。
```java
class Solution {
    public int hIndex(int[] citations) {
        int n = citations.length;
        int[] papers = new int[n+1];
        for(int c:citations) {
            papers[Math.min(n,c)]++;
        }
        int k =n;
        for(int s= papers[n];k >s;s += papers[k])
        k--;
        return k;
    }
}
```
278. 第一个错误的版本
* 思路：容易看出本题可以使用二分法求解。二分法的求解思路要清楚。
```java
public class Solution extends VersionControl {
    public int firstBadVersion(int n) {
        int left = 1;
        int right = n;
        while(left < right) {
            int mid = left + (right - left)/2;
            if(isBadVersion(mid)){
                right = mid;
            } else {
                left =mid+1;
            }
        }
        return left;
    }
}
```
283.移动零
* 思路：非零元素依次赋值，之后再将尾部的元素全都置零。
```java
class Solution {
    public void moveZeroes(int[] nums) {
        int i = 0;
        for(int j = 0;j < nums.length;j++) {
            if(nums[j] != 0) {
                nums[i++] = nums[j];
            }
        }
        for(int k = i;k<nums.length;k++)
        nums[k]=0;
        return;
    }
}
```
287.寻找重复数
* 思路： 使用哈希表先对其中一条链遍历
```java
class Solution {
    public int findDuplicate(int[] nums) {
        Set<Integer> set = new HashSet<>();
        for(int i:nums) {
            if(!set.add(i)) {
                return i;
            }
        }
        return 1;
    }
}
```
