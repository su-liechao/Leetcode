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

### 八、总结

![img](https://code-thinking-1253855093.file.myqcloud.com/pics/%E9%93%BE%E8%A1%A8%E6%80%BB%E7%BB%93.png)

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

## 字符串

### 一、反转字符串

[力扣题目链接](https://leetcode.cn/problems/reverse-string/)

编写一个函数，其作用是将输入的字符串反转过来。输入字符串以字符数组 char[] 的形式给出。

不要给另外的数组分配额外的空间，你必须原地修改输入数组、使用 O(1) 的额外空间解决这一问题。

你可以假设数组中的所有字符都是 ASCII 码表中的可打印字符。

示例 1：
输入：["h","e","l","l","o"]
输出：["o","l","l","e","h"]

示例 2：
输入：["H","a","n","n","a","h"]
输出：["h","a","n","n","a","H"]

思路：

对于字符串，我们定义两个指针（也可以说是索引下标），一个从字符串前面，一个从字符串后面，两个指针同时向中间移动，并交换元素。

以字符串`hello`为例，过程如下：

<img src="https://tva1.sinaimg.cn/large/008eGmZEly1gp0fvi91pfg30de0akwnq.gif" alt="344.反转字符串" style="zoom:67%;" />

```c++
class Solution {
public:
    void reverseString(vector<char>& s) {
        int left = 0;
        int right = s.size() - 1;
        for(; left < right; left++, right--) {
            swap(s[left], s[right]);
        }
    }
};
```

### 二、反转字符串II

[力扣题目链接](https://leetcode.cn/problems/reverse-string-ii/)

给定一个字符串 s 和一个整数 k，从字符串开头算起, 每计数至 2k 个字符，就反转这 2k 个字符中的前 k 个字符。

如果剩余字符少于 k 个，则将剩余字符全部反转。

如果剩余字符小于 2k 但大于或等于 k 个，则反转前 k 个字符，其余字符保持原样。

示例:

输入: s = "abcdefg", k = 2
输出: "bacdfeg"

思路：

在遍历字符串的过程中，只要让 i += (2 * k)，i 每次移动 2 * k 就可以了，然后判断是否需要有反转的区间

完整代码：

```c++
class Solution {
public:
    string reverseStr(string s, int k) {
        for(int i = 0; i < s.size(); i += 2*k) {    //一次循环处理2k个字符
            //前k个字符完整，反转前k个字符
            if(i + k < s.size()) {
                for(int left = i, right = i + k -1; left < right; left++, right--) {
                    swap(s[left], s[right]);
                }
            } else {// 否则反转i->s.end()的字符
                for(int left = i, right = s.size() - 1; left < right; left++, right--) {
                    swap(s[left], s[right]);
                }
            }
        }
        return s;
    }
};
```

### 三、剑指Offer 05.替换空格

[力扣题目链接(opens new window)](https://leetcode.cn/problems/ti-huan-kong-ge-lcof/)

请实现一个函数，把字符串 s 中的每个空格替换成"%20"。

示例 1： 输入：s = "We are happy."
输出："We%20are%20happy."



思路：

如果想把这道题目做到极致，就不要只用额外的辅助空间了！

首先扩充数组到每个空格替换成"%20"之后的大小。

然后从后向前替换空格，也就是双指针法，过程如下：

i指向新长度的末尾，j指向旧长度的末尾。

<img src="https://tva1.sinaimg.cn/large/e6c9d24ely1go6qmevhgpg20du09m4qp.gif" alt="替换空格" style="zoom:67%;" />

有同学问了，为什么要从后向前填充，从前向后填充不行么？

从前向后填充就是O(n^2)的算法了，因为每次添加元素都要将添加元素之后的所有元素向后移动。

**其实很多数组填充类的问题，都可以先预先给数组扩容带填充后的大小，然后在从后向前进行操作。**

这么做有两个好处：

1. 不用申请新数组。
2. 从后向前填充元素，避免了从前先后填充元素要来的 每次添加元素都要将添加元素之后的所有元素向后移动。

```c++
//创建新的字符串  空间复杂度O(N)
// class Solution {
// public:
//     string replaceSpace(string s) {
//         string ret;
//         for(int i = 0; i < s.size(); i++) {
//             if(s[i] == ' ') {
//                 ret += "%20";
//             } else {
//                 ret += s[i];
//             }
//         }
//         return ret;
//     }
// };

//原地修改(扩充字符串+双指针)  空间复杂度O(1)
class Solution {
public:
    string replaceSpace(string s) {
        int cnt = 0;
        int len = s.length();
        for(int i = 0; i < len; i++) {
            if(s[i] == ' ') {
                cnt++;
            }
        }
        s.resize(len + 2 * cnt);//扩充字符串
        for(int i = len - 1, j = s.size() - 1; i < j; i--, j--) {
            if(s[i] == ' ') {
                s[j -2] = '%';
                s[j -1] = '2';
                s[j] = '0';
                j -= 2;
            } else {
                s[j] = s[i];
            }
        }
        return s;
    }
};
```

### 四、翻转字符串里的单词

[力扣题目链接](https://leetcode.cn/problems/reverse-words-in-a-string/)

给定一个字符串，逐个翻转字符串中的每个单词。

示例 1：
输入: "the sky is blue"
输出: "blue is sky the"

示例 2：
输入: "  hello world!  "
输出: "world! hello"
解释: 输入字符串可以在前面或者后面包含多余的空格，但是反转后的字符不能包括。

示例 3：
输入: "a good  example"
输出: "example good a"
解释: 如果两个单词间有多余的空格，将反转后单词间的空格减少到只含一个

思路：

- 反转整个字符串
- 反转单词（去掉多余空格）
- 去除末尾无用字符

对于字符串可变的语言，就不需要再额外开辟空间了，直接在字符串上原地实现。在这种情况下，反转字符和去除空格可以一起完成。

<img src="https://pic.leetcode-cn.com/Figures/151/mutable2.png" alt="fig" style="zoom: 33%;" />



完整代码：

```c++
//整体翻转+局部翻转
class Solution {
public:
    string reverseWords(string s) {
        // 先反转整个字符串
        reverse(s.begin(), s.end());
        int n = s.size();
        int idx = 0;
        for(int start = 0; start < s.size(); start++) {
            //找到单词的第一个字母
            if(s[start] != ' ') {
                // cout << idx << endl;
                if(idx != 0) s[idx++] = ' ';//不是第一个单词，则用空格隔开
                int end = start; //使用end来给修改字符串的值
                while(end < n && s[end] != ' ') s[idx++] = s[end++];
                //反转该单词
                reverse(s.begin() + idx - (end - start), s.begin() + idx);
                start = end; //更新start的位置
            }
        }
        s.erase(s.begin() + idx, s.end());	//去除末尾无用字符
        return s;
    }
};
```

### 五、剑指Offer58-II.左旋转字符串

[力扣题目链接](https://leetcode.cn/problems/zuo-xuan-zhuan-zi-fu-chuan-lcof/)

字符串的左旋转操作是把字符串前面的若干个字符转移到字符串的尾部。请定义一个函数实现字符串左旋转操作的功能。比如，输入字符串"abcdefg"和数字2，该函数将返回左旋转两位得到的结果"cdefgab"。

示例 1：
输入: s = "abcdefg", k = 2
输出: "cdefgab"

示例 2：
输入: s = "lrloseumgh", k = 6
输出: "umghlrlose"

限制：
1 <= k < s.length <= 10000

思路：前面提到使用整体反转+局部反转就可以实现，反转单词顺序的目的。

这道题目也非常类似，依然可以通过局部反转+整体反转 达到左旋转的目的。

具体步骤为：

- 反转区间为前n的子串
- 反转区间为n到末尾的子串
- 反转整个字符串

最后就可以得到左旋n的目的，而不用定义新的字符串，完全在本串上操作。

如图：

<img src="https://code-thinking.cdn.bcebos.com/pics/%E5%89%91%E6%8C%87Offer58-II.%E5%B7%A6%E6%97%8B%E8%BD%AC%E5%AD%97%E7%AC%A6%E4%B8%B2.png" alt="img" style="zoom:50%;" />

最终得到左旋2个单元的字符串：cdefgab

完整代码：

```c++
class Solution {
public:
    string reverseLeftWords(string s, int n) {
        reverse(s.begin(), s.begin() + n);  //左闭右开
        reverse(s.begin() + n, s.end());
        reverse(s.begin(), s.end());
        return s;
    }
};
```

### 六、实现 strStr()

[力扣题目链接](https://leetcode.cn/problems/find-the-index-of-the-first-occurrence-in-a-string/)

实现 strStr() 函数。

给定一个 haystack 字符串和一个 needle 字符串，在 haystack 字符串中找出 needle 字符串出现的第一个位置 (从0开始)。如果不存在，则返回 -1。

示例 1: 输入: haystack = "hello", needle = "ll" 输出: 2

示例 2: 输入: haystack = "aaaaa", needle = "bba" 输出: -1

#### 1.暴力求解

直观的解法的是：枚举原串 haystack 中的每个字符作为「发起点」，每次从原串的「发起点」和匹配串的「首位」开始尝试匹配：

- 匹配成功：返回本次匹配的原串「发起点」。

- 匹配失败：枚举原串的下一个「发起点」，重新尝试匹配。

```c++
//暴力解法,复杂度O((n-m)*m)
class Solution {
public:
    int strStr(string haystack, string needle) {
        int n = haystack.length(), m = needle.length();
        for(int i = 0; i <= n - m; i++) {   //这里相当于剪枝了
            int index_ha = i, index_ne = 0;
            while(haystack[index_ha] == needle[index_ne] && index_ne < m) {
                index_ha++;
                index_ne++;
            }
            if(index_ne == m) return i;
        }
        return -1;
    }
};
```

#### 2.KMP

**KMP详解见：**[【宫水三叶】简单题学 KMP 算法 - 找出字符串中第一个匹配项的下标 - 力扣（LeetCode）](https://leetcode.cn/problems/find-the-index-of-the-first-occurrence-in-a-string/solution/shua-chuan-lc-shuang-bai-po-su-jie-fa-km-tb86/)

```c++
//KMP解法,复杂度O(m+n)
class Solution {
public:
    int strStr(string haystack, string needle) {
        int n = haystack.length(), m = needle.length();
        //构造next数组
        vector<int> next(m, 0);
        for(int i = 1, j = 0; i < m; i++) {
            while(j > 0 && needle[i] != needle[j]) j = next[j - 1];
            if(needle[i] == needle[j]) j++;
            next[i] = j;
        }
        
        //match
        for(int i = 0, j = 0; i < n; i++) {
            while(j > 0 && haystack[i] != needle[j]) j = next[j - 1];
            if(haystack[i] == needle[j]) j++;
            if(j == m) return i - m + 1;
        }
        return -1;
    }
};
```

总结：KMP算法中next数组为什么遇到字符不匹配的时候可以找到上一个匹配过的位置继续匹配，靠的是有计算好的前缀表。 前缀表里，统计了各个位置为终点字符串的最长相同前后缀的长度.

### 七、重复的子字符转

[力扣题目链接](https://leetcode.cn/problems/repeated-substring-pattern/)

给定一个非空的字符串，判断它是否可以由它的一个子串重复多次构成。给定的字符串只含有小写英文字母，并且长度不超过10000。

示例 1:
输入: "abab"
输出: True
解释: 可由子字符串 "ab" 重复两次构成。

示例 2:
输入: "aba"
输出: False

示例 3:
输入: "abcabcabcabc"
输出: True
解释: 可由子字符串 "abc" 重复四次构成。 (或者子字符串 "abcabc" 重复两次构成。)

#### 1.移动匹配

当一个字符串s：abcabc，内部又重复的子串组成，那么这个字符串的结构一定是这样的：

<img src="https://code-thinking-1253855093.file.myqcloud.com/pics/20220728104518.png" alt="图一" style="zoom:50%;" />

也就是又前后又相同的子串组成。

那么既然前面有相同的子串，后面有相同的子串，用 s + s，这样组成的字符串中，后面的子串做前串，前后的子串做后串，就一定还能组成一个s，如图：

<img src="https://code-thinking-1253855093.file.myqcloud.com/pics/20220728104931.png" alt="图二" style="zoom:50%;" />

所以判断字符串s是否有重复子串组成，只要两个s拼接在一起，里面还出现一个s的话，就说明是又重复子串组成。

当然，我们在判断 s + s 拼接的字符串里是否出现一个s的的时候，**要刨除 s + s 的首字符和尾字符**，这样避免在s+s中搜索出原来的s，我们要搜索的是中间拼接出来的s。

代码如下：

```c++
class Solution {
public:
    bool repeatedSubstringPattern(string s) {
        //size_t find(const string& str, size_t pos = 0) const; 
        //返回子字符串str第一次出现的起始下标,str待查找的子字符串,pos开始查找的位置下标
        return (s + s).find(s, 1) != s.size();
    }
};

```

#### 2.KMP解法

在由重复子串组成的字符串中，**最长相等前后缀**不包含的子串就是最小重复子串，这里那字符串s：abababab 来举例，ab就是最小重复单位，如图所示：

<img src="https://code-thinking-1253855093.file.myqcloud.com/pics/20220728205249.png" alt="图三" style="zoom:50%;" />

**如何找到最小重复子串？**最关键还是要理解 最长相等前后缀，如图：

<img src="https://code-thinking-1253855093.file.myqcloud.com/pics/20220728212157.png" alt="图四" style="zoom:50%;" />

步骤一：因为 这是相等的前缀和后缀，t[0] 与 k[0]相同， t[1] 与 k[1]相同，所以 s[0] 一定和 s[2]相同，s[1] 一定和 s[3]相同，即：，s[0]s[1]与s[2]s[3]相同 。

步骤二： 因为在同一个字符串位置，所以 t[2] 与 k[0]相同，t[3] 与 k[1]相同。

步骤三： 因为 这是相等的前缀和后缀，t[2] 与 k[2]相同 ，t[3]与k[3] 相同，所以，s[2]一定和s[4]相同，s[3]一定和s[5]相同，即：s[2]s[3] 与 s[4]s[5]相同。

步骤四：循环往复。

所以字符串s，s[0]s[1]与s[2]s[3]相同， s[2]s[3] 与 s[4]s[5]相同，s[4]s[5] 与 s[6]s[7] 相同。

**正是因为 最长相等前后缀的规则，当一个字符串由重复子串组成的，最长相等前后缀不包含的子串就是最小重复子串**。

KMP实现代码：

```c++
class Solution {
public:
    void getNext (int* next, const string& s){
        next[0] = 0;
        int j = 0;
        for(int i = 1;i < s.size(); i++){
            while(j > 0 && s[i] != s[j]) {
                j = next[j - 1];
            }
            if(s[i] == s[j]) {
                j++;
            }
            next[i] = j;
        }
    }
    bool repeatedSubstringPattern (string s) {
        if (s.size() == 0) {
            return false;
        }
        int next[s.size()];
        getNext(next, s);
        int len = s.size();
        if (next[len - 1] != 0 && len % (len - (next[len - 1] )) == 0) {
            return true;
        }
        return false;
    }
};
```

## 双指针

### 双指针法的例题以及总结

由于双指针法穿插应用于前面的一些例题中，这里给出应用到双指针的例题连接

[戳我](https://www.programmercarl.com/双指针总结.html)

## 栈和队列

### 一、队列：先入先出的数据结构

**队列的实现**

1.为了实现队列，可以使用**动态数组**和**指向队列头部的索引**。

如上所述，队列应支持两种操作：入队和出队。入队会向队列追加一个新元素，而出队会删除第一个元素。 所以我们需要一个索引来指出起点。

队列应支持两种操作：入队和出队。入队会向队列追加一个新元素，而出队会删除第一个元素。 所以我们需要一个索引来指出起点。

参考实现：

```c++
#include <iostream>

class MyQueue {
    private:
        // store elements
        vector<int> data;       
        // a pointer to indicate the start position
        int p_start;            
    public:
        MyQueue() {p_start = 0;}
        /** Insert an element into the queue. Return true if the operation is successful. */
        bool enQueue(int x) {
            data.push_back(x);
            return true;
        }
        /** Delete an element from the queue. Return true if the operation is successful. */
        bool deQueue() {
            if (isEmpty()) {
                return false;
            }
            p_start++;
            return true;
        };
        /** Get the front item from the queue. */
        int Front() {
            return data[p_start];
        };
        /** Checks whether the queue is empty or not. */
        bool isEmpty()  {
            return p_start >= data.size();
        }
};

int main() {
    MyQueue q;
    q.enQueue(5);
    q.enQueue(3);
    if (!q.isEmpty()) {
        cout << q.Front() << endl;
    }
    q.deQueue();
    if (!q.isEmpty()) {
        cout << q.Front() << endl;
    }
    q.deQueue();
    if (!q.isEmpty()) {
        cout << q.Front() << endl;
    }
}
```

缺点：上面的实现很简单，但在某些情况下效率很低。 随着头部指针的移动，浪费了越来越多的空间。 当我们有空间限制时，这将是难以接受的。

更有效的方法是使用**循环队列**。 具体来说，我们可以使用**固定大小的数组**和**两个指针**来指示起始位置和结束位置。 目的是**重用**我们之前提到的被**浪费的存储**。

#### 1.设计循环队列

[leetcode连接](https://leetcode.cn/leetbook/read/queue-stack/kzlb5/)

设计你的循环队列实现。 循环队列是一种线性数据结构，其操作表现基于 FIFO（先进先出）原则并且队尾被连接在队首之后以形成一个循环。它也被称为“环形缓冲器”。

循环队列的一个好处是我们可以利用这个队列之前用过的空间。在一个普通队列里，一旦一个队列满了，我们就不能插入下一个元素，即使在队列前面仍有空间。但是使用循环队列，我们能使用这些空间去存储新的值。

你的实现应该支持如下操作：

MyCircularQueue(k): 构造器，设置队列长度为 k 。
Front: 从队首获取元素。如果队列为空，返回 -1 。
Rear: 获取队尾元素。如果队列为空，返回 -1 。
enQueue(value): 向循环队列插入一个元素。如果成功插入则返回真。
deQueue(): 从循环队列中删除一个元素。如果成功删除则返回真。
isEmpty(): 检查循环队列是否为空。
isFull(): 检查循环队列是否已满。

完整代码：

```c++
//循环链表总结
//rear = (rear + 1) % capacity; 入队改变rear
//front = (front + 1) % capacity; 出队改变front
//rear == front; 判断是否为空
//front == (rear +1 ) % capacity; 判断是否为满
//elements[front]; 获取front的值
//elements[(rear - 1 + capacity) % capacity];获取rear的值
//size = (rear-front+capacity) % capacity;当前队列的元素个数size
class MyCircularQueue {
private:
    int front;
    int rear;
    int capacity;
    vector<int> elements;

public:
    MyCircularQueue(int k) {
        this->capacity = k + 1; //循环链表有一个位置不存放数据,所以要+1
        this->elements = vector<int>(capacity);
        rear = front = 0;   //初始位置
    }

    bool enQueue(int value) {
        if (isFull()) {
            return false;
        }
        elements[rear] = value;
        rear = (rear + 1) % capacity; //入队改变rear值
        return true;
    }

    bool deQueue() {
        if (isEmpty()) {
            return false;
        }
        front = (front + 1) % capacity; //出队改变front值
        return true;
    }

    int Front() {
        if (isEmpty()) {
            return -1;
        }
        return elements[front];
    }

    int Rear() {
        if (isEmpty()) {
            return -1;
        }
        return elements[(rear - 1 + capacity) % capacity];  //rear位置不存值,一直是空的.rear-1才存放值
    }

    bool isEmpty() {
        return rear == front;
    }

    bool isFull() {
        return ((rear + 1) % capacity) == front;
    }
};

/**
 * Your MyCircularQueue object will be instantiated and called as such:
 * MyCircularQueue* obj = new MyCircularQueue(k);
 * bool param_1 = obj->enQueue(value);
 * bool param_2 = obj->deQueue();
 * int param_3 = obj->Front();
 * int param_4 = obj->Rear();
 * bool param_5 = obj->isEmpty();
 * bool param_6 = obj->isFull();
 */
```

### 二、队列和BFS

​	**广度优先搜索（BFS）**的一个常见应用是找出**从根结点到目标结点的最短路径**。在本文中，这里提供了一个示例来解释在 BFS 算法中是如何逐步应用队列的。

**示例：**这里我们提供一个示例来说明如何使用 BFS 来找出根结点 `A` 和目标结点 `G` 之间的最短路径。见ppt.

[队列与BFS](https://leetcode.cn/leetbook/read/queue-stack/kyozi/)

#### 1.墙与门

给定一个 m × n 的二维网格，网格中有以下三种可能的初始化值：

-1 表示墙或是障碍物
0 表示一扇门
INF 无限表示一个空的房间。然后，我们用 2^31 - 1 = 2147483647 代表 INF。你可以认为通往门的距离总是小于 2147483647 的。
**你要给每个空房间位上填上该房间到 最近门的距离，如果无法到达门，则填 INF 即可。**

示例：

给定二维网格：

INF  -1  0  INF
INF INF INF  -1
INF  -1 INF  -1
  0  -1 INF INF
运行完你的函数后，该网格应该变成：

  3  -1   0   1
  2   2   1  -1
  1  -1   2  -1
  0  -1   3   4

BFS实现:

```c++
class Solution {
public:
    void wallsAndGates(vector<vector<int>>& rooms) {
        const int INF = 2147483647;
        int m = rooms.size(), n = rooms[0].size();
        vector<pair<int, int>> directs = {{1, 0}, {0, 1}, {-1, 0}, {0, -1}};
        queue<pair<int, int>> q;
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (rooms[i][j] != 0) continue;
                q.push(make_pair(i, j)); //所有门入队
            }
        }
        while (!q.empty()) {
            auto pos = q.front();q.pop();
            int x = pos.first, y = pos.second;
            int dp = rooms[x][y] + 1; 	//距离门的距离：当前位置的距离门的距离+1
            for (auto d : directs) {
                int nx = x + d.first, ny = y + d.second;
                if (nx < 0 || nx >= m || ny < 0 || ny >= n || rooms[nx][ny] != INF) continue;
                rooms[nx][ny] = dp; 
                q.push(make_pair(nx, ny));
            }
        }
        return;
    }
};

```

DFS实现：

```c++
class Solution {
public:
    c
    void wallsAndGates(vector<vector<int>>& rooms) {
        for (int i = 0; i < rooms.size(); ++i) {
            for (int j = 0; j < rooms[i].size(); ++j) {
                if (rooms[i][j] == 0) dfs(rooms, i, j, 0);
            }
        }
    }
    void dfs(vector<vector<int>>& rooms, int i, int j, int step) {
        if (i < 0 || i >= rooms.size() || j < 0 || j >= rooms[i].size() || rooms[i][j] < step) return;//rooms[i][j] < step：排除墙
        rooms[i][j] = step;
        dfs(rooms, i + 1, j, step + 1);
        dfs(rooms, i - 1, j, step + 1);
        dfs(rooms, i, j + 1, step + 1);
        dfs(rooms, i, j - 1, step + 1);
    }
};
```

#### 2.岛屿数量

[200. 岛屿数量 - 力扣（LeetCode）](https://leetcode.cn/problems/number-of-islands/)

思路：为了求出岛屿的数量，我们可以扫描整个二维网格。如果一个位置为 1，则将其加入队列，开始进行广度优先搜索。在广度优先搜索的过程中，每个搜索到的 1 都会被重新标记为 0。直到队列为空，搜索结束。最终**岛屿的数量就是我们进行广度优先搜索的次数**。

BFS代码：

```c++
class Solution {
public:
    int numIslands(vector<vector<char>>& grid) {
        int num = 0;
        for(int i = 0; i < grid.size(); i++) {
            for(int j = 0; j < grid[0].size(); j++) {
                if(grid[i][j] == '1') {
                    BFS(grid, i, j);
                    num++;
                }
            }
        }
        return num;
    }
private:
    void BFS(vector<vector<char>>& grid, int x, int y) {
        int m = grid.size();
        int n = grid[0].size();
        vector<pair<int, int>> directs = {{1, 0}, {0, 1}, {-1, 0}, {0, -1}};
        queue<pair<int, int>> q;
        q.push(make_pair(x, y));
        grid[x][y] = '0';
        while(!q.empty()) {
            auto index = q.front(); q.pop();
            int x = index.first, y = index.second;
            for(auto d : directs) {
                int nx = x + d.first, ny = y + d.second;
                if(nx < 0 || nx >= m || ny < 0 || ny >= n || grid[nx][ny] == '0') continue;//越界或已访问
                q.push(make_pair(nx, ny));
                grid[nx][ny] = '0';
            }
        }
    }
};
```

**DFS**:将二维网格看成一个无向图，竖直或水平相邻的 1 之间有边相连。为了求出岛屿的数量，我们可以扫描整个二维网格。如果一个位置为 1，则以其为起始节点开始进行**深度优先搜索**。在深度优先搜索的过程中，每个搜索到的 1 都会被重新标记为 0。最终**岛屿的数量就是我们进行深度优先搜索的次数**.

```c++
class Solution {
public:
    int numIslands(vector<vector<char>>& grid) {
        int m = grid.size();
        int n = grid[0].size();
        int num = 0;
        for(int i = 0; i < m; i++){
            for(int j = 0; j < n; j++){
                if(grid[i][j] == '1'){
                    DFS(grid, i, j);
                    num++;
                }
            }
        }
        return num;
    }
private:
    //DFS
    void DFS(vector<vector<char>>& grid, int x, int y){
        int m=grid.size();
        int n=grid[0].size();
        vector<pair<int, int>> directs = {{1, 0}, {0, 1}, {-1, 0}, {0, -1}};
        grid[x][y] = '0'; //访问过的1节点赋值为0
        for(auto d : directs) {
            int nx = x + d.first, ny = y + d.second;
            if(nx < 0 || nx >= m || ny < 0 || ny >= n || grid[nx][ny] == '0') continue; //越界或已访问
            DFS(grid, nx, ny);
        }
    }
};
```

#### 3.打开转盘锁

[752. 打开转盘锁 - 力扣（Leetcode）](https://leetcode.cn/problems/open-the-lock/description/)

你有一个带有四个圆形拨轮的转盘锁。每个拨轮都有10个数字： `'0', '1', '2', '3', '4', '5', '6', '7', '8', '9'` 。每个拨轮可以自由旋转：例如把 `'9'` 变为 `'0'`，`'0'` 变为 `'9'` 。每次旋转都只能旋转一个拨轮的一位数字。

锁的初始数字为 `'0000'` ，一个代表四个拨轮的数字的字符串。

列表 `deadends` 包含了一组死亡数字，一旦拨轮的数字和列表里的任何一个元素相同，这个锁将会被永久锁定，无法再被旋转。

字符串 `target` 代表可以解锁的数字，你需要给出解锁需要的最小旋转次数，如果无论如何不能解锁，返回 `-1` 。

**BFS**

```c++
class Solution {
public:
    int openLock(vector<string>& deadends, string target) {
        if(target == "0000") return 0;
        unordered_set<string> dead(deadends.begin(), deadends.end());
        if(dead.count("0000")) return -1; //初始值在dead中
        int step = 0;
        queue<pair<string, int>> q;
        q.emplace("0000", 0);
        unordered_set<string> seen = {"0000"};//记录已访问的密码
        while(!q.empty()) {
            auto [status, step] = q.front();q.pop();
            for(auto status_next : get_next(status)) {
                if(!dead.count(status_next) && !seen.count(status_next)) {
                    if(status_next == target) return step + 1;
                    q.emplace(status_next, step + 1);
                    seen.insert(status_next);
                }
            }
        }
        return -1;  //无解
    }
private:
    vector<string> get_next(string& status) {	// 枚举 status 通过一次旋转得到的数字
        vector<string> ret;
        auto num_prev = [](char x) -> char {
            return (x == '0' ? '9' : x - 1);
        };
        auto num_succ = [](char x) -> char {
            return (x == '9' ? '0' : x + 1);
        };
        for (int i = 0; i < 4; ++i) {
            char num = status[i];
            status[i] = num_prev(num);
            ret.push_back(status);
            status[i] = num_succ(num);
            ret.push_back(status);
            status[i] = num;    //每一次循环需要还原status
        }
        return ret;
    };
};
```

此类问题，通常我们会使用「BFS」求解，但朴素的 BFS 通常会带来搜索空间爆炸问题。可以考虑双向BFS解法。

<img src="https://pic.leetcode-cn.com/38dc5897de2b554ea606a92c5eada14b0e0030195334e9fd65943ed6d0f77c1d-image.png" alt="image.png" style="zoom:67%;" />

#### 4.单词接龙

[127. 单词接龙 - 力扣（Leetcode）](https://leetcode.cn/problems/word-ladder/description/)

字典 `wordList` 中从单词 `beginWord` 和 `endWord` 的 **转换序列** 是一个按下述规格形成的序列 `beginWord -> s1 -> s2 -> ... -> sk`：

- 每一对相邻的单词只差一个字母。
-  对于 `1 <= i <= k` 时，每个 `si` 都在 `wordList` 中。注意， `beginWord` 不需要在 `wordList` 中。
- `sk == endWord`

给你两个单词 `beginWord` 和 `endWord` 和一个字典 `wordList` ，返回 *从 `beginWord` 到 `endWord` 的 **最短转换序列** 中的 **单词数目*** 。如果不存在这样的转换序列，返回 `0` 。

这题和上面的一题很相似。可以采用BFS、双向BFS的方法。

**BFS**

```c++
class Solution {
public:
    int ladderLength(string beginWord, string endWord, vector<string>& wordList) {
        unordered_set<string> word_set(wordList.begin(), wordList.end());
        if(!word_set.count(endWord)) return 0;
        int step = 1;
        unordered_set<string> visit;	//visit集合其实可以省略，访问过的单词直接从wordlist中去掉
        int len = beginWord.length();
        queue<string> q;
        q.push(beginWord);
        visit.insert(beginWord);
        while(!q.empty()) {
            int q_size = q.size();  
            for(int i = 0; i < q_size; i++) {   //q_size:当前层队列中元素个数
                string cur_word = q.front(); q.pop();
                for(int j = 0; j < len; j++) {  //遍历cur_word的每一个字母
                    char original = cur_word[j]; //保存cur_word[j]原始字符,用于恢复cur_word
                    for(char k = 'a'; k <= 'z'; k++) { //每一个字母的25种改变可能
                        if(k == original) continue;   //跳过cur_word本身
                        cur_word[j] = k;
                        if(word_set.count(cur_word) && !visit.count(cur_word)) {    //未访问过,且在wordlist中
                            if(cur_word == endWord) return step + 1;
                            else {
                                q.push(cur_word);
                                visit.insert(cur_word);
                            }
                        }
                    }
                    cur_word[j] = original;  //还原cur_word
                }
            }
            step++; //遍历一层，则步数加一
        }
        return 0;
    }
};
```

**双向BFS**

```c++
class Solution {
public:
    int ladderLength(string beginWord, string endWord, vector<string>& wordList) {
        //第一步：将wordList放入哈希表中，便于判断某个单词是否在wordList中
        unordered_set<string>wordSet(wordList.begin(),wordList.end());
        if(wordSet.size()==0||wordSet.find(endWord)==wordSet.end()) return 0;

        //分别用左边和右边扩散的哈希表代表单向BFS里的队列，他们在双向BFS中交替使用
        unordered_set<string>beginVisited;
        beginVisited.insert(beginWord);
        unordered_set<string>endVisited;
        endVisited.insert(endWord);

        //第二步：执行双向BFS，左右交替扩散的步数之和为所求
        int step=1;
        while(!beginVisited.empty()&&!endVisited.empty()){
            //优先选择小的哈希表进行扩散，考虑的情况更少
            if(beginVisited.size()>endVisited.size()){
                unordered_set<string>tmp=beginVisited;
                beginVisited=endVisited;
                endVisited=tmp;
            }
            //保证beginVisited是相对较小的集合，nextLevelVisited在扩散完成以后，会成为新的beginVisited
            unordered_set<string>nextLevelVisited;
            for(string word:beginVisited){
                for(int i=0;i<word.size();++i){
                    string str=word;
                    for(char c='a';c<='z';++c){
                        if(str[i]==c) continue;
                        str[i]=c;
                        //在另外一边的BFS中汇合
                        if(endVisited.find(str)!=endVisited.end()){
                            return step+1;
                        }
                        //字符变化后在wordList中
                        if(wordSet.find(str)!=wordSet.end()){
                            nextLevelVisited.insert(str);
                            wordSet.erase(str);
                        }
                    }
                }
            }
            beginVisited=nextLevelVisited;
            step++;
        }
        return 0;
    }
};
```

#### 5.完全平方数

[279. 完全平方数 - 力扣（Leetcode）](https://leetcode.cn/problems/perfect-squares/description/)

给你一个整数 `n` ，返回 *和为 `n` 的完全平方数的最少数量* 。

**完全平方数** 是一个整数，其值等于另一个整数的平方；换句话说，其值等于一个整数自乘的积。例如，`1`、`4`、`9` 和 `16` 都是完全平方数，而 `3` 和 `11` 不是。

 

**示例 1：**

```
输入：n = 12
输出：3 
解释：12 = 4 + 4 + 4
```

**示例 2：**

```
输入：n = 13
输出：2
解释：13 = 4 + 9
```

**BFS解法**
这题让求的是若干个平方数的和等于n，并且平方数的个数最少。首先我们可以把它想象成为一颗m叉树，树的每一个节点的值都是平方数的和，如下图所示。

每一个节点的值都是从根节点到当前节点的累加。而**平方数的个数其实就是遍历到第几层的时候累加和等于target**。我们只需要一层一层的遍历，也就是常说的BFS，当遇到累加的和等于target的时候直接返回当前的层数即可。

<img src="https://pic.leetcode-cn.com/1616821848-TlFxpv-image.png" alt="image.png" style="zoom:50%;" />

```c++
class Solution {
public:
    int numSquares(int n) {
        int new_root = n,step = 0;
        queue<pair<int, int>> q;
        q.push({new_root, step});
        while(!q.empty()){
            auto [new_root, step] = q.front();
            q.pop();
            step++;
            for(int i=0; i*i<=new_root; i++){
                int temp = new_root - i * i;
                if(temp == 0) return step;	// step返回当前的层数
                q.push({temp,step});
            }
        }
        return 0;
    }
};
```

### 三、栈：后入先出的数据结构

#### 1.最小栈

[155. 最小栈 - 力扣（Leetcode）](https://leetcode.cn/problems/min-stack/description/)

设计一个支持 `push` ，`pop` ，`top` 操作，并能在常数时间内检索到最小元素的栈。

实现 `MinStack` 类:

- `MinStack()` 初始化堆栈对象。
- `void push(int val)` 将元素val推入堆栈。
- `void pop()` 删除堆栈顶部的元素。
- `int top()` 获取堆栈顶部的元素。
- `int getMin()` 获取堆栈中的最小元素。

**示例 1:**

```
输入：
["MinStack","push","push","push","getMin","pop","top","getMin"]
[[],[-2],[0],[-3],[],[],[],[]]

输出：
[null,null,null,null,-3,null,0,-2]

解释：
MinStack minStack = new MinStack();
minStack.push(-2);
minStack.push(0);
minStack.push(-3);
minStack.getMin();   --> 返回 -3.
minStack.pop();
minStack.top();      --> 返回 0.
minStack.getMin();   --> 返回 -2.
```

**辅助栈**:

<img src="https://pic.leetcode-cn.com/28724fa9f92b6952f7fdaf8760edd1dea850b137c22df28751f1cdd4d2680992-155.gif" alt="155.gif" style="zoom: 33%;" />

```c++
class MinStack {
public:
    MinStack() {

    }
    stack<int> stk0;
    stack<int> stk1;    
    void push(int val) {
        stk0.push(val);
        if(stk1.empty() || val <= getMin()) {   //min辅助栈为空或有新的最小值时，辅助栈压栈
            stk1.push(val);
        }
    }
    void pop() {
        if(stk0.top() == getMin()) {    //stk1中出栈的栈顶元素是最小值
            stk1.pop();
        }
        stk0.pop();
    }
    int top() {
        return stk0.top();
    }
    int getMin() {
        return stk1.top();
    }
};
```

#### 2.有效括号

[20. 有效的括号 - 力扣（Leetcode）](https://leetcode.cn/problems/valid-parentheses/description/)

给定一个只包括 `'('`，`')'`，`'{'`，`'}'`，`'['`，`']'` 的字符串 `s` ，判断字符串是否有效。

有效字符串需满足：

1. 左括号必须用相同类型的右括号闭合。
2. 左括号必须以正确的顺序闭合。
3. 每个右括号都有一个对应的相同类型的左括号。

**示例 1：**

```
输入：s = "()"
输出：true
```

**示例 2：**

```
输入：s = "()[]{}"
输出：true
```

**示例 3：**

```
输入：s = "(]"
输出：false
```

```c++
class Solution {
public:
    bool isValid(string s) {
        stack<char> stk;
        for(int i = 0; i < s.length(); i++) {
            if(!stk.empty()) {
                if(stk.top() == '(' && s[i] == ')') stk.pop();	//满足出栈的三种情况
                else if(stk.top() == '[' && s[i] == ']') stk.pop();
                else if(stk.top() == '{' && s[i] == '}') stk.pop();
                else stk.push(s[i]);	//否则继续入栈
            } else {
                stk.push(s[i]);
            }
        }
    return stk.empty();
    }
};
```

#### 3.每日温度

[739. 每日温度 - 力扣（Leetcode）](https://leetcode.cn/problems/daily-temperatures/description/)

给定一个整数数组 `temperatures` ，表示每天的温度，返回一个数组 `answer` ，其中 `answer[i]` 是指对于第 `i` 天，下一个更高温度出现在几天后。如果气温在这之后都不会升高，请在该位置用 `0` 来代替。

**示例 1:**

```
输入: temperatures = [73,74,75,71,69,72,76,73]
输出: [1,1,4,2,1,1,0,0]
```

暴力解法会超时，**这里使用单调栈的方法:**参考题解 [739. 每日温度 - 力扣（Leetcode）](https://leetcode.cn/problems/daily-temperatures/solutions/71433/leetcode-tu-jie-739mei-ri-wen-du-by-misterbooo/)

<img src="E:\Postgraduate\Study\Leetcode\images\每日温度.png" alt="每日温度"  />

```c++
class Solution {
public:
    vector<int> dailyTemperatures(vector<int>& temperatures) {
        int n = temperatures.size();
        vector<int> ret(n, 0);
        stack<int> stk; //构造温度递减栈(栈内的温度从栈顶依次递减)
        for(int i = 0; i < n; i++) {
            while(!stk.empty() && temperatures[i] > temperatures[stk.top()]) {  //当前温度大于栈顶温度
                int t = stk.top(); stk.pop();   //栈顶t:对应temperatures的索引
                ret[t] = i - t;
            }
            stk.push(i);    //更新栈顶(最大温度temperatures的索引)
        }
        return ret;
    }
};
```

#### 4.逆波兰表达式

[150. 逆波兰表达式求值 - 力扣（Leetcode）](https://leetcode.cn/problems/evaluate-reverse-polish-notation/description/)

根据逆波兰表示法，求表达式的值。

有效的算符包括 `+`、`-`、`*`、`/` 。每个运算对象可以是整数，也可以是另一个逆波兰表达式。

**注意** 两个整数之间的除法只保留整数部分。

可以保证给定的逆波兰表达式总是有效的。换句话说，表达式总会得出有效数值且不存在除数为 0 的情况。

**示例 1：**

```
输入：tokens = ["2","1","+","3","*"]
输出：9
解释：该算式转化为常见的中缀算术表达式为：((2 + 1) * 3) = 9
```

**示例 2：**

```
输入：tokens = ["4","13","5","/","+"]
输出：6
解释：该算式转化为常见的中缀算术表达式为：(4 + (13 / 5)) = 6
```



```c++
class Solution {
public:
    int evalRPN(vector<string>& tokens) {
        stack<long int> stk;
        for(int i = 0; i < tokens.size(); i++) {
            if(tokens[i] == "+" || tokens[i] == "-" || tokens[i] == "*" || tokens[i] == "/") {
                auto num1 = stk.top(); stk.pop();
                auto num2 = stk.top(); stk.pop();
                if(tokens[i] == "+") stk.push(num2 + num1);
                else if(tokens[i] == "-") stk.push(num2 - num1);
                else if(tokens[i] == "*") stk.push(num2 * num1);
                else stk.push(num2 / num1);	//tokens[i]是操作符，则弹出两个操作数并将计算结果压入栈中
            } else {
                stk.push(atoi(tokens[i].c_str()));	//tokens[i]是操作数，压栈  (string->char->int)
            }
        }
        return stk.top();
    }
};
```



### 四、栈和DFS

与 BFS 类似，`深度优先搜索`（DFS）也可用于查找从根结点到目标结点的路径。在本文中，我们提供了示例来解释 DFS 是如何工作的以及栈是如何逐步帮助 DFS 工作的。


示例：来看一个例子吧。我们希望通过 DFS 找出从根结点 `A` 到目标结点 `G` 的路径。见ppt.  [栈和DFS](https://leetcode.cn/leetbook/read/queue-stack/gro21/)

#### 1.岛屿数量





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

## 回溯算法

### 一、回溯理论基础

#### 什么是回溯法

回溯法也可以叫做回溯搜索法，它是一种搜索的方式。

在二叉树系列中，我们已经不止一次，提到了回溯，例如[二叉树：以为使用了递归，其实还隐藏着回溯](https://programmercarl.com/二叉树中递归带着回溯.html)。

回溯是递归的副产品，只要有递归就会有回溯。

**所以以下讲解中，回溯函数也就是递归函数，指的都是一个函数**。

#### 回溯法的效率

回溯法的性能如何呢，这里要和大家说清楚了，**虽然回溯法很难，很不好理解，但是回溯法并不是什么高效的算法**。

**因为回溯的本质是穷举，穷举所有可能，然后选出我们想要的答案**，如果想让回溯法高效一些，可以加一些剪枝的操作，但也改不了回溯法就是穷举的本质。

那么既然回溯法并不高效为什么还要用它呢？

因为没得选，**一些问题能暴力搜出来就不错了，撑死了再剪枝一下，还没有更高效的解法**。

此时大家应该好奇了，都什么问题，这么牛逼，只能暴力搜索。

#### 回溯法解决的问题

回溯法，一般可以解决如下几种问题：

- 组合问题：N个数里面按一定规则找出k个数的集合
- 切割问题：一个字符串按一定规则有几种切割方式
- 子集问题：一个N个数的集合里有多少符合条件的子集
- 排列问题：N个数按一定规则全排列，有几种排列方式
- 棋盘问题：N皇后，解数独等等

**相信大家看着这些之后会发现，每个问题，都不简单！**

#### 如何理解回溯法

**回溯法解决的问题都可以抽象为树形结构**，是的，指的是所有回溯法的问题都可以抽象为树形结构！

因为回溯法解决的都是在集合中递归查找子集，**集合的大小就构成了树的宽度，递归的深度，都构成的树的深度**。

递归就要有终止条件，所以必然是一棵高度有限的树（N叉树）。

#### 回溯法模板

**回溯三部曲**

- 回溯函数模板返回值以及参数

在回溯算法中，我的习惯是函数起名字为backtracking，这个起名大家随意。

回溯算法中函数返回值一般为void。

再来看一下参数，因为回溯算法需要的参数可不像二叉树递归的时候那么容易一次性确定下来，所以一般是先写逻辑，然后需要什么参数，就填什么参数。

但后面的回溯题目的讲解中，为了方便大家理解，我在一开始就帮大家把参数确定下来。

回溯函数伪代码如下：

```text
void backtracking(参数)
```

- 回溯函数终止条件

既然是树形结构，那么我们在讲解 二叉树的递归 的时候，就知道遍历树形结构一定要有终止条件。

所以**回溯也有要终止条件**。

什么时候达到了终止条件，树中就可以看出，一般来说搜到叶子节点了，也就找到了满足条件的一条答案，把这个答案存放起来，并结束本层递归。

所以回溯函数终止条件伪代码如下：

```c++
if (终止条件) {
    存放结果;
    return;
}
```

- 回溯搜索的遍历过程

在上面我们提到了，回溯法一般是在集合中递归搜索，集合的大小构成了树的宽度，递归的深度构成的树的深度。

如图：

<img src="https://img-blog.csdnimg.cn/20210130173631174.png" alt="回溯算法理论基础" style="zoom: 50%;" />

注意图中，特意举例集合大小和孩子的数量是相等的！

回溯函数遍历过程伪代码如下：

```c++
for (选择：本层集合中元素（树中节点孩子的数量就是集合的大小）) {
    处理节点;
    backtracking(路径，选择列表); // 递归
    回溯，撤销处理结果
}
```

for循环就是遍历集合区间，可以理解一个节点有多少个孩子，这个for循环就执行多少次。

backtracking这里自己调用自己，实现递归。

大家可以从图中看出**for循环可以理解是横向遍历，backtracking（递归）就是纵向遍历**，这样就把这棵树全遍历完了，一般来说，搜索叶子节点就是找的其中一个结果了。

分析完过程，回溯算法模板框架如下：

```c++
void backtracking(参数) {
    if (终止条件) {
        存放结果;
        return;
    }

    for (选择：本层集合中元素（树中节点孩子的数量就是集合的大小）) {
        处理节点;
        backtracking(路径，选择列表); // 递归
        回溯，撤销处理结果
    }
}
```

**这份模板很重要，后面做回溯法的题目都靠它了！**

#### 总结

本篇讲解了，什么是回溯算法，知道了回溯和递归是相辅相成的。

接着提到了回溯法的效率，回溯法其实就是暴力查找，并不是什么高效的算法。

然后列出了回溯法可以解决几类问题，可以看出每一类问题都不简单。

最后我们讲到回溯法解决的问题都可以**抽象为树形结构（N叉树）**，并给出了回溯法的模板。

### 二、组合

[77. 组合 - 力扣（Leetcode）](https://leetcode.cn/problems/combinations/description/)

给定两个整数 `n` 和 `k`，返回范围 `[1, n]` 中所有可能的 `k` 个数的组合。

你可以按 **任何顺序** 返回答案。

**示例 1：**

```
输入：n = 4, k = 2
输出：
[
  [2,4],
  [3,4],
  [2,3],
  [1,2],
  [1,3],
  [1,4],
]
```

**提示：**

- `1 <= n <= 20`
- `1 <= k <= n`

**思路**

把组合问题抽象为如下树形结构：

<img src="https://img-blog.csdnimg.cn/20201123195223940.png" alt="77.组合" style="zoom:50%;" />

可以看出这个棵树，一开始集合是 1，2，3，4， 从左向右取数，取过的数，不在重复取。

第一次取1，集合变为2，3，4 ，因为k为2，我们只需要再取一个数就可以了，分别取2，3，4，得到集合[1,2] [1,3] [1,4]，以此类推。

**每次从集合中选取元素，可选择的范围随着选择的进行而收缩，调整可选择的范围**。

**图中可以发现n相当于树的宽度，k相当于树的深度**。

那么如何在这个树上遍历，然后收集到我们要的结果集呢？

**图中每次搜索到了叶子节点，我们就找到了一个结果**。

相当于只需要把达到叶子节点的结果收集起来，就可以求得 n个数中k个数的组合集合。



#### **回溯三部曲**

- 递归函数的返回值以及参数

在这里要定义两个全局变量，一个用来存放符合条件单一结果，一个用来存放符合条件结果的集合。

代码如下：

```cpp
vector<vector<int>> result; // 存放符合条件结果的集合
vector<int> path; // 用来存放符合条件结果
```

其实不定义这两个全局变量也是可以的，把这两个变量放进递归函数的参数里，但函数里参数太多影响可读性，所以我定义全局变量了。

函数里一定有两个参数，既然是集合n里面取k的数，那么n和k是两个int型的参数。

然后还需要一个参数，为int型变量startIndex，这个参数用来记录本层递归的中，集合从哪里开始遍历（集合就是[1,...,n] ）。**startIndex 就是防止出现重复的组合。**

从下图中红线部分可以看出，在集合[1,2,3,4]取1之后，下一层递归，就要在[2,3,4]中取数了，那么下一层递归如何知道从[2,3,4]中取数呢，靠的就是startIndex。

<img src="https://img-blog.csdnimg.cn/20201123195328976.png" alt="77.组合2" style="zoom:50%;" />

所以需要startIndex来记录下一层递归，搜索的起始位置。那么整体代码如下：

```cpp
vector<vector<int>> result; // 存放符合条件结果的集合
vector<int> path; // 用来存放符合条件单一结果
void backtracking(int n, int k, int startIndex) 
```

- 回溯函数终止条件

什么时候到达所谓的叶子节点了呢？

path这个数组的大小如果达到k，说明我们找到了一个子集大小为k的组合了，在图中path存的就是根节点到叶子节点的路径。

如图红色部分：

<img src="https://img-blog.csdnimg.cn/20201123195407907.png" alt="77.组合3" style="zoom:50%;" />

此时用result二维数组，把path保存起来，并终止本层递归。

所以终止条件代码如下：

```cpp
if (path.size() == k) {
    result.push_back(path);
    return;
}
```

- 单层搜索的过程

回溯法的搜索过程就是一个树型结构的遍历过程，在如下图中，可以看出for循环用来横向遍历，递归的过程是纵向遍历。

<img src="https://img-blog.csdnimg.cn/20201123195242899.png" alt="77.组合1" style="zoom:50%;" />

如此我们才遍历完图中的这棵树。

for循环每次从startIndex开始遍历，然后用path保存取到的节点i。

代码如下：

```cpp
for (int i = startIndex; i <= n; i++) { // 控制树的横向遍历
    path.push_back(i); // 处理节点 
    backtracking(n, k, i + 1); // 递归：控制树的纵向遍历，注意下一层搜索要从i+1开始
    path.pop_back(); // 回溯，撤销处理的节点
}
```

可以看出backtracking（递归函数）通过不断调用自己一直往深处遍历，总会遇到叶子节点，遇到了叶子节点就要返回。

backtracking的下面部分就是**回溯**的操作了，**撤销本次处理的结果**。

完整代码：

```c++
class Solution {
private:
    vector<vector<int>> result; // 存放符合条件结果的集合
    vector<int> path; // 用来存放符合条件结果
    void backtracking(int n, int k, int startIndex) {
        if (path.size() == k) {
            result.push_back(path);
            return;
        }
        for (int i = startIndex; i <= n; i++) {
            path.push_back(i); // 处理节点 
            backtracking(n, k, i + 1); // 递归
            path.pop_back(); // 回溯，撤销处理的节点
        }
    }
public:
    vector<vector<int>> combine(int n, int k) {
        backtracking(n, k, 1);
        return result;
    }
};
```

#### 回溯剪枝优化

在遍历的过程中有如下代码：

```c++
for (int i = startIndex; i <= n; i++) {
    path.push_back(i);
    backtracking(n, k, i + 1);
    path.pop_back();
}
```

事实上，如果 `n = 7, k = 4`，从 `5` 开始搜索就已经没有意义了，这是因为：即使把 5 选上，后面的数只有 6 和 7，一共就 3 个候选数，凑不出 4 个数的组合。因此，搜索起点有上界，这个上界是多少，可以举几个例子分析。

分析搜索起点的上界，其实是在深度优先遍历的过程中剪枝，剪枝可以避免不必要的遍历，剪枝剪得好，可以大幅度节约算法的执行时间。

下面的图片绿色部分是剪掉的枝叶，当 n 很大的时候，能少遍历很多结点，节约了时间。

<img src="https://pic.leetcode-cn.com/3ddd55697423b5831cbbd42f4b901ebbade0daa456c651a70c758fe359d8a0d1-image.png" alt="77.组合4" style="zoom: 50%;" />

容易知道：搜索起点和当前还需要选几个数有关，而当前还需要选几个数与已经选了几个数有关，即与 path 的长度相关。我们举几个例子分析：

例如：n = 6 ，k = 4。

path.size() == 1 的时候，接下来要选择 3 个数，搜索起点最大是 4，最后一个被选的组合是 [4, 5, 6]；
path.size() == 2 的时候，接下来要选择 2 个数，搜索起点最大是 5，最后一个被选的组合是 [5, 6]；
path.size() == 3 的时候，接下来要选择 1 个数，搜索起点最大是 6，最后一个被选的组合是 [6]；

再如：n = 15 ，k = 4。 

path.size() == 1 的时候，接下来要选择 3 个数，搜索起点最大是 13，最后一个被选的是 [13, 14, 15]；
path.size() == 2 的时候，接下来要选择 2 个数，搜索起点最大是 14，最后一个被选的是 [14, 15]；
path.size() == 3 的时候，接下来要选择 1 个数，搜索起点最大是 15，最后一个被选的是 [15]；

可以归纳出：

`搜索起点的上界 + 接下来要选择的元素个数 - 1 = n`
其中，`接下来要选择的元素个数 = k - path.size()`，整理得到：

`搜索起点的上界 = n - (k - path.size()) + 1`
所以，我们的剪枝过程就是：把 `i <= n` 改成 `i <= n - (k - path.size()) + 1` ：

```c++
for (int i = startIndex; i <= n - (k - path.size()) + 1; i++)
```

优化后整体代码如下：

```cpp
class Solution {
private:
    vector<vector<int>> result;
    vector<int> path;
    void backtracking(int n, int k, int startIndex) {
        if (path.size() == k) {
            result.push_back(path);
            return;
        }
        for (int i = startIndex; i <= n - (k - path.size()) + 1; i++) { // 剪枝优化的地方
            path.push_back(i); // 处理节点
            backtracking(n, k, i + 1);
            path.pop_back(); // 回溯，撤销处理的节点
        }
    }
public:
    vector<vector<int>> combine(int n, int k) {
        backtracking(n, k, 1);
        return result;
    }
};
```

### 三、电话号码的字母组合

[17. 电话号码的字母组合 - 力扣（Leetcode）](https://leetcode.cn/problems/letter-combinations-of-a-phone-number/)

给定一个仅包含数字 `2-9` 的字符串，返回所有它能表示的字母组合。答案可以按 **任意顺序** 返回。

给出数字到字母的映射如下（与电话按键相同）。注意 1 不对应任何字母。

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2021/11/09/200px-telephone-keypad2svg.png)

**示例 1：**

```
输入：digits = "23"
输出：["ad","ae","af","bd","be","bf","cd","ce","cf"]
```

**提示：**

- `0 <= digits.length <= 4`
- `digits[i]` 是范围 `['2', '9']` 的一个数字。

**思路**

例如：输入："23"，抽象为树形结构，如图所示：

<img src="https://img-blog.csdnimg.cn/20201123200304469.png" alt="17. 电话号码的字母组合" style="zoom:50%;" />

图中可以看出遍历的深度，就是输入"23"的长度，而叶子节点就是我们要收集的结果，输出["ad", "ae", "af", "bd", "be", "bf", "cd", "ce", "cf"]。

回溯三部曲：

- 确定回溯函数参数

首先需要一个字符串s来收集叶子节点的结果，然后用一个字符串数组result保存起来，这两个变量我依然定义为全局。

再来看参数，参数指定是有题目中给的string digits，然后还要有一个参数就是int型的index。

注意这个index和之前组合例题中的startIndex不同了。

**这个index是记录遍历第几个数字了**，就是用来遍历digits的（题目中给出数字字符串），**同时index也表示树的深度**。

代码如下：

```cpp
vector<string> result;
string s;
void backtracking(const string& digits, int index)
```

- 确定终止条件

例如输入用例"23"，两个数字，那么根节点往下递归两层就可以了，叶子节点就是要收集的结果集。

那么终止条件就是如果index 等于 输入的数字个数（digits.size）了（本来index就是用来遍历digits的）。

然后收集结果，结束本层递归。

代码如下：

```cpp
if (index == digits.size()) {
    result.push_back(s);
    return;
}
```

- 确定单层遍历逻辑

首先要取index指向的数字，并找到对应的字符集（手机键盘的字符集）。

然后for循环来处理这个字符集，代码如下：

```cpp
int digit = digits[index] - '0';        // 将index指向的数字转为int
string letters = letterMap[digit];      // 取数字对应的字符集
for (int i = 0; i < letters.size(); i++) {
    s.push_back(letters[i]);            // 处理
    backtracking(digits, index + 1);    // 递归，注意index+1，一下层要处理下一个数字了
    s.pop_back();                       // 回溯
}
```

**注意这里for循环，可不像是在组合问题中从startIndex开始遍历的**。

**因为本题每一个数字代表的是不同集合，也就是求不同集合之间的组合，而[77. 组合](https://programmercarl.com/0077.组合.html)是求同一个集合中的组合！**

完整代码：

```c++
class Solution {
public:
    vector<string> result;
    string s;
    vector<string> letterCombinations(string digits) {
        if(digits.empty()) return result;
        backtracking(digits, 0);
        return result;
    }
private:
    void backtracking(string digits, int index) {   //index记录处理第几个数字
        if(index == digits.size()) {
            result.push_back(s);
            return;
        }
        int num = digits[index] - '0';  //转换为整数
        string digit_str = letterMap[num];
        for(int i = 0; i < digit_str.size(); i++) {
            s.push_back(digit_str[i]);	// 处理
            backtracking(digits, index + 1);	// 递归，注意index+1，一下层要处理下一个数字了
            s.pop_back();		// 回溯
        }
    }    
    const vector<string> letterMap= {
        "", // 0
        "", // 1
        "abc", // 2
        "def", // 3
        "ghi", // 4
        "jkl", // 5
        "mno", // 6
        "pqrs", // 7
        "tuv", // 8
        "wxyz", // 9
    };
};
```

### 四、组合总和

[39. 组合总和 - 力扣（Leetcode）](https://leetcode.cn/problems/combination-sum/description/)

给你一个 **无重复元素** 的整数数组 `candidates` 和一个目标整数 `target` ，找出 `candidates` 中可以使数字和为目标数 `target` 的 所有 **不同组合** ，并以列表形式返回。你可以按 **任意顺序** 返回这些组合。

`candidates` 中的 **同一个** 数字可以 **无限制重复被选取** 。如果至少一个数字的被选数量不同，则两种组合是不同的。 

对于给定的输入，保证和为 `target` 的不同组合数少于 `150` 个。

**示例 1：**

```
输入：candidates = [2,3,6,7], target = 7
输出：[[2,2,3],[7]]
解释：
2 和 3 可以形成一组候选，2 + 2 + 3 = 7 。注意 2 可以使用多次。
7 也是一个候选， 7 = 7 。
仅有这两种组合。
```

**示例 2：**

```
输入: candidates = [2,3,5], target = 8
输出: [[2,2,2,2],[2,3,3],[3,5]]
```

本题和[77.组合](https://programmercarl.com/0077.组合.html)，[216.组合总和III ](https://programmercarl.com/0216.组合总和III.html)和区别是：**本题没有数量要求，可以无限重复，但是有总和的限制**，所以间接的也是有个数的限制。

本题搜索的过程抽象成树形结构如下：

<img src="https://img-blog.csdnimg.cn/20201223170730367.png" alt="39.组合总和" style="zoom: 50%;" /> 

注意图中叶子节点的返回条件，因为本题没有组合数量要求，仅仅是总和的限制，所以递归没有层数的限制，只要选取的元素总和超过target，就返回！

而在[77.组合](https://programmercarl.com/0077.组合.html)和[216.组合总和III](https://programmercarl.com/0216.组合总和III.html)中都可以知道要递归K层，因为要取k个元素的组合。

**回溯三部曲**

- 递归函数参数

这里依然是定义两个全局变量，二维数组result存放结果集，数组path存放符合条件的结果。（这两个变量可以作为函数参数传入）

首先是题目中给出的参数，集合candidates, 和目标值target。

此外我还定义了int型的sum变量来统计单一结果path里的总和，其实这个sum也可以不用，用target做相应的减法就可以了，最后如何target==0就说明找到符合的结果了，但为了代码逻辑清晰，我依然用了sum。

**本题还需要startIndex来控制for循环的起始位置，对于组合问题，什么时候需要startIndex呢？**

我举过例子，如果是一个集合来求组合的话，就需要startIndex，例如：[77.组合 (opens new window)](https://programmercarl.com/0077.组合.html)，[216.组合总和III (opens new window)](https://programmercarl.com/0216.组合总和III.html)。

如果是多个集合取组合，各个集合之间相互不影响，那么就不用startIndex，例如：[17.电话号码的字母组合(opens new window)](https://programmercarl.com/0017.电话号码的字母组合.html)

**注意以上我只是说求组合的情况，如果是排列问题，又是另一套分析的套路，后面我再讲解排列的时候就重点介绍**。

代码如下：

```cpp
vector<vector<int>> result;
vector<int> path;
void backtracking(vector<int>& candidates, int target, int sum, int startIndex)
```

- 递归终止条件

从叶子节点可以清晰看到，终止只有两种情况，sum大于target和sum等于target。

sum等于target的时候，需要收集结果，代码如下：

```cpp
if (sum > target) {
    return;
}
if (sum == target) {
    result.push_back(path);
    return;
}
```

- 单层搜索的逻辑

单层for循环依然是从startIndex开始，搜索candidates集合。

**注意本题和[77.组合 (opens new window)](https://programmercarl.com/0077.组合.html)、[216.组合总和III (opens new window)](https://programmercarl.com/0216.组合总和III.html)的一个区别是：本题元素为可重复选取的**。

如何重复选取呢，看代码，注释部分：

```cpp
for (int i = startIndex; i < candidates.size(); i++) {
    sum += candidates[i];
    path.push_back(candidates[i]);
    backtracking(candidates, target, sum, i); // 关键点:不用i+1了，表示可以重复读取当前的数
    sum -= candidates[i];   // 回溯
    path.pop_back();        // 回溯
}
```

C++完整代码：

```c++
class Solution {
public:
    vector<vector<int>> result;
    vector<int> path;
    vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
        backtracking(candidates, target, 0, 0);
        return result;
    }
private:
    void backtracking(vector<int>& candidates, int target, int sum, int startIndex) {
        if(sum > target) return ;
        if(sum == target) {
            result.push_back(path);
            return ;
        }
        for(int i = startIndex; i < candidates.size(); i++) {
            path.push_back(candidates[i]);
            sum += candidates[i];
            // backtracking(candidates, target, sum);//例如:[2,4,3] 7; 这么写会出现[4,3],[3,4]的重复情况
            backtracking(candidates, target, sum, i);//这么写保证了下一个数的索引不小于当前索引,则不会出现[3,4];此处的i可以不用+1,因为同一数字可用多次
            sum -= candidates[i];
            path.pop_back();
        }
    }
};
```

**优化剪枝：**

在这个树形结构中：

<img src="https://img-blog.csdnimg.cn/20201223170730367.png" alt="39.组合总和" style="zoom:50%;" />

以及上面的版本一的代码大家可以看到，对于sum已经大于target的情况，其实是依然进入了下一层递归，只是下一层递归结束判断的时候，会判断sum > target的话就返回。

其实**如果已经知道下一层的sum会大于target，就没有必要进入下一层递归了。**

那么可以在for循环的搜索范围上做做文章了。

**对总集合排序之后，如果下一层的sum（就是本层的 sum + candidates[i]）已经大于target，就可以结束本轮for循环的遍历**。

如图：

<img src="https://img-blog.csdnimg.cn/20201223170809182.png" alt="39.组合总和1" style="zoom:50%;" />

```c++
class Solution {
public:
    vector<vector<int>> result;
    vector<int> path;
    vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
        sort(candidates.begin(), candidates.end()); // 优化剪枝需要排序
        backtracking(candidates, target, 0, 0);
        return result;
    }
private:
    void backtracking(vector<int>& candidates, int target, int sum, int startIndex) {
        // if(sum > target) return ;	//这种剪枝相比下面的剪枝会多进入一次递归
        if(sum == target) {
            result.push_back(path);
            return ;
        }
        for(int i = startIndex; i < candidates.size() && sum + candidates[i] <= target; i++) {
            path.push_back(candidates[i]);
            sum += candidates[i];
            // backtracking(candidates, target, sum);// 这么写会出现[3,4],[4,3]的情况
            backtracking(candidates, target, sum, i);// 这么写保证下一个数不小于当前数,则不会出现[4,3];此处的i可以不用+1,因为前后的数字可以重复
            sum -= candidates[i];
            path.pop_back();
        }
    }
};
```

### 五、组合总和II

[40. 组合总和 II - 力扣（Leetcode）](https://leetcode.cn/problems/combination-sum-ii/)

给定一个候选人编号的集合 `candidates` 和一个目标数 `target` ，找出 `candidates` 中所有可以使数字和为 `target` 的组合。

`candidates` 中的每个数字在每个组合中只能使用 **一次** 。

**注意：**解集不能包含重复的组合。 

**示例 1:**

```
输入: candidates = [10,1,2,7,6,1,5], target = 8,
输出:
[
[1,1,6],
[1,2,5],
[1,7],
[2,6]
]
```

与上题区别：

- 每个数字在每个组合中只能使用 **一次**
- 本题数组**candidates的元素是有重复的**

**思路**

元素在同一个组合内是可以重复的，但两个组合不能相同。

**所以我们要去重的是同一树层上的“使用过”，同一树枝上的都是一个组合里的元素，不用去重**。

**强调一下，树层去重的话，需要对数组排序！**

选择过程树形结构如图所示：

<img src="E:\Postgraduate\Study\Leetcode\images\1599718525-iXEiiy-image.png" alt="1599718525-iXEiiy-image" style="zoom: 50%;" />

完整代码：

```c++
class Solution {
public:
    vector<vector<int>> result;
    vector<int> path;
    vector<vector<int>> combinationSum2(vector<int>& candidates, int target) {
        sort(candidates.begin(), candidates.end());
        backtracking(candidates, target, 0, 0);
        return result;
    }
private:
    void backtracking(vector<int>& candidates, int target, int sum, int startIndex) {
        if(sum == target) {
            result.push_back(path);
            return ;
        }
        for(int cur = startIndex; cur < candidates.size() && sum + candidates[cur] <= target; cur++) {
            //当candidates = [1,1,2,5,6,7,10],candidates有两个1,会出现重复现象
            //[[1,1,6],[1,2,5],[1,7],[1,2,5],[1,7],[2,6]] 有重复集合
            //[[1,1,6],[1,2,5],[1,7],[2,6]] 正确
            // 对树的同一层使用过的元素进行跳过
            if(cur > startIndex && candidates[cur - 1] == candidates[cur]) {
                continue;
            }
            path.push_back(candidates[cur]);
            sum += candidates[cur];
            backtracking(candidates, target, sum, cur + 1);//因为每个数字在组合中只能使用一次，所以需要i+1
            sum -= candidates[cur];
            path.pop_back();
        }
    }
};
```

**理解去重的精髓**

参考[40. 组合总和 II 评论区大佬发言](https://leetcode.cn/problems/combination-sum-ii/solutions/14753/hui-su-suan-fa-jian-zhi-python-dai-ma-java-dai-m-3/)

```c++
解释语句: if(cur > startIndex && candidates[cur-1] == candidates[cur]) 是如何避免重复的。
这个避免重复当思想是在是太重要了。
这个方法最重要的作用是，可以让同一层级，不出现相同的元素。即
                  1
                 / \
                2   2  这种情况不会发生 但是却允许了不同层级之间的重复即：
               /     \
              5       5
                例2
                  1
                 /
                2      这种情况确是允许的
               /
              2  
                
为何会有这种神奇的效果呢？
首先 cur-1 == cur 是用于判定当前元素是否和之前元素相同的语句。这个语句就能砍掉例1。
可是问题来了，如果把所有当前与之前一个元素相同的都砍掉，那么例二的情况也会消失。 
因为当第二个2出现的时候，他就和前一个2相同了。
                
那么如何保留例2呢？
那么就用cur > startIndex 来避免这种情况，你发现例1中的两个2是处在同一个层级上的，例2的两个2是处在不同层级上的。
在一个for循环中，所有被遍历到的数都是属于一个层级的。我们要让一个层级中，
必须出现且只出现一个2，那么就放过第一个出现重复的2，但不放过后面出现的2。
一个层级中第一个出现的2的特点就是 cur == startIndex. 第二个出现的2特点是cur > startIndex.
```

**总结**：

- 树层去重的话，需要对数组排序

- 理解本题在同层间去重，不同层不去重的精髓

- 如果是一个集合来求组合的话，就需要startIndex，例如：[77.组合 ]，[216.组合总和III]。

  如果是多个集合取组合，各个集合之间相互不影响，那么就不用startIndex，例如：[17.电话号码的字母组合]

### 六、组合总和III

[216. 组合总和 III - 力扣（Leetcode）](https://leetcode.cn/problems/combination-sum-iii/description/)

找出所有相加之和为 `n` 的 `k` 个数的组合，且满足下列条件：

- 只使用数字1到9
- 每个数字 **最多使用一次** 

返回 *所有可能的有效组合的列表* 。该列表不能包含相同的组合两次，组合可以以任何顺序返回。

**示例 1:**

```
输入: k = 3, n = 7
输出: [[1,2,4]]
解释:
1 + 2 + 4 = 7
没有其他符合的组合了。
```



与前面两题的区别

- 组合 **长度限制为 k**
- 组合中每个数字 **最多使用一次** 



**思路**

本题k相当于了树的深度，9（因为整个集合就是9个数）就是树的宽度。

例如 k = 2，n = 4的话，就是在集合[1,2,3,4,5,6,7,8,9]中求 k（个数） = 2, n（和） = 4的组合。

选取过程如图：

<img src="https://img-blog.csdnimg.cn/20201123195717975.png" alt="216.组合总和III" style="zoom: 50%;" />

图中，可以看出，只有最后取到集合（1，3）和为4 符合条件。

完整代码：

```c++
class Solution {
public:
    vector<int> path;
    vector<vector<int>> result;
    vector<vector<int>> combinationSum3(int k, int n) {
        backtracking(k, n, 1);
        return result;
    }
private:
    void backtracking(int k, int n, int startIndex) {
        if(path.size() == k) {
            if(accumulate(path.begin(), path.end(), 0) == n) {
                result.push_back(path);
            }
            return ;
        }
        for(int i = startIndex; i <= 9; i++) {
            path.push_back(i);
            backtracking(k, n, i + 1);
            path.pop_back();
        }
    }
};
```

可以做剪枝：

```c++
//剪枝
class Solution {
public:
    vector<int> path;
    vector<vector<int>> result;
    vector<vector<int>> combinationSum3(int k, int n) {
        backtracking(k, n, 0, 1);
        return result;
    }
private:
    void backtracking(int k, int target, int sum, int startIndex) {
        if(sum > target) return ; // 剪枝操作sum > target时，直接返回
        if(path.size() == k) {
            if(sum == target) {
                result.push_back(path);
            }
            return ;
        }
        for(int i = startIndex; i <= 9 - (k - path.size()) + 1; i++) {  //for循环剪枝
            path.push_back(i);
            sum += i;
            backtracking(k, target, sum, i + 1);
            path.pop_back();
            sum -= i;		//处理过程 和 回溯过程是一一对应的，处理有加，回溯就要有减！//
        }
    }
};
```

### 七、分割回文串

[131. 分割回文串 - 力扣（Leetcode）](https://leetcode.cn/problems/palindrome-partitioning/)

给你一个字符串 `s`，请你将 `s` 分割成一些子串，使每个子串都是 **回文串** 。返回 `s` 所有可能的分割方案。

**回文串** 是正着读和反着读都一样的字符串。

**示例 1：**

```
输入：s = "aab"
输出：[["a","a","b"],["aa","b"]]
```

**示例 2：**

```
输入：s = "a"
输出：[["a"]]
```

**提示：**

- `1 <= s.length <= 16`
- `s` 仅由小写英文字母组成



**思路**

**其实切割问题类似组合问题**。

例如对于字符串abcdef：

- 组合问题：选取一个a之后，在bcdef中再去选取第二个，选取b之后在cdef中在选组第三个.....。
- 切割问题：切割一个a之后，在bcdef中再去切割第二段，切割b之后在cdef中在切割第三段.....。

所以切割问题，也可以抽象为一棵树形结构，如图：

<img src="https://code-thinking.cdn.bcebos.com/pics/131.%E5%88%86%E5%89%B2%E5%9B%9E%E6%96%87%E4%B8%B2.jpg" alt="131.分割回文串" style="zoom:50%;" />

递归用来纵向遍历，for循环用来横向遍历，切割线（就是图中的红线）切割到字符串的结尾位置，说明找到了一个切割方法。

此时可以发现，切割问题的回溯搜索的过程和组合问题的回溯搜索的过程是差不多的。



**回溯三部曲**

- 递归函数参数

全局变量数组path存放切割后回文的子串，二维数组result存放结果集。 （这两个参数可以放到函数参数里）

本题递归函数参数还需要startIndex，因为切割过的地方，不能重复切割，和组合问题也是保持一致的。

代码如下：

```cpp
vector<vector<string>> result;
vector<string> path; // 放已经回文的子串
void backtracking (const string& s, int startIndex)
```

- 递归函数终止条件

从树形结构的图中可以看出：切割线切到了字符串最后面，说明找到了一种切割方法，此时就是本层递归的终止终止条件。

```cpp
void backtracking (const string& s, int startIndex) {
    // 如果起始位置已经等于s的大小，说明已经找到了一组分割方案了
    if (startIndex == s.size()) {
        result.push_back(path);
        return;
    }
}
```

- 单层搜索的逻辑

**来看看在递归循环，中如何截取子串呢？**

在`for (int i = startIndex; i < s.size(); i++)`循环中，我们 定义了起始位置`startIndex`，那么 `[startIndex, i]` 就是要截取的子串。

首先判断这个子串是不是回文，如果是回文，就加入在`vector<string> path`中，path用来记录切割过的回文子串。

代码如下：

```cpp
for(int i = startIndex; i < s.length(); i++) {
       if(!isPalindrome(s, startIndex, i)) {
           continue;
       }
       string str = s.substr(startIndex, i - startIndex + 1);  //注意截取长度的计算: i - startIndex + 1
       path.push_back(str);
       backtracking(s, i + 1);     //改变截取的起始位置startIndex, 即N叉树的下一层
       path.pop_back();
}
```

**注意切割过的位置，不能重复切割，所以，backtracking(s, i + 1); 传入下一层的起始位置为i + 1**。

完整代码：

```c++
class Solution {
public:
    vector<vector<string>> result;
    vector<string> path;
    vector<vector<string>> partition(string s) {
        backtracking(s, 0);
        return result;
    }
private:
    void backtracking(string s, int startIndex) {
        if(startIndex == s.length()) {
            result.push_back(path);
            return ;
        }
        for(int i = startIndex; i < s.length(); i++) {
            if(!isPalindrome(s, startIndex, i)) {
                continue;
            }
            string str = s.substr(startIndex, i - startIndex + 1);  //注意截取长度的计算: i - startIndex + 1
            path.push_back(str);
            backtracking(s, i + 1);     //改变截取的起始位置startIndex, 即N叉树的下一层
            path.pop_back();
        }
    }
    bool isPalindrome(const string& s, int start, int end) {	//判断将要截取的子串是不是回文串
        for (int i = start, j = end; i < j; i++, j--) {
            if (s[i] != s[j]) {
                return false;
            }
        }
        return true;
    }
};
```

### 八、复原 IP 地址

[93. 复原 IP 地址 - 力扣（Leetcode）](https://leetcode.cn/problems/restore-ip-addresses/description/)

**有效 IP 地址** 正好由四个整数（每个整数位于 `0` 到 `255` 之间组成，且不能含有前导 `0`），整数之间用 `'.'` 分隔。

- 例如：`"0.1.2.201"` 和` "192.168.1.1"` 是 **有效** IP 地址，但是 `"0.011.255.245"`、`"192.168.1.312"` 和 `"192.168@1.1"` 是 **无效** IP 地址。

给定一个只包含数字的字符串 `s` ，用以表示一个 IP 地址，返回所有可能的**有效 IP 地址**，这些地址可以通过在 `s` 中插入 `'.'` 来形成。你 **不能** 重新排序或删除 `s` 中的任何数字。你可以按 **任何** 顺序返回答案。

**示例 1：**

```
输入：s = "25525511135"
输出：["255.255.11.135","255.255.111.35"]
```

**思路**：

其实只要意识到这是切割问题，**切割问题就可以使用回溯搜索法把所有可能性搜出来**，和刚做过的[131.分割回文串](https://programmercarl.com/0131.分割回文串.html)就十分类似了。

切割问题可以抽象为树型结构，如图：

<img src="https://img-blog.csdnimg.cn/20201123203735933.png" alt="93.复原IP地址" style="zoom:50%;" />

完整代码：

```c++
class Solution {
public:
    vector<string> result;
    vector<string> restoreIpAddresses(string s) {
        if(s.length() < 4 || s.length() > 12) return result;
        backtracking(s, 0, 0);
        return result;
    }
private:
    void backtracking(string& s, int startIndex, int pointNum) {
        if(pointNum == 3) {
            // 存在第四段子字符串 且 第四段子字符串合法
            if(startIndex <= s.length() - 1 && isValid(s, startIndex, s.length() - 1)) {
                result.push_back(s);
            }
            return ;
        }
        for(int i = startIndex; i < s.length(); i++) {
            if(!isValid(s, startIndex, i)) {   // 判断 [startIndex,i] 这个区间的子串是否合法
                continue;
            }
            s.insert(s.begin() + i + 1, '.'); //insert(pos, str); 在原串下标为pos的字符'前'插入字符串str
            pointNum++; 
            backtracking(s, i + 2, pointNum);   //插入逗点之后下一个子串的起始位置为i+2
            pointNum--;
            //str.erase(pos, n); 删除从pos开始的n个字符
            //str.erase(first, last); 删除从first到last之间的字符（first和last都是迭代器）
            s.erase(s.begin() + i + 1);
        }
    }
    // 判断子串是否合法
    bool isValid(string s, int start, int end) {
        if(s[start] == '0' && start != end) return false;   //①'0'开头无效
        string num = s.substr(start, end - start + 1);
        if(atoi(num.c_str()) > 255) return false; //②截取数字大于255无效
        while(start <= end) {
            if(s[start] < '0' || s[start] > '9') return false;  //③无效字符
            start++;
        }
        return true;
    }
};
```

### 九、子集

[78. 子集 - 力扣（Leetcode）](https://leetcode.cn/problems/subsets/description/)

给你一个整数数组 `nums` ，数组中的元素 **互不相同** 。返回该数组所有可能的子集（幂集）。

解集 **不能** 包含重复的子集。你可以按 **任意顺序** 返回解集。

**示例 1：**

```
输入：nums = [1,2,3]
输出：[[],[1],[2],[1,2],[3],[1,3],[2,3],[1,2,3]]
```

**提示：**

- `1 <= nums.length <= 10`
- `-10 <= nums[i] <= 10`
- `nums` 中的所有元素 **互不相同**



**思路**

其实子集也是一种组合问题，因为它的集合是无序的，子集{1,2} 和 子集{2,1}是一样的。

**那么既然是无序，取过的元素不会重复取，写回溯算法的时候，for就要从startIndex开始，而不是从0开始！**

有同学问了，什么时候for可以从0开始呢？

求排列问题的时候，就要从0开始，因为集合是有序的，{1, 2} 和{2, 1}是两个集合，排列问题我们后续的文章就会讲到的。

以示例中nums = [1,2,3]为例把求子集抽象为树型结构，如下：

<img src="https://img-blog.csdnimg.cn/202011232041348.png" alt="78.子集" style="zoom:50%;" />

从图中红线部分，可以看出**遍历这个树的时候，把所有节点都记录下来，就是要求的子集集合**。

完整代码：

```c++
class Solution {
public:
    vector<vector<int>> result;
    vector<int> path;
    vector<vector<int>> subsets(vector<int>& nums) {
        backtracking(nums, 0);
        return result;
    }
private:
    void backtracking(vector<int>& nums, int startIndex) {
        result.push_back(path);
        if(path.size() == nums.size()) {
            return ;
        }
        for(int i = startIndex; i < nums.size(); i++) {
            path.push_back(nums[i]);
            backtracking(nums, i + 1);
            path.pop_back();
        }
    }
};
```

### 十、集合Ⅱ

[90. 子集 II - 力扣（Leetcode）](https://leetcode.cn/problems/subsets-ii/description/)

给你一个整数数组 `nums` ，其中可能包含重复元素，请你返回该数组所有可能的子集（幂集）。

解集 **不能** 包含重复的子集。返回的解集中，子集可以按 **任意顺序** 排列。

**示例 1：**

```
输入：nums = [1,2,2]
输出：[[],[1],[1,2],[1,2,2],[2],[2,2]]
```



**思路**

**理解“树层去重”和“树枝去重”非常重要**。

用示例中的[1, 2, 2] 来举例，如图所示： （**注意去重需要先对集合排序**）

<img src="https://img-blog.csdnimg.cn/20201124195411977.png" alt="90.子集II" style="zoom:50%;" />

从图中可以看出，同一树层上重复取2 就要过滤掉，同一树枝上就可以重复取2，因为同一树枝上元素的集合才是唯一子集！

完整代码：

```c++
class Solution {
public:
    vector<vector<int>> result;
    vector<int> path;
    vector<vector<int>> subsetsWithDup(vector<int>& nums) {
        sort(nums.begin(), nums.end());
        backtracking(nums, 0);
        return result;
    }
private:
    void backtracking(vector<int>& nums, int startIndex) {
        result.push_back(path);
        if(path.size() == nums.size()) {
            return ;
        }
        for(int i = startIndex; i < nums.size(); i++) {
            if(startIndex < i && nums[i - 1] == nums[i]) continue;  //去重原理与组合Ⅱ相同
            path.push_back(nums[i]);
            backtracking(nums, i + 1);
            path.pop_back();
        }
    }
};
```

### 十一、递增子序列

[491. 递增子序列 - 力扣（Leetcode）](https://leetcode.cn/problems/increasing-subsequences/)

给你一个整数数组 `nums` ，找出并返回所有该数组中不同的递增子序列，递增子序列中 **至少有两个元素** 。你可以按 **任意顺序** 返回答案。

数组中可能含有重复元素，如出现两个整数相等，也可以视作递增序列的一种特殊情况。

**示例 1：**

```
输入：nums = [4,6,7,7]
输出：[[4,6],[4,6,7],[4,6,7,7],[4,7],[4,7,7],[6,7],[6,7,7],[7,7]]
```

**思路**

这个递增子序列比较像是取有序的子集。而且本题也要求不能有相同的递增子序列。

在**[子集Ⅱ]**中我们是通过排序，再加一个标记数组来达到去重的目的。

而本题求**自增子序列**，**是不能对原数组经行排序的**，排完序的数组都是自增子序列了。

**所以不能使用之前的去重逻辑！**

本题给出的示例，还是一个有序数组 [4, 6, 7, 7]，这更容易误导大家按照排序的思路去做了。

为了有鲜明的对比，我用[4, 7, 6, 7]这个数组来举例，抽象为树形结构如图：

<img src="https://img-blog.csdnimg.cn/20201124200229824.png" alt="491. 递增子序列1" style="zoom:50%;" />

- 递归函数参数

本题求子序列，很明显一个元素不能重复使用，所以需要startIndex，调整下一层递归的起始位置。

代码如下：

```cpp
vector<vector<int>> result;
vector<int> path;
void backtracking(vector<int>& nums, int startIndex)
```

- 终止条件

本题其实类似求子集问题，也是要遍历树形结构找每一个节点，所以和**[子集]**一样，可以不加终止条件，startIndex每次都会加1，并不会无限递归。

但本题收集结果有所不同，题目要求递增子序列大小至少为2，所以代码如下：

```cpp
if (path.size() > 1) {
    result.push_back(path);
}
// if(startIndex == nums.size()) return ; //这句可以不加,程序在for循环中退出
```

- 单层搜索逻辑

在图中可以看出，**同一父节点下的同层上使用过的元素就不能在使用了**。那么单层搜索代码如下：

```cpp
unordered_set<int> uset; // 使用set来对本层元素进行去重
for (int i = startIndex; i < nums.size(); i++) {
    if ((!path.empty() && nums[i] < path.back())
            || uset.find(nums[i]) != uset.end()) {
            continue;
    }
    uset.insert(nums[i]); // 记录这个元素在本层用过了，本层后面不能再用了
    path.push_back(nums[i]);
    backtracking(nums, i + 1);
    path.pop_back();
}
```

**对于已经习惯写回溯的同学，看到递归函数上面的`uset.insert(nums[i]);`，下面却没有对应的pop之类的操作，应该很不习惯吧，哈哈**

**这也是需要注意的点，`unordered_set<int> uset;` 是记录本层元素是否重复使用，新的一层uset都会重新定义（清空），所以要知道uset只负责本层！**

完整代码：

```c++
class Solution {
public:
    vector<vector<int>> result;
    vector<int> path;
    vector<vector<int>> findSubsequences(vector<int>& nums) {
        backtracking(nums, 0);
        return result;
    }
private:
    void backtracking(vector<int>& nums, int startIndex) {
        if(path.size() > 1) {
            result.push_back(path);
        }
        // if(startIndex == nums.size()) return ; //这句可以不加,程序在for循环中退出
        unordered_set<int> uset; // 记录本层已访问过的元素,用于本层元素的去重
        for(int i = startIndex; i < nums.size(); i++) {
            if(!path.empty() && nums[i] < path.back()   //本层元素不小于相邻的上层元素(满足path是递增的)
                || uset.count(nums[i]) != 0) {          //本层元素不能重复,去重
                    continue;
            }
            uset.insert(nums[i]);
            path.push_back(nums[i]);
            backtracking(nums, i + 1);
            path.pop_back();
        }
    }
};
```

### 十二、全排列

[46. 全排列 - 力扣（Leetcode）](https://leetcode.cn/problems/permutations/)

给定一个不含重复数字的数组 `nums` ，返回其 *所有可能的全排列* 。你可以 **按任意顺序** 返回答案。

**示例 1：**

```
输入：nums = [1,2,3]
输出：[[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]
```

**示例 2：**

```
输入：nums = [0,1]
输出：[[0,1],[1,0]]
```

**思路**

前面看过了组合问题，接下来看一看排列问题。相信这个排列问题就算是让你用for循环暴力把结果搜索出来，这个暴力也不是很好写。

以[1,2,3]为例，抽象成树形结构如下：

<img src="https://code-thinking-1253855093.file.myqcloud.com/pics/20211027181706.png" alt="46.全排列" style="zoom:50%;" />

**回溯三部曲**

- 递归函数参数

**首先排列是有序的，也就是说 [1,2] 和 [2,1] 是两个集合，这和之前分析的子集以及组合所不同的地方**。

可以看出元素1在[1,2]中已经使用过了，但是在[2,1]中还要在使用一次1，所以处理排列问题就不用使用startIndex了。

但排列问题需要一个used数组，标记已经选择的元素，如图橘黄色部分所示:

<img src="https://code-thinking-1253855093.file.myqcloud.com/pics/20211027181706.png" alt="46.全排列" style="zoom:50%;" />

代码如下：

```cpp
vector<vector<int>> result;
vector<int> path;
void backtracking (vector<int>& nums, vector<bool>& used)
```

- 递归终止条件

<img src="https://img-blog.csdnimg.cn/20201209174225145.png" alt="46.全排列" style="zoom:50%;" />

可以看出叶子节点，就是收割结果的地方。

那么什么时候，算是到达叶子节点呢？

当收集元素的数组path的大小达到和nums数组一样大的时候，说明找到了一个全排列，也表示到达了叶子节点。

代码如下：

```cpp
// 此时说明找到了一组
if (path.size() == nums.size()) {
    result.push_back(path);
    return;
}
```

- 单层搜索的逻辑

这里和[77.组合问题 ](https://programmercarl.com/0077.组合.html)、[131.切割问题 ](https://programmercarl.com/0131.分割回文串.html)和[78.子集问题 ](https://programmercarl.com/0078.子集.html)最大的不同就是for循环里不用startIndex了。

因为排列问题，每次都要从头开始搜索，例如元素1在[1,2]中已经使用过了，但是在[2,1]中还要再使用一次1。

**而used数组，其实就是记录此时path里都有哪些元素使用了，一个排列里一个元素只能使用一次**。

代码如下：

```cpp
for (int i = 0; i < nums.size(); i++) {
    if (used[i] == true) continue; // path里已经收录的元素，直接跳过
    used[i] = true;
    path.push_back(nums[i]);
    backtracking(nums, used);
    path.pop_back();
    used[i] = false;
}
```

完整代码：

```c++
class Solution {
public:
    vector<vector<int>> result;
    vector<int> path;
    vector<vector<int>> permute(vector<int>& nums) {
        vector<bool> used(nums.size(), false); //去重用
        backtracking(nums, used);
        return result;
    }
private:
    void backtracking(vector<int>& nums, vector<bool> used) {
        if(path.size() == nums.size()) {
            result.push_back(path);
            return ;
        }
        for(int i = 0; i < nums.size(); i++) {
            if(used[i] == true) continue;   // used里已经收录的元素，直接跳过
            used[i] = true;
            used.push_back(nums[i]);
            path.push_back(nums[i]);
            backtracking(nums, used);   //这里的used在不同的树层间传参，防止不同层间元素重复.区别于同层间去重
            path.pop_back();
            used[i] = false;
        }
    }
};
```

### 十三、全排列Ⅱ

[47. 全排列 II - 力扣（Leetcode）](https://leetcode.cn/problems/permutations-ii/description/)

给定一个可包含重复数字的序列 `nums` ，***按任意顺序*** 返回所有不重复的全排列。

**示例 1：**

```
输入：nums = [1,1,2]
输出：
[[1,1,2],
 [1,2,1],
 [2,1,1]]
```

这一题在[46. 全排列 - 力扣（Leetcode）](https://leetcode.cn/problems/permutations/) 全排列 的基础上增加了 **序列中的元素可重复** 这一条件，但要求：返回的结果又不能有重复元素。

思路是：在遍历的过程中，一边遍历一遍检测，**在会产生重复结果集的地方剪枝**。



一个比较容易想到的办法是在结果集中去重。但是问题来了，这些结果集的元素是一个又一个列表，对列表去重不像用哈希表对基本元素去重那样容易。

如果要比较两个列表是否一样，一个容易想到的办法是对列表分别排序，然后逐个比对。既然要排序，我们就可以 **在搜索之前就对候选数组排序**，一旦发现某个分支搜索下去可能搜索到重复的元素就停止搜索，这样结果集中不会包含重复列表。

画出树形结构如下：重点想象深度优先遍历在这棵树上执行的过程，哪些地方遍历下去一定会产生重复，这些地方的状态的特点是什么？ **对比图中标注 ① 和 ② 的地方。相同点是：这一次搜索的起点和上一次搜索的起点一样。不同点是：**

- **标注 ① 的地方上一次搜索的相同的数刚刚被撤销；**
- **标注 ② 的地方上一次搜索的相同的数刚刚被使用。**

<img src="E:\Postgraduate\Study\Leetcode\images\全排列Ⅱ.png" alt="全排列Ⅱ" style="zoom:50%;" />


产生重复结点的地方，正是图中标注了「剪刀」，且被绿色框框住的地方。

大家也可以把第 2 个 `1` 加上 `'` ，即 `[1, 1', 2]` 去想象这个搜索的过程。只要遇到起点一样，就有可能产生重复。这里还有一个很细节的地方：

在图中 ② 处，搜索的数也和上一次一样，但是上一次的 `1` 还在使用中；
**在图中 ① 处，搜索的数也和上一次一样，但是上一次的 `1` 刚刚被撤销，正是因为刚被撤销，下面的搜索中还会使用到，因此会产生重复，剪掉的就应该是这样的分支。**
**代码实现方面，在第 46 题的基础上，要加上这样一段代码：**

```c++
if (i > 0 && nums[i] == nums[i - 1] && !used[i - 1]) {
    continue;
}
```

这段代码就能检测到标注为 ① 的两个结点，跳过它们。

```c++
class Solution {
public:
    vector<vector<int>> result;
    vector<int> path;
    vector<vector<int>> permuteUnique(vector<int>& nums) {
        sort(nums.begin(), nums.end());
        vector<bool> used(nums.size(), false);
        backtracking(nums, used);
        return result;
    }
private:
    void backtracking(vector<int>& nums, vector<bool> used) {
        if(path.size() == nums.size()) {
            result.push_back(path);
        }
        for(int i = 0; i < nums.size(); i++) {
            //当前值用过了 或 
            //当前值等于前一个值： 两种情况：
            //1 nums[i-1] 没用过 说明回溯到了同一层 此时接着用num[i] 则会与 同层用num[i-1] 重复
            //2 nums[i-1] 用过了 说明此时在num[i-1]的下一层 相等不会重复
            if(used[i] || (i>0 && nums[i] == nums[i-1] && !used[i-1])) continue;
            // if(i > 0 && nums[i - 1] == nums[i]) continue;   //这么写，会把不同树层重复数字的情况也去掉,如[1 1 2]
            used[i] = true;
            path.push_back(nums[i]);
            backtracking(nums,used);
            path.pop_back();
            used[i] = false;
        }
    }
};
```

复杂度分析：（理由同第 46 题，重复元素越多，剪枝越多。但是计算复杂度的时候需要考虑最差情况。）

时间复杂度：O(N×N!)，这里 N 为数组的长度。
空间复杂度：O(N×N!)。

### 十四、重新安排行程

[332. 重新安排行程 - 力扣（Leetcode）](https://leetcode.cn/problems/reconstruct-itinerary/description/)

给你一份航线列表 `tickets` ，其中 `tickets[i] = [fromi, toi]` 表示飞机出发和降落的机场地点。请你对该行程进行重新规划排序。

所有这些机票都属于一个从 `JFK`（肯尼迪国际机场）出发的先生，所以该行程必须从 `JFK` 开始。如果存在多种有效的行程，请你按字典排序返回最小的行程组合。

- 例如，行程 `["JFK", "LGA"]` 与 `["JFK", "LGB"]` 相比就更小，排序更靠前。

假定所有机票至少存在一种合理的行程。且所有的机票 必须都用一次 且 只能用一次。

**示例 1：**

![img](https://assets.leetcode.com/uploads/2021/03/14/itinerary1-graph.jpg)

```
输入：tickets = [["MUC","LHR"],["JFK","MUC"],["SFO","SJC"],["LHR","SFO"]]
输出：["JFK","MUC","LHR","SFO","SJC"]
```



参考：[代码随想录 (programmercarl.com)](https://www.programmercarl.com/0332.重新安排行程.html#思路)

完整代码：

```c++
class Solution {
public:
    vector<string> ans;
    bool backtracking(vector<vector<string>>& tickets,vector<bool>& vis, string from){
        //一般函数返回值都是void,这里是bool是因为我们只需要找到一个行程.当找到时回溯函数会一直返回
        if(ans.size() == tickets.size() + 1){
            return true;
        }
        string to;
        for(int i = 0; i < tickets.size(); i++){
            if(tickets[i][0] == from && vis[i] == false){
                to = tickets[i][1];
                vis[i] = true;
                ans.push_back(to);
                if(backtracking(tickets, vis, to)){
                    return true;
                }
                vis[i] = false; //向上回溯
                ans.pop_back();
            }
        }
        return false;
    }
    static bool cmp(vector<string>& x, vector<string>& y){  //按照升序的排序规则
        return x[1] < y[1];
    }
    vector<string> findItinerary(vector<vector<string>>& tickets) {
        vector<bool> vis(tickets.size(), false);
        sort(tickets.begin(), tickets.end(), cmp);
        ans.push_back("JFK");
        backtracking(tickets, vis, "JFK");
        return ans;
    }
};
```



## 贪心算法

**贪心的本质是选择每一阶段的局部最优，从而达到全局最优。**

### 1.分发饼干

[455. 分发饼干 - 力扣（Leetcode）](https://leetcode.cn/problems/assign-cookies/)

**思路**

为了满足更多的小孩，就不要造成饼干尺寸的浪费。

大尺寸的饼干既可以满足胃口大的孩子也可以满足胃口小的孩子，那么就应该优先满足胃口大的。

**这里的局部最优就是大饼干喂给胃口大的，充分利用饼干尺寸喂饱一个，全局最优就是喂饱尽可能多的小孩**。

可以尝试使用贪心策略，先将饼干数组和小孩数组排序。

然后从后向前遍历小孩数组，用大饼干优先满足胃口大的，并统计满足小孩数量。

如图：

<img src="https://img-blog.csdnimg.cn/20201123161809624.png" alt="455.分发饼干" style="zoom:50%;" />

这个例子可以看出饼干9只有喂给胃口为7的小孩，这样才是整体最优解，并想不出反例，那么就可以撸代码了。

C++代码整体如下：

```cpp
// 时间复杂度：O(nlogn)
// 空间复杂度：O(1)
class Solution {
public:
    int findContentChildren(vector<int>& g, vector<int>& s) {
        sort(g.begin(), g.end());
        sort(s.begin(), s.end());
        int index = s.size() - 1; // 饼干数组的下标
        int result = 0;
        for (int i = g.size() - 1; i >= 0; i--) {
            if (index >= 0 && s[index] >= g[i]) {
                result++;
                index--;
            }
        }
        return result;
    }
};
```

从代码中可以看出我用了一个index来控制饼干数组的遍历，遍历饼干并没有再起一个for循环，而是采用自减的方式，这也是常用的技巧。

### 2.摆动序列

[376. 摆动序列 - 力扣（Leetcode）](https://leetcode.cn/problems/wiggle-subsequence/description/)

**思路**

本题要求通过从原始序列中删除一些（也可以不删除）元素来获得子序列，剩下的元素保持其原始顺序。

相信这么一说吓退不少同学，这要求最大摆动序列又可以修改数组，这得如何修改呢？

来分析一下，要求删除元素使其达到最大摆动序列，应该删除什么元素呢？

用示例二来举例，如图所示：

<img src="https://img-blog.csdnimg.cn/20201124174327597.png" alt="376.摆动序列" style="zoom:50%;" />

**局部最优：删除单调坡度上的节点（不包括单调坡度两端的节点），那么这个坡度就可以有两个局部峰值**。

**整体最优：整个序列有最多的局部峰值，从而达到最长摆动序列**。

局部最优推出全局最优，并举不出反例，那么试试贪心！

（为方便表述，以下说的峰值都是指局部峰值）

**实际操作上，其实连删除的操作都不用做，因为题目要求的是最长摆动子序列的长度，所以只需要统计数组的峰值数量就可以了（相当于是删除单一坡度上的节点，然后统计长度）**

**这就是贪心所贪的地方，让峰值尽可能的保持峰值，然后删除单一坡度上的节点**。

本题代码实现中，还有一些技巧，例如统计峰值的时候，数组最左面和最右面是最不好统计的。

例如序列[2,5]，它的峰值数量是2，如果靠统计差值来计算峰值个数就需要考虑数组最左面和最右面的特殊情况。

所以可以针对序列[2,5]，可以假设为[2,2,5]，这样它就有坡度了即preDiff = 0，如图：

![376.摆动序列1](https://img-blog.csdnimg.cn/20201124174357612.png)

针对以上情形，result初始为1（默认最右面有一个峰值），此时curDiff > 0 && preDiff <= 0，那么result++（计算了左面的峰值），最后得到的result就是2（峰值个数为2即摆动序列长度为2）

C++代码如下（和上图是对应的逻辑）：

```cpp
class Solution {
public:
    int wiggleMaxLength(vector<int>& nums) {
        if (nums.size() <= 1) return nums.size();
        int curDiff = 0; // 当前一对差值
        int preDiff = 0; // 前一对差值
        int result = 1;  // 记录峰值个数，序列默认序列最右边有一个峰值
        for (int i = 0; i < nums.size() - 1; i++) {
            curDiff = nums[i + 1] - nums[i];
            // 出现峰值
            if ((curDiff > 0 && preDiff <= 0) || (preDiff >= 0 && curDiff < 0)) {
                result++;
                preDiff = curDiff;
            }
        }
        return result;
    }
};
```

- 时间复杂度：O(n)
- 空间复杂度：O(1)

**贪心到底贪在哪里**：上升的时候尽量上升的最高，下降的时候尽量下降到最低。

### 3.最大子数组和

[53. 最大子数组和 - 力扣（Leetcode）](https://leetcode.cn/problems/maximum-subarray/description/)

给你一个整数数组 `nums` ，请你找出一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。

**子数组** 是数组中的一个连续部分。

**示例 1：**

```
输入：nums = [-2,1,-3,4,-1,2,1,-5,4]
输出：6
解释：连续子数组 [4,-1,2,1] 的和最大，为 6 。
```

**思路**

**贪心贪的是哪里呢？**

如果 -2 1 在一起，计算起点的时候，一定是从1开始计算，因为负数只会拉低总和，这就是贪心贪的地方！

局部最优：**当前“连续和”为负数的时候立刻放弃**，**从下一个元素重新计算“连续和”**，**因为负数加上下一个元素 “连续和”只会越来越小**。

全局最优：选取最大“连续和”

**局部最优的情况下，并记录最大的“连续和”，可以推出全局最优**。

从代码角度上来讲：遍历nums，从头开始用count累积，如果count一旦加上nums[i]变为负数，那么就应该从nums[i+1]开始从0累积count了，因为已经变为负数的count，只会拖累总和。

**这相当于是暴力解法中的不断调整最大子序和区间的起始位置**。

**那有同学问了，区间终止位置不用调整么？ 如何才能得到最大“连续和”呢？**

区间的终止位置，其实就是如果count取到最大值了，及时记录下来了。例如如下代码：

```c++
if (count > result) result = count;
```

**这样相当于是用result记录最大子序和区间和（变相的算是调整了终止位置）**。

如动画所示：

![53.最大子序和](https://code-thinking.cdn.bcebos.com/gifs/53.%E6%9C%80%E5%A4%A7%E5%AD%90%E5%BA%8F%E5%92%8C.gif)

红色的起始位置就是贪心每次取count为正数的时候，开始一个区间的统计。

```c++
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        if(nums.size() == 1) return nums[0];
        int max_sum = INT_MIN;
        int count = 0;
        for(int i = 0; i < nums.size(); i++) {
            count += nums[i];
            if(count > max_sum) {   // 取区间累计的最大值（相当于不断确定最大子序终止位置）
                max_sum = count;
            }
            if(count < 0) {
                count = 0;    //// 丢弃累积和，相当于重置最大子序起始位置，因为遇到负数一定是拉低总和
            }
        }
        return max_sum;
    }
};
```

- 时间复杂度：O(n)
- 空间复杂度：O(1)





### 4.买卖股票的最佳时机系列

股票问题参考解法：

[121. 买卖股票题目汇总 - 力扣（Leetcode）](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock/solutions/38477/bao-li-mei-ju-dong-tai-gui-hua-chai-fen-si-xiang-b/)

[121. 买卖股票的最佳时机 - 力扣（Leetcode）](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock/solutions/8753/yi-ge-fang-fa-tuan-mie-6-dao-gu-piao-wen-ti-by-l-3/)

六个股票问题是根据 `k` 的值进行分类的，其中 `k` 是允许的**最大交易次数**。最后两个问题有附加限制，包括「冷冻期」和「手续费」。通解可以应用于每个股票问题。

#### 问题分析

首先介绍一些符号：

- 用 `n` 表示股票价格数组的长度；
- 用 `i` 表示第 `i` 天（`i` 的取值范围是 `0` 到 `n - 1`）；
- 用 `k` 表示允许的最大交易次数；
- 用 `T[i][k]` 表示在第 `i` 天**结束时**，最多进行 `k` 次交易的情况下可以获得的最大收益。

​		基准情况是显而易见的：`T[-1][k] = T[i][0] = 0`，表示没有进行股票交易时没有收益（注意第一天对应 `i = 0`，因此 `i = -1` 表示没有股票交易）。现在如果可以将 `T[i][k]` 关联到子问题，例如 `T[i - 1][k]、T[i][k - 1]、T[i - 1][k - 1]` 等子问题，就能得到状态转移方程，并对这个问题求解。如何得到状态转移方程呢？

​		最直接的办法是看第 `i` 天可能的操作。有多少个选项？答案是三个：买入、卖出、休息。应该选择哪个操作？答案是：并不知道哪个操作是最好的，但是可以通过计算得到选择每个操作可以得到的最大收益。假设没有别的限制条件，则可以尝试每一种操作，并选择可以最大化收益的一种操作。但是，题目中确实有限制条件，规定不能同时进行多次交易，因此如果决定在第 `i` 天买入，在买入之前必须持有 `0` 份股票，如果决定在第 `i` 天卖出，在卖出之前必须恰好持有 `1` 份股票。持有股票的数量是上文提及到的隐藏因素，该因素影响第 `i` 天可以进行的操作，进而影响最大收益。

因此对 `T[i][k]` 的定义需要分成两项：

- `T[i][k][0]` 表示在第 `i` 天结束时，**最多**进行 `k` 次交易且在进行操作后持有 `0` 份股票的情况下可以获得的最大收益；
- `T[i][k][1]` 表示在第 `i` 天结束时，**最多**进行 `k` 次交易且在进行操作后持有 `1` 份股票的情况下可以获得的最大收益。

使用新的状态表示之后，可以得到基准情况和状态转移方程。

基准情况：

```c++
T[-1][k][0] = 0, T[-1][k][1] = -Infinity
T[i][0][0] = 0, T[i][0][1] = -Infinity
```

状态转移方程：

```c++
T[i][k][0] = max(T[i - 1][k][0], T[i - 1][k][1] + prices[i])// i-1->i时，k在卖出时不变化，但持有数量-1
T[i][k][1] = max(T[i - 1][k][1], T[i - 1][k - 1][0] - prices[i])// i-1->i时，k只有在买入时+1，此时持有数量也+1
```

​		基准情况中，`T[-1][k][0] = T[i][0][0] = 0` 的含义和上文相同，`T[-1][k][1] = T[i][0][1] = -Infinity` 的含义是在没有进行股票交易时不允许持有股票。

​		对于状态转移方程中的 `T[i][k][0]`，第 `i` 天进行的操作只能是休息或卖出，因为在第 `i` 天结束时持有的股票数量是 `0`。`T[i - 1][k][0]` 是休息操作可以得到的最大收益，`T[i - 1][k][1] + prices[i]` 是卖出操作可以得到的最大收益。注意到允许的最大交易次数是不变的，因为每次交易包含两次成对的操作，买入和卖出。**只有买入操作会改变允许的最大交易次数**。

​		对于状态转移方程中的 `T[i][k][1]`，第 `i` 天进行的操作只能是休息或买入，因为在第 `i` 天结束时持有的股票数量是 `1`。`T[i - 1][k][1]` 是休息操作可以得到的最大收益，`T[i - 1][k - 1][0] - prices[i]` 是买入操作可以得到的最大收益。注意到允许的最大交易次数减少了一次，因为每次买入操作会使用一次交易。

​		为了得到最后一天结束时的最大收益，可以遍历股票价格数组，根据状态转移方程计算 `T[i][k][0]` 和 `T[i][k][1]` 的值。最终答案是 `T[n - 1][k][0]`，因为结束时持有 `0` 份股票的收益一定大于持有 `1` 份股票的收益。



#### 情况一：k = 1

[121. 买卖股票的最佳时机 - 力扣（Leetcode）](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock/solutions/)

给定一个数组 `prices` ，它的第 `i` 个元素 `prices[i]` 表示一支给定股票第 `i` 天的价格。

你只能选择 **某一天** 买入这只股票，并选择在 **未来的某一个不同的日子** 卖出该股票。设计一个算法来计算你所能获取的最大利润。

返回你可以从这笔交易中获取的最大利润。如果你不能获取任何利润，返回 `0` 。

**示例 1：**

```
输入：[7,1,5,3,6,4]
输出：5
解释：在第 2 天（股票价格 = 1）的时候买入，在第 5 天（股票价格 = 6）的时候卖出，最大利润 = 6-1 = 5 。
     注意利润不能是 7-1 = 6, 因为卖出价格需要大于买入价格；同时，你不能在买入前卖出股票。
```

**贪心**

若在前 i 天选择买入，若想达到最高利润，则一定选择价格最低的交易日买入。考虑根据此贪心思想，遍历价格列表 prices 并执行两步：

- 更新前 i 天的最低价格，即最低买入成本 cost；
- 更新前 i 天的最高利润 profit ，即选择「前 i−1 天最高利润 profit 」和「第 i 天卖出的最高利润 price - cost 」中的最大值 ；

参考题解：[121. 买卖股票的最佳时机 - 力扣（Leetcode）](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock/solutions/1692872/by-jyd-cu90/)

完整代码：

```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int profit = 0; //记录最大利润
        int cost = INT_MAX; //记录最小支出
        for(int price : prices) {
            cost = min(cost, price);
            profit = max(profit, price - cost);
        }
        return profit;
    }
};
```

**动态规划**

对于情况一，每天有两个未知变量：`T[i][1][0]` 和 `T[i][1][1]`，状态转移方程如下：

```c++
T[i][1][0] = max(T[i - 1][1][0], T[i - 1][1][1] + prices[i])
T[i][1][1] = max(T[i - 1][1][1], T[i - 1][0][0] - prices[i]) = max(T[i - 1][1][1], -prices[i])
```

第二个状态转移方程利用了 `T[i][0][0] = 0`。

根据上述状态转移方程，可以写出时间复杂度为 `O(n)`和空间复杂度为 `O(n)` 的解法。

```text
原型：
dp[i][1][0] = max(dp[i - 1][1][0], dp[i - 1][1][1] + prices[i])
dp[i][1][1] = max(dp[i - 1][1][1], dp[i - 1][0][0] - prices[i]) 	//dp[i - 1][0][0]=0，k全为1
化简为：
dp[i][0] = max(dp[i - 1][0], dp[i - 1][1] + prices[i]);
dp[i][1] = max(dp[i - 1][1], -prices[i]);

第0天的base case:
dp[0][0] = max(dp[-1][0], dp[-1][1] + prices[0]) = dp[-1][0] = 0  (其中dp[-1][1] = -∞)
dp[0][1] = max(dp[-1][1], dp[-1][0] - prices[0]) = dp[-1][0] - prices[0] = -prices[0] (其中dp[-1][0] = -∞)
```

完整代码：

```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int n = prices.size();
        vector<vector<int>> dp(n, vector<int>(2));
        dp[0][0] = 0, dp[0][1] = -prices[0];
        for (int i = 1; i < n; i++) {
            dp[i][0] = max(dp[i - 1][0], dp[i - 1][1] + prices[i]);
            dp[i][1] = max(dp[i - 1][1], -prices[i]);
        }
        return dp[n-1][0];
    }
};
```

如果注意到第 `i` 天的最大收益只和第 `i - 1` 天的最大收益相关，空间复杂度可以降到 O(1)。

```c++
//空间优化
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int n = prices.size();
        int dp0_0 = 0, dp0_1 = -prices[0];
        for (int i = 1; i < n; i++) {
            dp0_0 = max(dp0_0, dp0_1 + prices[i]);
            dp0_1 = max(dp0_1, -prices[i]);
        }
        return dp0_0;
    }
};
```



#### 情况二：k 为正无穷

[122. 买卖股票的最佳时机 II - 力扣（Leetcode）](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-ii/description/)

给你一个整数数组 `prices` ，其中 `prices[i]` 表示某支股票第 `i` 天的价格。

在每一天，你可以决定是否购买和/或出售股票。你在任何时候 **最多** 只能持有 **一股** 股票。你也可以先购买，然后在 **同一天** 出售。

返回 *你能获得的 **最大** 利润* 。

**示例 1：**

```
输入：prices = [7,1,5,3,6,4]
输出：7
解释：在第 2 天（股票价格 = 1）的时候买入，在第 3 天（股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5 - 1 = 4 。
     随后，在第 4 天（股票价格 = 3）的时候买入，在第 5 天（股票价格 = 6）的时候卖出, 这笔交易所能获得利润 = 6 - 3 = 3 。
     总利润为 4 + 3 = 7 。
```

如果 `k` 为正无穷，则 `k` 和 `k - 1` 可以看成是相同的，因此有 `T[i - 1][k - 1][0] = T[i - 1][k][0] 和 T[i - 1][k - 1][1] = T[i - 1][k][1]`。每天仍有两个未知变量：`T[i][k][0]` 和 `T[i][k][1]`，其中 `k` 为正无穷，状态转移方程如下：

```text
T[i][k][0] = max(T[i - 1][k][0], T[i - 1][k][1] + prices[i])
T[i][k][1] = max(T[i - 1][k][1], T[i - 1][k - 1][0] - prices[i]) = max(T[i - 1][k][1], T[i - 1][k][0] - prices[i])
可化简为：
       dp[i][0] = max(dp[i - 1][0], dp[i - 1][1] + prices[i]);
       dp[i][1] = max(dp[i - 1][1], dp[i - 1][0] - prices[i]);
第0天的base case:
	   dp[0][0] = 0
	   dp[0][1] = -prices[0];
```

第二个状态转移方程利用了 `T[i - 1][k - 1][0] = T[i - 1][k][0]`。

根据上述状态转移方程，可以写出时间复杂度为 O(n) 和空间复杂度为 O(n) 的解法。

完整代码：

```c++
//等效于k=+∞
// O(n) , O(n) 
// class Solution {
// public:
//     int maxProfit(vector<int>& prices) {
//         int n = prices.size();
//         vector<vector<int>> dp(n, vector<int>(2));
//         dp[0][0] = 0, dp[0][1] = -prices[0];
//         for(int i = 1; i < n; i++) {
//             dp[i][0] = max(dp[i - 1][0], dp[i - 1][1] + prices[i]);
//             dp[i][1] = max(dp[i - 1][1], dp[i - 1][0] - prices[i]);
//         }
//         return dp[n - 1][0];
//     }
// };

//优化 O(n) , O(1) 
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int n = prices.size();
        int dp0_0 = 0, dp0_1 = -prices[0];
        for(int i = 1; i < n; i++) {
            int dp0_0_temp = dp0_0; //暂存dp0_0，用于计算新的dp0_1
            dp0_0 = max(dp0_0, dp0_1 + prices[i]);
            dp0_1 = max(dp0_1, dp0_0_temp - prices[i]);
        }
        return dp0_0;
    }
};
```

#### 情况三：k=2

情况三对应的题目是「[123. 买卖股票的最佳时机 III - 力扣（Leetcode）](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-iii/)」。

给定一个数组，它的第 `i` 个元素是一支给定的股票在第 `i` 天的价格。

设计一个算法来计算你所能获取的最大利润。你**最多**可以完成 **两笔** 交易。

**注意：**你不能同时参与多笔交易（你**必须在再次购买前出售掉之前的股票**）。

**示例 1:**

```
输入：prices = [3,3,5,0,0,3,1,4]
输出：6
解释：在第 4 天（股票价格 = 0）的时候买入，在第 6 天（股票价格 = 3）的时候卖出，这笔交易所能获得利润 = 3-0 = 3 。
     随后，在第 7 天（股票价格 = 1）的时候买入，在第 8 天 （股票价格 = 4）的时候卖出，这笔交易所能获得利润 = 4-1 = 3 。
```



**分析**：

情况三和情况一相似，区别之处是，对于情况三，每天有四个未知变量：`T[i][1][0]、T[i][1][1]、T[i][2][0]、T[i][2][1]`，状态转移方程如下：

```text
状态方程：
T[i][2][0] = max(T[i - 1][2][0], T[i - 1][2][1] + prices[i])
T[i][2][1] = max(T[i - 1][2][1], T[i - 1][1][0] - prices[i])
T[i][1][0] = max(T[i - 1][1][0], T[i - 1][1][1] + prices[i])
T[i][1][1] = max(T[i - 1][1][1], T[i - 1][0][0] - prices[i]) = max(T[i - 1][1][1], -prices[i])

第0天的base case：将i=0带入状态方程得到
	T[0][2][0] = max(T[-1][2][0], T[-1][2][1] + prices[0]) = T[-1][2][0] = 0
	T[0][2][1] = max(T[-1][2][1], T[-1][2][1] - prices[0]) = - prices[0]
	T[0][1][0] = max(T[-1][1][0], T[-1][1][1] + prices[0]) = T[-1][1][0] = 0
	T[0][1][1] = max(T[-1][1][1], T[-1][1][1] - prices[0]) = - prices[0]
	
注意：
第二个维度表示最多交易次数，而不是实际交易次数，最多交易两次可能实际交易一次，由于第三个维度是 1 表示持有股票，因此一定是买入了股票，又由于在第 0 天只可能买入，不可能卖出，因此 dp[0][2][1] = -prices[0] 就是该状态的最大值。
实际上不只是 k = 2 的情况如此，k 为任意值的情况也是如此，即 dp[0][i][1] = -prices[0] 对任意 1 <= i <= k 都成立。根据状态转移方程计算也可得到同样的结果
```

第四个状态转移方程利用了 `T[i][0][0] = 0`。

根据上述状态转移方程，可以写出时间复杂度为 O(n) 和空间复杂度为 O(n) 的解法。

如果注意到第 `i` 天的最大收益只和第 `i - 1` 天的最大收益相关，空间复杂度可以降到 O(1)。

完整代码：

```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int n = prices.size();
        int dp[n][3][2];
        //base case:
	    dp[0][2][0] = 0;
	    dp[0][2][1] = -prices[0];
	    dp[0][1][0] = 0;
	    dp[0][1][1] = -prices[0];
        for(int i = 1; i < n; i++) {
            dp[i][2][0] = max(dp[i - 1][2][0], dp[i - 1][2][1] + prices[i]);
            dp[i][2][1] = max(dp[i - 1][2][1], dp[i - 1][1][0] - prices[i]);
            dp[i][1][0] = max(dp[i - 1][1][0], dp[i - 1][1][1] + prices[i]);
            dp[i][1][1] = max(dp[i - 1][1][1], -prices[i]);
        }
        return dp[n - 1][2][0];
    }
};

//优化
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int n = prices.size();
        //base case:
	    int dp2_0 = 0;
	    int dp2_1 = -prices[0];
	    int dp1_0 = 0;
	    int dp1_1 = -prices[0];
        for(int i = 1; i < n; i++) {
            dp2_0 = max(dp2_0, dp2_1 + prices[i]);
            dp2_1 = max(dp2_1, dp1_0 - prices[i]);
            dp1_0 = max(dp1_0, dp1_1 + prices[i]);
            dp1_1 = max(dp1_1, -prices[i]);
        }
        return dp2_0;
    }
}
```

#### 情况四：k 为任意值

[188. 买卖股票的最佳时机 IV - 力扣（Leetcode）](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-iv/description/)

给定一个整数数组 `prices` ，它的第 `i` 个元素 `prices[i]` 是一支给定的股票在第 `i` 天的价格。

设计一个算法来计算你所能获取的最大利润。你最多可以完成 **k** 笔交易。

**注意：**你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。

**示例 1：**

```
输入：k = 2, prices = [2,4,1]
输出：2
解释：在第 1 天 (股票价格 = 2) 的时候买入，在第 2 天 (股票价格 = 4) 的时候卖出，这笔交易所能获得利润 = 4-2 = 2 。
```

**分析**

​	情况四是最通用的情况，对于每一天需要使用不同的 `k` 值更新所有的最大收益，对应持有 `0` 份股票或 `1` 份股票。**如果 `k` 超过一个临界值，最大收益就不再取决于允许的最大交易次数，而是取决于股票价格数组的长度，因此可以进行优化**。那么这个临界值是什么呢？

​	一个有收益的交易至少需要两天（在前一天买入，在后一天卖出，前提是买入价格低于卖出价格）。**如果股票价格数组的长度为 `n`，则有收益的交易的数量最多为 `n/2`（整数除法）**。因此 **k 的临界值是 `n/2`**。**如果给定的 `k` 不小于临界值，即 `k >= n/2`，则可以将 k 扩展为正无穷，此时问题等价于情况二**。

根据状态转移方程，可以写出时间复杂度为`O(nk)` 和空间复杂度为 `O(nk)`的解法。

完整代码：

```c++
class Solution {
public:    
    int maxProfit(int k, vector<int> &prices) {
        if(k == 0 || prices.empty()) return 0;
        // Max profit by k transaction from 0 to i
        const int n = prices.size();        
        if(k >= n / 2) return maxProfitInf(prices);

        // balance - the balance with at most j transactions with item 0 to i
        // profit - the profit with at most j transactions with item 0 to i
        vector<int> balance(k + 1, INT_MIN), profit(k + 1);

        for(int i = 0; i < n; ++i) {
            for(int j = 1; j <= k; ++j) {
                balance[j] = max(balance[j], profit[j - 1] - prices[i]); // whether to buy at prices[i]
                profit[j] = max(profit[j], balance[j] + prices[i]); // whether to sell at prices[i]
            }
        }
        return profit[k];
    }
private:
    int maxProfitInf(vector<int> &prices) {
        int buyin = INT_MAX, profit = 0;
        for(auto & price : prices) {
            if(price > buyin) profit += price - buyin;                
            buyin = price;
        }
        return profit;
    }
};
```

#### 情况五：k 为正无穷但有冷却时间

[309. 最佳买卖股票时机含冷冻期 - 力扣（Leetcode）](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-with-cooldown/)

给定一个整数数组`prices`，其中第 `prices[i]` 表示第 `*i*` 天的股票价格 。

设计一个算法计算出最大利润。在满足以下约束条件下，你可以尽可能地完成更多的交易（多次买卖一支股票）:

- **卖出股票后，你无法在第二天买入股票 (即冷冻期为 1 天)。**

**注意：**你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。

**示例 1:**

```
输入: prices = [1,2,3,0,2]
输出: 3 
解释: 对应的交易状态为: [买入, 卖出, 冷冻期, 买入, 卖出]
```

**分析**

由于具有相同的 k 值，因此情况五和情况二非常相似，不同之处在于情况五有「冷却时间」的限制，因此需要对状态转移方程进行一些修改。

**情况二**的状态转移方程如下：

```tex
T[i][k][0] = max(T[i - 1][k][0], T[i - 1][k][1] + prices[i])
T[i][k][1] = max(T[i - 1][k][1], T[i - 1][k][0] - prices[i])
```

但是在**有「冷却时间」的情况下**，如果在第 `i - 1` 天卖出了股票，就不能在第 `i` 天买入股票。因此，如果要在第 `i` 天买入股票，第二个状态转移方程中就不能使用 `T[i - 1][k][0]`，而应该使用 `T[i - 2][k][0]`。状态转移方程中的别的项保持不变，新的状态转移方程如下：

```tex
T[i][k][0] = max(T[i - 1][k][0], T[i - 1][k][1] + prices[i])
T[i][k][1] = max(T[i - 1][k][1], T[i - 2][k][0] - prices[i])
```

根据上述状态转移方程，可以写出时间复杂度为 O(n) 和空间复杂度为 O(n) 的解法。

完整代码：

```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int n = prices.size();
        int dp[n][2];
        dp[0][0] = 0, dp[0][1] = -prices[0];
        for(int i = 1; i < n; i++) {
            dp[i][0] = max(dp[i - 1][0], dp[i - 1][1] + prices[i]);
            dp[i][1] = max(dp[i - 1][1], (i >= 2 ? dp[i - 2][0] : 0) - prices[i]);
        }
        return dp[n - 1][0];
    }
};

//空间优化
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int n = prices.size();
        int dp0_0 = 0, dp0_1 = -prices[0];
        int pre_pre_dp0_0, pre_dp0_0;
        for(int i = 1; i < n; i++) {
            pre_pre_dp0_0 = pre_dp0_0;  //pre_pre_dp0_0 是 dp0_0 的前两天的值 
            pre_dp0_0 = dp0_0;
            dp0_0 = max(dp0_0, dp0_1 + prices[i]);
            dp0_1 = max(dp0_1, (i >= 2 ? pre_pre_dp0_0 : 0) - prices[i]);
        }
        return dp0_0;
    }
};
```

#### 情况六：k 为正无穷但有手续费

[714. 买卖股票的最佳时机含手续费 - 力扣（Leetcode）](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/description/)

给定一个整数数组 `prices`，其中 `prices[i]`表示第 `i` 天的股票价格 ；整数 `fee` 代表了交易股票的手续费用。

你可以无限次地完成交易，但是你每笔交易都需要付手续费。如果你已经购买了一个股票，在卖出它之前你就不能再继续购买股票了。

返回获得利润的最大值。

**注意：**这里的一笔交易指买入持有并卖出股票的整个过程，每笔交易你只需要为支付一次手续费。

**示例 1：**

```
输入：prices = [1, 3, 2, 8, 4, 9], fee = 2
输出：8
解释：能够达到的最大利润:  
在此处买入 prices[0] = 1
在此处卖出 prices[3] = 8
在此处买入 prices[4] = 4
在此处卖出 prices[5] = 9
总利润: ((8 - 1) - 2) + ((9 - 4) - 2) = 8
```

由于具有相同的 k 值，因此情况六和情况二非常相似，不同之处在于情况六有「手续费」，因此需要对状态转移方程进行一些修改。

情况二的状态转移方程如下：

```tex
T[i][k][0] = max(T[i - 1][k][0], T[i - 1][k][1] + prices[i])
T[i][k][1] = max(T[i - 1][k][1], T[i - 1][k][0] - prices[i])
```

由于需要对每次交易付手续费，因此在每次买入或卖出股票之后的收益需要扣除手续费，新的状态转移方程有两种表示方法。

第一种表示方法，**在每次买入股票时扣除手续费**：

```tex
T[i][k][0] = max(T[i - 1][k][0], T[i - 1][k][1] + prices[i])
T[i][k][1] = max(T[i - 1][k][1], T[i - 1][k][0] - prices[i] - fee)
```

第二种表示方法，**在每次卖出股票时扣除手续费**：

```tex
T[i][k][0] = max(T[i - 1][k][0], T[i - 1][k][1] + prices[i] - fee)
T[i][k][1] = max(T[i - 1][k][1], T[i - 1][k][0] - prices[i])
```

根据上述状态转移方程，可以写出时间复杂度为 `O(n)` 和空间复杂度为 `O(n)` 的解法。

完整代码：

```c++
class Solution {
public:
    int maxProfit(vector<int>& prices, int fee) {
        int n = prices.size();
        int dp[n][2];
        dp[0][0] = 0, dp[0][1] = -prices[0];
        for(int i = 1; i < n; i++) {
            dp[i][0] = max(dp[i - 1][0], dp[i - 1][1] + prices[i] - fee);
            dp[i][1] = max(dp[i - 1][1], dp[i - 1][0] - prices[i]);
        }
        return dp[n - 1][0];
    }
};

//空间优化
class Solution {
public:
    int maxProfit(vector<int>& prices, int fee) {
        int n = prices.size();
        int dp0_0 = 0, dp0_1 = -prices[0];
        for(int i = 1; i < n; i++) {
            int pre_dp0_0 = dp0_0;
            dp0_0 = max(dp0_0, dp0_1 + prices[i] - fee);
            dp0_1 = max(dp0_1, pre_dp0_0 - prices[i]);
        }
        return dp0_0;
    }
};
```

## 动态规划

### 1.引入

首先，**动态规划问题的一般形式就是求最值**。动态规划其实是运筹学的一种最优化方法，只不过在计算机问题上应用比较多，比如说让你求最长递增子序列，最小编辑距离等等。

既然是要求最值，核心问题是什么呢？**求解动态规划的核心问题是穷举**。因为要求最值，肯定要把所有可行的答案穷举出来，然后在其中找最值呗。

动态规划这么简单，就是穷举就完事了？我看到的动态规划问题都很难啊！

首先，虽然动态规划的核心思想就是穷举求最值，但是问题可以千变万化，穷举所有可行解其实并不是一件容易的事，需要你熟练掌握递归思维，只有列出**正确的「状态转移方程」**，才能正确地穷举。而且，你需要判断算法问题是否**具备「最优子结构」**，是否能够通过子问题的最值得到原问题的最值。另外，动态规划问题**存在「重叠子问题」**，如果暴力穷举的话效率会很低，所以需要你使用「备忘录」或者「DP table」来优化穷举过程，避免不必要的计算。

以上提到的重叠子问题、最优子结构、状态转移方程就是动态规划三要素。具体什么意思等会会举例详解，但是在实际的算法问题中，写出状态转移方程是最困难的，这也就是为什么很多朋友觉得动态规划问题困难的原因，我来提供我总结的一个思维框架，辅助你思考状态转移方程：

**明确 base case -> 明确「状态」-> 明确「选择」 -> 定义 `dp` 数组/函数的含义**。



1. **「状态转移方程」**
2. **「最优子结构」**：可以从子问题的最优结果推出更大规模问题的最优结果
3. **「重叠子问题」**

<img src="E:\Postgraduate\Study\Leetcode\images\dp.png" alt="dp" style="zoom:50%;" />

#### 斐波那契数列

1.**暴力递归**

​	代码虽然简洁易懂，但是十分低效，低效在哪里？假设 n = 20，请画出递归树：

​			[<img src="https://labuladong.gitee.io/algo/images/%e5%8a%a8%e6%80%81%e8%a7%84%e5%88%92%e8%af%a6%e8%a7%a3%e8%bf%9b%e9%98%b6/1.jpg" alt="img" style="zoom:50%;" />](https://labuladong.gitee.io/algo/images/动态规划详解进阶/1.jpg)

> PS：但凡遇到需要递归的问题，最好都画出递归树，这对你分析算法的复杂度，寻找算法低效的原因都有巨大帮助。

这个递归树怎么理解？就是说想要计算原问题 `f(20)`，我就得先计算出子问题 `f(19)` 和 `f(18)`，然后要计算 `f(19)`，我就要先算出子问题 `f(18)` 和 `f(17)`，以此类推。最后遇到 `f(1)` 或者 `f(2)` 的时候，结果已知，就能直接返回结果，递归树不再向下生长了。

**递归算法的时间复杂度怎么计算？就是用子问题个数乘以解决一个子问题需要的时间**。

首先计算子问题个数，即递归树中节点的总数。显然二叉树节点总数为指数级别，所以子问题个数为 `O(2^n)`。

然后计算解决一个子问题的时间，在本算法中，没有循环，只有 `f(n - 1) + f(n - 2)` 一个加法操作，时间为 `O(1)`。

所以，这个算法的时间复杂度为二者相乘，即 `O(2^n)`，指数级别，爆炸。

观察递归树，很明显发现了算法低效的原因：存在大量重复计算，比如 `f(18)` 被计算了两次，而且你可以看到，以 `f(18)` 为根的这个递归树体量巨大，多算一遍，会耗费巨大的时间。更何况，还不止 `f(18)` 这一个节点被重复计算，所以这个算法及其低效。

这就是动态规划问题的第一个性质：**重叠子问题**。下面，我们想办法解决这个问题。

2.**记忆化搜索**

<img src="https://labuladong.gitee.io/algo/images/%e5%8a%a8%e6%80%81%e8%a7%84%e5%88%92%e8%af%a6%e8%a7%a3%e8%bf%9b%e9%98%b6/2.jpg" alt="img" style="zoom:50%;" />

实际上，带「备忘录」的递归算法，把一棵存在巨量冗余的递归树通过「剪枝」，改造成了一幅不存在冗余的递归图，极大减少了子问题（即递归图中节点）的个数。复杂度下降为`O(N)`,每个子问题只计算一次。

<img src="https://labuladong.gitee.io/algo/images/%e5%8a%a8%e6%80%81%e8%a7%84%e5%88%92%e8%af%a6%e8%a7%a3%e8%bf%9b%e9%98%b6/3.jpg" alt="img" style="zoom: 50%;" />

**3.dp**

<img src="https://labuladong.gitee.io/algo/images/%e5%8a%a8%e6%80%81%e8%a7%84%e5%88%92%e8%af%a6%e8%a7%a3%e8%bf%9b%e9%98%b6/4.jpg" alt="img" style="zoom:50%;" />

啥叫「自顶向下」？注意我们刚才画的递归树（或者说图），是从上向下延伸，都是从一个规模较大的原问题比如说 `f(20)`，向下逐渐分解规模，直到 `f(1)` 和 `f(2)` 这两个 base case，然后逐层返回答案，这就叫「自顶向下」。

啥叫「自底向上」？反过来，我们直接从最底下、最简单、问题规模最小、已知结果的 `f(1)` 和 `f(2)`（base case）开始往上推，直到推到我们想要的答案 `f(20)`。这就是「递推」的思路，这也是动态规划一般都脱离了递归，而是由循环迭代完成计算的原因。

这里，引出「状态转移方程」这个名词，实际上就是描述问题结构的数学形式：

​																[![img](https://labuladong.gitee.io/algo/images/%e5%8a%a8%e6%80%81%e8%a7%84%e5%88%92%e8%af%a6%e8%a7%a3%e8%bf%9b%e9%98%b6/fib.png)](https://labuladong.gitee.io/algo/images/动态规划详解进阶/fib.png)

为啥叫「状态转移方程」？其实就是为了听起来高端。

`f(n)` 的函数参数会不断变化，所以你把参数 `n` 想做一个状态，这个状态 `n` 是由状态 `n - 1` 和状态 `n - 2` 转移（相加）而来，这就叫状态转移，仅此而已。

你会发现，上面的几种解法中的所有操作，例如 `return f(n - 1) + f(n - 2)`，`dp[i] = dp[i - 1] + dp[i - 2]`，以及对备忘录或 DP table 的初始化操作，都是围绕这个方程式的不同表现形式。

可见列出「状态转移方程」的重要性，它是解决问题的核心，而且很容易发现，其实状态转移方程直接代表着暴力解法。

完整代码：

```c++
//暴力递归
class Solution {
public:
    int fib(int n) {
        if(n == 0 || n == 1) return n;
        int result;
        return fib(n - 1) + fib(n - 2);
    }
};

//记忆化搜索
//把一棵存在巨量冗余的递归树通过「剪枝」，改造成了一幅不存在冗余的递归图
class Solution {
public:
    int fib(int n) {
        vector<int> table(n + 1, 0);
        return helper(table, n);
    }
private:
    int helper(vector<int>& table, int n) {
        if(n == 0 || n == 1) return n;
        if(table[n] != 0) return table[n];
        table[n] = helper(table, n - 1) + helper(table, n - 2);
        return table[n];
    }
};

//动态规划dp
class Solution {
public:
    int fib(int n) {
        if(n == 0) return 0;
        vector<int> dp(n + 1, 0);
        dp[0] = 0, dp[1] = 1;
        for(int i = 2; i <= n; i++) {
            dp[i] = dp[i - 1] + dp[i - 2];
        }
        return dp[n];
    }
};

//dp的空间优化
class Solution {
public:
    int fib(int n) {
        if(n == 0 || n == 1) return n;
        int dp0 = 0, dp1 = 1, dp2;
        for(int i = 2; i <= n; i++) {
            dp2 = dp0 + dp1;
            dp0 = dp1; dp1 = dp2;
        }
        return dp2;
    }
};
```

#### 零钱兑换

[322. 零钱兑换 - 力扣（Leetcode）](https://leetcode.cn/problems/coin-change/description/)

[参考题解：动态规划解题套路框架 :: labuladong的算法小抄 (gitee.io)](https://labuladong.gitee.io/algo/1/7/)

给你 `k` 种面值的硬币，面值分别为 `c1, c2 ... ck`，每种硬币的数量无限，再给一个总金额 `amount`，问你**最少**需要几枚硬币凑出这个金额，如果不可能凑出，算法返回 -1 。

比如说 `k = 3`，面值分别为 1，2，5，总金额 `amount = 11`。那么最少需要 3 枚硬币凑出，即 11 = 5 + 5 + 1。

你认为计算机应该如何解决这个问题？显然，就是把所有可能的凑硬币方法都穷举出来，然后找找看最少需要多少枚硬币。

```c++
// 暴力递归
class Solution {
public:
    int coinChange(vector<int>& coins, int amount) {
        // base case
        if(amount == 0) return 0;
        if(amount < 0) return -1;
        int result = INT_MAX;
        for(auto coin : coins) {
            // 计算子问题的结果
            int subproblem = coinChange(coins, amount - coin);
            // 子问题无解则跳过
            if(subproblem == -1) continue;
            // 在子问题中选择最优解，然后硬币个数+1
            result = min(result, subproblem + 1);
        }
        return result == INT_MAX ? -1 : result;
    }
};

// 带备忘录的递归
class Solution {
public:
    int coinChange(vector<int>& coins, int amount) {
        vector<int> dp(amount + 1, INT_MAX); 
        return helper(coins, amount, dp);
    }
private:
    int helper(vector<int>& coins, int amount, vector<int>& dp) {
        // base case
        if(amount == 0) return 0;
        if(amount < 0) return -1;
        // 子问题有记录则直接返回
        if(dp[amount] != INT_MAX) return dp[amount];
        int result = INT_MAX;
        for(auto coin : coins) {
            // 计算子问题的结果
            int subproblem = helper(coins, amount - coin, dp);
            // 子问题无解则跳过
            if(subproblem == -1) continue;
            // 在子问题中选择最优解，然后硬币个数+1
            result = min(result, subproblem + 1);
        }
        // 记录子问题的结果
        dp[amount] = result == INT_MAX ? -1 : result;
        return dp[amount];
    }
};

// dp数组
class Solution {
public:
    int coinChange(vector<int>& coins, int amount) {
        vector<int> dp(amount + 1, amount + 1); //初始化为amount+1 > amount,而初始化为INT_MAX,dp[i - coin] + 1会溢出
        dp[0] = 0;  // base case
        for(int i = 1; i <= amount; i++) { // 外层for循环在遍历所有状态的所有取值
            for(int coin : coins) { // 内层for循环在求所有选择的最小值
                if(i - coin < 0) continue; 
                dp[i] = min(dp[i], dp[i - coin] + 1);
            }
        }
        return dp[amount] = dp[amount] == amount + 1 ? -1 : dp[amount];
    }
};
```

### 2.最长递增子序列

**经典题目**

[300. 最长递增子序列 - 力扣（Leetcode）](https://leetcode.cn/problems/longest-increasing-subsequence/description/)

1.`dp[i]`的定义

**dp[i]表示i之前包括i的以nums[i]结尾最长上升子序列的长度**

2.状态转移方程

位置`i`的最长升序子序列等于`j`从`0`到`i-1`各个位置的最长升序子序列 + 1 的最大值。

所以：`if (nums[i] > nums[j]) dp[i] = max(dp[i], dp[j] + 1)`;

**注意这里不是要dp[i] 与 dp[j] + 1进行比较，而是我们要取dp[j] + 1的最大值**。

3.`dp[i]`的初始化

每一个`i`，对应的`dp[i]`（即最长上升子序列）起始大小至少都是1.

4.确定遍历顺序

`dp[i]` 是有`0`到`i-1`各个位置的最长升序子序列 推导而来，那么遍历i一定是从前向后遍历。

<img src="E:\Postgraduate\Study\Leetcode\images\7.jpeg" alt="7" style="zoom:50%;" />

```c++
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        int n = nums.size();
        vector<int> dp(n + 1, 1);   // base case
        for(int i = 0; i < n; i++) {
            for(int j = 0; j < i; j++) {
                if(nums[i] > nums[j]) {
                    dp[i] = max(dp[i], dp[j] + 1);
                }
            }
        }
        return *max_element(dp.begin(), dp.end());
    }
};
```



# C++杂项

#### **find**()方法

不同于map、set对应的六种数据类型有find方法，vector本身是没有find这一方法，其find是依靠algorithm来实现的。

对于set、map类型：

```c++
set<T> st; //declaration
set<T>::iterator it; //iterator declaration
it=st.find( const T item); //find
```

对于vector类型，依靠algorithm来实现：

```c++
vector<int>::iterator it = find(vec.begin(), vec.end(), num)
```

#### count()方法

**map**

**map::count()**是C++ STL中的内置函数，**如果在映射容器中存在带有键K的元素，则该函数返回1**。如果容器中不存在键为K的元素，则返回0。

**用法:**

```c++
map<T>::iterator it = map_name.count(key k)
```

**参数：**该函数接受强制性参数k，该参数指定要在Map容器中搜索的键。

**返回值：**该函数返回键K在Map容器中的出现次数。如果 key 存在于容器中，则返回1，因为映射仅包含唯一 key 。如果键在Map容器中不存在，则返回0。

**set**

**set::count()**是C++ STL中的内置函数，它返回元素在集合中出现的次数。**由于set容器仅包含唯一元素，因此只能返回1或0**。

**用法:**

```c++
set<T>::iterator it = set_name.count(element) 
```

**参数：**该函数接受一个强制性参数element ，该元素指定要返回其计数的元素。

**返回值：**该函数返回1或0，因为该集合仅包含唯一元素。如果设置的容器中存在该值，则返回1。如果容器中不存在它，则返回0。

**vector**

vector**本身是没有count()这一方法**，其find是依靠algorithm来实现的。

```c++
int myints[] = {10,20,30,30,20,10,10,20};   // 8 elements
std::vector<int> myvector (myints, myints+8);
// counting elements in container:
int mycount = std::count (myvector.begin(), myvector.end(), 20);
```

#### count_if()方法

C++ 函数 **std::algorithm::count_if()** 返回满足条件的范围中值的出现次数。

```c++
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

bool predicate(int n) {
   return (n > 3);
}

int main(void) {
   vector<int> v = {1, 2, 3, 4, 5};
   int cnt;

   cnt = count_if(v.begin(), v.end(), predicate);

   cout << "There are " << cnt << " numbers are greater that 3." << endl;

   return 0;
}
```

让我们编译并运行上面的程序，这将产生以下结果 −

```c++
There are 2 numbers are greater that 3.
```

[]: 
