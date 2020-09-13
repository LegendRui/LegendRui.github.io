# LeetCode刷题记录

## 打家劫舍
[链接](https://leetcode-cn.com/problems/house-robber/)

**思路：（动态规划）**
建立数组`benifit`，元素`benefit[i]`表示小偷偷到第i家房屋能获得的最大收益。由于需要间隔着偷，有两种选择：
+ 偷第`i`家，则收益为`benifit[i - 2] + money[i]`
+ 不偷第`i`家，则收益为`benifit[i - 1]`

为窃取最多金额，取二者最大。因此动态规划的转移方程为：
`benifit[i] = max(benifit[i-2] + money[i], benifit[i-1])`

```
int rob(int* nums, int numsSize){
    if (nums == NULL || numsSize == 0) {
        return 0;
    }
    if (numsSize < 2) {
        return nums[0];
    }

    int *benefit = (int *)malloc(sizeof(int) * numsSize);
    benefit[0] = nums[0];
    benefit[1] = nums[0] > nums[1] ? nums[0] : nums[1];
    for (int i = 2; i < numsSize; i++) {
        int choose = benefit[i - 2] + nums[i];
        int unchoose = benefit[i - 1];
        benefit[i] = choose > unchoose ? choose : unchoose;
    }
    return benefit[numsSize - 1];
}
```

## 最大子序和
[链接](https://leetcode-cn.com/problems/maximum-subarray/)
**思路：（动态规划）**
建立数组`dp`,`dp[i]`表示第`i`个元素被选中参与构建最大子序中所能获得的最大的和。
`dp[i]`有两种可能：
+ 与前一个元素一起参与构建最大子序列，此时`dp[i] = dp[i-1] + nums[i]`
+ 自己作为最大子序列的起始位置，此时`dp[i] = nums[i]`

因此，转移方程为：
`dp[i] = max(dp[i-1]+nums[i], nums[i])`

最大子序和即为数组`dp`中的最大元素。

```
int myMax(int a, int b) {
    return a > b ? a : b;
}

int maxSubArray(int* nums, int numsSize){
    if (nums == NULL || numsSize == 0) {
        return 0;
    }

    if (numsSize < 2) {
        return nums[0];
    }

    int *dp = (int *)malloc(sizeof(int) * numsSize);
    dp[0] = nums[0];
    for (int i = 1; i <numsSize; i++) {
        dp[i] = myMax(dp[i - 1] + nums[i], nums[i]);
        }

    int max = dp[0];
    for (int i = 0; i < numsSize; i++) {
        max = myMax(max, dp[i]);
    }
    return max;
}
```

## 判断子序列
[链接](https://leetcode-cn.com/problems/is-subsequence/)

**思路：**
指针`s`和`t`是指向两个字符串的指针。若二者指向的字符相同，即：`*s==*t`。则二者同时自增1，否则只将t自增1。
如果`s`能遍历完所指向的字符串（即最后`*s='\0'`），则`s`是`t`的子序列，否则不是。
```
bool isSubsequence(char * s, char * t){
    if (s == NULL || t == NULL) {
        return false;
    }
    while (*s != '\0' && *t != '\0') {
        if (*s == *t) {
            s++;
        }
        t++;
    }
    if (*s == '\0') {
        return true;
    } else {
        return false;
    }
}
```

## 买卖股票的最佳时机II
[链接](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-ii/)

**思路（动态规划）：**
建立数组`bef`，`bef[i]`表示第i天能获得的最大利润。有两种情况：
+ `prices[i] > prices[i -1`时，`bef[i]=bef[i-1]+prices[i]-prices[i-1]`
+ `prices[i]<=prices[i-1]时`，`bef[i]=bef[i-1]`

```
int maxProfit(int* prices, int pricesSize){
    if (prices == NULL || pricesSize < 2) {
        return 0;
    }
    int *bef = (int *)malloc(sizeof(int) * pricesSize);
    bef[0] = 0;
    for (int i = 1; i < pricesSize; i++) {
        if (prices[i] > prices[i-1]) {
            bef[i] = bef[i-1] + prices[i] - prices[i-1];
        } else {
            bef[i] = bef[i-1];
        }
    }
    return bef[pricesSize-1];
}
```

## 分发饼干
[链接](https://leetcode-cn.com/problems/assign-cookies/)

**思路：（优先级队列）**
将孩子的胃口值数组`g`和饼干尺寸数组`s`升序排列。如果`g[gHead] < s[sHead]`，则说明最小尺寸饼干足以满足最小胃口值，二者同时出队列；否则最小尺寸的饼干出队列（因为这块饼干无用，不足以满足现有队列中任意一个孩子的胃口）。胃口值队列出列的次数，即为能满足的最大孩子数。

```
int cmp(const void* a, const void* b) {
    return *(int *)a - *(int *)b;
}

int findContentChildren(int* g, int gSize, int* s, int sSize){
    if (g == NULL || gSize == 0 || sSize == NULL || sSize == 0) {
        return 0;
    }
    qsort(g, gSize, sizeof(int), cmp);
    qsort(s, sSize, sizeof(int), cmp);

    int gHead, sHead;
    gHead = sHead = 0;
    while (gHead < gSize && sHead < sSize) {
        if (g[gHead] <= s[sHead]) {
            gHead++;
        }
        sHead++;
    }

    return gHead;
}
```

## 二叉树的最小深度
[链接](https://leetcode-cn.com/problems/minimum-depth-of-binary-tree/)

**思路：（递归）**
递归求取左子树（如果存在）和右子树（如果存在）的最小深度。
```
int minDepthCore(struct TreeNode* root, int curDepth) {
    if (root->left == NULL && root->right == NULL) {
        return curDepth;
    } 
    if (root->left != NULL && root->right != NULL) {
        int minLeftDepth = minDepthCore(root->left, curDepth + 1);
        int minRightDepth = minDepthCore(root->right, curDepth + 1);
        return minLeftDepth < minRightDepth ? minLeftDepth : minRightDepth;
    } 
    if (root->left != NULL) {
        return minDepthCore(root->left, curDepth + 1);
    }
    return minDepthCore(root->right, curDepth + 1);
}
int minDepth(struct TreeNode* root){
    if (root == NULL) {
        return 0;
    }
    return minDepthCore(root, 1);
}
```

以上代码借助函数调用时的栈实现的，也可自己模拟栈实现：
```
#define MAX_NODE_NUM 256
int minDepth(struct TreeNode* root){
    if (root == NULL) {
        return 0;
    }
    // return minDepthCore(root, 1);
    struct TreeNode *stackKey[MAX_NODE_NUM] = {NULL};
    int *stackValue[MAX_NODE_NUM];
    int stackTop = 0;
    stackKey[stackTop] = root;
    stackValue[stackTop++] = 1;
    int depth = 1, min = INT_MAX;
    while (stackTop > 0) {
        struct TreeNode *node = stackKey[stackTop - 1];
        int depth = stackValue[--stackTop];
        if (node->left == NULL && node->right==NULL) {
            min = min < depth ? min : depth;
        } else {
            if (node->left != NULL) {
                stackKey[stackTop] = node->left;
                stackValue[stackTop++] = depth + 1;
            }
            if (node->right != NULL) {
                stackKey[stackTop] = node->right;
                stackValue[stackTop++] = depth + 1;
            }
        }
    }
    return min;
}
```

## 相同的树
[链接](https://leetcode-cn.com/problems/same-tree/)

**思路：**
在两棵树中同时遍历：如果两个节点同为NULL，认为这两个节点等同；否则，如果这两个节点的值相等且左右子树相同，也认为这两个节点等同；否则，认为这两个节点不等同，树不相同。
```
bool isSameTree(struct TreeNode* p, struct TreeNode* q){
    if (p == NULL && q == NULL) {
        return true;
    }
    if (p != NULL && q != NULL) {
        return (p->val == q->val) && isSameTree(p->left, q->left) && isSameTree(p->right, q->right); 
    } else {
        return false;
    }
}
```

## 最近的请求次数
[链接](https://leetcode-cn.com/problems/number-of-recent-calls/)
**思路：（优先队列）**
构建降序的优先队列，每次来一个ping，将ping的时刻放进队列中，并剔除栈中距离此时刻大于3000ms的元素。所需要的返回值就是栈的长度。

```
typedef struct {
    int *queue;
    int tail;
} RecentCounter;

#define MAX_QUEUE_LEN 10001

RecentCounter* recentCounterCreate() {
    RecentCounter *rc = (RecentCounter *)malloc(sizeof(RecentCounter));
    rc->queue = (int *) malloc(sizeof(int) * MAX_QUEUE_LEN);
    memset(rc->queue, 0, sizeof(int) * MAX_QUEUE_LEN);
    rc->tail = 0;
    return rc;
}

int cmp(const void* p1, const void* p2) {
    int *pi1 = (int*)p1;
    int *pi2 = (int*)p2;
    return (*pi2 - *pi1);
}

int recentCounterPing(RecentCounter* obj, int t) {
    obj->queue[obj->tail] = t;
    (obj->tail)++;
    qsort(obj->queue, obj->tail, sizeof(int), cmp);
    for (int i = 0; i < obj->tail; i++) {
        if (t - obj->queue[i] > 3000) {
            obj->tail = i;
            break;
        }
    }
    return obj->tail;
}

void recentCounterFree(RecentCounter* obj) {
    free(obj->queue);
    obj->queue = NULL;
    free(obj);
    obj = NULL;
}
```

## 二叉树的层序遍历
[链接](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/)

**思路：（队列）**
从根节点开始遍历二叉树，并将遍历到的每个节点的左右子节点（如果存在）也放到队列中。遍历的同时记录下一层的起始节点。