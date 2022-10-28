# leetcode笔记

## 数组

### 一、二分查找

[力扣题目链接](https://leetcode.cn/problems/binary-search/)

给定一个 n 个元素有序的（升序）整型数组 nums 和一个目标值 target  ，写一个函数搜索 nums 中的 target，如果目标值存在返回下标，否则返回 -1。

示例 1:

```text
输入: nums = [-1,0,3,5,9,12], target = 9     
输出: 4       
解释: 9 出现在 nums 中并且下标为 4     
```

定义 target 是在一个在左闭右闭的区间里，**也就是[left, right] （这个很重要非常重要）**。

区间的定义这就决定了二分法的代码应该如何写，**因为定义target在[left, right]区间，所以有如下两点：**

- while (left <= right) 要使用 <= ，因为left == right是有意义的，所以使用 <=
- if (nums[mid] > target) right 要赋值为 mid - 1，因为当前这个nums[mid]一定不是target，那么接下来要查找的左区间结束下标位置就是 mid - 1

例如在数组：1,2,3,4,7,9,10中查找元素2，如图所示：

<img src="https://img-blog.csdnimg.cn/20210311153055723.jpg" alt="704.二分查找" style="zoom:50%;" />

代码如下：

```c++
//二分查找
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int left = 0;
        int right = nums.size() - 1; // 定义target在左闭右闭的区间里，[left, right]
        while (left <= right) { // 当left==right，区间[left, right]依然有效，所以用 <=
            int middle = left + ((right - left) / 2);// 防止溢出 等同于(left + right)/2
            if (nums[middle] > target) {
                right = middle - 1; // target 在左区间，所以[left, middle - 1]
            } else if (nums[middle] < target) {
                left = middle + 1; // target 在右区间，所以[middle + 1, right]
            } else { // nums[middle] == target
                return middle; // 数组中找到目标值，直接返回下标
            }
        }
        // 未找到目标值
        return -1;
    }
};
```

总结：主要就是对区间的定义没有理解清楚，在循环中没有始终坚持根据查找区间的定义来做边界处理。区间的定义就是不变量，那么在循环中坚持根据查找区间的定义来做边界处理，就是循环不变量规则。

### 二、移除元素

[力扣题目链接](https://leetcode.cn/problems/remove-element/)

给你一个数组 nums 和一个值 val，你需要 原地 移除所有数值等于 val 的元素，并返回移除后数组的新长度。

不要使用额外的数组空间，你必须仅使用 O(1) 额外空间并**原地**修改输入数组。

元素的顺序可以改变。你不需要考虑数组中超出新长度后面的元素。

示例 1: 给定 nums = [3,2,2,3], val = 3, 函数应该返回新的长度 2, 并且 nums 中的前两个元素均为 2。 你不需要考虑数组中超出新长度后面的元素。

示例 2: 给定 nums = [0,1,2,2,3,0,4,2], val = 2, 函数应该返回新的长度 5, 并且 nums 中的前五个元素为 0, 1, 3, 0, 4。

**你不需要考虑数组中超出新长度后面的元素。**

#### 1.暴力解法

这个题目暴力的解法就是两层for循环，一个for循环遍历数组元素 ，第二个for循环更新数组。

删除过程如下：

<img src="https://tva1.sinaimg.cn/large/008eGmZEly1gntrc7x9tjg30du09m1ky.gif" alt="27.移除元素-暴力解法" style="zoom: 80%;" />

代码如下：

```cpp
// 时间复杂度：O(n^2)
// 空间复杂度：O(1)
class Solution {
public:
    int removeElement(vector<int>& nums, int val) {
        int size = nums.size();
        for (int i = 0; i < size; i++) {
            if (nums[i] == val) { // 发现需要移除的元素，就将数组集体向前移动一位
                for (int j = i + 1; j < size; j++) {
                    nums[j - 1] = nums[j];
                }
                i--; // 因为下标i以后的数值都向前移动了一位，所以i也向前移动一位
                size--; // 此时数组的大小-1
            }
        }
        return size;

    }
};
```

- 时间复杂度：O(n^2)
- 空间复杂度：O(1)

#### 2.双指针

双指针法（快慢指针法）： **通过一个快指针和慢指针在一个for循环下完成两个for循环的工作。**

定义快慢指针

- 快指针：寻找新数组的元素 ，新数组就是不含有目标元素的数组
- 慢指针：指向更新 新数组下标的位置

很多同学这道题目做的很懵，就是不理解 快慢指针究竟都是什么含义，所以一定要明确含义，后面的思路就更容易理解了。

删除过程如下：

![27.移除元素-双指针法](https://tva1.sinaimg.cn/large/008eGmZEly1gntrds6r59g30du09mnpd.gif)

**双指针法（快慢指针法）在数组和链表的操作中是非常常见的，很多考察数组、链表、字符串等操作的面试题，都使用双指针法。**

后续都会一一介绍到，本题代码如下：

```cpp
// 时间复杂度：O(n)
// 空间复杂度：O(1)
class Solution {
public:
    int removeElement(vector<int>& nums, int val) {
        int slowIndex = 0;
        for (int fastIndex = 0; fastIndex < nums.size(); fastIndex++) {
            if (val != nums[fastIndex]) {
                nums[slowIndex++] = nums[fastIndex];
            }
        }
        return slowIndex;
    }
};
```

注意这些实现方法并没有改变元素的相对位置！

- 时间复杂度：O(n)
- 空间复杂度：O(1)

### 三、有序数组的平方

[力扣题目链接](https://leetcode.cn/problems/squares-of-a-sorted-array/)

给你一个按 非递减顺序 排序的整数数组 nums，返回 每个数字的平方 组成的新数组，要求也按 非递减顺序 排序。

示例 1： 输入：nums = [-4,-1,0,3,10] 输出：[0,1,9,16,100] 解释：平方后，数组变为 [16,1,0,9,100]，排序后，数组变为 [0,1,9,16,100]

示例 2： 输入：nums = [-7,-3,2,3,11] 输出：[4,9,9,49,121]

#### 1.暴力排序

最直观的想法，莫过于：每个数平方之后，排个序，美滋滋，代码如下：

```cpp
class Solution {
public:
    vector<int> sortedSquares(vector<int>& A) {
        for (int i = 0; i < A.size(); i++) {
            A[i] *= A[i];
        }
        sort(A.begin(), A.end()); // 快速排序
        return A;
    }
};
```

这个时间复杂度是 O(n + nlogn)， 可以说是O(nlogn)的时间复杂度，但为了和下面双指针法算法时间复杂度有鲜明对比，我记为 O(n + nlog n)。

#### 2.双指针

数组其实是有序的， 只不过负数平方之后可能成为最大数了。

那么数组平方的最大值就在数组的两端，不是最左边就是最右边，不可能是中间。

此时可以考虑双指针法了，i指向起始位置，j指向终止位置。

定义一个新数组result，和A数组一样的大小，让k指向result数组终止位置。

如果`A[i] * A[i] < A[j] * A[j]` 那么`result[k--] = A[j] * A[j];` 。

如果`A[i] * A[i] >= A[j] * A[j]` 那么`result[k--] = A[i] * A[i];` 。

如动画所示：

![img](https://code-thinking.cdn.bcebos.com/gifs/977.%E6%9C%89%E5%BA%8F%E6%95%B0%E7%BB%84%E7%9A%84%E5%B9%B3%E6%96%B9.gif)

不难写出如下代码：

```cpp
class Solution {
public:
    vector<int> sortedSquares(vector<int>& A) {
        int k = A.size() - 1;
        vector<int> result(A.size(), 0);
        for (int i = 0, j = A.size() - 1; i <= j;) { // 注意这里要i <= j，因为最后要处理两个元素
            if (A[i] * A[i] < A[j] * A[j])  {
                result[k--] = A[j] * A[j];
                j--;
            }
            else {
                result[k--] = A[i] * A[i];
                i++;
            }
        }
        return result;
    }
};
```

此时的时间复杂度为O(n)，相对于暴力排序的解法O(n + nlog n)还是提升不少的。

### 四、长度最小的子数组

[力扣题目链接](https://leetcode.cn/problems/minimum-size-subarray-sum/)

给定一个含有 n 个正整数的数组和一个正整数 s ，找出该数组中满足其和 ≥ s 的长度最小的 连续 子数组，并返回其长度。如果不存在符合条件的子数组，返回 0。

示例：

输入：s = 7, nums = [2,3,1,2,4,3] 输出：2 解释：子数组 [4,3] 是该条件下的长度最小的子数组。

#### 1.暴力枚举所以可能区间

这道题目暴力解法当然是 两个for循环，然后不断的寻找符合条件的子序列，时间复杂度很明显是**O(n^2)**。

代码如下：

```c++
//暴力枚举
class Solution {
public:
    int minSubArrayLen(int target, vector<int>& nums) {
        int ans = INT_MAX;
        for(int left = 0; left < nums.size(); left++) {
            int sum = 0;
            for(int right = left; right < nums.size(); right++) {
                sum += nums[right];
                if(sum >= target) {
                    ans = min(ans, right - left + 1);
                    break;
                }
            }
        }
        return ans == INT_MAX ? 0 : ans;
    }
};
```

- 时间复杂度：O(n^2)
- 空间复杂度：O(1)

后面力扣更新了数据，暴力解法已经超时了。

#### 2.滑动窗口(双指针)

滑动窗口的起始位置如何移动呢？

这里还是以题目中的示例来举例，s=7， 数组是 2，3，1，2，4，3，来看一下查找的过程：

<img src="https://code-thinking.cdn.bcebos.com/gifs/209.%E9%95%BF%E5%BA%A6%E6%9C%80%E5%B0%8F%E7%9A%84%E5%AD%90%E6%95%B0%E7%BB%84.gif" alt="209.长度最小的子数组" style="zoom:50%;" />

最后找到 4，3 是最短距离。

其实从动画中可以发现滑动窗口也可以理解为**双指针法**的一种！只不过这种解法更像是一个窗口的移动，所以叫做滑动窗口更适合一些。

在本题中实现滑动窗口，主要确定如下三点：

- 窗口内是什么？
- 如何移动窗口的起始位置？
- 如何移动窗口的结束位置？

窗口就是 满足其和 ≥ s 的长度最小的 连续 子数组。

窗口的起始位置如何移动：如果当前窗口的值大于s了，窗口就要向前移动了（也就是该缩小了）。

窗口的结束位置如何移动：窗口的结束位置就是遍历数组的指针，也就是for循环里的索引。

解题的关键在于 窗口的起始位置如何移动，如图所示：

<img src="https://img-blog.csdnimg.cn/20210312160441942.png" alt="leetcode_209" style="zoom:50%;" />

可以发现**滑动窗口的精妙之处在于根据当前子序列和大小的情况，不断调节子序列的起始位置。从而将O(n^2)暴力解法降为O(n)。**



思路: 定义两个指针 `left` 和 `right` 分别表示子数组（滑动窗口窗口）的开始位置和结束位置，维护变量 `sum` 存储子数组中的元素和（即从`nums[left]` 到 `nums[right]` 的元素和）。

初始状态下，`left` 和 `right` 都指向下标 0，`sum` 的值为 0。

每一轮迭代，将 `nums[right]` 加到 `sum`，如果 `sum≥s`，则更新子数组的最小长度（此时子数组的长度是 `right - left + 1`），然后将`nums[left]` 从 `sum` 中减去并将 `left` 右移，直到 `sum<s`，在此过程中同样更新子数组的最小长度。在每一轮迭代的最后，将 `right` 右移。

完整代码：

```c++
// 滑窗(双指针)
class Solution {
public:
    int minSubArrayLen(int target, vector<int>& nums) {
        int sum = 0;
        int SubLen = INT_MAX;
        int left = 0;   //左端点
        for(int right = 0; right < nums.size(); right++) {  //right右端点
            sum += nums[right];
            while(sum >= target) {
                SubLen = min(right - left + 1, SubLen);
                sum -= nums[left];
                left ++;    //左端点往右移动一位
            }
        }
        return SubLen == INT_MAX ? 0 : SubLen;
    }
};
```

### 五、螺旋矩阵II

[力扣题目链接(opens new window)](https://leetcode.cn/problems/spiral-matrix-ii/)

给定一个正整数 n，生成一个包含 1 到 n^2 所有元素，且元素按顺时针顺序螺旋排列的正方形矩阵。

示例:

输入: 3 输出: [ [ 1, 2, 3 ], [ 8, 9, 4 ], [ 7, 6, 5 ] ]

思路：要如何画出这个螺旋排列的正方形矩阵呢？相信很多同学刚开始做这种题目的时候，上来就是一波判断猛如虎。

结果运行的时候各种问题，然后开始各种修修补补，最后发现改了这里那里有问题，改了那里这里又跑不起来了。

大家还记得我们在这篇文章[数组：每次遇到二分法，都是一看就会，一写就废 (opens new window)](https://programmercarl.com/0704.二分查找.html)中讲解了二分法，提到如果要写出正确的二分法一定要坚持**循环不变量原则**。而求解本题依然是要坚持循环不变量原则。

模拟顺时针画矩阵的过程:

- 填充上行从左到右
- 填充右列从上到下
- 填充下行从右到左
- 填充左列从下到上

由外向内一圈一圈这么画下去。

​	可以发现这里的边界条件非常多，在一个循环中，如此多的边界条件，如果不按照固定规则来遍历，那就是**一进循环深似海，从此offer是路人**。

这里一圈下来，我们要画每四条边，这四条边怎么画，每画一条边都要坚持一致的左闭右开，或者左开右闭的原则，这样这一圈才能按照统一的规则画下来。

那么我按照左闭右开的原则，来画一圈，大家看一下：

<img src="https://code-thinking-1253855093.file.myqcloud.com/pics/20220922102236.png" alt="img" style="zoom:33%;" />

这里每一种颜色，代表一条边，我们遍历的长度，可以看出每一个拐角处的处理规则，拐角处让给新的一条边来继续画。

这也是坚持了每条边左闭右开的原则。

一些同学做这道题目之所以一直写不好，**代码越写越乱**。

就是因为在画每一条边的时候，一会左开右闭，一会左闭右闭，一会又来左闭右开，岂能不乱。

代码如下，已经详细注释了每一步的目的，可以看出while循环里判断的情况是很多的，代码里处理的原则也是统一的左闭右开。

```c++
class Solution {
public:
    vector<vector<int>> generateMatrix(int n) {
        int start_x ,start_y = 0;
        int offset = 1;
        int loop_time = n / 2;
        int count = 1;  //赋值用
        int i, j;
        vector<vector<int>> ret(n, vector<int>(n ,0));
        while(loop_time--) {
            // i = start_x;
            // j = start_y;
            for(j = start_y; j < n - offset; j++) {   //top
                ret[start_x][j] = count++;
            }
            for(i = start_x; i < n - offset; i++) {   //right
                ret[i][j] = count++;                  //j为top结束时的值
            }
            for(; j > start_y; j--) {   //bottom 其中j的初始值为top结束时的值
                ret[i][j] = count++;
            }
            for(; i > start_x; i--) {   //left 其中j的初始值为top结束时的值
                ret[i][j] = count++;
            }
            start_x++;
            start_y++;
            offset++;
        }
        if(n % 2 != 0) ret[n / 2][n / 2] = count;
        return ret;
    }
};
```

## 链表

### 一、移除链表元素

[力扣题目链接](https://leetcode.cn/problems/remove-linked-list-elements/)

题意：删除链表中等于给定值 val 的所有节点。

示例 1：
输入：head = [1,2,6,3,4,5,6], val = 6
输出：[1,2,3,4,5]

示例 2：
输入：head = [], val = 1
输出：[]

示例 3：
输入：head = [7,7,7,7], val = 7
输出：[]

思路：

这里以链表 1 4 2 4 来举例，移除元素4。

<img src="https://img-blog.csdnimg.cn/20210316095351161.png" alt="203_链表删除元素1" style="zoom:33%;" />

如果使用C，C++编程语言的话，不要忘了还要从内存中删除这两个移除的节点， 清理节点内存之后如图：

<img src="https://img-blog.csdnimg.cn/20210316095418280.png" alt="203_链表删除元素2" style="zoom:33%;" />

其实**可以设置一个虚拟头结点**，这样原链表的所有节点就都可以按照统一的方式进行移除了。

来看看如何设置一个虚拟头。依然还是在这个链表中，移除元素1。

<img src="https://img-blog.csdnimg.cn/20210316095619221.png" alt="203_链表删除元素6" style="zoom:33%;" />

这里来给链表添加一个虚拟头结点为新的头结点，此时要移除这个旧头结点元素1。

这样是不是就可以使用和移除链表其他节点的方式统一了呢？

来看一下，如何移除元素1 呢，还是熟悉的方式，然后从内存中删除元素1。

最后呢在题目中，return 头结点的时候，别忘了 `return help->next;`， 这才是新的头结点

```c++
class Solution {                                                                                                                 
public:
    ListNode* removeElements(ListNode* head, int val) {
        ListNode* help = new ListNode(0, head);		//help为辅助节点，将虚拟头结点指向head，这样方面后面做删除操作
        ListNode* cur = help;
        while(cur->next != nullptr) {
            if(cur->next->val == val) {
                cur->next = cur->next->next;
            } else{
                cur = cur->next;
            }
        }
        return help->next;
    }
};
```

### 二、设计链表

[力扣题目链接](https://leetcode.cn/problems/design-linked-list/)

题意：

在链表类中实现这些功能：

- get(index)：获取链表中第 index 个节点的值。如果索引无效，则返回-1。
- addAtHead(val)：在链表的第一个元素之前添加一个值为 val 的节点。插入后，新节点将成为链表的第一个节点。
- addAtTail(val)：将值为 val 的节点追加到链表的最后一个元素。
- addAtIndex(index,val)：在链表中的第 index 个节点之前添加值为 val 的节点。如果 index 等于链表的长度，则该节点将附加到链表的末尾。如果 index 大于链表长度，则不会插入节点。如果index小于0，则在头部插入节点。
- deleteAtIndex(index)：如果索引 index 有效，则删除链表中的第 index 个节点。

<img src="https://img-blog.csdnimg.cn/20200814200558953.png" alt="707示例" style="zoom:50%;" />

```c++
class MyLinkedList {
public:
    struct Node{
        int value;
        Node* next;
        Node() : value(0), next(nullptr){}
        Node(int x) : value(x), next(nullptr){}
    };
    
    MyLinkedList() {    //初始化
        head = new Node(0);
        size = 0;
    }
    
    int get(int index) {
        if(index < 0 || index >= size) return -1;
        Node* cur = head->next;
        while(index--) {
            cur = cur->next;
        }
        return cur->value;
    }

    void addAtHead(int val) {
        Node* node = new Node(val);
        node->next = head->next;
        head->next = node;
        size++;
    }

    void addAtTail(int val) {
        Node* cur = head;
        Node* node = new Node(val);
        while(cur->next != nullptr) {
            cur = cur->next;
        }// 遍历到尾节点
        cur->next = node;
        size++;
    }
    
    void addAtIndex(int index, int val) {
        if(index == size) addAtTail(val);
        else if(index <= 0) {
            addAtHead(val);
        }
        else if(index > size) return;
        else {
            Node* cur = head;
            Node* node = new Node(val);
            while(index--) cur = cur->next;
            node->next = cur->next;
            cur->next = node;
            size++;
        }
    }
    
    void deleteAtIndex(int index) {
        if(index < 0 || index >= size) return ; //不在单链表的索引范围
        Node *cur = head;
        while(index--) cur = cur->next;
        cur->next = cur->next->next;
        size--;
    }
private:
    Node* head;
    int size;
};
```

### 三、反转链表

[力扣题目链接](https://leetcode.cn/problems/reverse-linked-list/)

题意：反转一个单链表。

示例: 输入: 1->2->3->4->5->NULL 输出: 5->4->3->2->1->NULL

<img src="https://img-blog.csdnimg.cn/20210218090901207.png" alt="206_反转链表" style="zoom:50%;" />

#### 1.迭代

​	拿示例中的链表来举例，如动画所示：（纠正：动画应该是先移动pre，在移动cur）

<img src="https://tva1.sinaimg.cn/large/008eGmZEly1gnrf1oboupg30gy0c44qp.gif" alt="img" style="zoom:50%;" />

```c++
//迭代
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        ListNode* pre = nullptr;
        ListNode* cur = head;
        while(cur) {
            ListNode* next = cur->next;
            cur->next = pre;
            pre = cur;
            cur = next;
        }
        return pre;
    }
};
```

#### 2.递归

1. 递归上来就先写终止条件：如果head为空或者head.next为空，返回head

2. 新头结点newHead指向尾结点，此处进入递归，递归一直到遍历到尾结点时才会返回
3. 每一层递归，该层递归中的head会让下一个节点指向自己，head.next.next = head；然后head自己指向空。以此达到反转的目的。
4. 返回新链表的头结点newHead

<img src="https://pic.leetcode-cn.com/1616764754-bfxwfc-%E5%8F%8D%E8%BD%AC%E9%93%BE%E8%A1%A8-%E9%80%92%E5%BD%92%E8%BF%87%E7%A8%8B.jpg" alt="反转链表-递归过程.jpg" style="zoom:33%;" />

```c++
//递归
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        if(!head || !head->next) {
            return head;
        }
        ListNode* new_head = reverseList(head->next);
        head->next->next = head;
        head->next = nullptr;
        return new_head;
    }
};
```

### 四、删除链表倒数第N个节点

```c++
class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        ListNode* help = new ListNode(0, head);//虚拟节点
        ListNode* slow = help;
        ListNode* fast = help;
        while(n--) { //fast指针先走n步
            fast = fast->next;
        }
        while(fast->next != nullptr) {
            fast = fast->next;
            slow = slow->next;
        }
        slow->next = slow->next->next;
        return help->next;
    }
};
```

### 五、链表相交

[力扣题目链接](https://leetcode.cn/problems/intersection-of-two-linked-lists-lcci/)

给你两个单链表的头节点 headA 和 headB ，请你找出并返回两个单链表相交的起始节点。如果两个链表没有交点，返回 null 。

图示两个链表在节点 c1 开始相交：

<img src="https://code-thinking-1253855093.file.myqcloud.com/pics/20211219221657.png" alt="img" style="zoom:50%;" />

题目数据 保证 整个链式结构中不存在环。

注意，函数返回结果后，链表必须 保持其原始结构 。

示例 1：

<img src="https://code-thinking-1253855093.file.myqcloud.com/pics/20211219221723.png" alt="img" style="zoom:50%;" />

示例 2：

<img src="https://code-thinking-1253855093.file.myqcloud.com/pics/20211219221749.png" alt="img" style="zoom:50%;" />

思路：参考[面试题 02.07. 链表相交（双指针，清晰图解） - 链表相交 - 力扣（LeetCode）](https://leetcode.cn/problems/intersection-of-two-linked-lists-lcci/solution/mian-shi-ti-0207-lian-biao-xiang-jiao-sh-b8hn/)

完整代码如下：

```c++
class Solution {
public:
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        ListNode* A = headA;
        ListNode* B = headB;
        while(A != B) {
            A = A != NULL ? A->next : headB;
            B = B != NULL ? B->next : headA;
        }
        return A;
    }
};
```

### 七、环形链表II

[力扣题目链接](https://leetcode.cn/problems/linked-list-cycle-ii/)

题意： 给定一个链表，返回链表开始入环的第一个节点。 如果链表无环，则返回 null。

为了表示给定链表中的环，使用整数 pos 来表示链表尾连接到链表中的位置（索引从 0 开始）。 如果 pos 是 -1，则在该链表中没有环。

**说明**：不允许修改给定的链表。

<img src="https://img-blog.csdnimg.cn/20200816110112704.png" alt="循环链表" style="zoom:50%;" />

思路：主要考察两知识点：

- 判断链表是否环
- 如果有环，如何找到这个环的入口

#### 1.判断链表是否有环

可以使用快慢指针法，分别定义 fast 和 slow 指针，从头结点出发，fast指针每次移动两个节点，slow指针每次移动一个节点，如果 fast 和 slow指针在途中相遇 ，说明这个链表有环。

为什么fast 走两个节点，slow走一个节点，有环的话，一定会在环内相遇呢，而不是永远的错开呢

首先第一点：**fast指针一定先进入环中，如果fast指针和slow指针相遇的话，一定是在环中相遇，这是毋庸置疑的。**

那么来看一下，**为什么fast指针和slow指针一定会相遇呢？**

可以画一个环，然后让 fast指针在任意一个节点开始追赶slow指针。

会发现最终都是这种情况， 如下图：

<img src="https://img-blog.csdnimg.cn/20210318162236720.png" alt="142环形链表1" style="zoom:67%;" />

fast和slow各自再走一步， fast和slow就相遇了

这是因为fast是走两步，slow是走一步，**其实相对于slow来说，fast是一个节点一个节点的靠近slow的**，所以fast一定可以和slow重合。

动画如下：

<img src="https://tva1.sinaimg.cn/large/008eGmZEly1goo4xglk9yg30fs0b6u0x.gif" alt="141.环形链表" style="zoom: 80%;" />

#### 2.如果有环，如何找到这个环的入口

**此时已经可以判断链表是否有环了，那么接下来要找这个环的入口了。**

假设从头结点到环形入口节点 的节点数为x。 环形入口节点到 fast指针与slow指针相遇节点 节点数为y。 从相遇节点 再到环形入口节点节点数为 z。 如图所示：

<img src="https://code-thinking-1253855093.file.myqcloud.com/pics/20220925103433.png" alt="img" style="zoom: 50%;" />

那么相遇时： slow指针走过的节点数为: `x + y`， fast指针走过的节点数：`x + y + n (y + z)`，n为fast指针在环内走了n圈才遇到slow指针， （y+z）为 一圈内节点的个数A。

因为fast指针是一步走两个节点，slow指针一步走一个节点， 所以 fast指针走过的节点数 = slow指针走过的节点数 * 2：

```
(x + y) * 2 = x + y + n (y + z)
```

两边消掉一个（x+y）: `x + y = n (y + z)`

因为要找环形的入口，那么要求的是x，因为x表示 头结点到 环形入口节点的的距离。

所以要求x ，将x单独放在左面：`x = n (y + z) - y` ,

再从n(y+z)中提出一个 （y+z）来，整理公式之后为如下公式：`x = (n - 1) (y + z) + z` 注意这里n一定是大于等于1的，因为 fast指针至少要多走一圈才能相遇slow指针。

这个公式说明什么呢？

先拿n为1的情况来举例，意味着fast指针在环形里转了一圈之后，就遇到了 slow指针了。

当 n为1的时候，公式就化解为 `x = z`，

这就意味着，**从头结点出发一个指针，从相遇节点 也出发一个指针，这两个指针每次只走一个节点， 那么当这两个指针相遇的时候就是 环形入口的节点**。

也就是在相遇节点处，定义一个指针index1，在头结点处定一个指针index2。

让index1和index2同时移动，每次移动一个节点， 那么他们相遇的地方就是 环形入口的节点。

动画如下：

![142.环形链表II（求入口）](https://tva1.sinaimg.cn/large/008eGmZEly1goo58gauidg30fw0bi4qr.gif)

那么 n如果大于1是什么情况呢，就是fast指针在环形转n圈之后才遇到 slow指针。

其实这种情况和n为1的时候 效果是一样的，一样可以通过这个方法找到 环形的入口节点，只不过，index1 指针在环里 多转了(n-1)圈，然后再遇到index2，相遇点依然是环形的入口节点。

代码如下：

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
        ListNode* fast = head;
        ListNode* slow = head;
        while(fast != NULL && fast->next != NULL) {
            slow = slow->next;
            fast = fast->next->next;
            // 快慢指针相遇，此时从head 和 相遇点，同时查找直至相遇
            if (slow == fast) {
                ListNode* index1 = fast;
                ListNode* index2 = head;
                while (index1 != index2) {
                    index1 = index1->next;
                    index2 = index2->next;
                }
                return index2; // 返回环的入口
            }
        }
        return NULL;
    }
};
```

#### 3.补充

在推理过程中，大家可能有一个疑问就是：**为什么第一次在环中相遇，slow的 步数 是 x+y 而不是 x + 若干环的长度 + y 呢？**

<img src="https://img-blog.csdnimg.cn/20210318165123581.png" alt="142环形链表5" style="zoom: 50%;" />

首先slow进环的时候，fast一定是先进环来了。

如果slow进环入口，fast也在环入口，那么把这个环展开成直线，就是如下图的样子：

<img src="https://img-blog.csdnimg.cn/2021031816503266.png" alt="142环形链表3" style="zoom:50%;" />

可以看出如果slow 和 fast同时在环入口开始走，一定会在环入口3相遇，slow走了一圈，fast走了两圈。

重点来了，slow进环的时候，fast一定是在环的任意一个位置，如图：

<img src="https://img-blog.csdnimg.cn/2021031816515727.png" alt="142环形链表4" style="zoom:50%;" />

那么fast指针走到环入口3的时候，已经走了k + n 个节点，slow相应的应该走了(k + n) / 2 个节点。

因为k是小于n的（图中可以看出），所以(k + n) / 2 一定小于n。

**也就是说slow一定没有走到环入口3，而fast已经到环入口3了**。

这说明什么呢？

**在slow开始走的那一环已经和fast相遇了**。

为什么fast不能跳过去呢？ 在刚刚已经说过一次了，**fast相对于slow是一次移动一个节点，所以不可能跳过去**。

## 哈希表

### 常见的三种哈希结构

当我们想使用哈希法来解决问题的时候，我们一般会选择如下三种数据结构。

- 数组
- set （集合）
- map(映射)

这里数组就没啥可说的了，我们来看一下set。

在C++中，set 和 map 分别提供以下三种数据结构，其底层实现以及优劣如下表所示：

| 集合               | 底层实现 | 是否有序 | 数值是否可以重复 | 能否更改数值 | 查询效率 | 增删效率 |
| ------------------ | -------- | -------- | ---------------- | ------------ | -------- | -------- |
| std::set           | 红黑树   | 有序     | 否               | 否           | O(log n) | O(log n) |
| std::multiset      | 红黑树   | 有序     | **是**           | 否           | O(logn)  | O(logn)  |
| std::unordered_set | 哈希表   | 无序     | 否               | 否           | O(1)     | O(1)     |

std::unordered_set底层实现为哈希表，std::set 和std::multiset 的底层实现是红黑树，**红黑树是一种平衡二叉搜索树**，所以key值是有序的，但key不可以修改，改动key值会导致整棵树的错乱，所以只能删除和增加。

| 映射               | 底层实现 | 是否有序 | 数值是否可以重复 | 能否更改数值 | 查询效率 | 增删效率 |
| ------------------ | -------- | -------- | ---------------- | ------------ | -------- | -------- |
| std::map           | 红黑树   | key有序  | key不可重复      | key不可修改  | O(logn)  | O(logn)  |
| std::multimap      | 红黑树   | key有序  | **key可重复**    | key不可修改  | O(log n) | O(log n) |
| std::unordered_map | 哈希表   | key无序  | key不可重复      | key不可修改  | O(1)     | O(1)     |

std::unordered_map 底层实现为哈希表，std::map 和std::multimap 的底层实现是红黑树。同理，std::map 和std::multimap 的key也是有序的（这个问题也经常作为面试题，考察对语言容器底层的理解）。

当我们要使用集合来解决哈希问题的时候，优先使用unordered_set，因为它的查询和增删效率是最优的，如果需要集合是有序的，那么就用set，如果要求不仅有序还要有重复数据的话，那么就用multiset。

那么再来看一下map ，在map 是一个<key,  value> 的数据结构，map中，对key是有限制，对value没有限制的，因为key的存储方式使用红黑树实现的。

这里在说一下，一些C++的经典书籍上, 例如STL源码剖析，说到了hash_se,  hash_map，这个与unordered_set，unordered_map又有什么关系呢？

实际上功能都是一样一样的， 但是unordered_set在C++11的时候被引入标准库了，而hash_set并没有，所以建议还是使用unordered_set比较好，这就好比一个是官方认证的，hash_set，hash_map 是C++11标准之前民间高手自发造的轮子。

![哈希表6](https://img-blog.csdnimg.cn/20210104235134572.png)

**c++中map与unordered_map的区别**
运行效率方面：unordered_map最高，而map效率较低但 提供了稳定效率和有序的序列。
占用内存方面：map内存占用略低，unordered_map内存占用略高,而且是线性成比例的。
**1 头文件**
map: #include < map >
unordered_map: #include < unordered_map >

**2** **内部实现机理**
map： **map内部实现了一个红黑树**，该结构具有自动排序的功能，因此map内部的所有元素都是有序的，红黑树的每一个节点都代表着map的一个元素，因此，对于map进行的查找，删除，添加等一系列的操作都相当于是对红黑树进行这样的操作，故红黑树的效率决定了map的效率。
unordered_map: **unordered_map内部实现了一个哈希表**，因此其元素的排列顺序是杂乱的，无序的

**3 优点、缺点、使用场景**
map
优点：有序性，这是map结构最大的优点，其元素的有序性在很多应用中都会简化很多的操作。红黑树，内部实现一个红黑书使得map的很多操作在lgn的时间复杂度下就可以实现，**因此效率非常的高。**

缺点：**空间占用率高**，因为map内部实现了红黑树，虽然提高了运行效率，但是**因为每一个节点都需要额外保存父节点，孩子节点以及红/黑性质**，使得每一个节点都占用大量的空间

适用处：对于那些有顺序要求的问题，用map会更高效一些。

unordered_map
优点：**内部实现了哈希表**，因此其**查找速度是常量级别的**。

缺点：哈希表的建立比较耗费时间

**总结**：

**当我们遇到了要快速判断一个元素是否出现集合里的时候，就要考虑哈希法**。

但是哈希法也是**牺牲了空间换取了时间**，因为我们要使用额外的数组，set或者是map来存放数据，才能实现快速的查找。

**如果在做面试题目的时候遇到需要判断一个元素是否出现过的场景也应该第一时间想到哈希法！**

### 一、有效的字母异位词

[力扣题目链接](https://leetcode.cn/problems/valid-anagram/)

给定两个字符串 s 和 t ，编写一个函数来判断 t 是否是 s 的字母异位词。

示例 1: 输入: s = "anagram", t = "nagaram" 输出: true

示例 2: 输入: s = "rat", t = "car" 输出: false

**说明:** 你可以假设字符串只包含小写字母

#### 1.排序

```c++
class Solution {
public:
    bool isAnagram(string s, string t) {
        if(s.size() != t.size()) return false;
        sort(s.begin(), s.end());
        sort(t.begin(), t.end());
        
        return s == t;
    }
};
```

复杂度分析

时间复杂度：O(nlogn)，其中 n 为 s 的长度。排序的时间复杂度为 O(nlogn)，比较两个字符串是否相等时间复杂度为 O(n)，因此总体时间复杂度为 O(nlog n+n)=O(nlogn)。

空间复杂度：O(logn)。排序需要 O(logn) 的空间复杂度。

#### 2.哈希表

完整代码：

```c++
class Solution {
public:
    bool isAnagram(string s, string t) {
        if(s.size() != t.size()) return false;
        unordered_map<char, int> umap;
        for(int i = 0; i < s.size(); i++) {
            umap[s[i]]++;    //s字符串中出现的字母加1
            umap[t[i]]--;    //t字符串中出现的字母减1
        }
        for(unordered_map<char, int>::iterator it = umap.begin(); it != umap.end(); it++) {//end()指向的是最后一个元素的下一个位置
            if(it->second != 0) return false;
        }
        return true;
    }
};
```

### 二、两个数组的交集

[力扣题目链接](https://leetcode.cn/problems/intersection-of-two-arrays/)

题意：给定两个数组，编写一个函数来计算它们的交集。

<img src="https://img-blog.csdnimg.cn/20200818193523911.png" alt="349. 两个数组的交集" style="zoom:50%;" />

**说明：** 输出结果中的每个元素一定是唯一的。 我们可以不考虑输出结果的顺序。



这道题目，主要要**学会使用一种哈希数据结构：unordered_set**，这个数据结构可以解决很多类似的问题。

注意题目特意说明：**输出结果中的每个元素一定是唯一的，也就是说输出的结果的去重的， 同时可以不考虑输出结果的顺序**

这道题用暴力的解法时间复杂度是O(n^2)，那来看看使用哈希法进一步优化。

那么用数组来做哈希表也是不错的选择，例如[242. 有效的字母异位词](https://programmercarl.com/0242.有效的字母异位词.html)

但是要注意，**使用数组来做哈希的题目，是因为题目都限制了数值的大小。**

而这道题目没有限制数值的大小，就无法使用数组来做哈希表了。

**而且如果哈希值比较少、特别分散、跨度非常大，使用数组就造成空间的极大浪费。**

此时就要使用另一种结构体了，set ，关于set，C++ 给提供了如下三种可用的数据结构：

- std::set
- std::multiset
- std::unordered_set

std::set和std::multiset底层实现都是红黑树，std::unordered_set的底层实现是哈希表， 使用unordered_set 读写效率是最高的，并不需要对数据进行排序，而且还不要让数据重复，所以选择unordered_set。

思路如图所示：

<img src="https://code-thinking-1253855093.file.myqcloud.com/pics/20220707173513.png" alt="set哈希法" style="zoom:50%;" />

C++代码如下：

```c++
class Solution {
public:
    vector<int> intersection(vector<int>& nums1, vector<int>& nums2) {
        unordered_set<int> s1(nums1.begin(), nums1.end());
        unordered_set<int> result_set;
        for(int i = 0; i < nums2.size(); i++) {
            if(s1.find(nums2[i]) != s1.end()) {
                //find()在 s1 容器中查找值为 val 的元素，如果成功找到，则返回指向该元素的双向迭代器；反之，则返回和 end() 方法一样的迭代器
                result_set.insert(nums2[i]);
            }
        }
        return vector<int>(result_set.begin(), result_set.end()); 
    }
};
```

### 三、快乐数

[力扣题目链接](https://leetcode.cn/problems/happy-number/)

编写一个算法来判断一个数 n 是不是快乐数。

「快乐数」定义为：对于一个正整数，每一次将该数替换为它每个位置上的数字的平方和，然后重复这个过程直到这个数变为 1，也可能是 无限循环 但始终变不到 1。如果 可以变为 1，那么这个数就是快乐数。

如果 n 是快乐数就返回 True ；不是，则返回 False 。

**示例：**

输入：19
输出：true
解释：
1^2 + 9^2 = 82
8^2 + 2^2 = 68
6^2 + 8^2 = 100
1^2 + 0^2 + 0^2 = 1

思路：**使用 “快慢指针” 思想**

1.找出循环：“快指针” 每次走两步，“慢指针” 每次走一步，当二者相等时，即为一个循环周期。

2.此时，判断是不是因为 1 引起的循环，是的话就是快乐数，否则不是快乐数。

```c++
class Solution {
public:
    int bitSquareSum(int n) {
        int sum = 0;
        while(n > 0)
        {
            int bit = n % 10;
            sum += bit * bit;
            n = n / 10;
        }
        return sum;
    }
    
    bool isHappy(int n) {
        int slow = n, fast = n;
        do{
            slow = bitSquareSum(slow);
            fast = bitSquareSum(fast);
            fast = bitSquareSum(fast);
        }while(slow != fast);
        
        return slow == 1;
    }
};
```

此题不建议用集合记录每次的计算结果来判断是否进入循环，因为这个集合可能大到无法存储。

### 四、两数之和

[力扣题目链接(opens new window)](https://leetcode.cn/problems/two-sum/)

给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那 两个 整数，并返回他们的数组下标。

你可以假设每种输入只会对应一个答案。但是，数组中同一个元素不能使用两遍。

**示例:**

给定 nums = [2, 7, 11, 15], target = 9

因为 nums[0] + nums[1] = 2 + 7 = 9

所以返回 [0, 1]

#### 1.暴力枚举

```c++
//暴力枚举
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        vector<int> ret;
        for(int i = 0; i < nums.size(); i++) {
            for(int j = i + 1; j < nums.size(); j++) {
                if(nums[i] + nums[j] == target) {
                    ret.push_back(i);
                    ret.push_back(j);
                    return ret;
                }
            }
        }
        return ret;
    }
};
```

#### 2.哈希表

​	**什么时候使用哈希法**，当我们需要查询一个元素是否出现过，或者一个元素是否在集合里的时候，就要第一时间想到哈希法。

本题就需要一个集合来存放我们遍历过的元素，然后在遍历数组的时候去询问这个集合，某元素是否遍历过，也就是 是否出现在这个集合。

那么我们就应该想到使用哈希法了。

因为本题，我们不仅要知道元素有没有遍历过，还有知道这个元素对应的下标，**需要使用 key value结构来存放，key来存元素，value来存下标，那么使用map正合适**。

再来看一下使用数组和set来做本题的局限。

- 数组的大小是受限制的，而且如果元素很少，而哈希值太大会造成内存空间的浪费。
- set是一个集合，里面放的元素只能是一个key，而两数之和这道题目，不仅要判断y是否存在而且还要记录y的下标位置，因为要返回x 和 y的下标。所以set 也不能用。

此时就要选择另一种数据结构：map ，map是一种key value的存储结构，可以用key保存数值，用value在保存数值所在的下标。

C++中map，有三种类型：

| 映射               | 底层实现 | 是否有序 | 数值是否可以重复 | 能否更改数值 | 查询效率 | 增删效率 |
| ------------------ | -------- | -------- | ---------------- | ------------ | -------- | -------- |
| std::map           | 红黑树   | key有序  | key不可重复      | key不可修改  | O(log n) | O(log n) |
| std::multimap      | 红黑树   | key有序  | key可重复        | key不可修改  | O(log n) | O(log n) |
| std::unordered_map | 哈希表   | key无序  | key不可重复      | key不可修改  | O(1)     | O(1)     |

std::unordered_map 底层实现为哈希表，std::map 和std::multimap 的底层实现是红黑树。

同理，std::map 和std::multimap 的key也是有序的（这个问题也经常作为面试题，考察对语言容器底层的理解）。 

**这道题目中并不需要key有序，选择std::unordered_map 效率更高！** 使用其他语言的录友注意了解一下自己所用语言的数据结构就行。

接下来需要明确两点：

- **map用来做什么**
- **map中key和value分别表示什么**

map目的用来存放我们访问过的元素，因为遍历数组的时候，需要记录我们之前遍历过哪些元素和对应的下表，这样才能找到与当前元素相匹配的（也就是相加等于target）

接下来是map中key和value分别表示什么。

这道题 我们需要 给出一个元素，判断这个元素是否出现过，如果出现过，返回这个元素的下标。

那么判断元素是否出现，这个元素就要作为key，所以数组中的元素作为key，有key对应的就是value，value用来存下标。

所以 map中的存储结构为 {key：数据元素，value：数组元素对应的下表}。

在遍历数组的时候，只需要向map去查询是否有和目前遍历元素比配的数值，如果有，就找到的匹配对，如果没有，就把目前遍历的元素放进map中，因为map存放的就是我们访问过的元素。

过程如下：

<img src="https://code-thinking-1253855093.file.myqcloud.com/pics/20220711202638.png" alt="过程一" style="zoom:33%;" />

<img src="https://code-thinking-1253855093.file.myqcloud.com/pics/20220711202708.png" alt="过程二" style="zoom:33%;" />

C++代码：

```c++
//哈希表
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map<int, int> m;  //<num, index>
        for(int i = 0; i < nums.size(); i++) {
            int temp = target - nums[i];
            auto iter = m.find(temp);
            if(iter != m.end()) {
                return {i, iter->second};
            }
            m.insert(pair<int, int>(nums[i], i));
        }
        return{};
    }
};
```

### 五、四数相加II

[力扣题目链接](https://leetcode.cn/problems/4sum-ii/)

给定四个包含整数的数组列表 A , B , C , D ,计算有多少个元组 (i, j, k, l) ，使得 A[i] + B[j] + C[k] + D[l] = 0。

为了使问题简单化，所有的 A, B, C, D 具有相同的长度 N，且 0 ≤ N ≤ 500 。所有整数的范围在 -2^28 到 2^28 - 1 之间，最终结果不会超过 2^31 - 1 。

**例如:**

输入:

- A = [ 1, 2]
- B = [-2,-1]
- C = [-1, 2]
- D = [ 0, 2]

输出:

2

**解释:**

两个元组如下:

1. (0, 0, 0, 1) -> A[0] + B[0] + C[0] + D[1] = 1 + (-2) + (-1) + 2 = 0
2. (1, 1, 0, 0) -> A[1] + B[1] + C[0] + D[0] = 2 + (-1) + (-1) + 0 = 0



**本题是使用哈希法的经典题目，而[0015.三数之和](https://programmercarl.com/0015.三数之和.html)，[0018.四数之和 ](https://programmercarl.com/0018.四数之和.html)并不合适使用哈希法**，因为三数之和和四数之和这两道题目使用哈希法在不超时的情况下做到对结果去重是很困难的，很有多细节需要处理。

**而这道题目是四个独立的数组，只要找到A[i] + B[j] + C[k] + D[l] = 0就可以，不用考虑有重复的四个元素相加等于0的情况，所以相对于题目18. 四数之和，题目15.三数之和，还是简单了不少！**

如果本题想难度升级：就是给出一个数组（而不是四个数组），在这里找出四个元素相加等于0，答案中不可以包含重复的四元组，大家可以思考一下，后续的文章我也会讲到的。

本题解题步骤：

1. 首先定义 一个unordered_map，key放a和b两数之和，value 放a和b两数之和出现的次数。
2. 遍历大A和大B数组，统计两个数组元素之和，和出现的次数，放到map中。
3. 定义int变量count，用来统计 a+b+c+d = 0 出现的次数。
4. 在遍历大C和大D数组，找到如果 0-(c+d) 在map中出现过的话，就用count把map中key对应的value也就是出现次数统计出来。
5. 最后返回统计值 count 就可以了

```c++
class Solution {
public:
    int fourSumCount(vector<int>& nums1, vector<int>& nums2, vector<int>& nums3, vector<int>& nums4) {
        unordered_map<int, int> map1;// <key, value>存<A+B, times>
        for(auto A : nums1) {
            for(auto B : nums2) {
                map1[A + B]++;
            }
        }
        int ans = 0;
        for(auto C : nums3) {
            for(auto D : nums4) {
                if(map1.find(-C-D) != map1.end()) {
                    ans += map1[-C-D];
                }
            }
        }
        return ans;
    }
};
```

### 六、赎金信

[力扣题目链接](https://leetcode.cn/problems/ransom-note/)

给定一个赎金信 (ransom) 字符串和一个杂志(magazine)字符串，判断第一个字符串 ransom 能不能由第二个字符串 magazines 里面的字符构成。如果可以构成，返回 true ；否则返回 false。

(题目说明：为了不暴露赎金信字迹，要从杂志上搜索各个需要的字母，组成单词来表达意思。杂志字符串中的每个字符只能在赎金信字符串中使用一次。)

**注意：**

你可以假设两个字符串均只含有小写字母。

canConstruct("a", "b") -> false
canConstruct("aa", "ab") -> false
canConstruct("aa", "aab") -> true



思路：这道题目和[242.有效的字母异位词](https://programmercarl.com/0242.有效的字母异位词.html)很像，[242.有效的字母异位词](https://programmercarl.com/0242.有效的字母异位词.html)相当于求 字符串a 和 字符串b 是否可以相互组成 ，而这道题目是求 字符串a能否组成字符串b，而不用管字符串b 能不能组成字符串a。

本题判断第一个字符串ransom能不能由第二个字符串magazines里面的字符构成，但是这里需要注意两点。

- 第一点“为了不暴露赎金信字迹，要从杂志上搜索各个需要的字母，组成单词来表达意思” 这里*说明杂志里面的字母不可重复使用。*
- 第二点 “你可以假设两个字符串均只含有小写字母。” *说明只有小写字母*，这一点很重要

完整代码：

```c++
//哈希表
// class Solution {
// public:
//     bool canConstruct(string ransomNote, string magazine) {
//         if (ransomNote.size() > magazine.size()) {
//             return false;
//         }
//         unordered_map<char, int> mz; //<char, times>
//         for(auto ch : magazine) {
//             mz[ch]++;
//         }
//         for(auto ch : ransomNote) {
//             if(mz.find(ch) != mz.end() && mz[ch] != 0) {
//                 mz[ch]--;
//             } else {
//                 return false;
//             }
//         }
//         return true;
//     }
// };

//数组在哈希法中的应用
class Solution {
public:
    bool canConstruct(string ransomNote, string magazine) {
        if(ransomNote.size() > magazine.size()) {
            return false;
        }
        int cnt[26] = {0};
        for(auto& ch : magazine) {
            cnt[ch - 'a']++;
        }
        for(auto& ch : ransomNote) {
            if(cnt[ch - 'a'] > 0) {
                cnt[ch - 'a']--;
            } else {
                return false;
            }
        }
        return true;
    }
};
```

**总结：**

因为题目所只有小写字母，那可以采用空间换取时间的哈希策略， 用一个长度为26的数组还记录magazine里字母出现的次数。

然后再用ransomNote去验证这个数组是否包含了ransomNote所需要的所有字母。依然是数组在哈希法中的应用。

一些同学可能想，用数组干啥，都用map完事了，**其实在本题的情况下，使用map的空间消耗要比数组大一些的，因为map要维护红黑树或者哈希表，而且还要做哈希函数，是费时的！数据量大的话就能体现出来差别了。 所以数组更加简单直接有效！**

### 七、三数之和

# 三数之和

[力扣题目链接](https://leetcode.cn/problems/3sum/)

给你一个包含 n 个整数的数组 nums，判断 nums 中是否存在三个元素 a，b，c ，使得 a + b + c = 0 ？请你找出所有满足条件且不重复的三元组。

**注意：** 答案中不可以包含重复的三元组。

示例：

给定数组 nums = [-1, 0, 1, 2, -1, -4]，

满足要求的三元组集合为： [ [-1, 0, 1], [-1, -1, 2] ]



**此题直接背，无他.**



双指针法，**这道题目使用双指针法 要比哈希法高效一些**，那么来讲解一下具体实现的思路。

动画效果如下：

<img src="https://code-thinking.cdn.bcebos.com/gifs/15.%E4%B8%89%E6%95%B0%E4%B9%8B%E5%92%8C.gif" alt="15.三数之和" style="zoom: 80%;" />

拿这个nums数组来举例，首先将数组排序，然后有一层for循环，i从下标0的地方开始，同时定一个下标left 定义在i+1的位置上，定义下标right 在数组结尾的位置上。

依然还是在数组中找到 abc 使得a + b +c =0，我们这里相当于 a = nums[i]，b = nums[left]，c = nums[right]。

接下来如何移动left 和right呢， 如果nums[i] + nums[left] + nums[right] > 0 就说明 此时三数之和大了，因为数组是排序后了，所以right下标就应该向左移动，这样才能让三数之和小一些。

如果 nums[i] + nums[left] + nums[right] < 0 说明 此时 三数之和小了，left 就向右移动，才能让三数之和大一些，直到left与right相遇为止。

时间复杂度：O(n^2)。

双指针法C++代码:

```c++
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        vector<vector<int>> ret;
        sort(nums.begin(), nums.end());
        for(int i = 0; i < nums.size(); i++) {
            if(nums[0] > 0 ) {// 排序之后如果第一个元素已经大于零，那么无论如何组合都不可能凑成三元组，直接返回结果就可以了
                return ret;
            }
            //第一层去重
            if(i > 0 && nums[i - 1] == nums[i]) {
                continue;
            }
            int left = i + 1;
            int right = nums.size() - 1;
            while(left < right) {
                if(nums[i] + nums[left] + nums[right] < 0) left++;
                else if(nums[i] + nums[left] + nums[right] > 0) right--;
                else {
                    ret.push_back({nums[i], nums[left], nums[right]});
                    // 第二层(双指针的一层)去重;下面的情况只可能与上面加入ret的三元组相同.如[-4 -3 -3 0 4 7 7]
                    while(left < right && nums[left] == nums[left + 1]) left++;
                    while(left < right && nums[right] == nums[right - 1]) right--;
                    // 找到答案时，双指针同时收缩
                    left++;
                    right--;
                }
            }
        }
        return ret;
    }
};
```

**总结：**一层for循环num[i]为确定值，然后循环内有left和right下标作为双指针，找到nums[i] + nums[left] + nums[right] == 0

### 八、四数之和

[力扣题目链接](https://leetcode.cn/problems/4sum/)

题意：给定一个包含 n 个整数的数组 nums 和一个目标值 target，判断 nums 中是否存在四个元素 a，b，c 和 d ，使得 a + b + c + d 的值与 target 相等？找出所有满足条件且不重复的四元组。

**注意：**

答案中不可以包含重复的四元组。

示例： 给定数组 nums = [1, 0, -1, 0, -2, 2]，和 target = 0。 满足要求的四元组集合为： [ [-1, 0, 0, 1], [-2, -1, 1, 2], [-2, 0, 0, 2] ]



**思路：**

四数之和，和[15.三数之和 ](https://programmercarl.com/0015.三数之和.html)是一个思路，都是使用双指针法, 基本解法就是在[15.三数之和 ](https://programmercarl.com/0015.三数之和.html)的基础上再套一层for循环。

但是有一些细节需要注意，例如： 不要判断`nums[k] > target` 就返回了，三数之和 可以通过 `nums[i] > 0` 就返回了，因为 0 已经是确定的数了，四数之和这道题目 target是任意值。比如：数组是`[-4, -3, -2, -1]`，`target`是`-10`，不能因为`-4 > -10`而跳过。但是我们依旧可以去做剪枝，逻辑变成`nums[i] > target && (nums[i] >=0 || target >= 0)`就可以了。

[15.三数之和 ](https://programmercarl.com/0015.三数之和.html)的双指针解法是一层for循环num[i]为确定值，然后循环内有left和right下标作为双指针，找到nums[i] + nums[left] + nums[right] == 0。

四数之和的双指针解法是两层for循环nums[k] + nums[i]为确定值，依然是循环内有left和right下标作为双指针，找出nums[k] + nums[i] + nums[left] + nums[right] == target的情况，三数之和的时间复杂度是O(n^2)，四数之和的时间复杂度是O(n^3) 。

那么一样的道理，五数之和、六数之和等等都采用这种解法。

对于[15.三数之和 双指针法就是将原本暴力O(n^3)的解法，降为O(n^2)的解法，四数之和的双指针解法就是将原本暴力O(n^4)的解法，降为O(n^3)的解法。

之前我们讲过哈希表的经典题目：[454.四数相加II ](https://programmercarl.com/0454.四数相加II.html)，相对于本题简单很多，因为本题是要求在一个集合中找出四个数相加等于target，同时四元组不能重复。

而四数相加II 是四个独立的数组，只要找到A[i] + B[j] + C[k] + D[l] = 0就可以，不用考虑有重复的四个元素相加等于0的情况，所以相对于本题还是简单了不少！



我们来回顾一下，几道题目使用了双指针法。

双指针法将时间复杂度：O(n^2)的解法优化为 O(n)的解法。也就是降一个数量级，题目如下：

- [27.移除元素](https://programmercarl.com/0027.移除元素.html)
- [15.三数之和](https://programmercarl.com/0015.三数之和.html)
- [18.四数之和](https://programmercarl.com/0018.四数之和.html)

链表相关双指针题目：

- [206.反转链表](https://programmercarl.com/0206.翻转链表.html)
- [19.删除链表的倒数第N个节点](https://programmercarl.com/0019.删除链表的倒数第N个节点.html)
- [面试题 02.07. 链表相交](https://programmercarl.com/面试题02.07.链表相交.html)
- [142题.环形链表II](https://programmercarl.com/0142.环形链表II.html)



此题完整代码：

```c++
class Solution {
public:
    vector<vector<int>> fourSum(vector<int>& nums, int target) {
        vector<vector<int>> result;
        sort(nums.begin(), nums.end());
        for (int k = 0; k < nums.size(); k++) {
            // 剪枝处理
            if (nums[k] > target && nums[k] >= 0) {
            	break; // 这里使用break，统一通过最后的return返回
            }
            // 对nums[k]去重
            if (k > 0 && nums[k] == nums[k - 1]) {
                continue;
            }
            for (int i = k + 1; i < nums.size(); i++) {
                // 2级剪枝处理
                if (nums[k] + nums[i] > target && nums[k] + nums[i] >= 0) {
                    break;
                }

                // 对nums[i]去重
                if (i > k + 1 && nums[i] == nums[i - 1]) {
                    continue;
                }
                int left = i + 1;
                int right = nums.size() - 1;
                while (right > left) {
                    // nums[k] + nums[i] + nums[left] + nums[right] > target 会溢出
                    if ((long) nums[k] + nums[i] + nums[left] + nums[right] > target) right--;
                    else if ((long) nums[k] + nums[i] + nums[left] + nums[right]  < target) left++;
                    else {
                        result.push_back(vector<int>{nums[k], nums[i], nums[left], nums[right]});
                        // 对nums[left]和nums[right]去重
                        while (right > left && nums[right] == nums[right - 1]) right--;
                        while (right > left && nums[left] == nums[left + 1]) left++;
                        // 找到答案时，双指针同时收缩
                        right--;
                        left++;
                    }
                }
            }
        }
        return result;
    }
};
```

**总结：**四数之和的双指针解法是两层for循环nums[k] + nums[i]为确定值，依然是循环内有left和right下标作为双指针，找出nums[k] + nums[i] + nums[left] + nums[right] == target的情况，三数之和的时间复杂度是O(n^2)，四数之和的时间复杂度是O(n^3) 

## 二叉树

<img src="https://img-blog.csdnimg.cn/20210219190809451.png" alt="二叉树大纲" style="zoom:50%;" />

### 一、二叉树的理论基础

#### 1.二叉树的种类

1. 满二叉树
2. 二叉搜索树
3. 平衡二叉树
4. 平衡二叉搜索树



**满二叉树**

满二叉树：如果一棵二叉树只有度为0的结点和度为2的结点，并且度为0的结点在同一层上，则这棵二叉树为满二叉树。

如图所示：

<img src="https://img-blog.csdnimg.cn/20200806185805576.png" alt="img" style="zoom: 33%;" />

这棵二叉树为满二叉树，也可以说深度为k，有2^k-1个节点的二叉树。

**完全二叉树**

完全二叉树的定义如下：在完全二叉树中，除了最底层节点可能没填满外，其余每层节点数都达到最大值，并且最下面一层的节点都集中在该层最左边的若干位置。若最底层为第 h 层，则该层包含 1~ 2^(h-1)  个节点。

**大家要自己看完全二叉树的定义，很多同学对完全二叉树其实不是真正的懂了。**

我来举一个典型的例子如题：

<img src="https://img-blog.csdnimg.cn/20200920221638903.png" alt="img" style="zoom:50%;" />

相信不少同学最后一个二叉树是不是完全二叉树都中招了。

**之前我们刚刚讲过优先级队列其实是一个堆，堆就是一棵完全二叉树，同时保证父子节点的顺序关系。**

**二叉搜索树**（二叉排序树）

前面介绍的树，都没有数值的，而二叉搜索树是有数值的了，**二叉搜索树是一个有序树**。

- 若它的左子树不空，则**左子树上所有结点的值均小于它的根结点**的值；
- 若它的右子树不空，则右子树上所有结点的值均大于它的根结点的值；
- 它的**左、右子树也分别为二叉排序树**

下面这两棵树都是搜索树 <img src="https://img-blog.csdnimg.cn/20200806190304693.png" alt="img" style="zoom: 67%;" />

**平衡二叉搜索树**

平衡二叉搜索树：又被称为AVL（Adelson-Velsky and Landis）树，且具有以下性质：它是一棵空树或它的**左右两个子树的高度差的绝对值不超过1**，并且左右两个子树都是一棵平衡二叉树。

如图：

<img src="https://img-blog.csdnimg.cn/20200806190511967.png" alt="img" style="zoom:50%;" />

最后一棵 不是平衡二叉树，因为它的左右两个子树的高度差的绝对值超过了1。

**C++中map、set、multimap，multiset的底层实现都是平衡二叉搜索树**，所以map、set的增删操作时间时间复杂度是logn，注意我这里没有说unordered_map、unordered_set，unordered_map、unordered_map底层实现是哈希表。

**所以大家使用自己熟悉的编程语言写算法，一定要知道常用的容器底层都是如何实现的，最基本的就是map、set等等，否则自己写的代码，自己对其性能分析都分析不清楚！**

#### 2.二叉树的存储方式

1. 链式存储

2. 顺序存储

   顾名思义就是顺序存储的元素在内存是连续分布的，而链式存储则是通过指针把分布在散落在各个地址的节点串联一起。

   链式存储如图：

   <img src="https://img-blog.csdnimg.cn/2020092019554618.png" alt="链式存储" style="zoom:50%;" />

顺序存储如图：

<img src="https://img-blog.csdnimg.cn/20200920200429452.png" alt="顺序存储" style="zoom:50%;" />

用数组来存储二叉树如何遍历的呢？

**如果父节点的数组下标是 i，那么它的左孩子就是 i \* 2 + 1，右孩子就是 i \* 2 + 2。**

#### 3.二叉树的遍历方式

- 深度优先遍历
  - 前序遍历（递归法，迭代法）
  - 中序遍历（递归法，迭代法）
  - 后序遍历（递归法，迭代法）
- 广度优先遍历
  - 层次遍历（迭代法）

#### 4.二叉树的定义

链式存储的二叉树节点的定义方式：

```c++
struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x), left(NULL), right(NULL) {}
};
```

### 二、二叉树的遍历

#### 1.二叉树的递归遍历

**每次写递归，都按照这三要素来写，可以保证写出正确的递归算法！**

1. **确定递归函数的参数和返回值：** 确定哪些参数是递归的过程中需要处理的，那么就在递归函数里加上这个参数， 并且还要明确每次递归的返回值是什么进而确定递归函数的返回类型。
2. **确定终止条件：** 写完了递归算法, 运行的时候，经常会遇到栈溢出的错误，就是没写终止条件或者终止条件写的不对，操作系统也是用一个栈的结构来保存每一层递归的信息，如果递归没有终止，操作系统的内存栈必然就会溢出。
3. **确定单层递归的逻辑：** 确定每一层递归需要处理的信息。在这里也就会重复调用自己来实现递归的过程。

**以前序遍历为例：**

1. **确定递归函数的参数和返回值**：因为要打印出前序遍历节点的数值，所以参数里需要传入vector在放节点的数值，除了这一点就不需要在处理什么数据了也不需要有返回值，所以递归函数返回类型就是void，代码如下：

   ```c++
   void traversal(TreeNode* cur, vector<int>& vec)
   ```

2. **确定终止条件**：在递归的过程中，如何算是递归结束了呢，当然是当前遍历的节点是空了，那么本层递归就要要结束了，所以如果当前遍历的这个节点是空，就直接return，代码如下：

   ```c++
   if(cur == nullptr) return;
   ```

3. **确定单层递归的逻辑**：前序遍历是中左右的循序，所以在单层递归的逻辑，是要先取中节点的数值，代码如下：

   ```c++
   vec.push_back(cur->val);    // 中
   traversal(cur->left, vec);  // 左
   traversal(cur->right, vec); // 右
   ```

单层递归的逻辑就是按照中左右的顺序来处理的，这样二叉树的前序遍历，基本就写完了，再看一下完整代码：

前序遍历:

   ```c++
   class Solution {
   public:
       void traversal(TreeNode* cur, vector<int>& vec) {
           if (cur == NULL) return;
           vec.push_back(cur->val);    // 中
           traversal(cur->left, vec);  // 左
           traversal(cur->right, vec); // 右
       }
       vector<int> preorderTraversal(TreeNode* root) {
           vector<int> result;
           traversal(root, result);
           return result;
       }
   };
   ```

中序遍历:

```cpp
void traversal(TreeNode* cur, vector<int>& vec) {
    if (cur == NULL) return;
    traversal(cur->left, vec);  // 左
    vec.push_back(cur->val);    // 中
    traversal(cur->right, vec); // 右
}
```

后序遍历：

```c++
void traversal(TreeNode* cur, vector<int>& vec) {
    if (cur == NULL) return;
    traversal(cur->left, vec);  // 左
    traversal(cur->right, vec); // 右
    vec.push_back(cur->val);    // 中
}
```

相关例题：

[144.二叉树的前序遍历](https://leetcode.cn/problems/binary-tree-preorder-traversal/)

[145.二叉树的后序遍历](https://leetcode.cn/problems/binary-tree-postorder-traversal/)

[94.二叉树的中序遍历](https://leetcode.cn/problems/binary-tree-inorder-traversal/)

#### 2.二叉树的迭代遍历

**前序遍历**(迭代法)：

<img src="https://tva1.sinaimg.cn/large/008eGmZEly1gnbmss7603g30eq0d4b2a.gif" alt="二叉树前序遍历（迭代法）" style="zoom: 67%;" />

```c++
class Solution {
public:
    vector<int> preorderTraversal(TreeNode* root) {
        stack<TreeNode*> st;
        vector<int> result;
        if (root == NULL) return result;
        st.push(root);
        while (!st.empty()) {
            TreeNode* node = st.top();                       // 中
            st.pop();
            result.push_back(node->val);
            if (node->right) st.push(node->right);           // 右(先右节点再左节点，因为出栈后的顺序是先左再右节点)
            if (node->left) st.push(node->left);             // 左
        }
        return result;
    }
};
```

**后序遍历**(迭代法)：在前序遍历的基础上，稍作调整

<img src="https://img-blog.csdnimg.cn/20200808200338924.png" alt="前序到后序" style="zoom:50%;" />

```c++
class Solution {
public:
    vector<int> postorderTraversal(TreeNode* root) {
        stack<TreeNode*> st;
        vector<int> result;
        if (root == NULL) return result;
        st.push(root);
        while (!st.empty()) {
            TreeNode* node = st.top();
            st.pop();
            result.push_back(node->val);
            if (node->left) st.push(node->left); // 相对于前序遍历，这更改一下入栈顺序
            if (node->right) st.push(node->right); // 空节点不入栈
        }
        reverse(result.begin(), result.end()); // 将结果反转之后就是左右中的顺序了
        return result;
    }
};
```

**中序遍历**(迭代法)：**与前序、后序遍历代码结构稍有不同**

```c++
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> result;
        stack<TreeNode*> st;
        TreeNode* cur = root;
        while (cur != NULL || !st.empty()) {
            if (cur != NULL) { // 指针来访问节点，访问到最底层
                st.push(cur); // 将访问的节点放进栈
                cur = cur->left;                // 左
            } else {
                cur = st.top(); // 从栈里弹出的数据，就是要处理的数据（放进result数组里的数据）
                st.pop();
                result.push_back(cur->val);     // 中
                cur = cur->right;               // 右
            }
        }
        return result;
    }
};
```

#### 3.**二叉树的层序遍历**(重要)

与层序遍历相关的例题(掌握了层序遍历后都可解决)：

- **102.二叉树的层序遍历**
- **107.二叉树的层次遍历II**
- **199.二叉树的右视图**
- **637.二叉树的层平均值**
- **429.N叉树的层序遍历**
- **515.在每个树行中找最大值**
- **116.填充每个节点的下一个右侧节点指针**
- **117.填充每个节点的下一个右侧节点指针II**
- **104.二叉树的最大深度**
- **111.二叉树的最小深度**

[力扣题目链接](https://leetcode.cn/problems/binary-tree-level-order-traversal/)

<img src="https://img-blog.csdnimg.cn/20210203144842988.png" alt="102.二叉树的层序遍历" style="zoom: 67%;" />

```c++
class Solution {
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
        queue<TreeNode*> que;
        if (root != NULL) que.push(root);
        vector<vector<int>> result;
        while (!que.empty()) {
            int size = que.size();
            vector<int> vec;
            // 这里一定要使用固定大小size，不要使用que.size()，因为que.size是不断变化的
            for (int i = 0; i < size; i++) {
                TreeNode* node = que.front();
                que.pop();
                vec.push_back(node->val);
                if (node->left) que.push(node->left);
                if (node->right) que.push(node->right);
            }
            result.push_back(vec);
        }
        return result;
    }
};
```

二叉树的层序遍历，**就是图论中的广度优先搜索在二叉树中的应用**，需要借助队列来实现（此时又发现队列的一个应用了）。

**层序遍历遍历相对容易一些，只要掌握基本写法（也就是框架模板），剩下的就是在二叉树每一行遍历的时候做做逻辑修改。**

### 三、翻转二叉树

[力扣题目链接](https://leetcode.cn/problems/invert-binary-tree/)

<img src="https://img-blog.csdnimg.cn/20210203192644329.png" alt="226.翻转二叉树" style="zoom: 67%;" />

思路：可以发现想要翻转它，其实就把每一个节点的左右孩子交换一下就可以了。关键在于遍历顺序，前中后序应该选哪一种遍历顺序？

#### 1.递归方法

我们来看一下递归三部曲：

1.确定递归函数的参数和返回值

```cpp
TreeNode* invertTree(TreeNode* root)
```

2.当前节点为空的时候，就返回

```cpp
if (root == NULL) return root;
```

3.确定单层递归的逻辑

因为是先前序遍历，所以先进行交换左右孩子节点，然后反转左子树，反转右子树。

```cpp
swap(root->left, root->right);
invertTree(root->left);
invertTree(root->right);
```

基于这递归三步法，代码基本写完，C++代码如下：

```cpp
//前序遍历+递归
class Solution {
public:
    TreeNode* invertTree(TreeNode* root) {
        if (root == NULL) return root;
        swap(root->left, root->right);  // 中
        invertTree(root->left);         // 左
        invertTree(root->right);        // 右
        return root;
    }
};
```

#### 2.DFS

本题只需将前序遍历的代码稍作修改便可。

```cpp
//前序遍历+迭代法
class Solution {
public:
    TreeNode* invertTree(TreeNode* root) {
        TreeNode* node;
        stack<TreeNode*> stk;
        if(!root) return root;
        stk.push(root);
        while(!stk.empty()) {
            node = stk.top();
            stk.pop();
            if(node->left) stk.push(node->left);
            if(node->right) stk.push(node->right);
            swap(node->left, node->right);
        }
        return root;
    }
};
```

#### 3.BFS

也就是层序遍历，层数遍历也是可以翻转这棵树的，因为层序遍历也可以把每个节点的左右孩子都翻转一遍，代码如下：

```C++
class Solution {
public:
    TreeNode* invertTree(TreeNode* root) {
        if(root == nullptr) return root;
        TreeNode* node; 
        queue<TreeNode*> q;
        q.push(root);
        while(!q.empty()) {
            int size = q.size();
            for(int i = 0; i < size; i++) {
                node = q.front();
                q.pop();
                if(node->left) q.push(node->left);
                if(node->right) q.push(node->right);
                swap(node->left, node->right);
            }
        }
        return root;
    }
};
```

### 四、对称二叉树

对于二叉树是否对称，要比较的是根节点的左子树与右子树是不是相互翻转的，理解这一点就知道了**其实我们要比较的是两个树（这两个树是根节点的左右子树）**，所以在递归遍历的过程中，也是要同时遍历两棵树。

比较的是两个子树的里侧和外侧的元素是否相等。如图所示：

<img src="https://img-blog.csdnimg.cn/20210203144624414.png" alt="101. 对称二叉树1" style="zoom: 33%;" />

那么遍历的顺序应该是什么样的呢？

本题遍历只能是“后序遍历”，因为我们要通过递归函数的返回值来判断两个子树的内侧节点和外侧节点是否相等。

**正是因为要遍历两棵树而且要比较内侧和外侧节点，所以准确来说是一个树的遍历顺序是左右中，一个树的遍历顺序是右左中。**

#### 1.递归

```c++
//后序遍历+递归
class Solution {
public:
    bool compare(TreeNode* left, TreeNode* right) {// 1.递归函数
    //2.递归函数终止条件
        if(!left && right) return false;
        else if(left && !right) return false;
        else if(!left && !right) return true;
        else if(left->val != right->val) return false; //经过上面三个判断处理,此处left和right不会为空指针
    //3.单层递归的逻辑
        // 此时就是：左右节点都不为空，且数值相同的情况
        // 此时才做递归，做下一层的判断
        bool outside = compare(left->left, right->right);   // 左子树：左、 右子树：右
        bool inside = compare(left->right, right->left);    // 左子树：右、 右子树：左
        bool isSame = outside && inside;                    // 左子树：中、 右子树：中 （逻辑处理）
        return isSame;
    }
    bool isSymmetric(TreeNode* root) {
        bool result;
        result = compare(root->left, root->right);
        return result;
    }
};
```

#### 2.迭代

这里我们可以使用队列来比较两个树（根节点的左右子树）是否相互翻转，（**注意这不是层序遍历**）

**使用队列**

通过队列来判断根节点的左子树和右子树的内侧和外侧是否相等，如动画所示：

<img src="https://tva1.sinaimg.cn/large/008eGmZEly1gnwcimlj8lg30hm0bqnpd.gif" alt="101.对称二叉树"  />

```c++
//使用队列迭代
class Solution {
public:
    bool isSymmetric(TreeNode* root) {
        bool result;
        queue<TreeNode*> q;
        q.push(root->left);
        q.push(root->right);
        while(!q.empty()) {
            TreeNode* leftNode = q.front(); q.pop();
            TreeNode* rightNode = q.front(); q.pop();
            if(!leftNode && !rightNode) { //左节点为空、右节点为空，此时说明是对称的
                continue;
            }
            // 左右一个节点不为空，或者都不为空但数值不相同，返回false
            if(!leftNode || !rightNode || leftNode->val != rightNode->val) {
                return false;
            }
            q.push(leftNode->left);
            q.push(rightNode->right);
            q.push(leftNode->right);
            q.push(rightNode->left);
        }
        return true;
    }
};
```

### 五、二叉树深度

#### 1.二叉树的最大深度

[力扣题目链接](https://leetcode.cn/problems/maximum-depth-of-binary-tree/)

**1.1递归**

​	1.确定递归函数的参数和返回值：参数就是传入树的根节点，返回就返回这棵树的深度，所以返回值为int类型。

代码如下：

```cpp
int getdepth(treenode* node)
```

​	2.确定终止条件：如果为空节点的话，就返回0，表示高度为0。

代码如下：

```cpp
if (node == NULL) return 0;
```

​	3.确定单层递归的逻辑：先求它的左子树的深度，再求的右子树的深度，最后取左右深度最大的数值 再+1 （加1是因为算上当前中间节点）就是目前节点为根节点的树的深度。

代码如下：

```cpp
int leftdepth = getdepth(node->left);       // 左
int rightdepth = getdepth(node->right);     // 右
int depth = 1 + max(leftdepth, rightdepth); // 中
return depth;
```

所以整体c++代码如下：

```cpp
class solution {
public:
    int getdepth(treenode* node) {
        if (node == NULL) return 0;
        int leftdepth = getdepth(node->left);       // 左
        int rightdepth = getdepth(node->right);     // 右
        int depth = 1 + max(leftdepth, rightdepth); // 中
        return depth;
    }
    int maxdepth(treenode* root) {
        return getdepth(root);
    }
};
```

**1.2迭代(层序遍历)**

```c++
// 层序遍历
class Solution {
public:
    int maxDepth(TreeNode* root) {
        TreeNode* node;
        queue<TreeNode*> q;
        int depth = 0;
        if(root == nullptr) {
            return 0;
        }
        q.push(root);
        while(!q.empty()) {
            depth++;
            int size = q.size();
            for(int i = 0; i < size; i++) {
                node = q.front();q.pop();
                if(node->left) q.push(node->left);
                if(node->right) q.push(node->right);
            }
        }
        return depth;
    }
};
```



#### 2.二叉树的最小深度

[力扣题目链接](https://leetcode.cn/problems/minimum-depth-of-binary-tree/)

**审题：最小深度是从根节点到最近叶子节点的最短路径上的节点数量。**注意是叶子节点。

<img src="https://img-blog.csdnimg.cn/20210203155800503.png" alt="111.二叉树的最小深度" style="zoom: 50%;" />

**1.1递归**

​	1.确定递归函数的参数和返回值

参数为要传入的二叉树根节点，返回的是int类型的深度。

代码如下：

```c++
int getDepth(TreeNode* node)
```

​	2.确定终止条件

终止条件也是遇到空节点返回0，表示当前节点的高度为0。

代码如下：

```c++
if (node == NULL) return 0;
```

​	3.确定单层递归的逻辑

这块和求最大深度可就不一样了，**如下代码会犯上图的错误：**

```c++
int leftDepth = getDepth(node->left);
int rightDepth = getDepth(node->right);
int result = 1 + min(leftDepth, rightDepth);
return result;
```

如果这么求的话，没有左孩子的分支会算为最短深度。

所以，如果左子树为空，右子树不为空，说明最小深度是 1 + 右子树的深度。

反之，右子树为空，左子树不为空，最小深度是 1 + 左子树的深度。 最后如果左右子树都不为空，返回左右子树深度最小值 + 1 。

正确的单层递归逻辑：

```c++
int leftDepth = getDepth(node->left);           // 左
int rightDepth = getDepth(node->right);         // 右
                                                // 中
// 当一个左子树为空，右不为空，这时并不是最低点
if (node->left == NULL && node->right != NULL) { 
    return 1 + rightDepth;
}   
// 当一个右子树为空，左不为空，这时并不是最低点
if (node->left != NULL && node->right == NULL) { 
    return 1 + leftDepth;
}
int result = 1 + min(leftDepth, rightDepth);
return result;
```

**完整代码：**

```c++
class Solution {
public:
    int getDepth(TreeNode* node) {
        if (node == NULL) return 0;
        int leftDepth = getDepth(node->left);           // 左
        int rightDepth = getDepth(node->right);         // 右
                                                        // 中
        // 当一个左子树为空，右不为空，这时并不是最低点
        if (node->left == NULL && node->right != NULL) { 
            return 1 + rightDepth;
        }   
        // 当一个右子树为空，左不为空，这时并不是最低点
        if (node->left != NULL && node->right == NULL) { 
            return 1 + leftDepth;
        }
        // 左右都空或都非空
        int result = 1 + min(leftDepth, rightDepth);
        return result;
    }

    int minDepth(TreeNode* root) {
        return getDepth(root);
    }
};
```

**1.2迭代(层序遍历)**

**只有当左右孩子都为空的时候，才说明遍历的最低点了。如果其中一个孩子为空则不是最低点(即第一次出现的叶子节点)**

```c++
class Solution {
public:

    int minDepth(TreeNode* root) {
        if (root == NULL) return 0;
        int depth = 0;
        queue<TreeNode*> que;
        que.push(root);
        while(!que.empty()) {
            int size = que.size();
            depth++; // 记录最小深度
            for (int i = 0; i < size; i++) {
                TreeNode* node = que.front();
                que.pop();
                if (node->left) que.push(node->left);
                if (node->right) que.push(node->right);
                if (!node->left && !node->right) { // 当左右孩子都为空的时候，说明是最低点的一层了，退出
                    return depth;
                }
            }
        }
        return depth;
    }
};
```

### 六、完全二叉树的节点个数

#### **1.递归**

```c++
//递归O(n)
class Solution {
public:
    int getCount(TreeNode* node) {
        if(!node) return 0;
        int leftNum = getCount(node->left); //左
        int rightNum = getCount(node->right); //右
        int num = leftNum + rightNum + 1; //中
        return num;
    }
    int countNodes(TreeNode* root) {
        return getCount(root);
    }
};
```

#### **2.迭代**

```c++
//层序遍历O(n)
class Solution {
public:
    int countNodes(TreeNode* root) {
        TreeNode* node;
        queue<TreeNode*> q;
        int num = 0;
        if(!root) return 0;
        q.push(root);
        while(!q.empty()) {
            int size = q.size();
            num += size;
            for(int i = 0; i < size; i++) {
                node = q.front(); q.pop();
                if(node->left) q.push(node->left);
                if(node->right) q.push(node->right);
            }
        }
        return num;
    }
};
```

#### 3.**完全二叉树的性质**

以上方法都是按照普通二叉树来做的，在完全二叉树中，除了最底层节点可能没填满外，其余每层节点数都达到最大值，并且最下面一层的节点都集中在该层最左边的若干位置。若最底层为第 h 层，则该层包含 1~ 2^(h-1)  个节点。

举一个典型的例子如题：

<img src="https://img-blog.csdnimg.cn/20200920221638903.png" alt="img" style="zoom:33%;" />

完全二叉树只有两种情况，情况一：就是满二叉树，情况二：最后一层叶子节点没有满。

对于情况一，可以直接用 2^h - 1 来计算，注意这里根节点深度为1。

对于情况二，分别递归左孩子，和右孩子，递归到某一深度一定会有左孩子或者右孩子为满二叉树，然后依然可以按照情况1来计算。

完全二叉树（一）如图：

​												 <img src="https://img-blog.csdnimg.cn/20201124092543662.png" alt="222.完全二叉树的节点个数" style="zoom:50%;" />	

完全二叉树（二）如图：

​												 <img src="https://img-blog.csdnimg.cn/20201124092634138.png" alt="222.完全二叉树的节点个数1" style="zoom: 50%;" />

可以看出如果整个树不是满二叉树，就递归其左右孩子，直到遇到满二叉树为止，用公式计算这个子树（满二叉树）的节点数量。

这里关键在于如果去判断一个左子树或者右子树是不是满二叉树呢？

在完全二叉树中，如果递归向左遍历的深度等于递归向右遍历的深度，那说明就是满二叉树。如图：

<img src="https://code-thinking-1253855093.file.myqcloud.com/pics/20220829163554.png" alt="img" style="zoom:50%;" />

在完全二叉树中，如果递归向左遍历的深度不等于递归向右遍历的深度，则说明不是满二叉树，如图：

<img src="https://code-thinking-1253855093.file.myqcloud.com/pics/20220829163709.png" alt="img" style="zoom:50%;" />

那录友说了，这种情况，递归向左遍历的深度等于递归向右遍历的深度，但也不是满二叉树，如题：

<img src="https://code-thinking-1253855093.file.myqcloud.com/pics/20220829163811.png" alt="img" style="zoom:50%;" />

如果这么想，大家就是对 完全二叉树理解有误区了，**以上这棵二叉树，它根本就不是一个完全二叉树**！

判断其子树岂不是满二叉树，如果是则利用用公式计算这个子树（满二叉树）的节点数量，如果不是则继续递归，那么 在递归三部曲中，第二部：终止条件的写法应该是这样的：

```cpp
if (root == nullptr) return 0; 
// 开始根据做深度和有深度是否相同来判断该子树是不是满二叉树
TreeNode* left = root->left;
TreeNode* right = root->right;
int leftDepth = 0, rightDepth = 0; // 这里初始为0是有目的的，为了下面求指数方便
while (left) {  // 求左子树深度
    left = left->left;
    leftDepth++;
}
while (right) { // 求右子树深度
    right = right->right;
    rightDepth++;
}
if (leftDepth == rightDepth) {
    return (2 << leftDepth) - 1; // 注意(2<<1) 相当于2^2，返回满足满二叉树的子树节点数量
}
```

递归三部曲，第三部，单层递归的逻辑：（可以看出使用后序遍历）

```cpp
int leftTreeNum = countNodes(root->left);       // 左
int rightTreeNum = countNodes(root->right);     // 右
int result = leftTreeNum + rightTreeNum + 1;    // 中
return result;
```

该部分精简之后代码为：

```cpp
return countNodes(root->left) + countNodes(root->right) + 1; 
```

最后整体C++代码如下：

```cpp
class Solution {
public:
    int countNodes(TreeNode* root) {
        if (root == nullptr) return 0;
        TreeNode* left = root->left;
        TreeNode* right = root->right;
        int leftDepth = 0, rightDepth = 0; // 这里初始为0是有目的的，为了下面求指数方便
        while (left) {  // 求左子树深度
            left = left->left;
            leftDepth++;
        }
        while (right) { // 求右子树深度
            right = right->right;
            rightDepth++;
        }
        //满二叉树
        if (leftDepth == rightDepth) {
            return (2 << leftDepth) - 1; // 注意(2<<1) 相当于2^2，所以leftDepth初始为0
        }
        //不是满二叉树
        int leftTreeNum = countNodes(root->left);       // 左
		int rightTreeNum = countNodes(root->right);     // 右
		int result = leftTreeNum + rightTreeNum + 1;    // 中
		return result;
    }
};
```

- 时间复杂度：O(log n × log n)
- 空间复杂度：O(log n)

### 七、平衡二叉树

[leetcode题目链接]([110. 平衡二叉树 - 力扣（LeetCode）](https://leetcode.cn/problems/balanced-binary-tree/))

给定一个二叉树，判断它是否是高度平衡的二叉树。

本题中，一棵高度平衡二叉树定义为：一个二叉树每个节点 的左右两个子树的高度差的绝对值不超过1。

示例 1:

给定二叉树 [3,9,20,null,null,15,7]

<img src="https://img-blog.csdnimg.cn/2021020315542230.png" alt="110.平衡二叉树" style="zoom:50%;" />

返回true

示例2：

给定二叉树 [1,2,2,3,3,null,null,4,4]

<img src="https://img-blog.csdnimg.cn/20210203155447919.png" alt="110.平衡二叉树1" style="zoom:50%;" />

返回 false 。

#### 1.自底向上的递归

**自底向上递归的做法类似于后序遍历**，对于当前遍历到的节点，先递归地判断其左右子树是否平衡，再判断以当前节点为根的子树是否平衡。如果一棵子树是平衡的，则返回其高度（高度一定是非负整数），否则返回-1。如果存在一棵子树不平衡，则整个二叉树一定不平衡。[自底向上的递归动图](https://leetcode.cn/problems/balanced-binary-tree/solution/ping-heng-er-cha-shu-by-leetcode-solution/)。

递归三步曲分析：

​	1.明确递归函数的参数和返回值

参数：当前传入节点。
返回值：以当前传入节点为根节点的树的高度。

那么如何标记左右子树是否差值大于1呢？

如果当前传入节点为根节点的二叉树已经不是二叉平衡树了，还返回高度的话就没有意义了。

所以如果已经不是二叉平衡树了，可以返回-1 来标记已经不符合平衡树的规则了。

代码如下：

```cpp
// -1 表示已经不是平衡二叉树了，否则返回值是以该节点为根节点树的高度
int height(TreeNode* node)
```

​	2.明确终止条件

递归的过程中依然是遇到空节点了为终止，返回0，表示当前节点为根节点的树高度为0

代码如下：

```cpp
if(node == nullptr) {
	return 0;
}
```

​	3.明确单层递归的逻辑

如何判断以当前传入节点为根节点的二叉树是否是平衡二叉树呢？当然是其左子树高度和其右子树高度的差值。

分别求出其左右子树的高度，然后如果差值小于等于1，则返回当前二叉树的高度，否则则返回-1，表示已经不是二叉平衡树了。

代码如下：

```c++
int left_height = height(node->left); //左
int right_height = height(node->right); //右
if(left_height == -1 || right_height == -1 || abs(left_height - right_height) > 1) {//中
	return -1;
} else {
	return max(left_height, right_height) + 1; //如果一棵子树是平衡的，则返回其高度
}
```

​	完整代码：

```c++
class Solution {
public:
    bool isBalanced(TreeNode* root) {
        return height(root) >= 0;
    }
private:
    int height(TreeNode* node) {//1.明确递归函数的参数和返回值
        if(node == nullptr) { //2.明确终止条件
            return 0;
        }
        //3.明确单层递归的逻辑(后序遍历)
        int left_height = height(node->left); //左
        int right_height = height(node->right); //右
        if(left_height == -1 || right_height == -1 || abs(left_height - right_height) > 1) {    //中
            return -1;
        } else {
            return max(left_height, right_height) + 1; //如果一棵子树是平衡的，则返回其高度
        }
    }
};
```

**总结**：求深度适合用前序遍历，而求高度适合用后序遍历。

### 八、二叉树的所有路径

#### **1.深度优先搜索**

思路与算法：

最直观的方法是使用深度优先搜索。在深度优先搜索遍历二叉树时，我们需要考虑当前的节点以及它的孩子节点。

- 如果当前节点不是叶子节点，则在当前的路径末尾添加该节点，并继续递归遍历该节点的每一个孩子节点。

- 如果当前节点是叶子节点，则在当前路径末尾添加该节点后我们就得到了一条从根节点到叶子节点的路径，将该路径加入到答案即可。

如此，当遍历完整棵二叉树以后我们就得到了所有从根节点到叶子节点的路径。

代码如下：

```c++
//DFS(前序遍历)
class Solution {
public:
    vector<string> binaryTreePaths(TreeNode* root) {
        vector<string> ret;
        getPath(root, "", ret);
        return ret;
    }
private:
    void getPath(TreeNode* node, string path, vector<string>& paths) {
        if(node != nullptr) {
            path += to_string(node->val);
            if(node->left == nullptr && node->right == nullptr) { //左右节点皆为空时，到达了叶子节点
                paths.push_back(path);
            } else {
                path += "->";
                getPath(node->left, path, paths);
                getPath(node->right, path, paths);
            }
        }
    }
};
```

### 九、左叶子之和

[力扣题目链接](https://leetcode.cn/problems/sum-of-left-leaves/)

计算给定二叉树的所有左叶子之和。

示例：

<img src="https://img-blog.csdnimg.cn/20210204151927654.png" alt="404.左叶子之和1" style="zoom:50%;" />

**判断当前节点是不是左叶子是无法判断的，必须要通过节点的父节点来判断其左孩子是不是左叶子。**

如果该节点的左节点不为空，该节点的左节点的左节点为空，该节点的左节点的右节点为空，则找到了一个左叶子，判断代码如下：

```c++
if (node->left != NULL && node->left->left == NULL && node->left->right == NULL) {
    左叶子节点处理逻辑
}
```

**1.递归**

递归的遍历顺序为后序遍历（左右中），是因为要通过递归函数的返回值来累加求取左叶子数值之和。

递归三部曲：

​	1.确定递归函数的参数和返回值

判断一个树的左叶子节点之和，那么一定要传入树的根节点，递归函数的返回值为数值之和，所以为int。使用题目中给出的函数就可以了。

```c++
int sumOfLeftLeaves(TreeNode* root)
```

​	2.确定终止条件

如果遍历到空节点，那么左叶子值一定是0

```cpp
if (root == nullptr) return 0;
```

​	3.确定单层递归的逻辑

当遇到左叶子节点的时候，记录数值，然后通过递归求取左子树左叶子之和与右子树左叶子之和，相加便是整个树的左叶子之和。

代码如下：

```c++
// 当前节点的左子树是一个左叶子的情况
if(root->left != nullptr && root->left->left == nullptr && root->left->right == nullptr) {
	return root->left->val + sumOfLeftLeaves(root->right);
} else {
	return sumOfLeftLeaves(root->left) + sumOfLeftLeaves(root->right);
}
```

完整代码：

```c++
class Solution {
public:
    int sumOfLeftLeaves(TreeNode* root) {
        // 左叶子节点，因此只有root->left才可以判断是否是叶子节点
        if(root == nullptr){
            return 0;
        }
        // 当前节点的左子树是一个左叶子的情况
        if(root->left != nullptr && root->left->left == nullptr && root->left->right == nullptr) {
            return root->left->val + sumOfLeftLeaves(root->right);
        } else {
            return sumOfLeftLeaves(root->left) + sumOfLeftLeaves(root->right);
        }
    }
};
```

2.**迭代**

```c++
//迭代
class Solution {
public:
    int sumOfLeftLeaves(TreeNode* root) {
        if(root == nullptr){
            return 0;
        }
        int sum = 0;
        TreeNode* node;
        stack<TreeNode*> stk;
        stk.push(root);
        while(!stk.empty()) {
            node = stk.top(); stk.pop();
            if(node->left != nullptr && node->left->left == nullptr && node->left->right == nullptr) {
                sum += node->left->val;
            }
            if(node->left) stk.push(node->left);
            if(node->right) stk.push(node->right);
        }
        return sum;
    }
};
```

### 十、找树左下角的值

[力扣题目链接](https://leetcode.cn/problems/find-bottom-left-tree-value/)

给定一个二叉树，在树的最后一行找到最左边的值。

示例 :

<img src="https://img-blog.csdnimg.cn/20210204153017586.png" alt="513.找树左下角的值1" style="zoom:50%;" />



#### **1.广度优先搜索(层序遍历)**

​	只需要记录最后一行第一个节点的数值就可以了。

```c++
class Solution {
public:
    int findBottomLeftValue(TreeNode* root) {
        if(root == nullptr) return 0;
        int ret = 0;
        TreeNode* node;
        queue<TreeNode*> q;
        q.push(root);
        while(!q.empty()) {
            int size = q.size();
            for(int i = 0; i < size; i++) {
                node = q.front(); q.pop();
                if(i == 0) ret = node->val; // 记录最后一行第一个元素
                if(node->left) q.push(node->left);
                if(node->right) q.push(node->right);
            }
        }
        return ret;
    }
};
```

​	**层序遍历优化版**

​	使用广度优先搜索遍历每一层的节点。在遍历一个节点时，**需要先把它的非空右子节点放入队列，然后再把它的非空左子节点放入队列**，这样才能保证从右到左遍历每一层的节点。广度优先搜索所遍历的最后一个节点的值就是最底层最左边节点的值。

```c++
class Solution {
public:
    int findBottomLeftValue(TreeNode* root) {
        if(root == nullptr) return 0;
        int ret = 0;
        TreeNode* node;
        queue<TreeNode*> q;
        q.push(root);
        while(!q.empty()) {
            node = q.front(); q.pop();
            if(node->right) q.push(node->right);
            if(node->left) q.push(node->left);
            ret = node->val; // 记录最后一行第一个元素
        }
        return ret;
    }
};
```

#### 2.递归

```c++
class Solution {//TODO
public:
    void dfs(TreeNode *root, int height, int &curVal, int &curHeight) {
        if (root == nullptr) {
            return;
        }
        height++;
        dfs(root->left, height, curVal, curHeight);
        dfs(root->right, height, curVal, curHeight);
        if (height > curHeight) {
            curHeight = height;
            curVal = root->val;
        }
    }

    int findBottomLeftValue(TreeNode* root) {
        int curVal, curHeight = 0;
        dfs(root, 0, curVal, curHeight);
        return curVal;
    }
};
```



### 十一、路径总和

[力扣题目链接](https://leetcode.cn/problems/path-sum/)

给定一个二叉树和一个目标和，判断该树中是否存在根节点到叶子节点的路径，这条路径上所有节点值相加等于目标和。

说明: 叶子节点是指没有子节点的节点。

示例: 给定如下二叉树，以及目标和 sum = 22

<img src="https://img-blog.csdnimg.cn/20210203160355234.png" alt="112.路径总和1" style="zoom:50%;" />

返回 true, 因为存在目标和为 22 的根节点到叶子节点的路径 5->4->11->2。

#### 1.递归

可以使用深度优先遍历的方式来遍历二叉树

思路：观察要求我们完成的函数，可以归纳出它的功能：询问是否存在从当前节点 root 到叶子节点的路径，满足其路径和为 sum。

假定从根节点到当前节点的值之和为 val，我们可以将这个问题转化为：是否存在从当前节点的子节点到叶子的路径，满足其路径和为 sum - val。

不难发现这满足递归的性质，**若当前节点就是叶子节点，那么我们直接判断 sum 是否等于 val 即可**（因为路径和已经确定，就是当前节点的值，我们只需要判断该路径和是否满足条件）。若当前节点不是叶子节点，我们只需要递归地询问它的子节点是否能满足条件即可。

```c++
class Solution {
public:
    bool hasPathSum(TreeNode* root, int targetSum) {
        if(root == nullptr) {
            return false;
        }
        //当前节点就是叶子节点，那么我们直接判断 sum 是否等于 val 即可
        if(root->left == nullptr && root->right == nullptr) {
            return targetSum == root->val;
        }
        return hasPathSum(root->left, targetSum - root->val) || hasPathSum(root->right, targetSum - root->val);
    }
};
```

#### **2.BFS**

题解思路：[路径总和思路](https://leetcode.cn/problems/path-sum/solution/lu-jing-zong-he-by-leetcode-solution/)

代码：

```c++
//BFS
class Solution {
public:
    bool hasPathSum(TreeNode* root, int targetSum) {
        if(root == nullptr) {
            return false;
        }
        TreeNode* node;
        int temp = 0;
        queue<TreeNode*> q_node;
        queue<int> q_value;
        q_node.push(root); q_value.push(root->val);
        while(!q_node.empty()) {
            node = q_node.front();
            q_node.pop();
            temp = q_value.front();
            q_value.pop();
            if(node->left == nullptr && node->right == nullptr && temp == targetSum) {
                return true;
            }
            else {
                if(node->left) {
                    q_node.push(node->left);
                    q_value.push(temp + node->left->val);//注意：这里不是加node->val而是node->left->val
                }
                if(node->right) {
                    q_node.push(node->right);
                    q_value.push(temp + node->right->val);
                }
            }
        }
        return false;
    }
};
```

#### 路径总和ii

[力扣题目链接](https://leetcode.cn/problems/path-sum-ii/)

给定一个二叉树和一个目标和，找到**所有从根节点到叶子节点路径总和等于给定目标和的路径**。

说明: 叶子节点是指没有子节点的节点。

示例: 给定如下二叉树，以及目标和 sum = 22

<img src="https://img-blog.csdnimg.cn/20210203160854654.png" alt="113.路径总和ii1.png" style="zoom: 50%;" />

#### 1.递归

思路：113.路径总和ii要遍历整个树，找到所有路径，**所以递归函数不要返回值！**

如图：

<img src="https://img-blog.csdnimg.cn/20210203160922745.png" alt="113.路径总和ii" style="zoom:50%;" />

```c++
class Solution {
public:
    vector<vector<int>> ret;
    vector<int> path;
    vector<vector<int>> pathSum(TreeNode* root, int targetSum) { 
        dfs(root, targetSum);
        return ret;
    }
private:
    void dfs(TreeNode* node, int targetSum) {
        if(node == nullptr) {
            return;
        }
        path.push_back(node->val);
        targetSum -= node->val;
        if(node->left == nullptr && node->right == nullptr && targetSum == 0) {
            ret.push_back(path);
        }
        dfs(node->left, targetSum);
        dfs(node->right, targetSum);
        //当递归到叶子节点,但不满足targetSum == 0时,需回溯。把path.push_back(node->val)这一步的节点弹出
        path.pop_back();
    }
};
```

**总结**：递归函数什么时候需要返回值？什么时候不需要返回值？这里总结如下三点：

- 如果需要**搜索整棵二叉树且不用处理递归返回值**，递归函数就不要返回值。（113.路径总和ii）
- 如果需要搜索整棵二叉树且需要处理递归返回值，递归函数就需要返回值。（这种情况我们在[236. 二叉树的最近公共祖先](https://programmercarl.com/0236.二叉树的最近公共祖先.html)中介绍）
- 如果要**搜索其中一条符合条件的路径**，那么递归一定需要返回值，因为遇到符合条件的路径了就要及时返回。（112.路径总和）

### 十二、1.从中序与后序遍历序列构造二叉树

[力扣题目链接](https://leetcode.cn/problems/construct-binary-tree-from-inorder-and-postorder-traversal/)

根据一棵树的中序遍历与后序遍历构造二叉树。

注意: 你可以假设树中没有重复的元素。

例如，给出中序遍历 inorder = [9,3,15,20,7] 后序遍历 postorder = [9,15,7,20,3] 返回如下的二叉树：

<img src="https://img-blog.csdnimg.cn/20210203154316774.png" alt="106. 从中序与后序遍历序列构造二叉树1" style="zoom:50%;" />

首先解决这道题我们需要明确给定一棵二叉树，我们是如何对其进行中序遍历与后序遍历的：

中序遍历的顺序是每次遍历左孩子，再遍历根节点，最后遍历右孩子。
后序遍历的顺序是每次遍历左孩子，再遍历右孩子，最后遍历根节点。
写成代码的形式即：

```c++
// 中序遍历
void inorder(TreeNode* root) {
    if (root == nullptr) {
        return;
    }
    inorder(root->left);
    ans.push_back(root->val);
    inorder(root->right);
}
// 后序遍历
void postorder(TreeNode* root) {
    if (root == nullptr) {
        return;
    }
    postorder(root->left);
    postorder(root->right);
    ans.push_back(root->val);
}
```

​	因此根据上文所述，我们可以发现**后序遍历的数组最后一个元素代表的即为根节点**。知道这个性质后，我们可以**利用已知的根节点信息在中序遍历的数组中找到根节点所在的下标，然后根据其将中序遍历的数组分成左右两部分，左边部分即左子树，右边部分为右子树**，针对每个部分可以用同样的方法继续递归下去构造。

流程如图：

<img src="https://img-blog.csdnimg.cn/20210203154249860.png" alt="106.从中序与后序遍历序列构造二叉树" style="zoom:33%;" />

**算法**

- 为了高效查找根节点元素在中序遍历数组中的下标，我们选择创建哈希表来存储中序序列，即建立一个（元素，下标）键值对的哈希表。


- 定义递归函数 helper(in_left, in_right) 表示当前递归到中序序列中当前子树的左右边界，递归入口为helper(0, n - 1) ：


​		如果 in_left > in_right，说明子树为空，返回空节点。

​		选择后序遍历的最后一个节点作为根节点。

​		利用哈希表 O(1)O(1) 查询当根节点在中序遍历中下标为 index。从 in_left 到 index - 1 属于左子树，从 index + 1 到 in_right 属于右子树。

​		根据后序遍历逻辑，递归创建右子树 helper(index + 1, in_right) 和左子树 helper(in_left, index - 1)。注意这里有需要先创建右子树，再创建左子树的依赖关系。可以理解为在后序遍历的数组中整个数组是先存储左子树的节点，再存储右子树的节点，最后存储根节点，如果按每次选择「后序遍历的最后一个节点」为根节点，则先被构造出来的应该为右子树。

​	返回根节点 root。

完整测试代码如下：

```c++
#include<unordered_map>
#include<vector>
using namespace std;
struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode() : val(0), left(nullptr), right(nullptr) {}
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
    TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
};

class Solution {
    int post_idx;
    unordered_map<int, int> idx_map;
public:
    TreeNode* helper(int in_left, int in_right, vector<int>& inorder, vector<int>& postorder){
        // 如果这里没有节点构造二叉树了，就结束
        if (in_left > in_right) {
            return nullptr;
        }

        // 选择 post_idx 位置的元素作为当前子树根节点
        int root_val = postorder[post_idx];
        TreeNode* root = new TreeNode(root_val);

        // 根据 root 所在位置分成左右两棵子树
        int index = idx_map[root_val];

        // 下标减一
        post_idx--;
        // 构造右子树
        root->right = helper(index + 1, in_right, inorder, postorder);
        // 构造左子树
        root->left = helper(in_left, index - 1, inorder, postorder);
        return root;
    }
    TreeNode* buildTree(vector<int>& inorder, vector<int>& postorder) {
        // 从后序遍历的最后一个元素开始
        post_idx = (int)postorder.size() - 1;

        // 建立（元素，下标）键值对的哈希表
        int idx = 0;
        for (auto& val : inorder) {
            idx_map[val] = idx++;
        }
        return helper(0, (int)inorder.size() - 1, inorder, postorder);
    }
};

int main() {
    Solution s;
    vector<int> inorder = {9,3,15,20,7};
    vector<int> postorder = {9,15,7,20,3};
    TreeNode* ret = s.buildTree(inorder, postorder);
    return 0;
}
```

**注意：从中序与后序遍历序列构造二叉树：先构造右子树，再构造左子树**

#### 2.从前序与中序遍历序列构造二叉树

[力扣题目链接](https://leetcode.cn/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)

根据一棵树的前序遍历与中序遍历构造二叉树。

注意: 你可以假设树中没有重复的元素。

例如，给出前序遍历 preorder = [3,9,20,15,7] 中序遍历 inorder = [9,3,15,20,7] 返回如下的二叉树：

<img src="https://img-blog.csdnimg.cn/20210203154626672.png" alt="105. 从前序与中序遍历序列构造二叉树" style="zoom: 50%;" />

**注意区别：从前序与中序遍历序列构造二叉树：先构造左子树，再构造右子树**

和上一题的区别：

```c++
pre_idx = 0;
......
// 下标加一
pre_idx++;
// 构造左子树
root->left = helper(in_left, index - 1, preorder, inorder);
// 构造右子树
root->right = helper(index + 1, in_right, preorder, inorder);
```



```c++
#include<unordered_map>
#include<vector>
using namespace std;
struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode() : val(0), left(nullptr), right(nullptr) {}
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
    TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
};
//从前序与中序遍历序列构造二叉树
class Solution {
    int pre_idx;
    unordered_map<int, int> idx_map;
public:
    TreeNode* helper(int in_left, int in_right, vector<int>& preorder, vector<int>& inorder) {
        // 如果这里没有节点构造二叉树了，就结束
        if (in_left > in_right) {
            return nullptr;
        }
        // 选择 pre_idx 位置的元素作为当前子树根节点
        int root_val = preorder[pre_idx];
        TreeNode* root = new TreeNode(root_val);

        // 根据 root 所在位置,将中序遍历的数组分成左右两棵子树
        int index = idx_map[root_val];

        // 下标加一
        pre_idx++;
        // 构造左子树
        root->left = helper(in_left, index - 1, preorder, inorder);
        // 构造右子树
        root->right = helper(index + 1, in_right, preorder, inorder);
        return root;
    }
    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        // 前序遍历index
        pre_idx = 0;

