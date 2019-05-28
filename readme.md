1.树的子结构
-
>输入两棵二叉树A，B，判断B是不是A的子结构。（ps：我们约定空树不是任意一个树的子结构）

思路：先判断AB树父节点是否值相等，相等的话看AB是否`equals`,不是的话继续对A的左右节点继续进行该判断，`递归`求解<br><br>
该思路有误，在于如果AB父节点相等，不应该仅看AB是否`equals`，而是看B是否是A的`子结构`。例如B的某节点遍历到null了，但是A的该节点的左右孩子都有值，应该也满足B是A的子结构。<br><br>
代码如下:



    public static boolean HasSubtree(TreeNode root1, TreeNode root2) {
        if (root1 == null || root2 == null) return false;
        if (doesTree1HavaTree2(root1, root2)) return true;
        return HasSubtree(root1.left, root2) || HasSubtree(root1.right, root2);
    }

    private static boolean doesTree1HavaTree2(TreeNode node1, TreeNode node2) {
        if(node2==null) return true;
        if(node1==null||node1.val!=node2.val) return false;
        return doesTree1HavaTree2(node1.left, node2.left) && doesTree1HavaTree2(node1.right, node2.right);
    }

2.链表环的入口节点
-
>给一个链表，若其中包含环，请找出该链表的环的入口结点，否则，输出null。

思路：先通过一个快慢指针的相遇，找到环的长度x(如果没有环则返回Null),之后用两个指针，一个从起点出发，一个先走x步，相遇时，从起点出发的指针走了l,l即为起点到入口的长度。<br><br>

思路没问题，这个题目要注意代码的`鲁棒性`，如果链表中没有环，循环调用ListNode的next方法时，可能会发生`NPE`，这点需要注意。<br><br>
代码如下:

	public ListNode entryNodeOfLoop(ListNode pHead) {
        if (pHead == null || pHead.next == null) return null;
        //创建两个指针，一个速度为1，一个速度为2
        ListNode fast = pHead;
        ListNode slow = pHead;
        int lengthOfCircle = 0;
        do {
            if (fast.next == null || fast.next.next == null) return null;
            fast = fast.next.next;
            slow = slow.next;
            lengthOfCircle++;
        } while (fast != slow);
        ListNode formal = pHead;
        ListNode letter = pHead;
        for (int i = 0; i < lengthOfCircle; i++) {
            letter = letter.next;
        }
        while (formal != letter) {
            formal = formal.next;
            letter = letter.next;
        }
        return formal;
    }

3.斐波拉契数列
-
>大家都知道斐波那契数列，现在要求输入一个整数n，请你输出斐波那契数列的第n项（从0开始，第0项为0)n<=39

思路：斐波拉契数列用递归求解，有递归方法和非递归方法。<br><br>
在牛客网上试了下两种解法的时间和内存使用，如下 <br>
非递归方法 运行时间：14ms 占用内存：8948k<br>
递归方法 运行时间：1142ms 占用内存：9320k<br>
可以看出，非递归方法不仅速度比较快，而且占用内存小，递归申请的栈的深度比较深，内存占用大，对执行速度也有影响。<br><br>
代码如下:

	public int FibonacciInRecursion(int n) {
	        if (n == 0) return 0;
	        if (n == 1) return 1;
	        return FibonacciInRecursion(n - 1) + FibonacciInRecursion(n - 2);
    }
	    
    public int FinonacciInLoop(int n) {
        if (n == 0) return 0;
        if (n == 1) return 1;
        int formal = 0;
        int letter = 1;
        for (int i = 1; i < n; i++) {
            int tmp = formal + letter;
            formal = letter;
            letter = tmp;
        }
        return letter;
    }
    
4.链表中倒数第k个节点
-
>输入一个链表，输出该链表中倒数第k个结点。

