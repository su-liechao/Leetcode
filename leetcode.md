# leetcode笔记

## 二叉树

<img src="https://img-blog.csdnimg.cn/20210219190809451.png" alt="二叉树大纲" style="zoom:50%;" />

### 一、二叉树的理论基础

#### 1.二叉树的种类

[二叉树种类](https://www.programmercarl.com/%E4%BA%8C%E5%8F%89%E6%A0%91%E7%90%86%E8%AE%BA%E5%9F%BA%E7%A1%80.html#%E4%BA%8C%E5%8F%89%E6%A0%91%E7%9A%84%E7%A7%8D%E7%B1%BB)

1. 满二叉树
2. 二叉搜索树
3. 平衡二叉树
4. 平衡二叉搜索树

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

[力扣题目链接(opens new window)](https://leetcode.cn/problems/find-bottom-left-tree-value/)

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



### 二叉树总结

<img src="https://code-thinking-1253855093.file.myqcloud.com/pics/20211030125421.png" alt="img" style="zoom:150%;" />