        // 建立中序遍历（元素，下标）键值对的哈希表
        int idx = 0;
        for (auto& val : inorder) {
            idx_map[val] = idx++;
        }
        return helper(0, (int)inorder.size() - 1, preorder, inorder);
    }
};

int main() {
    Solution s;
    vector<int> preorder = {3, 9, 20, 15, 7};
    vector<int> inorder = {9, 3, 15, 20, 7};
    TreeNode* ret = s.buildTree(preorder, inorder);
    return 0;
}
```

### 十三、最大二叉树

[力扣题目地址](https://leetcode.cn/problems/maximum-binary-tree/)

**这道题完全可以参考上一题的思路**

给定一个不含重复元素的整数数组。一个以此数组构建的最大二叉树定义如下：

- 二叉树的根是数组中的最大元素。
- 左子树是通过数组中最大值左边部分构造出的最大二叉树。
- 右子树是通过数组中最大值右边部分构造出的最大二叉树。

通过给定的数组构建最大二叉树，并且输出这个树的根节点。

示例 ：

<img src="https://img-blog.csdnimg.cn/20210204154534796.png" alt="654.最大二叉树" style="zoom: 50%;" />

提示：

给定的数组的大小在 [1, 1000] 之间。

#### 1.DFS

完整代码：

```c++
class Solution {
public:
    TreeNode* constructMaximumBinaryTree(vector<int>& nums) {
        return build(0, nums.size() - 1, nums);
    }
private:
    TreeNode* build(int left, int right, vector<int>& nums) {
        if(left > right) 
            return nullptr;
        //找出子数组的最大值下标
        int max_index = left;
        for( int i = left + 1; i <= right; i++) {
            if(nums[i] > nums[max_index])
                max_index = i;
        }
        //构建节点
        int max_val = nums[max_index];
        TreeNode* root = new TreeNode(max_val);
        //递归构建节点的左右子树
        root->left = build(left, max_index - 1, nums);
        root->right = build(max_index + 1, right, nums);
        return root;
    }
};
```

### 十四、合并二叉树

[力扣题目链接](https://leetcode.cn/problems/merge-two-binary-trees/)

给定两个二叉树，想象当你将它们中的一个覆盖到另一个上时，两个二叉树的一些节点便会重叠。

你需要将他们合并为一个新的二叉树。合并的规则是如果两个节点重叠，那么将他们的值相加作为节点合并后的新值，否则不为 NULL 的节点将直接作为新二叉树的节点。

示例 1:

<img src="https://img-blog.csdnimg.cn/20210204153634809.png" alt="617.合并二叉树" style="zoom:50%;" />

注意: 合并必须从两个树的根节点开始。

**思路**：其实和遍历一个树逻辑是一样的，只不过传入两个树的节点，同时操作。**(三种遍历顺序都可采用)**

```c++
//前序遍历两棵树
class Solution {
public:
    int val;
    TreeNode* mergeTrees(TreeNode* root1, TreeNode* root2) {
        return dfs(root1, root2);
    }
private:
    TreeNode* dfs(TreeNode* node1, TreeNode* node2) {
        if(node2 == nullptr)   return node1;
        else if(node1 == nullptr)   return node2;
        TreeNode* root = new TreeNode(node1->val + node2->val);
        root->left = dfs(node1->left, node2->left);
        root->right = dfs(node1->right, node2->right);
        return root;
    }
};