思路一：直观的方法是先遍历一遍链表求出总长度l,然后用一个指针根结点走l-k步，即为倒数第k个结点。这种方法有个弊端是等于遍历两遍链表，会浪费一些时间。<br>
思路二：用一个A指针走k步，另一个指针B在起点，两个指针同时向后走，当A指针走到末尾的时候，B指针即在倒数第k的位置。<br><br>
思路一 运行时间：34ms 占用内存：9392k <br>
思路二 运行时间：24ms 占用内存：9636k <br><br>
代码如下:
	
	public ListNode findKthToTail(ListNode head, int k) {
        if (k <= 0) return null;
        if (head == null) return null;
        ListNode formal = head;
        ListNode letter = head;
        //formal走k步
        for (int i = 0; i < k - 1; i++) {
            if (formal.next == null) return null;
            formal = formal.next;
        }
        while (formal.next != null) {
            formal = formal.next;
            letter = letter.next;
        }
        return letter;
    }

5.平衡二叉树
-
>输入一棵二叉树，判断该二叉树是否是平衡二叉树。

附上`平衡二叉树`的性质：它是一棵空树或它的左右两个子树的高度差的绝对值不超过1，并且左右两个子树都是一棵平衡二叉树。这个方案很好的解决了二叉查找树退化成`链表`的问题，把插入，查找，删除的时间复杂度最好情况和最坏情况都维持在O(logN)。但是频繁旋转会使插入和删除牺牲掉O(logN)左右的时间，不过相对二叉查找树来说，时间上稳定了很多。
 
思路一`[自上而下]`：判断根节点左右子树的高度，确定该节点是平衡的，再递归判断左右子树是否为平衡二叉树<br>
这种思路有个弊端，在递归的判断子树是否为平衡树的过程中，多次求了每个子树的高度，相当于底层的节点被遍历了很多遍<br><br>
代码如下:

	public boolean IsBalanced_Solution(TreeNode root) {
	        if (root == null) return true;
	        return (Math.abs(getHeightOfTree(root.left) - getHeightOfTree(root.right)) <= 1)
	                && IsBalanced_Solution(root.left) && IsBalanced_Solution(root.right);
    }

    private int getHeightOfTree(TreeNode treeNode) {
        if (treeNode == null) return 0;
        if (treeNode.left == null && treeNode.right == null) return 1;
        int leftLength = getHeightOfTree(treeNode.left);
        int rightLength = getHeightOfTree(treeNode.right);
        return 1+Math.max(leftLength,rightLength);
    }

思路二`[自下而上]`：自底向上求高度，如果存在一个节点不是平衡树，则返回-1<br><br>
代码如下:
	
	public boolean isBalancedSolution(TreeNode root) {
	        if (root == null) return true;
	        return getDepth(root) != -1;
    }

    private int getDepth(TreeNode node) {
        if (node == null) return 0;
        if (getDepth(node.left) == -1 || getDepth(node.right) == -1) return -1;
        return Math.abs(getDepth(node.left) - getDepth(node.right)) > 1 ? -1 : Math.max(getDepth(node.left), getDepth(node.right));
    }

6.对称二叉树
-
>请实现一个函数，用来判断一颗二叉树是不是对称的。注意，如果一个二叉树同此二叉树的镜像是同样的，定义其为对称的。

思路：递归求解的时候应该注意的是，不是所有节点都完全对称，这个和题意还是有区别的<br><br>
代码如下:

    private static boolean isSymmetrical(TreeNode pRoot) {
        if(pRoot==null) return true;
        return isMirrorNode(pRoot.left,pRoot.right);
    }

    private static boolean isMirrorNode(TreeNode left, TreeNode right) {
        if(left==null&&right==null) return true;
        if((left==null&&right!=null)||(left!=null&&right==null)||left.val!=right.val) return false;
        return isMirrorNode(left.left, right.right)&&isMirrorNode(left.right,right.left);
    }
    
7.跳台阶
-
>一只青蛙一次可以跳上1级台阶，也可以跳上2级。求该青蛙跳上一个n级的台阶总共有多少种跳法（先后次序不同算不同的结果）。

思路：这个问题其实是一个动态规划的问题，动态规划的问题可以参考下<<算法导论>>，可以做一个简单的思考，当青蛙处于N层台阶的时候，它的选择是N-1台阶走一步+N-2台阶走两步，
这两种情况，这样既可得到关系式:f(N)=f(N-1)+f(N-2)，关于性能和优化的问题可以参考斐波拉契那道题目。<br><br>
代码如下：

    public int jump(int target) {
        if (target == 1) {
            return 1;
        } else if (target == 2) {
            return 2;
        } else {
            return jump(target - 1) + jump(target - 2);
        }
    }
    
