# Speedbridge

> 蓝桥杯训练讲义

## 回溯及搜索相关技巧详解

**时间：2021年1月24日**

### 回溯算法的简介

简单来说：**回溯/搜索是蓝桥杯常考算法之一，掌握了回溯/搜索基本能进省二及以上**。其基本实现是通过递归保存程序状态，然后再根据当前状态一步步探索出所有可能解。

回溯算法实际上一个类似枚举的搜索尝试过程，主要是在搜索尝试过程中寻找问题的解，当发现已不满足求解条件时，就 “回溯” 返回，尝试别的路径。回溯法是一种选优搜索法，按选优条件向前搜索，以达到目标。但当探索到某一步时，发现原先选择并不优或达不到目标，就退回一步重新选择，这种走不通就退回再走的技术为回溯法，而满足回溯条件的某个状态的点称为 “回溯点”。许多复杂的，规模较大的问题都可以使用回溯法，有**“通用解题方法”**的美称。

回溯算法的基本思想是：从一条路往前走，能进则进，不能进则退回来，换一条路再试。 ——来源：力扣（LeetCode）

### 回溯问题的引入

1. [P1036 [NOIP2002 普及组] 选数](https://www.luogu.com.cn/problem/P1036)
2. [P1025 [NOIP2001 提高组] 数的划分](https://www.luogu.com.cn/problem/P1025)

### 回溯解题技巧起步

#### 解答树

解答树又称为状态树，是入门回溯必不可少的一种解题技巧。它主要是用来解决递归过程中递归状态难以确定的问题。

使用解答树确定状态的另一个目的是防止我们想知道递归过程是如何进行的。注意：**不要深究递归程序是如何运行的，我们只用知道这里用递归是为了解决什么问题即可**。（比如汉诺塔问题）

##### 画出一颗解答树

**画出解答树的关键是清楚"我们目前有哪些选择"**，一个生活中的例子：去食堂还是去宿舍。如下图，我们将不断决策的过程画成树的形状就得到了一颗解答树。

在现实生活中，我们做出的大部分选择都是无法返回的，在回溯程序中则正好相反，我们可以将所有选择都尝试一遍，最后得出最优解。可以这样做的原因是因为我们要到了“递归”，即利用递归将每次选择的状态保存下来。

![解答树例子](https://gitee.com/hwz-magicweb/speedbridge/raw/master/images/解答树例子.jpg)

##### 例子

题目：给出一个数target，求1~target中选出若干个数相加组合成target能有几种方案。注意：**每个数字只能选一次，要求输出方案且方案不能重复**。

例如：target = 3, 可行的方案有：[1, 2], [3]，因为1 + 2 = 3, 3 = 3。

思路（简略版）：

当target为3时，我们第一次可以选的数有[1, 2, 3]；

如果选1，接下来还能选[2, 3]；

​	如果选2，答案满足，可以直接返回。

​	如果选3:，不满足答案，直接返回。

依次类推可以得到下面的解答树：

![解答树例题](https://gitee.com/hwz-magicweb/speedbridge/raw/master/images/解答树例题.jpg)

代码：（建议在学习下面的内容后再来思考为什么要这样做）

```cpp
#include <iostream>
#include <algorithm>
#include <vector>
#define endl '\n'
using namespace std;
void dfs(int level, int deepth, int target, vector<int >& vec) {
	if (!target) {
		for (int i = 0; i < vec.size(); i++) cout << vec[i] << ' ';
		cout << endl;
		return;
	}
	for (int i = level; i <= deepth; i++) {
		if (target - i < 0) continue;
		vec.push_back(i);
		dfs(i + 1, deepth, target - i, vec);
		vec.pop_back();
	}
}
int main() {
	ios::sync_with_stdio(false), cin.tie(0), cout.tie(0);
	int target;
	cin >> target;
	vector<int > vec;
	dfs(1, target ,target, vec);
	return 0;
}
```

#### 两种题型与模板

回溯算法和大部分常用算法一样，有着固定的写法。在学习回溯的过程中，解答树只是用来帮助我们理解问题的状态的构成，并不能实际的解决我们的问题，随着刷题量的增长，我们会发现大部分题目只需要套上模板，然后修改一下就行。下面给出几种常用的回溯模板，**如果我们能够熟练运用这些模板，也就彻底掌握了回溯算法**。

注意：此处的“模板”并不是直接套上去就行的，我们往往要对模板的参数部分进行修改，换句话说就是**将我们要表示的状态通过参数传递进去**。

##### 非子集型问题

简单来说，**非子集型问题的特征就是答案只在解答树的叶子节点出现**。上面举的例子就是非子集型问题。

例题：

1. [P1036 [NOIP2002 普及组] 选数](https://www.luogu.com.cn/problem/P1036)，回溯入门题。
2. [P1025 [NOIP2001 提高组] 数的划分](https://www.luogu.com.cn/problem/P1025)，和组合总和 III一样的题，但数据范围更大，回溯算法可以拿80分。
3. [39. 组合总和](https://leetcode-cn.com/problems/combination-sum/)，单个选项可以无限选择的题。
4. [216. 组合总和 III](https://leetcode-cn.com/problems/combination-sum-iii/)，单个选项只能一次选择，但限制了选择个数。

变量含义

1. level：表示当前处于解答树的那一层。
2. deepth：表示解答树最高有多少层，绝大部分问题的解答树的高度就是有几种选择。比如上面的问题，它的解答树最高就有target层。

```cpp
void dfs(int level, int deepth) {
	// 如果这一次递归的层数已经大于或等于解答树的层次了，
    // 就说明当前已经处于解答树的叶子节点了，此时就可以统计答案了
    if (level >= deepth) {
        return;
    }
    // 对解答树进行遍历
	for (int i = level; i < deepth; i++) {
        // 如果每个选项只能选一次，使用这种方式
		dfs(i + 1, deepth);
        // 如果每个选择可以无限选取，使用这种方式
        dfs(i, deepth);
	}
}
```

##### 子集型问题

简单来说，答案是解答树上的路径的问题就是子集问题。

为了方便理解，请看下面的例子：

题目：求出集合{1, 2, 3}的所有子集。

首先我们可以得到下面这个解答树，我们可以发现，如果按照上面提到的叶子节点才是答案只能得到{3}, {2}这两个集合。而如果我们将红蓝两条路径上经过的所有节点都记录下来才能得到正确的集合。即**子集型问题的答案是整条路径，而不是叶子节点。**

![解答树子集例子](https://gitee.com/hwz-magicweb/speedbridge/raw/master/images/解答树子集例子.jpg)

例题：

1. [78. 子集](https://leetcode-cn.com/problems/subsets/)，子集型问题入门题。
2. [1239. 串联字符串的最大长度](https://leetcode-cn.com/problems/maximum-length-of-a-concatenated-string-with-unique-characters/)，在求子集的同时加入了字符串去重。

变量含义：

1. level：表示当前处于解答树的那一层。
2. deepth：表示解答树最高有多少层，绝大部分问题的解答树的高度就是有几种选择。比如上面的问题，它的解答树最高就有target层。

```cpp
void dfs(int level, int deepth) {
	// 在这里添加答案
    ADD_RESULT();
    
    // 对解答树进行遍历
	for (int i = level; i < deepth; i++) {
		dfs(i + 1, deepth);
	}
}
```

在这里我们可以对比下子集型问题和非子集型问题的模板有什么不同：

首先我们去掉了```if (level >= deepth) {return;}```，并将记录答案的代码直接写在了原来判断是否到达了叶子节点的地方。这是因为前面提到的，我们的答案是整条路径上的，而不仅仅是叶子节点上的。另外我们也去掉了```dfs(i, deepth);```	这种可以无限选择同一个选项的写法，这是因为集合的定义之一就是“没有重复元素”，子集型问题自然也就不会出现可以重复选择的情况。

#### 剪枝优化

剪枝简单来说就是如果当前节点的状态是不合法的，程序直接返回，不进行更深层次的递归。对于绝大多数题目而言剪枝是必须的，这是因为回溯算法的时间复杂度非常高，不加任何优化很可能就会超时。

##### 可行性剪枝

就是判断当前答案是否合法，比如算和出现大于目标值的答案，一定程度上能加快DFS的速度。

##### 最优性剪枝

比如算最短步数出现了更长的答案，它适用于各种求最值的情况。

### 例题及AC代码

[[P1036 [NOIP2002 普及组] 选数](https://www.luogu.com.cn/problem/P1036)

```cpp
#include <iostream>
#include <algorithm>
#include <vector>
#define endl '\n'
using namespace std;

const int N = 20 + 10;
int nums[N];
int cnt = 0;

bool isPrime(int n) {
	for (int i = 2; i <= n / i; i++) {
		if (!(n % i)) return false;
	}
	return true;
}

void dfs(int level, int deepth, int k, int sum) {
	if (!k) {
		if (isPrime(sum)) cnt++;
		return;
	}
	for (int i = level; i < deepth; i++) {
		dfs(i + 1, deepth, k - 1, sum + nums[i]);
	}
}

int main() {
	ios::sync_with_stdio(false), cin.tie(0), cout.tie(0);
	int n, k;
	cin >> n >> k;
	for (int i = 0; i < n; i++) cin >> nums[i];
	dfs(0, n, k, 0);
	cout << cnt << endl;
	return 0;
}
```

[P1025 [NOIP2001 提高组] 数的划分](https://www.luogu.com.cn/problem/P1025)

```cpp
#include <iostream>
#define endl '\n'
using namespace std;
int cnt = 0;

void dfs(int level, int deepth, int target, int k, string path) {
	if (k < 0) return;
	if (!target && !k) {
		// cout << path << endl;
		cnt++;
		return;
	}
	for (int i = level; i < deepth; i++) {
		if (target - i < 0) continue;
		dfs(i, deepth, target - i, k - 1, path + char(i + '0'));
	}
	
}

int main() {
	ios::sync_with_stdio(false), cin.tie(0), cout.tie(0);
	int n, k;
	cin >> n >> k;
	dfs(1, n, n, k, "");
	cout << cnt << endl;
	return 0;
}
```

[39. 组合总和](https://leetcode-cn.com/problems/combination-sum/)

```cpp
class Solution {
public:
    vector<vector<int>> res;
    vector<int > temp, nums;
    void dfs(int level, int deepth, int target) {
        if (target <= 0) {
            if (target == 0) res.push_back(temp);
            return;
        }
        for (int i = level; i < deepth; i++) {
            // 进行剪枝
            if (target - nums[i] < 0) continue;
            temp.push_back(nums[i]);
            dfs(i, deepth, target - nums[i]);
            temp.pop_back();
        }
    }
    vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
        this->nums = candidates;
        dfs(0, candidates.size(), target);
        return res;
    }
};
```

[216. 组合总和 III](https://leetcode-cn.com/problems/combination-sum-iii/)

```cpp
class Solution {
public:
    vector<vector<int > > vec;
    void dfs(int level, int deepth, vector<int >& temp, int n, int k) {
        if (k < 0) return;
        if (!n && !k) {
            vec.push_back(temp);
            return;
        }
        for (int i = level; i <= 9; i++) {
            if (n - i < 0) continue;
            temp.push_back(i);
            dfs(i + 1, deepth, temp, n - i, k - 1);
            temp.pop_back();
        }
    }
    vector<vector<int>> combinationSum3(int k, int n) {
        vector<int > temp;
        dfs(1, 9, temp, n, k);
        return this->vec;
    }
};
```

[78. 子集](https://leetcode-cn.com/problems/subsets/)

```cpp
class Solution {
public:
    vector<vector<int>> res;
    vector<int > temp, nums;
    void dfs(int level, int deepth) {
        res.push_back(temp);
        for (int i = level; i < deepth; i++) {
            temp.push_back(nums[i]);
            dfs(i + 1, deepth);
            temp.pop_back();
        }
    }
    vector<vector<int>> subsets(vector<int>& nums) {
        this->nums = nums;
        dfs(0, nums.size());
        return res;
    }
};
```

[1239. 串联字符串的最大长度](https://leetcode-cn.com/problems/maximum-length-of-a-concatenated-string-with-unique-characters/)

```cpp
class Solution {
public:
    vector<string> arr;
    int res = 0;
    int isUnique(string str) {
        int bucket[26], sLen = str.size();
        memset(bucket, 0, sizeof bucket);
        for (int i = 0; i < sLen; i++) {
            if (bucket[str[i] - 'a'] >= 1) return 0;
            bucket[str[i] - 'a']++;
        }
        return sLen;
    }
    void dfs(int level, int deepth, string str) {
        res = max(isUnique(str), res);
        for (int i = level; i < deepth; i++) {
            dfs(i + 1, deepth, str + arr[i]);
        }
    }
    int maxLength(vector<string>& arr) {
        this->arr = arr;
        dfs(0, arr.size(), "");
        return this->res;
        return 0;
    }
};
```