//前序遍历；在其中一棵树的基础上做修改
class Solution {
public:
    TreeNode* mergeTrees(TreeNode* root1, TreeNode* root2) {
        return dfs(root1, root2);
    }
private:
    TreeNode* dfs(TreeNode* node1, TreeNode* node2) {
        if(node2 == nullptr)   return node1;
        else if(node1 == nullptr)   return node2;
        node1->val += node2->val; //改变tree1
        node1->left = dfs(node1->left, node2->left);
        node1->right = dfs(node1->right, node2->right);
        return node1;
    }
};
```

### 十五、二叉搜索树中的搜索

**二叉搜索树的一条性质要牢记：中序遍历二叉搜索树得到的序列是升序的。**

[力扣题目地址](https://leetcode.cn/problems/search-in-a-binary-search-tree/)

给定二叉搜索树（BST）的根节点和一个值。 你需要在BST中找到节点值等于给定值的节点。 返回以该节点为根的子树。 如果节点不存在，则返回 NULL。

例如，

<img src="https://img-blog.csdnimg.cn/20210204155522476.png" alt="700.二叉搜索树中的搜索" style="zoom:50%;" />

在上述示例中，如果要找的值是 5，但因为没有节点值为 5，我们应该返回 NULL。

#### **1.递归**

二叉搜索树满足如下性质：

左子树所有节点的元素值均小于根的元素值；
右子树所有节点的元素值均大于根的元素值。
据此可以得到如下算法：

- 若 root 为空则返回空节点；

- 若 val=root.val，则返回root；
- 若 val<root.val，递归左子树；
- 若val>root.val，递归右子树。

```c++
//递归
class Solution {
public:
    TreeNode* searchBST(TreeNode* root, int val) {
        if(root == nullptr || root->val == val) return root;
        if(root->val > val) return searchBST(root->left, val);
        return searchBST(root->right, val);
    }
};
```

#### 2.迭代

将方法一的递归改成迭代写法：

- 若root 为空则跳出循环，并返回空节点；
- 若val=root.val，则返回root；
- 若val<root.val，将root 置为root.left；
- 若val>root.val，将root 置为root.right。

```c++
//迭代
class Solution {
public:
    TreeNode* searchBST(TreeNode* root, int val) {
        if(root == nullptr) return nullptr;
        TreeNode* node = root;
        while(node) {
            if(node->val == val) {
                return node;
            } 
            else {
                node = node->val > val ? node->left : node->right;
            }
        }
        return nullptr;
    }
};
```

### 十六、验证二叉搜索树

[力扣题目链接](https://leetcode.cn/problems/validate-binary-search-tree/)

给定一个二叉树，判断其是否是一个有效的二叉搜索树。

假设一个二叉搜索树具有如下特征：

- 节点的左子树只包含小于当前节点的数。
- 节点的右子树只包含大于当前节点的数。
- 所有左子树和右子树自身必须也是二叉搜索树。

<img src="https://img-blog.csdnimg.cn/20210203144334501.png" alt="98.验证二叉搜索树" style="zoom: 50%;" />

#### 1.递归（前序）

我的错误算法：

```c++
class Solution {
public:
    bool isValidBST(TreeNode* root) {
        if(root == nullptr) return true;
        if(!root->left && !root->right) return true;
        if(root->left && !root->right) {
            if(root->val > root->left->val)
                return isValidBST(root->left);
            else
                return false;
        }
        if(!root->left && root->right) {
            if(root->val < root->right->val)
                return isValidBST(root->right);
            else
                return false;
        }
        if(root->left && root->right) {
            if(root->val < root->right->val && root->val > root->left->val)
                return isValidBST(root->right) && isValidBST(root->left);
            else
                return false;
        }
        return false;
    }
};
```

注意：如二叉搜索树的右子树的某个左节点不仅要大于子树的根节点还必须大于整棵树的根节点，则该右子树的左节点需在区间 (root->val, sub_root->val)内。

**我们要比较的是 左子树所有节点小于中间节点，右子树所有节点大于中间节点**。所以以上代码的判断逻辑是错误的。

**不能单纯的比较左节点小于中间节点，右节点大于中间节点就完事了**。

例如： [10,5,15,null,null,6,20] 这个case：

<img src="https://img-blog.csdnimg.cn/20200812191501419.png" alt="二叉搜索树" style="zoom:50%;" />

节点10大于左节点5，小于右节点15，但右子树里出现了一个6 这就不符合了！

注意：样例中最小节点 可能是int的最小值，如果这样使用最小的int来比较也是不行的。

此时可以初始化比较元素为longlong类型。

正确的递归代码：

```c++
class Solution {
public:
    bool helper(TreeNode* root, long long lower, long long upper) {
        if (root == nullptr) {
            return true;
        }
        if (root -> val <= lower || root -> val >= upper) {
            return false;
        }
        return helper(root -> left, lower, root -> val) && helper(root -> right, root -> val, upper);
    }
    bool isValidBST(TreeNode* root) {
        return helper(root, LONG_MIN, LONG_MAX);
    }
};
```



**★更好的解法★**：二叉搜索树**「中序遍历」**得到的值构成的序列一定是升序的，这启示我们在中序遍历的时候实时检查当前节点的值是否大于前一个中序遍历到的节点的值即可。如果均大于说明这个序列是升序的，整棵树是二叉搜索树，否则不是.

#### 2.中序遍历（递归）

```c++
class Solution {
public:
    TreeNode* pre = NULL; // 用来记录前一个节点
    bool isValidBST(TreeNode* root) {
        if (root == NULL) return true;
        bool left = isValidBST(root->left);

        if (pre != NULL && pre->val >= root->val) return false;
        pre = root; // 记录前一个节点

        bool right = isValidBST(root->right);
        return left && right;
    }
};
```

#### 3.中序遍历（迭代）

```c++
//中序(迭代)
class Solution {
public:
    bool isValidBST(TreeNode* root) {
        TreeNode* cur = root;
        TreeNode* pre = nullptr;
        stack<TreeNode*> stk;
        while(cur != nullptr || !stk.empty()) {
            if(cur != nullptr) {
                stk.push(cur);
                cur = cur->left;    //左
            } else {
                cur = stk.top();    //中
                stk.pop();
                if(pre != nullptr && cur->val <= pre->val) //pre != nullptr第一次需要，遍历的节点值非严格升序，false
                return false; 
                pre = cur;			//保存前一个访问的结点
                cur = cur->right;   //右
            }
        }
        return true;
    }
};
```

### 十七、二叉搜索树的最小绝对差

[力扣题目链接](https://leetcode.cn/problems/minimum-absolute-difference-in-bst/)

给你一棵所有节点为非负值的二叉搜索树，请你计算树中任意两节点的差的绝对值的最小值。

示例：

<img src="https://img-blog.csdnimg.cn/20201014223400123.png" alt="530二叉搜索树的最小绝对差" style="zoom:50%;" />

提示：树中至少有 2 个节点。

思路：二叉搜索树采用中序遍历，其实就是一个有序数组。**在一个有序数组上求两个数最小差值。**

最直观的想法，就是遍历一遍二叉搜索树，相邻元素做差，就统计出来最小差值了。

**需要用一个pre节点记录一下cur节点的前一个节点。**

如图：

<img src="https://img-blog.csdnimg.cn/20210204153247458.png" alt="530.二叉搜索树的最小绝对差" style="zoom:50%;" />

```c++
//中序遍历(迭代),依次做差
class Solution {
public:
    int getMinimumDifference(TreeNode* root) {
        TreeNode* cur = root;
        TreeNode* pre = nullptr;
        stack<TreeNode*> stk;
        int abs_min=INT_MAX;
        while(cur != nullptr || !stk.empty()) {
            if(cur != nullptr) {
                stk.push(cur);
                cur = cur->left;
            } else {
                cur = stk.top();
                stk.pop();
                if(pre != nullptr)
                    abs_min = min(cur->val - pre->val, abs_min);
                pre = cur;
                cur = cur->right;
            }
        }
        return abs_min;
    }
};