8.旋转数组的最小数字
-
>把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。输入一个非减排序的数组的一个旋转，输出旋转数组的最小元素。 例如数组{3,4,5,1,2}为{1,2,3,4,5}的一个旋转，该数组的最小值为1。 NOTE：给出的所有元素都大于0，若数组大小为0，请返回0。

思路：初次思考，阅读题目之后，发现只要找到了节点N<N+1，则N+1节点即为最小元素。不过这个方法等于遍历了整个数组，更加高效的方法是用二分法，可以将时间复杂度从O(n)减小到O(logn)。除此之外，数组为单调非递减数组，可能会出现{1,1,1,1,0,1}这样的数组，这种情况只能遍历数组。最后，应该注意程序的鲁棒性。<br><br>
代码如下：

    public int minNumberInRotateArray(int[] array) {
            if (array.length == 0) {
                return 0;
            }
            int left = 0;
            int right = array.length - 1;
            while (left != right) {
                int mid = (left + right) / 2;
                if (array[left] > array[mid]) {
                    right = mid;
                } else if (array[left] < array[mid]) {
                    left = mid;
                } else {
                    left = left + 1;
                    if (array[left-1] > array[left]) {
                        return array[left];
                    }
                }
            }
            return array[left];
    }
    
9.二叉树的镜像
-
>操作给定的二叉树，将其变换为源二叉树的镜像。

思路：这题的思路是先将节点的左右子节点交换，对每个子节点，继续递归交换节点。<br><br>
代码如下：

    public void Mirror(TreeNode root) {
        if (root != null) {
            TreeNode tmp = root.left;
            root.left = root.right;
            root.right = tmp;
            Mirror(root.left);
            Mirror(root.right);
        }
    }

10.最小的K个数
-
>输入n个整数，找出其中最小的K个数。例如输入4,5,1,6,2,7,3,8这8个数字，则最小的4个数字是1,2,3,4。

思路：topk问题，不需要去重，求最大topK用最小堆，求最小topK用最大堆。堆排序建堆时间复杂度O(NlogN)。<br><br>
代码如下:

    public ArrayList<Integer> GetLeastNumbersSolution(int[] input, int k) {
        if (input.length <= 0 || k <= 0 || k > input.length) {
            return new ArrayList();
        }
        PriorityQueue<Integer> leastQueue = new PriorityQueue<>(Comparator.reverseOrder());
        for (int num : input) {
            if (leastQueue.size() < k) {
                leastQueue.add(num);
            } else if (leastQueue.peek() > num) {
                leastQueue.poll();
                leastQueue.add(num);
            }
        }
        ArrayList result = new ArrayList();
        while (leastQueue.size() > 0) {
            result.add(leastQueue.poll());
        }
        return result;
    }

11.最大子矩阵
-
>已知矩阵的大小定义为矩阵中所有元素的和。
 给定一个矩阵，你的任务是找到最大的非空(大小至少是1 * 1)子矩阵。
 比如，如下4 * 4的矩阵
 0 -2 -7 0
 9 2 -6 2
 -4 1 -4 1
 -1 8 0 -2
 的最大子矩阵是
 9 2
 -4 1
 -1 8
 这个子矩阵的大小是15。
 
总体思路：先简化为一维数组如何求最大子数组的问题，再讲二维数组压缩，转化为一维数组求最大子数组的问题。

步骤一：求一维数组的最大子数组
思路：首先是选择什么解法，最慢的是遍历，然后对遍历的结果求最大值。其次慢的是带记忆的遍历，即遍历的时候给最大值存在一个max变量中，时间复杂度会小一点，但是这两种方法都不太好。<br>
最优解的思路是动态规划，设i长度数组num[]的最大子矩阵值为f(i),第m个元素的值为num[i],可以得到公式:<br>
```
    f(i) = Math.max(f(i-1)+num[i],num[i])
```
这个公式是指从index为1的数开始向前累加，并且记录max，如果求和的值大于max则替换，一旦连续数组的和小于0，从i+1开始新建一个新的子数组，继续向前遍历。

步骤二：将二维数组遍历所有的行组成的矩阵，i(0<i<n)行,j(i<j<n),i,j行之间的矩阵按列求和，即转换为一维数组的求最大子数组的问题。<br>
代码参考 MaxSubMatrix