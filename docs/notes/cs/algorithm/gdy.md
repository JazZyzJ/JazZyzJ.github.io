---
comment: true
---


# Greedy Algotithms

## Intro 

解决最优问题:

- 优化问题：给定一组**约束条件**(constraints)，和一个**优化函数**(optimization function)，称满足约束条件的解为**可行解**(feasible solution)，称使得优化函数取得最优值的可行解为**最优解**(optimal solution)。
- 贪心算法：通过一系列局部最优的选择，期望能够导致全局最优解。
    - 贪心算法通常是自顶向下的，也就是说当前作出的决策在后面是不允许改变的，所以要确保每一步的选择都是最优的。
    - 只有当**局部最优**=**全局最优**，贪心算法才能保证得到最优解。
    - 很多时候贪心找不到最优解，但是可以用贪心找到一个局部优解作为剪枝的参照物

>贪心算法的概念比较简单，但是yy哥说过，往往定义简单的东西应用起来会比较难，因为缺少固定的范式，但是在算法研究中，贪心法有着广泛的使用，尤其是在人工智能算法中求解组合优化时十分常见。

## Example



### Activity Selection

!!! question "问题描述"
	
	<span style="font-size: 1.1em">
	    给定一组活动集$S = \{a_1, a_2, \cdots, a_n\}$，每个活动$a_i$都有一个开始时间$s_i$和一个结束时间$f_i$，已经保证了这些活动按照结束时间排序，即$f_1 \leq f_2 \leq \cdots \leq f_n$。现在要求选出一个最大的相互兼容的活动子集。
	</span>

我们先来用DP的方法解决这个问题：

!!! note "DP Solution"

	<span style="font-size: 1.1em">
		DP的思路就是一种类似遍历的思路，定义$c_{ij}$为活动$a_i$和$a_j$之间的最大兼容活动子集的大小，那么有状态转移方程：
	</span>

	$$
	c_{ij} = \max_{ak \in S} (c_{ik} +  c_{kj} + 1, c_{ij})
	$$

	此时我们可以分析时间复杂度是$O(n^3)$，因为需要遍历$i, j, k$三个变量。

	??? quote "代码"

	    ```cpp
        vector<cevtor<int>> c(n, vector<int>(n, 0));

		for (int len = 2; len <= n; len++>) {
			for (int i = 0; i <= n - len; i++) {
				int j = i + len - 1;
				c[i][j] = 0;
				for(int k = i+1; k < j; k++) {
					if (s[k] >= f[i] && f[k] <= s[j]) {// 活动k在活动i和活动j之间
						c[i][j] = max(c[i][j], c[i][k] + c[k][j] + 1);
					}
				}
			}
		}
		```

	<span style="font-size: 1.1em">
		所以我们需要找到一个更好的算法，这里给出的贪心算法是选择最早结束的活动（这里分别否定了选择最早开始、选择最短活动、选择最少冲突活动的方法，找到反例的方法我认为还是比较简单的，但是为什么选择最早结束的活动是正确的呢，这里需要给出证明）。
	</span>

	!!! tip "交换参数证明法"
		在非空子问题$S_k$中，令$A_k$为最优解，$a_m$是$S_k$中最早结束的活动，那么$a_m$在$A_k$中。

		- 令$A_k$为最优解，$a_{ef}$是$A_k$中最早结束的活动
		- 如果$a_m = a_{ef}$，则$a_m$在$A_k$中，否则
		- 用$a_m$替换$a_{ef}$，得到$A_k'$，因为结束时间$f_m \leq f_{ef}$，所以$A_k'$也是$S_k$的最优解，得证。
    
	<br>

	<span style="font-size: 1.1em">
		经过证明后我们使用贪心算法优化DP，重新得到状态转移方程：
	</span>

	$$
	c_{1,j} = \begin{cases}
		1 & \text{if } j = 1 \\
		\max(c_{1,j-1}, c_{1,k(j)} + 1) & \text{if } j > 1
	\end{cases}
	$$

	<span style="font-size: 1.1em">
	    这里我们对从第一个活动到第$j$个活动这个范围进行遍历，找到在第$j$个活动开始前结束的活动$a_{k(j)}$，然后进行判断：不选择$a_j$，则$c_{1,j} = c_{1,j-1}$，选择$a_j$，则$c_{1,j}$就是在$a_j$活动开始前结束的活动$a_{k(j)}$加上$a_j$。（这里$k(j)$是$j$的前一个活动，所以$a_{k(j)}$也就是一个最优子结构）
	</span>

	??? quote "优化代码"

		先对$k(j)$进行预处理，然后进行DP：
		```cpp

		struct Activity {
			int start, finish;
		};

		int activity_selector(const vector<Activity>& activities) {
			int n = activities.size();
			vector<int> dp(n, 0);
			vector<int> k(n, -1);

			// 预处理k(j)
			for(int j = 1; j < n; j-- ) {
				for(int i = n-1; i >= 0; i--) {
					if (activities[i].finish <= activities[j].start) {
						k[j] = i;
						break;
					}
				}
			}
		

		dp[0] = 1;

		for(int j = 1; j < n; j++) {
				dp[j] = dp[j-1]; // 不选择a_j
				if(k[j] != -1) {
					dp[j] = max(dp[j], dp[k[j]] + 1); // 选择a_j
				} else {
					dp[j] = max(dp[j], 1); // 没有兼容的活动，选择自己
				}
			}
		}

		return dp[n-1];

		```
	<span style="font-size: 1.1em">
		此时如果使用二分查找确定$k(j)$，时间复杂度可以优化到$O(n \log n)$。
	</span>