//中序遍历(递归)
class Solution {
public:
    TreeNode* pre = nullptr;
    int abs_min = INT_MAX;
    int getMinimumDifference(TreeNode* root) {
        dfs(root);
        return abs_min;
    }
private:
    void dfs(TreeNode* cur) {
        if(cur == nullptr) return;
        dfs(cur->left);    //左
        if(pre != nullptr) {    //中
            abs_min = min(abs_min, cur->val - pre->val);
        }
        pre = cur;
        dfs(cur->right);    //右
    }
};
```

### 十八、二叉搜索树中的众数

[力扣题目链接](https://leetcode.cn/problems/find-mode-in-binary-search-tree/)

给定一个有相同值的二叉搜索树（BST），找出 BST 中的所有众数（出现频率最高的元素）。

假定 BST 有如下定义：

- 结点左子树中所含结点的值小于等于当前结点的值
- 结点右子树中所含结点的值大于等于当前结点的值
- 左子树和右子树都是二叉搜索树

例如：

给定 BST [1,null,2,2],

<img src="https://img-blog.csdnimg.cn/20201014221532206.png" alt="501. 二叉搜索树中的众数" style="zoom:50%;" />

返回[2].

提示：如果众数超过1个，不需考虑输出顺序

**思路：**

这道题目呢，递归法我从两个维度来讲。

**首先如果不是二叉搜索树的话，应该怎么解题，是二叉搜索树，又应该如何解题**，两种方式做一个比较，可以加深大家对二叉树的理解。

#### 1.递归法

**如果不是二叉搜索树**

如果不是二叉搜索树，最直观的方法一定是**把这个树都遍历了，用map统计频率，把频率排个序，最后取前面高频的元素的集合**。

具体步骤如下：

​	1.这个树都遍历了，用map统计频率

至于用前中后序那种遍历也不重要，因为就是要全遍历一遍，怎么个遍历法都行，层序遍历都没毛病！

这里采用前序遍历，代码如下：

```cpp
// map<int, int> key:元素，value:出现频率
void searchBST(TreeNode* cur, unordered_map<int, int>& map) { // 前序遍历
    if (cur == NULL) return ;
    map[cur->val]++; // 中：统计元素频率
    searchBST(cur->left, map); //左
    searchBST(cur->right, map);	//右
    return ;
}
```

​	2.把统计的出来的出现频率（即map中的value）排个序

有的同学可能可以想直接对map中的value排序，还真做不到，C++中如果使用std::map或者std::multimap可以对key排序，但不能对value排序。

所以要把map转化数组即vector，再进行排序，当然vector里面放的也是`pair<int, int>`类型的数据，第一个int为元素，第二个int为出现频率。

代码如下：

```cpp
bool static cmp (const pair<int, int>& a, const pair<int, int>& b) {
    return a.second > b.second; // 按照频率从大到小排序
}

vector<pair<int, int>> vec(map.begin(), map.end());
sort(vec.begin(), vec.end(), cmp); // 按照cmp排序规则，给频率排个序
```

​	3.取前面高频的元素

此时数组vector中已经是存放着按照频率排好序的pair，那么把前面高频的元素取出来就可以了。

代码如下：

```cpp
result.push_back(vec[0].first);
for (int i = 1; i < vec.size(); i++) {
    // 取最高的放到result数组中
    if (vec[i].second == vec[0].second) result.push_back(vec[i].first);
    else break;
}
return result;
```

整体C++代码如下：

```c++
class Solution {
private:

void searchBST(TreeNode* cur, unordered_map<int, int>& map) { // 前序遍历
    if (cur == NULL) return ;
    map[cur->val]++; // 统计元素频率
    searchBST(cur->left, map);
    searchBST(cur->right, map);
    return ;
}
bool static cmp (const pair<int, int>& a, const pair<int, int>& b) {
    return a.second > b.second;
}
public:
    vector<int> findMode(TreeNode* root) {
        unordered_map<int, int> map; // key:元素，value:出现频率
        vector<int> result;
        if (root == NULL) return result;
        searchBST(root, map);
        vector<pair<int, int>> vec(map.begin(), map.end());//map转vector
        sort(vec.begin(), vec.end(), cmp); // 给频率排个序
        result.push_back(vec[0].first);
        for (int i = 1; i < vec.size(); i++) {//是否有频率相同的数
            // 取最高的放到result数组中
            if (vec[i].second == vec[0].second) result.push_back(vec[i].first);
            else break;
        }
        return result;
    }
};
```

#### 2.迭代法

**如果是二叉搜索树，它中序遍历就是有序的**，这里使用迭代法求解。

```c++
class Solution {
public:        
    TreeNode* cur;;
    TreeNode* pre = nullptr;
    vector<int> ret;
    int max_cnt = 0, cnt = 0;
    vector<int> findMode(TreeNode* root) {
        cur = root;
        stack<TreeNode*> stk;
        while(cur != nullptr || !stk.empty()) {
            if(cur != nullptr) {
                stk.push(cur);
                cur = cur->left;    //左
            } else {    //中
                cur = stk.top();
                stk.pop();
                update(cur->val);
                pre = cur;
                cur = cur->right;   //右
            }
        }
        return ret;
    }
private:
    void update(int val) {
        if(pre == nullptr) { //处理第一个节点
            cnt = 1;
        } else if (pre->val == cur->val) { //与前一个节点值相同
            cnt++;
        } else {    //与前一个节点值不相同
            cnt = 1;
        }
        if(cnt == max_cnt) { //如果和最大值相同，放进result中
            ret.push_back(cur->val);
        }
        if(cnt > max_cnt) { //如果大于最大值
            max_cnt = cnt;  //更新最大值
            ret.clear(); // 很关键的一步，不要忘记清空result，之前result里的元素都失效了
            ret.push_back(cur->val);
        }
    }
};
```

### 十九、二叉树的最近公共祖先

[力扣题目链接](https://leetcode.cn/problems/lowest-common-ancestor-of-a-binary-tree/)

给定一个二叉树, 找到该树中两个指定节点的最近公共祖先。

百度百科中最近公共祖先的定义为：“对于有根树 T 的两个结点 p、q，最近公共祖先表示为一个结点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（一个节点也可以是它自己的祖先）。”

例如，给定如下二叉树: root = [3,5,1,6,2,0,8,null,null,7,4]

<img src="https://img-blog.csdnimg.cn/20201016173414722.png" alt="236. 二叉树的最近公共祖先" style="zoom:50%;" />

示例 1: 输入: root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 1 输出: 3 解释: 节点 5 和节点 1 的最近公共祖先是节点 3。

说明:

- 所有节点的值都是唯一的。
- p、q 为不同节点且均存在于给定的二叉树中。

**思路**：**遇到一个节点的左子树里有p，右子树里有q，那么当前节点就是最近公共祖先。**

```c++
//后序遍历(回溯)
class Solution {
public:
    TreeNode* ret;
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) { 
        dfs(root, p, q);
        return ret;
    }