## Design 

- 贪心算法与动态规划区别在于：
	- 动态规划是自底向上的，而贪心算法是自顶向下的。
	- 对每个子问题的解决方案都做出选择，不能回退。动态规划则会保存以前的运算结果，并根据以前的结果对当前进行选择，有回退功能。

!!! success "常见方法"

	<span style="font-size: 1.1em">
		在OIWiki中贪心算法有两种常见解法：
	</span>

	<span style="font-size: 1.1em">
		1. 邻项交换法
	</span>

	<span style="font-size: 1.1em">
		2. 后悔法
	</span>

	<span/>

邻项交换的方法我并不是很理解，这里给出OIWiki的例题讲解：[国王游戏](https://oi-wiki.org/basic/greedy/#%E9%82%BB%E9%A1%B9%E4%BA%A4%E6%8D%A2%E6%B3%95%E7%9A%84%E4%BE%8B%E9%A2%98)

这里给出后悔法的讲解，因为课上并没有提及。

1. 后悔法先按照某种策略做出选择
2. 如果发现当前选择不是最优的，则进行反悔，选择其他策略

!!! question "Work Scheduling"

	约翰有太多的工作要做。为了让农场高效运转，他必须靠他的工作赚钱，每项工作花一个单位时间。 他的工作日从0时刻开始，有$10^9$个单位时间。在任一时刻，他都可以选择编号1到N的工作中的一项。因为他在每个单位时间里只能做一个工作，而每项工作又有一个截止日期，所以他很难有时间完成所有N个工作。对于第$i$个工作有一个截止时间$D_i$，如果他可以完成这个工作，可以获利$P_i$。

	现在要求求出约翰可以获得的最大利润。

!!! success "题解"
	1. 默认策略为所有工作都做，将各项工作按照截止时间排序
	2. 当前时间未超过截止时间，就加入当前工作
	3. 当前时间超过截止时间且当前工作价值大于堆顶工作价值，则放弃堆顶工作，选择当前工作

	??? quote "代码"

		```cpp
		struct Work {
			int d, p;
		}a[10005];

		bool cmp(Work a, Work b) {
			return a.d < b.d;
		}

		priority_queue<int, vector<int>, greater<int>> q;

		int main() {
			int n;
			cin >> n;
			for(int i = 1; i <= n; i++) {
                cin >> a[i].d >> a[i].p;
			}

			sort(a+1, a+n+1, cmp);
			int ans = 0;
			for(int i = 1; i <= n; i++) {
				if(a[i].d <= (int)q.size()) {// 当前时间超过截止时间
					if(q.top() < a[i].p) {// 当前工作价值大于堆顶工作价值
						ans += a[i].p - q.top();// 反悔
						q.pop();
						q.push(a[i].p);
					}
				} else {// 当前时间未超过截止时间
					ans += a[i].p;
					q.push(a[i].p);
				}
			}
			cout << ans << endl;
			return 0;
		}
		```