private:
    bool dfs(TreeNode* root, TreeNode* p, TreeNode* q) {
        if(root == NULL) return false;
        bool left = dfs(root->left, p, q); //左
        bool right = dfs(root->right, p, q); //右
        //①当前节点的左右子树都找到p和q了；②当前节点的左子树或右子树找到p或q，且当前节点本身是q或p； 则当前节点就是最近祖先
        if((left && right) || (left || right) && (root->val == p->val || root->val == q->val)) { //中
            ret = root;//记录最近公共祖先
        }
        //递归返回需要的结果，即：子树是否包含节点p或q
        return (left || right) || (root->val == p->val || root->val == q->val);
    }
};
```

注意的两点:

1. 求最近公共祖先，需要从底向上遍历，那么二叉树，只能通过**后序遍历**（即：回溯）实现从低向上的遍历方式。
2. 在回溯的过程中，必然**要遍历整棵二叉树，即使已经找到结果了，依然要把其他节点遍历完**，因为要使用递归函数的返回值（也就是代码中的left和right）做逻辑判断。

### 二十、二叉搜索树的最近公共祖先

[力扣题目链接](https://leetcode.cn/problems/lowest-common-ancestor-of-a-binary-search-tree/)

给定一个二叉搜索树, 找到该树中两个指定节点的最近公共祖先。

百度百科中最近公共祖先的定义为：“对于有根树 T 的两个结点 p、q，最近公共祖先表示为一个结点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（一个节点也可以是它自己的祖先）。”

例如，给定如下二叉搜索树: root = [6,2,8,0,4,7,9,null,null,3,5]

<img src="https://img-blog.csdnimg.cn/20201018172243602.png" alt="235. 二叉搜索树的最近公共祖先" style="zoom:50%;" />

示例 1:

- 输入: root = [6,2,8,0,4,7,9,null,null,3,5], p = 2, q = 8
- 输出: 6
- 解释: 节点 2 和节点 8 的最近公共祖先是 6。

#### 1.当作普通二叉树处理

​	做法和上一题完全相同

#### 2.利用二叉搜索树的性质

​	因为是有序树，所有 如果 中间节点是 q 和 p 的公共祖先，那么 中节点的数组 一定是在 [p, q]区间的。即 中节点 > p && 中节点 < q 或者 中节点 > q && 中节点 < p。那么只要**自顶向下遍历二叉搜索树**(上一题的普通二叉树是自底向上查找),遇到 cur节点是数值在[p, q]区间中则一定可以说明该节点cur就是q 和 p的公共祖先。

递归：

```c++
//二叉搜索树的递归方法
class Solution {
    public:
    TreeNode* ret;
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        return dfs(root, p, q);
    }
    TreeNode* dfs(TreeNode* root, TreeNode* p, TreeNode* q) {
        if(root == NULL) return NULL;
        //自顶向下遍历，当前节点值都大于p、q,或都小于p,q，则访问左节点或右节点
        if(root->val > p->val && root->val > q->val) {
            return dfs(root->left, p, q);
        }
        else if(root->val < p->val && root->val < q->val){
            return dfs(root->right, p, q);
        }
        else {//root的值位于p、q值之间
            return root;
        }
    }
};
};
```

迭代：

```c++
//二叉搜索树的迭代方法
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        while(root) {
            if (root->val > p->val && root->val > q->val) {
                root = root->left;
            } else if (root->val < p->val && root->val < q->val) {
                root = root->right;
            } else return root;
        }
        return NULL;
    }
};
```

### 二十一、二叉搜索树中的插入操作

[力扣题目链接](https://leetcode.cn/problems/insert-into-a-binary-search-tree/)

给定二叉搜索树（BST）的根节点和要插入树中的值，将值插入二叉搜索树。 返回插入后二叉搜索树的根节点。 输入数据保证，新值和原始二叉搜索树中的任意节点值都不同。

注意，可能存在多种有效的插入方式，只要树在插入后仍保持为二叉搜索树即可。 你可以返回任意有效的结果。

<img src="https://img-blog.csdnimg.cn/20201019173259554.png" alt="701.二叉搜索树中的插入操作" style="zoom:50%;" />

提示：

- 给定的树上的节点数介于 0 和 10^4 之间
- 每个节点都有一个唯一整数值，取值范围从 0 到 10^8
- -10^8 <= val <= 10^8
- 新值和原始二叉搜索树中的任意节点值都不同

**思路：**

​	其实这道题目其实是一道简单题目，**但是题目中的提示：有多种有效的插入方式，还可以重构二叉搜索树...，**其实可以不考虑题目中提示所说的改变树的结构的插入方式。

如下演示视频中可以看出：只要按照二叉搜索树的规则去遍历，遇到空节点就插入节点就可以了。

<img src="https://tva1.sinaimg.cn/large/008eGmZEly1gnbk63ina5g30eo08waja.gif" alt="701.二叉搜索树中的插入操作" style="zoom:50%;" />

例如插入元素10 ，需要找到末尾节点插入便可，一样的道理来插入元素15，插入元素0，插入元素6，**并不需要调整二叉树结构**。。

只要遍历二叉搜索树，找到空节点 插入元素就可以了，那么这道题其实就简单了。

接下来就是遍历二叉搜索树的过程了。

#### 1.DFS

递归三部曲：

- 确定递归函数参数以及返回值

参数就是根节点指针，以及要插入元素，这里递归函数要不要有返回值呢？

可以有，也可以没有，但递归函数如果没有返回值的话，实现是比较麻烦的，下面也会给出其具体实现代码。

**有返回值的话，可以利用返回值完成新加入的节点与其父节点的赋值操作**。（下面会进一步解释）

递归函数的返回类型为节点类型TreeNode * 。

代码如下：

```c++
TreeNode* insertIntoBST(TreeNode* root, int val)
```

- 确定终止条件

终止条件就是找到遍历的节点为null的时候，就是要插入节点的位置了，并把插入的节点返回。

代码如下：

```c++
if (root == NULL) {
    TreeNode* node = new TreeNode(val);
    return node;
}
```

这里把添加的节点返回给上一层，就完成了父子节点的赋值操作了，详细再往下看。

- 确定单层递归的逻辑

此时要明确，需要遍历整棵树么？

别忘了这是搜索树，遍历整棵搜索树简直是对搜索树的侮辱，哈哈。

搜索树是有方向的，可以根据插入元素的数值，选择递归方向。

代码如下：

```c++
if (root->val > val) root->left = insertIntoBST(root->left, val);
if (root->val < val) root->right = insertIntoBST(root->right, val);
return root;
```

**到这里能感受到，如何通过递归函数返回值完成了新加入节点的父子关系赋值操作了，下一层将加入节点返回，本层用root->left或者root->right将其接住**。

完整代码：

```c++
class Solution {
public:
    TreeNode* insertIntoBST(TreeNode* root, int val) {
        if(root == nullptr) {
            TreeNode* node = new TreeNode(val);
            return node;
        }
        if(root->val > val) root->left = insertIntoBST(root->left, val);
        if(root->val < val) root->right = insertIntoBST(root->right, val);
        return root;//别忘了递归返回
    }
};
```

### 二十二、删除二叉搜索树中的节点

[力扣题目链接](https://leetcode.cn/problems/delete-node-in-a-bst/)

给定一个二叉搜索树的根节点 root 和一个值 key，删除二叉搜索树中的 key 对应的节点，并保证二叉搜索树的性质不变。返回二叉搜索树（有可能被更新）的根节点的引用。

一般来说，删除节点可分为两个步骤：

首先找到需要删除的节点； 如果找到了，删除它。 说明： 要求算法时间复杂度为 O(h)，h 为树的高度。

示例:

<img src="https://img-blog.csdnimg.cn/20201020171048265.png" alt="450.删除二叉搜索树中的节点" style="zoom:50%;" />

提示:

节点数的范围 [0, 10^4].
-10^5 <= Node.val <= 10^5
节点值唯一
root 是合法的二叉搜索树
-10^5<= key <= 10^5

#### 1.二叉搜索树的方法

**思路：**根据二叉搜索树的性质：

- 如果目标节点大于当前节点值，则去右子树中删除；

- 如果目标节点小于当前节点值，则去左子树中删除；
- 如果目标节点就是当前节点，分为以下三种情况：

​			1.其无左子：其右子顶替其位置，删除了该节点；
​			2.其无右子：其左子顶替其位置，删除了该节点；
​			3.其左右子节点都有：其左子树转移到其右子树的最左节点的左子树上，然后右子树顶替其位置，由此删除了该节点。

完整代码：

```c++
class Solution {
public:
    TreeNode* deleteNode(TreeNode* root, int key) {
        if(root == nullptr) return root;
        if(root->val == key) {
            if(root->left == nullptr) { //左节点为空，右节点直接补上当前删除节点
                root = root->right;
            }
            else if(root->right == nullptr) {   //右节点为空，左节点直接补上当前删除节点
                root = root->left;
            }
            else {  //左右节点皆非空
                TreeNode* cur = root->right;//右子树的根节点
                while(cur->left) {
                    cur = cur->left;
                }
                cur->left = root->left;//左子树移到右子树最左端.
                TreeNode* tmp = root;// 把root节点保存一下，下面来删除
                root = root->right;//右子树补上当前删除节点
                delete tmp;       // 释放节点内存
            }       
        }
        else if(root->val > key) root->left = deleteNode(root->left, key);
        else root->right = deleteNode(root->right, key);
        return root;
    }
};
```

#### 2.普通二叉树的删除方式

这里我在介绍一种通用的删除，普通二叉树的删除方式（没有使用搜索树的特性，遍历整棵树），用交换值的操作来删除目标节点。

代码中目标节点（要删除的节点）被操作了两次：

- 第一次是和目标节点的右子树最左面节点交换。
- 第二次直接被NULL覆盖了。

思路有点绕，感兴趣的同学可以画图自己理解一下。

代码如下：（关键部分已经注释）

```cpp
class Solution {
public:
    TreeNode* deleteNode(TreeNode* root, int key) {
        if (root == nullptr) return root;
        if (root->val == key) {
            if (root->right == nullptr) { // 这里第二次操作目标值：最终删除的作用
                return root->left;
            }
            TreeNode *cur = root->right;
            while (cur->left) {
                cur = cur->left;
            }
            swap(root->val, cur->val); // 这里第一次操作目标值：交换目标值其右子树最左面节点。
        }
        root->left = deleteNode(root->left, key);
        root->right = deleteNode(root->right, key);
        return root;
    }
};
```

### 二十三、修剪二叉搜索树

[力扣题目链接](https://leetcode.cn/problems/trim-a-binary-search-tree/)

给定一个二叉搜索树，同时给定最小边界L 和最大边界 R。通过修剪二叉搜索树，使得所有节点的值在[L, R]中 (R>=L) 。你可能需要改变树的根节点，所以结果应当返回修剪好的二叉搜索树的新的根节点。

<img src="https://img-blog.csdnimg.cn/20201014173115788.png" alt="669.修剪二叉搜索树" style="zoom: 50%;" />

<img src="https://img-blog.csdnimg.cn/20201014173219142.png" alt="669.修剪二叉搜索树1" style="zoom:50%;" />

#### 1.DFS

思路：

- 对根结点root 进行深度优先遍历。对于当前访问的结点，如果结点为空结点，直接返回空结点；
- 如果结点的值小于low，那么**说明该结点及它的左子树都不符合要求**，我们返回对它的右结点进行修剪后的结果；
- 如果结点的值大于high，那么**说明该结点及它的右子树都不符合要求**，我们返回对它的左子树进行修剪后的结果；
- 如果结点的值位于区间[low, high]，我们将结点的左结点设为对它的左子树修剪后的结果，右结点设为对它的右子树进行修剪后的结果。

完整代码：

```c++
//递归
class Solution {
public:
    TreeNode* trimBST(TreeNode* root, int low, int high) {
        if(root == nullptr) return nullptr;
        if(root->val < low) {
            return trimBST(root->right, low, high);
        } else if(root->val > high) {
            return trimBST(root->left, low, high);
        } else {
            root->left = trimBST(root->left, low, high);
            root->right = trimBST(root->right, low, high);
            return root;
        }
    }
};
```

**复杂度分析**

- 时间复杂度：O(n)，其中 n 为二叉树的结点数目。
- 空间复杂度：O(n)。递归栈最坏情况下需要 O(n) 的空间。

#### 2.迭代

思路：

起始先从给定的 root 进行出发，找到第一个满足值符合[low ,high] 范围的节点，该节点为最后要返回的根节点 root。

随后考虑如何修剪 root 的左右节点：当根节点符合 [low ,high] 要求时，**修剪左右节点过程中仅需考虑一边的边界值即可**。即对于 root->left只需考虑将值小于 low 的节点去掉（因为二叉搜索树的特性，root->val 满足不大于 high 要求，则其左节点必然满足）；同理 root->right 只需要考虑将大于 high 的节点去掉即可。

完整代码：

```c++
//迭代
class Solution {
public:
    TreeNode* trimBST(TreeNode* root, int low, int high) {
        while(root && (root->val < low || root->val > high)) {
            if(root->val < low) {
                root = root->right;
            } else {
                root = root->left;
            }
        }
        //根节点已经满足要求，下面处理左子树
        TreeNode* cur = root;
        while(cur) {
            while(cur->left && cur->left->val < low) {//不用判断cur->val > high,因为根节点已经满足小于high，左子树必然满足
                cur->left = cur->left->right;
            }
            cur = cur->left;
        }
        //处理右子树
        cur = root;
        while(cur) {
            while(cur->right && cur->right->val > high) {
                cur->right = cur->right->left;
            }
            cur = cur->right;
        }
        return root;
    }
};
```

### 二十四、将有序数组转换为二叉搜索树

[力扣题目链接](https://leetcode.cn/problems/convert-sorted-array-to-binary-search-tree/)

将一个按照升序排列的有序数组，转换为一棵高度平衡二叉搜索树。

本题中，一个高度平衡二叉树是指一个二叉树每个节点 的左右两个子树的高度差的绝对值不超过 1。

示例:

<img src="https://img-blog.csdnimg.cn/20201022164420763.png" alt="108.将有序数组转换为二叉搜索树" style="zoom:50%;" />

思路：

强调是平衡二叉搜索树。其实数组构造二叉树，构成平衡树是自然而然的事情，因为大家默认都是从**数组中间位置取值作为节点元素**，一般不会随机取。**所以想构成不平衡的二叉树是自找麻烦**。

在[二叉树：构造二叉树登场！](https://programmercarl.com/0106.从中序与后序遍历序列构造二叉树.html)和[二叉树：构造一棵最大的二叉树 ](https://programmercarl.com/0654.最大二叉树.html)中其实已经讲过了，如果根据数组构造一棵二叉树。

**本质就是寻找分割点，分割点作为当前节点，然后递归左区间和右区间**。

**分割点就是数组中间位置的节点**。

那么为问题来了，如果数组长度为偶数，中间节点有两个，取哪一个？

取哪一个都可以，只不过构成了不同的平衡二叉搜索树。

例如：输入：[-10,-3,0,5,9]

如下两棵树，都是这个数组的平衡二叉搜索树：

<img src="https://code-thinking.cdn.bcebos.com/pics/108.%E5%B0%86%E6%9C%89%E5%BA%8F%E6%95%B0%E7%BB%84%E8%BD%AC%E6%8D%A2%E4%B8%BA%E4%BA%8C%E5%8F%89%E6%90%9C%E7%B4%A2%E6%A0%91.png" alt="108.将有序数组转换为二叉搜索树" style="zoom:50%;" />

如果要分割的数组长度为偶数的时候，中间元素为两个，是取左边元素 就是树1，取右边元素就是树2。

**这也是题目中强调答案不是唯一的原因。 理解这一点，这道题目算是理解到位了**。

#### 1.DFS

完整代码：

```c++
class Solution {
public:
    TreeNode* sortedArrayToBST(vector<int>& nums) {
       TreeNode* root = dfs(nums, 0, nums.size() - 1);
       return root;
    }
private:
    TreeNode* dfs(vector<int>& nums, int left, int right) {
        if(left > right) return nullptr;
        int mid = left + (right - left) / 2;
        TreeNode* node = new TreeNode(nums[mid]);
        node->left = dfs(nums, left, mid - 1);
        node->right = dfs(nums, mid + 1, right);
        return node;
    }
};
```

### 二十五、把二叉搜索树转换为累加树

[力扣题目链接](https://leetcode.cn/problems/convert-bst-to-greater-tree/)

给出二叉 搜索 树的根节点，该树的节点值各不相同，请你将其转换为累加树（Greater Sum Tree），使每个节点 node 的新值等于原树中大于或等于 node.val 的值之和。

提醒一下，二叉搜索树满足下列约束条件：

节点的左子树仅包含键 小于 节点键的节点。 节点的右子树仅包含键 大于 节点键的节点。 左右子树也必须是二叉搜索树。

示例 1：

<img src="https://img-blog.csdnimg.cn/20201023160751832.png" alt="538.把二叉搜索树转换为累加树" style="zoom:50%;" />

- 输入：[4,1,6,0,2,5,7,null,null,null,3,null,null,null,8]
- 输出：[30,36,21,36,35,26,15,null,null,null,33,null,null,null,8]

**思路**：本题中要求我们将每个节点的值修改为原来的节点值加上所有大于它的节点值之和。这样我们只需要**反序中序遍历该二叉搜索树**，记录过程中的节点值之和，并不断更新当前遍历到的节点的节点值，即可得到题目要求的累加树。

**1.递归**

```c++
//反序中序遍历--递归
class Solution {
public:
    int pre = 0;	// 记录前一个节点的数值
    TreeNode* convertBST(TreeNode* root) {
        if(root == nullptr) return nullptr;
        convertBST(root->right);
        pre += root->val;
        root->val = pre;
        convertBST(root->left);
        return root;
    }
};
```

**2.迭代**

```c++
//反序中序遍历--迭代
class Solution {
public:
    TreeNode* convertBST(TreeNode* root) {
        int pre = 0;	// 记录前一个节点的数值
        TreeNode* cur = root;
        stack<TreeNode*> stk;
        while(cur != nullptr || !stk.empty()) {
            if(cur != nullptr) {
                stk.push(cur);
                cur = cur->right;
            } else {
                cur = stk.top();
                stk.pop();
                pre += cur->val;
                cur->val = pre;
                cur = cur->left;
            }
        }
        return root;
    }
};
```



### 二十六、二叉树总结

**在二叉树题目选择什么遍历顺序，可以大体分分类**。

- 涉及到二叉树的构造，无论普通二叉树还是二叉搜索树一定前序，都是先构造中节点。
- 求普通二叉树的属性，一般是后序，一般要通过递归函数的返回值做计算。
- **求二叉搜索树的属性，一定是中序了，要不白瞎了有序性了。**

注意在普通二叉树的属性中，我用的是一般为后序，这是为了方便让父节点指向子节点。

所以求普通二叉树的属性还是要具体问题具体分析。

二叉树专题汇聚为一张图：

<img src="https://code-thinking-1253855093.file.myqcloud.com/pics/20211030125421.png" alt="img" style="zoom:150%;" />

