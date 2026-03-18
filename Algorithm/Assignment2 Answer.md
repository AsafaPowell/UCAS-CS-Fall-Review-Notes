# AI协作题（1）
## 前缀和⾮负的排序数

### Answer1
```python

n  = int(input())

f = [[0] * (2 * n + 2) for _ in range(2 * n + 2)]

f[0][0] = 1

for i in range(1, 2 * n + 1) :
    for j in range((i + 1) // 2, i + 1) :
        f[i][j] = f[i - 1][j]
        if 2 * (j - 1) >= i - 1 :
            f[i][j] += f[i - 1][j - 1]
f[0][0] = 0
print(f[2 * n][n])
```

### Answer2
```python
def main():
    n = int(input().strip())

    if n == 0:
        print(0)
        return

    # 计算组合数 C(2n, n)
    result = 1
    for i in range(1, n + 1):
        result = result * (2 * n - i + 1) // i

    # 计算卡特兰数 C_n = C(2n, n) / (n + 1)
    catalan = result // (n + 1)
    print(catalan)


if __name__ == "__main__":
    main()

```

### Answer3
```python
import math

n = int(input())
if n == 0:
    print(0)
else:
    # 计算组合数 C(2n, n)
    comb = math.comb(2 * n, n)
    # 计算卡特兰数
    catalan = comb // (n + 1)
    print(catalan)
```

### Answer4
```python
def prod(a, b):
    if a > b:
        return 1
    if a == b:
        return a
    if b - a == 1:
        return a * b
    mid = (a + b) // 2
    return prod(a, mid) * prod(mid + 1, b)

def comb(n, k):
    if k < 0 or k > n:
        return 0
    k = min(k, n - k)
    num = prod(n - k + 1, n)
    den = prod(1, k)
    return num // den

def catalan(n):
    if n == 0:
        return 0
    return comb(2 * n, n) // (n + 1)

def main():
    data = input()
    n = int(data)
    print(catalan(n))

if __name__ == "__main__":
    main()
```

### Answer5
```python
def main():
    import sys
    n_line = sys.stdin.readline().strip()
    n = int(n_line) if n_line else 0
    if n == 0:
        print(0)
        return

    # dp[b]：走到第 s 步时，前缀和为 b 的方案数
    dp = [0] * (2 * n + 2)
    dp[0] = 1

    for s in range(0, 2 * n):
        ndp = [0] * (2 * n + 2)
        for b in range(0, 2 * n + 1):
            v = dp[b]
            if v == 0:
                continue

            # 根据 (s,b) 还原已用的 plus/minus
            # 由于从 (0,0) 出发，每次 b 变化 1，因此 (s+b) 必为偶数
            plus = (s + b) // 2
            minus = s - plus

            # 放 +1：plus + 1 <= n
            if plus < n:
                ndp[b + 1] += v

            # 放 -1：要求 b > 0 且 minus + 1 <= n
            if b > 0 and minus < n:
                ndp[b - 1] += v

        dp = ndp

    print(dp[0])


if __name__ == "__main__":
    main()
```

### Answer6
```cpp
#include <iostream>
using namespace std;

int main() {
    int n;
    cin >> n;

    if (n == 0) {
        cout << 0 << endl;
        return 0;
    }

    long long catalan[21]; // n <= 20
    catalan[0] = 1;

    for (int i = 1; i <= n; ++i) {
        catalan[i] = 0;
        for (int j = 0; j < i; ++j) {
            catalan[i] += catalan[j] * catalan[i - 1 - j];
        }
    }

    cout << catalan[n] << endl;
    return 0;
}
```

### Answer7
```cpp
#include <iostream>

using namespace std;

// 计算组合数 C(n, k)
// 使用 long long 防止溢出，鉴于 n <= 20，中间结果也在 long long 范围内
long long combination(int n, int k) {
    if (k < 0 || k > n) {
        return 0;
    }
    if (k == 0 || k == n) {
        return 1;
    }
    if (k > n / 2) {
        k = n - k;
    }
    
    long long res = 1;
    for (int i = 1; i <= k; ++i) {
        // 先乘后除，注意防止溢出。
        // 由于是计算组合数，每一步除法一定能整除
        res = res * (n - i + 1) / i;
    }
    return res;
}

int main() {
    int n;
    if (cin >> n) {
        // 特判：根据题目要求，当 n=0 时输出 0
        if (n == 0) {
            cout << 0 << endl;
        } else {
            // 卡特兰数公式: C_n = C(2n, n) / (n + 1)
            // 当 n=20 时，C(40, 20) 约为 1.37e11，远小于 long long 上限 (9e18)
            long long c_2n_n = combination(2 * n, n);
            long long ans = c_2n_n / (n + 1);
            
            cout << ans << endl;
        }
    }
    return 0;
}
```

### Answer8
```cpp
#include <iostream>
#include <algorithm>
#include <cmath>
#include <vector>

// 使用 64 位整型 long long 来存储结果，以防止 n=20 时溢出。
typedef long long ll;

/**
 * @brief 计算第 n 个卡特兰数 C_n。
 * C_n = (1 / (n + 1)) * C(2n, n)
 *
 * @param n 卡特兰数序号。
 * @return 满足条件的不同排列数。
 */
ll catalan_number(int n) {
    if (n < 0) {
        return 0; // n不能是负数
    }
    if (n == 0) {
        return 0; 
    }

    // 1. 计算 C(2n, n)
    // C(2n, n) = (2n * (2n-1) * ... * (n+1)) / (n!)
    // 使用迭代计算 C(2n, n) 可以有效控制中间结果的大小，避免不必要的溢出风险。
    ll combination = 1; 

    // 迭代 i 从 1 到 n
    for (int i = 1; i <= n; ++i) {
        // 乘法部分： (2n - i + 1)
        // C(2n, n) 的分子项 (2n) -> (2n-1) -> ... -> (n+1)
        combination = combination * (2 * n - i + 1);
        
        // 除法部分： / i
        // C(2n, n) 的分母项 n!
        // 这里的除法是精确的，因为 C(2n, n) 总是整数。
        combination = combination / i;
    }

    // 2. 计算 C_n = C(2n, n) / (n + 1)
    // 由于 n >= 1，n + 1 肯定大于 0。
    ll result = combination / (n + 1);

    return result;
}

int main() {
    // 提高输入/输出速度
    std::ios_base::sync_with_stdio(false);
    std::cin.tie(NULL);

    int n;
    // 循环读取输入，直到文件结束 (EOF)
    while (std::cin >> n) {
        // 根据数据范围 0 <= n <= 20，直接调用卡特兰数公式即可。
        ll result = catalan_number(n);
        std::cout << result << "\n";
    }

    return 0;
}
```

### Answer9
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;
int main() {
    int n;
    cin >> n;
    if (n == 0) {
        cout << 0;
        return 0;
    }
    __int128_t res = 1;
    for (int i = 1; i <= n; ++i) {
        res = res * (n + i) / i;
    }
    res = res / (n + 1);
    cout << (long long) res;
    return 0;
}

```

### Answer10
```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    int n;
    if (!(cin >> n)) return 0;

    // 根据评测的期望：n = 0 时输出 0
    if (n == 0) {
        cout << 0 << '\n';
        return 0;
    }

    vector<long long> C(n + 1, 0);
    C[0] = 1;  // 从 C_0 开始递推

    for (int k = 1; k <= n; ++k) {
        long long sum = 0;
        for (int i = 0; i < k; ++i) {
            sum += C[i] * C[k - 1 - i];
        }
        C[k] = sum;
    }

    cout << C[n] << '\n';
    return 0;
}

```

# AI协作题（2）
## 最小化最大值之和

### Answer1
```python
import sys

def main():
    n, m = map(int, sys.stdin.readline().split())
    h = [0] * (n + 2)  # 1-based索引存储书的长度
    for i in range(1, n + 1):
        h[i] = int(sys.stdin.readline())
    
    # 初始化数据结构
    q = [0] * (n + 2)  # 单调队列，存储书的索引
    stk = [[0] * (n + 2) for _ in range(2)]  # 两个栈：左栈(0)和右栈(1)
    top = [0, 0]  # 栈顶指针
    l, r = 1, 0  # 队列的左右指针
    mid = 0  # 队列中点，用于分割左右栈
    f = [0] * (n + 2)  # dp数组，f[i]表示前i本书的最小宽度和
    tmpf = [0] * (n + 2)  # 存储队列中对应位置的临时f值
    
    st = 1  # 当前层的起始位置
    sum_h = 0  # 当前层的长度和
    
    def push(x, i):
        """将x推入栈i(0或1)，维护栈的单调性"""
        nonlocal top
        if top[i] == 0:
            top[i] += 1
            stk[i][top[i]] = x
        else:
            if tmpf[stk[i][top[i]]] > tmpf[x]:
                top[i] += 1
                stk[i][top[i]] = x
    
    def rebuild():
        """重建左右栈，根据当前队列的中点分割"""
        nonlocal mid, top
        mid = (l + r) // 2
        top[0] = top[1] = 0
        # 左栈：从mid到l倒序推入
        for i in range(mid, l - 1, -1):
            push(i, 0)
        # 右栈：从mid+1到r正序推入
        for i in range(mid + 1, r + 1):
            push(i, 1)
    
    for i in range(1, n + 1):
        sum_h += h[i]
        # 调整当前层的起始位置，确保和不超过m
        while sum_h > m:
            sum_h -= h[st]
            st += 1
        
        # 维护单调队列：确保队列中元素的h值递减
        while l <= r and h[q[r]] <= h[i]:
            # 弹出队尾元素
            if top[0] > 0 and stk[0][top[0]] == r:
                top[0] -= 1
            if top[1] > 0 and stk[1][top[1]] == r:
                top[1] -= 1
            r -= 1
            # 若队尾越过中点，重建栈
            if r <= mid:
                rebuild()
        
        # 计算新队尾的临时f值
        if l > r:
            tmpf[r + 1] = f[st - 1] + h[i]
        else:
            tmpf[r + 1] = f[q[r]] + h[i]
        # 将当前书加入队列
        r += 1
        q[r] = i
        push(r, 1)  # 推入右栈
        
        # 处理队头过期元素（不在合法范围内）
        # 检查栈顶是否为队头，是的话弹出
        if top[0] > 0 and stk[0][top[0]] == l:
            top[0] -= 1
        if top[1] > 0 and stk[1][top[1]] == l:
            top[1] -= 1
        
        # 弹出所有过期的队头元素
        while l <= r and q[l] < st:
            l += 1
            # 若队头越过中点，重建栈
            if l > mid:
                rebuild()
            # 再次检查栈顶是否为新队头
            if top[0] > 0 and stk[0][top[0]] == l:
                top[0] -= 1
            if top[1] > 0 and stk[1][top[1]] == l:
                top[1] -= 1
        
        # 计算当前f[i]的最小值
        f[i] = f[st - 1] + h[q[l]]  # 从起始位置到当前的段
        # 比较左右栈中的最小值
        if top[0] > 0:
            f[i] = min(f[i], tmpf[stk[0][top[0]]])
        if top[1] > 0:
            f[i] = min(f[i], tmpf[stk[1][top[1]]])
    
    print(f[n])

if __name__ == "__main__":
    main()
```

### Answer2
```C++
#include <iostream>
#include <vector>
#include <algorithm>
#include <stack>

using namespace std;

const long long INF = 1e18;
const int MAXN = 100005;

int n;
long long m;
long long h[MAXN];
long long dp[MAXN];

// 线段树节点
struct Node {
    long long min_dp;    // 存储单纯的 dp[j]
    long long min_val;   // 存储 dp[j] + max_h (也就是题目要求的计算值)
    long long lazy_tag;  // 懒标记：用于更新区间内的 max_h
} tree[MAXN * 4];

// 建树
void build(int node, int start, int end) {
    tree[node].lazy_tag = -1;
    tree[node].min_dp = INF;
    tree[node].min_val = INF;
    if (start == end) {
        return;
    }
    int mid = (start + end) / 2;
    build(node * 2, start, mid);
    build(node * 2 + 1, mid + 1, end);
}

// 下传标记
void push_down(int node) {
    if (tree[node].lazy_tag != -1) {
        long long tag = tree[node].lazy_tag;
        
        // 左孩子
        tree[node * 2].min_val = tree[node * 2].min_dp + tag;
        tree[node * 2].lazy_tag = tag;
        
        // 右孩子
        tree[node * 2 + 1].min_val = tree[node * 2 + 1].min_dp + tag;
        tree[node * 2 + 1].lazy_tag = tag;
        
        tree[node].lazy_tag = -1;
    }
}

// 更新 dp[pos] 的值 (单点更新)
void update_dp(int node, int start, int end, int pos, long long val) {
    if (start == end) {
        tree[node].min_dp = val;
        // 注意：此时 min_val 还不确定，因为还不知道这一段的 max_h 是多少
        // 但在实际流程中，update_dp 之后紧接着会被 range_update_max 覆盖，
        // 或者作为新的一段初始值，其 max_h 暂时可以认为是无穷大或等待更新。
        // 这里我们暂时不更新 min_val，它主要由 range_update_max 来计算。
        // 但为了防止查询出错，如果 min_val 还是 INF，可以先保留。
        return;
    }
    push_down(node);
    int mid = (start + end) / 2;
    if (pos <= mid) update_dp(node * 2, start, mid, pos, val);
    else update_dp(node * 2 + 1, mid + 1, end, pos, val);
    
    tree[node].min_dp = min(tree[node * 2].min_dp, tree[node * 2 + 1].min_dp);
    tree[node].min_val = min(tree[node * 2].min_val, tree[node * 2 + 1].min_val);
}

// 区间更新 max_h 的值
// 将区间 [l, r] 内的 max_h 更新为 new_h，并重新计算 min_val
void update_range_max(int node, int start, int end, int l, int r, long long new_h) {
    if (l > end || r < start) return;
    if (l <= start && end <= r) {
        tree[node].min_val = tree[node].min_dp + new_h;
        tree[node].lazy_tag = new_h;
        return;
    }
    push_down(node);
    int mid = (start + end) / 2;
    update_range_max(node * 2, start, mid, l, r, new_h);
    update_range_max(node * 2 + 1, mid + 1, end, l, r, new_h);
    
    tree[node].min_dp = min(tree[node * 2].min_dp, tree[node * 2 + 1].min_dp);
    tree[node].min_val = min(tree[node * 2].min_val, tree[node * 2 + 1].min_val);
}

// 区间查询最小值
long long query(int node, int start, int end, int l, int r) {
    if (l > end || r < start) return INF;
    if (l <= start && end <= r) {
        return tree[node].min_val;
    }
    push_down(node);
    int mid = (start + end) / 2;
    return min(query(node * 2, start, mid, l, r), query(node * 2 + 1, mid + 1, end, l, r));
}

int main() {
    // 优化输入输出速度
    ios::sync_with_stdio(false);
    cin.tie(0);

    if (!(cin >> n >> m)) return 0;

    for (int i = 1; i <= n; ++i) {
        cin >> h[i];
    }

    build(1, 0, n);

    // dp[0] = 0，其余默认为 INF
    update_dp(1, 0, n, 0, 0);

    // 单调栈：存储下标，对应 h 值递减
    stack<int> stk;
    
    int L = 0; // 满足 sum(h[L+1...i]) <= m 的左边界
    long long current_sum = 0;

    for (int i = 1; i <= n; ++i) {
        // 1. 维护滑动窗口 L
        current_sum += h[i];
        while (current_sum > m) {
            L++;
            current_sum -= h[L];
        }

        // 2. 维护单调栈，并更新线段树
        // h[i] 将成为 (stk.top(), i] 这些 split point 对应的段的新最大值
        while (!stk.empty() && h[stk.top()] <= h[i]) {
            stk.pop();
        }
        
        int prev_idx = stk.empty() ? 0 : stk.top();
        // 对于 j 在 [prev_idx, i-1] 范围内的分割点，
        // 新的一段是 h[j+1 ... i]，其最大值现在确认为 h[i]
        update_range_max(1, 0, n, prev_idx, i - 1, h[i]);
        
        stk.push(i);

        // 3. 查询最优解
        // 有效的分割点 j 必须 >= L (保证 sum <= m)
        // 并且 j < i (至少分一段)
        dp[i] = query(1, 0, n, L, i - 1);
        
        // 4. 将算出的 dp[i] 加入线段树，供未来使用
        update_dp(1, 0, n, i, dp[i]);
    }

    cout << dp[n] << endl;

    return 0;
}
```

### Answer3
```C++
#include <bits/stdc++.h>
using namespace std;
using ll = long long;
const ll INF = (1LL<<60);

struct SegTree {
    int n;
    vector<ll> mn, add;
    SegTree(int _n=0){ init(_n); }
    void init(int _n){
        n = _n;
        mn.assign(4*n+5, INF);
        add.assign(4*n+5, 0);
    }
    void push(int o){
        if(add[o]!=0){
            ll v = add[o];
            int L = o<<1, R = o<<1|1;
            add[L] += v; add[R] += v;
            mn[L] += v; mn[R] += v;
            add[o] = 0;
        }
    }
    void range_add_impl(int o,int l,int r,int ql,int qr,ll v){
        if(ql>r || qr<l) return;
        if(ql<=l && r<=qr){
            mn[o] += v;
            add[o] += v;
            return;
        }
        push(o);
        int m = (l+r)>>1;
        range_add_impl(o<<1, l, m, ql, qr, v);
        range_add_impl(o<<1|1, m+1, r, ql, qr, v);
        mn[o] = min(mn[o<<1], mn[o<<1|1]);
    }
    void range_add(int L,int R,ll v){
        if(L>R) return;
        range_add_impl(1,1,n,L,R,v);
    }
    ll range_min_impl(int o,int l,int r,int ql,int qr){
        if(ql>r || qr<l) return INF;
        if(ql<=l && r<=qr) return mn[o];
        push(o);
        int m = (l+r)>>1;
        return min(range_min_impl(o<<1, l, m, ql, qr),
                   range_min_impl(o<<1|1, m+1, r, ql, qr));
    }
    ll range_min(int L,int R){
        if(L>R) return INF;
        return range_min_impl(1,1,n,L,R);
    }
    void point_set(int pos, ll val){
        ll cur = range_min(pos,pos);
        ll delta = val - cur;
        if(delta!=0) range_add(pos,pos,delta);
    }
};

int main(){
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    int n;
    if(!(cin>>n)) return 0;
    long long m;
    cin >> m;
    vector<ll> h(n+1,0);
    for(int i=1;i<=n;i++) cin>>h[i];
    vector<ll> pref(n+1,0);
    for(int i=1;i<=n;i++) pref[i]=pref[i-1]+h[i];

    SegTree seg(n);
    vector<ll> dp(n+1, INF);
    dp[0]=0;
    vector<int> st;
    int L = 1;
    for(int i=1;i<=n;i++){
        while(L<=i && pref[i]-pref[L-1] > m){
            seg.point_set(L, INF);
            L++;
        }
        seg.point_set(i, dp[i-1] + h[i]);
        while(!st.empty() && h[st.back()] <= h[i]){
            int x = st.back(); st.pop_back();
            int left = st.empty() ? 1 : st.back()+1;
            int lbound = max(left, L);
            int rbound = x;
            if(lbound <= rbound){
                seg.range_add(lbound, rbound, h[i] - h[x]);
            }
        }
        st.push_back(i);
        dp[i] = seg.range_min(L, i);
    }
    cout << dp[n] << '\n';
    return 0;
}
```

### Answer4
```C++
#include <iostream>
#include <stack>
#include <algorithm>
#include <vector>
using namespace std;

const int MAXN = 100010;
const long long INF = 1e18;

int n, m;
long long h[MAXN], sum[MAXN];
long long dp[MAXN];

struct SegmentTree {
    struct Node {
        int l, r;
        long long min_dp;
        long long lazy_val;
        long long min_f;
    } tree[MAXN * 4];

    void build(int o, int l, int r) {
        tree[o].l = l;
        tree[o].r = r;
        tree[o].lazy_val = -1;
        tree[o].min_dp = INF;
        tree[o].min_f = INF;
        if (l == r) {
            return;
        }
        int mid = (l + r) >> 1;
        build(o << 1, l, mid);
        build(o << 1 | 1, mid + 1, r);
    }

    void push_down(int o) {
        if (tree[o].lazy_val != -1) {
            tree[o << 1].lazy_val = tree[o].lazy_val;
            tree[o << 1 | 1].lazy_val = tree[o].lazy_val;
            tree[o << 1].min_f = tree[o << 1].min_dp + tree[o].lazy_val;
            tree[o << 1 | 1].min_f = tree[o << 1 | 1].min_dp + tree[o].lazy_val;
            tree[o].lazy_val = -1;
        }
    }

    void update_dp(int o, int pos, long long val) {
        if (tree[o].l == tree[o].r) {
            tree[o].min_dp = val;
            if (tree[o].lazy_val != -1) {
                tree[o].min_f = tree[o].min_dp + tree[o].lazy_val;
            } else {
                tree[o].min_f = INF;
            }
            return;
        }
        push_down(o);
        int mid = (tree[o].l + tree[o].r) >> 1;
        if (pos <= mid) {
            update_dp(o << 1, pos, val);
        } else {
            update_dp(o << 1 | 1, pos, val);
        }
        tree[o].min_dp = min(tree[o << 1].min_dp, tree[o << 1 | 1].min_dp);
        tree[o].min_f = min(tree[o << 1].min_f, tree[o << 1 | 1].min_f);
    }

    void update_m(int o, int L, int R, long long val) {
        if (L <= tree[o].l && tree[o].r <= R) {
            tree[o].lazy_val = val;
            tree[o].min_f = tree[o].min_dp + val;
            return;
        }
        push_down(o);
        int mid = (tree[o].l + tree[o].r) >> 1;
        if (L <= mid) {
            update_m(o << 1, L, R, val);
        }
        if (R > mid) {
            update_m(o << 1 | 1, L, R, val);
        }
        tree[o].min_dp = min(tree[o << 1].min_dp, tree[o << 1 | 1].min_dp);
        tree[o].min_f = min(tree[o << 1].min_f, tree[o << 1 | 1].min_f);
    }

    long long query(int o, int L, int R) {
        if (L <= tree[o].l && tree[o].r <= R) {
            return tree[o].min_f;
        }
        push_down(o);
        int mid = (tree[o].l + tree[o].r) >> 1;
        long long ans = INF;
        if (L <= mid) {
            ans = min(ans, query(o << 1, L, R));
        }
        if (R > mid) {
            ans = min(ans, query(o << 1 | 1, L, R));
        }
        return ans;
    }
};

SegmentTree st;

int main() {
    cin >> n >> m;
    for (int i = 1; i <= n; i++) {
        cin >> h[i];
        sum[i] = sum[i - 1] + h[i];
    }

    st.build(1, 0, n);
    st.update_dp(1, 0, 0);

    stack<int> stk;
    stk.push(0);
    h[0] = INF;

    int left = 0;
    for (int i = 1; i <= n; i++) {
        while (sum[i] - sum[left] > m) {
            left++;
        }

        while (!stk.empty() && h[stk.top()] <= h[i]) {
            stk.pop();
        }
        int top = stk.top();
        stk.push(i);

        if (top <= i - 1) {
            st.update_m(1, top, i - 1, h[i]);
        }

        dp[i] = st.query(1, left, i - 1);
        st.update_dp(1, i, dp[i]);
    }

    cout << dp[n] << endl;
    return 0;
}
```

### Answer5
```python
import sys
import heapq
from collections import deque

# 设置最大递归深度（虽然本解法主要用迭代，但作为保险）
sys.setrecursionlimit(200000)

def solve():
    # 1. 快速输入读取
    input_data = sys.stdin.read().split()
    if not input_data:
        return
    
    iterator = iter(input_data)
    try:
        n = int(next(iterator))
        m = int(next(iterator))
    except StopIteration:
        return

    h = [0] * (n + 1)
    for i in range(1, n + 1):
        h[i] = int(next(iterator))

    # 2. 预处理前缀和
    # 用于快速判断区间和是否 <= m
    prefix_sum = [0] * (n + 1)
    current_sum = 0
    for i in range(1, n + 1):
        current_sum += h[i]
        prefix_sum[i] = current_sum

    # 3. 构建线段树 (用于查询 DP 区间最小值)
    # 树的大小通常是 n 的下一个 2 的幂次的两倍
    size = 1
    while size <= n:
        size *= 2
    tree = [float('inf')] * (2 * size)

    # 单点更新函数 (迭代版)
    def update(pos, val):
        idx = pos + size
        tree[idx] = val
        while idx > 1:
            idx //= 2
            tree[idx] = min(tree[2 * idx], tree[2 * idx + 1])

    # 区间查询函数 [l, r) (迭代版)
    def query(l, r):
        res = float('inf')
        l += size
        r += size
        while l < r:
            if l % 2 == 1:
                res = min(res, tree[l])
                l += 1
            if r % 2 == 1:
                r -= 1
                res = min(res, tree[r])
            l //= 2
            r //= 2
        return res

    # DP 数组初始化
    dp = [0] * (n + 1)
    update(0, 0) # dp[0] = 0

    # 4. 数据结构初始化
    # 单调队列 (存储下标，对应 h 值单调递减)
    q = deque()
    
    # 候选堆 (存储非队头元素的决策值)
    # 堆中元素为 (cost, index)
    candidates = []
    
    # 记录每个下标当前在堆中对应的有效 cost
    # 如果堆顶元素 (cost, idx) 中的 cost 不等于 active_contribution[idx]，说明该元素已失效（被懒删除）
    active_contribution = {}

    L = 0 # 滑动窗口左边界 (prefix_sum[i] - prefix_sum[L] <= m)

    # 5. 主循环 DP
    for i in range(1, n + 1):
        # A. 移动左边界 L，保证当前总和约束
        while prefix_sum[i] - prefix_sum[L] > m:
            L += 1
        
        # B. 维护单调队列：插入新元素 i
        # 当新元素 h[i] >= 队尾元素时，队尾元素被“盖过”，弹出
        while q and h[q[-1]] <= h[i]:
            popped = q.pop()
            # 如果被弹出的元素在 candidates 堆中有记录，标记为失效
            if popped in active_contribution:
                del active_contribution[popped]
        
        # 计算新元素作为“非队头”时的贡献值
        # 如果队列不为空，h[i] 的管辖范围左边界被 q[-1] 限制
        # 对应的决策区间为 [q[-1], i)
        if q:
            prev_idx = q[-1]
            # 查找 range min DP
            min_dp_val = query(prev_idx, i) 
            if min_dp_val != float('inf'):
                cost = h[i] + min_dp_val
                heapq.heappush(candidates, (cost, i))
                active_contribution[i] = cost
        
        q.append(i)
        
        # C. 维护单调队列：处理队头
        # 如果队头下标 <= L，说明它不在当前的有效切分范围内 (j 必须 >= L)
        while q and q[0] <= L:
            removed = q.popleft()
            if removed in active_contribution:
                del active_contribution[removed]
            
            # 注意：原来的第二元素现在变成了新的队头
            # 新队头的计算方式变为“动态左边界”，不能再使用堆中的静态值
            if q:
                new_head = q[0]
                if new_head in active_contribution:
                    del active_contribution[new_head]
        
        # D. 计算 DP[i]
        res = float('inf')
        
        # 来源 1: 堆中的最小值 (非队头元素)
        while candidates:
            cost, idx = candidates[0]
            # 检查有效性 (懒删除检查)
            if idx in active_contribution and active_contribution[idx] == cost:
                res = min(res, cost)
                break
            else:
                heapq.heappop(candidates)
        
        # 来源 2: 队头元素 (单独计算，因为它受 L 限制)
        if q:
            head_idx = q[0]
            # 队头覆盖的有效切分点范围是 [L, head_idx)
            # 线段树查询范围 [L, head_idx)
            min_dp_head = query(L, head_idx)
            if min_dp_head != float('inf'):
                res = min(res, h[head_idx] + min_dp_head)
        
        dp[i] = res
        update(i, res)

    print(dp[n])

if __name__ == "__main__":
    solve()
```

### Answer6
```C++
//知识点: 线段树优化DP
/*
By:Luckyblock
*/
#include <cstdio>
#include <ctype.h>
#include <cstring>
#include <algorithm>
#define ll long long
#define ls (now<<1)
#define rs (now<<1|1)
const int kMaxn = 1e5 + 10;
const ll kInf = 1e12 + 2077;
//=============================================================
struct SegmentTree {
  int L, R;
  ll f, ans, tag;
} t[kMaxn << 2];
ll n, m, h[kMaxn], w[kMaxn], sum[kMaxn], pre[kMaxn], f[kMaxn];
ll top, sta[kMaxn];
//=============================================================
inline ll read() {
  ll f = 1, w = 0; char ch = getchar();
  for (; !isdigit(ch); ch = getchar()) if (ch == '-') f = -1;
  for (; isdigit(ch); ch = getchar()) w = (w << 3) + (w << 1) + (ch ^ '0');
  return f * w;
}
void Pushup(int now) {
  t[now].f = std :: min(t[ls].f, t[rs].f);
  t[now].ans = std :: min(t[ls].ans, t[rs].ans);
}
void Pushdown(int now) {
  t[ls].ans = t[ls].f + t[now].tag;
  t[rs].ans = t[rs].f + t[now].tag;
  t[ls].tag = t[rs].tag = t[now].tag;
  t[now].tag = kInf;
}
void Build(int now, int L, int R) {
  t[now].L = L, t[now].R = R;
  if (L == R) {
    t[now].f = t[now].ans = t[now].tag = kInf;
    return ;
  }
  int mid = (L + R) >> 1;
  Build(ls, L, mid), Build(rs, mid + 1, R);
  Pushup(now);
}
ll Query(int now, int L, int R) {
  if (L <= t[now].L && t[now].R <= R) return t[now].ans;
  if (t[now].tag != kInf) Pushdown(now); 
  int mid = (t[now].L + t[now].R) >> 1;
  ll ret = kInf;
  if (L <= mid) ret = std :: min(ret, Query(ls, L, R));
  if (R > mid) ret = std :: min(ret, Query(rs, L, R));
  return ret;
}
void Update(int now, int L, int R, ll val) {
  if (L <= t[now].L && t[now].R <= R) {
    t[now].ans = t[now].f + val;
    t[now].tag = val;
    return ;
  }
  if (t[now].tag != kInf) Pushdown(now);
  int mid = (t[now].L + t[now].R) >> 1;
  if (L <= mid) Update(ls, L, R, val);
  if (R > mid) Update(rs, L, R, val);
  Pushup(now);
}
void Modify(int now, int pos) {
  if (t[now].L == t[now].R) {
    t[now].ans = kInf;
    t[now].f = f[pos - 1];
    return ;
  }
  if (t[now].tag != kInf) Pushdown(now);
  int mid = (t[now].L + t[now].R) >> 1;
  if (pos <= mid) Modify(ls, pos);
  else Modify(rs, pos);
  Pushup(now);
}
void Prepare() {
  n = read(), m = read();
  for (int i = 1; i <= n; ++ i) {
    h[i] = read();
    sum[i] = sum[i - 1] + h[i];
  }

  sta[++ top] = 1;
  for (int i = 2; i <= n; ++ i) {
    while (top && h[sta[top]] < h[i]) top --;
    if (top) pre[i] = sta[top];
    sta[++ top] = i;
  }
  Build(1, 1, n);
}

int main() {
  Prepare();
  for (int i = 1; i <= n; ++ i) {
    Modify(1, i);
    if (pre[i] < i) Update(1, pre[i] + 1, i, h[i]);
    int l = std ::lower_bound(sum, sum + i + 1, sum[i] - m) - sum;
    if (l < i) f[i] = Query(1, l + 1, i);
  }
  printf("%lld", f[n]);
  return 0;
}
```

### Answer7
```C++
#include<bits/stdc++.h>
//#define endl '\n'
#define ll long long
using namespace std;

const ll N = 1e5 + 5;
ll h[N], pre[N];
ll n, m;
ll f[N], q[N * 4];
multiset<ll> S;

inline void sol() {
    cin >> n >> m;
    for (int i = 1; i <= n; ++i) { cin >> h[i]; if (i == 1)pre[i] = h[i]; else pre[i] = pre[i - 1] + h[i]; }
    ll l, r; l = 1; r = 0;
    for (int i = 1; i <= n; ++i) {
        while (l<r && pre[i] - pre[q[l]]>m) {
            S.erase(f[q[l]] + h[q[l + 1]]);
            l++;
        }
        while (l <= r && h[q[r]] < h[i]) {
            S.erase(f[q[r - 1]] + h[q[r]]);
            r--;
        }
        if (l <= r) {
            S.insert(f[q[r]] + h[i]);
        }
        q[++r] = i;
        int L = 0, R = i - 1, c;
        while (L <= R) {
            int mid = (L + R) >> 1;
            if (pre[i] - pre[mid] > m) {
                L = mid + 1;
            }
            else {
                c = mid;
                R = mid - 1;
            }
        }
        f[i] = f[c] + h[q[l]];
        if (!S.empty()) {
            f[i] = min(f[i], *S.begin());
        }
    }
    cout << f[n] << endl;
}

int main() {
    ios::sync_with_stdio(0);
    cin.tie(0); cout.tie(0);

    int t;
    //cin>>t;
    t = 1;
    while (t--)
    {
        sol();
    }
    return 0;
}
```

### Answer8
```C++
#include <iostream>
#include <vector>
#include <algorithm>
#include <climits>
using namespace std;

const long long INF = 1e18;

// 线段树（区间最小值）
class SegTree {
private:
    int n;
    vector<long long> data;

public:
    SegTree(int size) {
        n = 1;
        while (n < size) n <<= 1;
        data.resize(2 * n, INF);
    }

    void build(const vector<long long>& arr) {
        for (int i = 0; i < arr.size(); ++i) {
            data[n + i] = arr[i];
        }
        for (int i = n - 1; i > 0; --i) {
            data[i] = min(data[2 * i], data[2 * i + 1]);
        }
    }

    void point_update(int idx, long long val) {
        idx += n;
        data[idx] = val;
        for (idx >>= 1; idx > 0; idx >>= 1) {
            data[idx] = min(data[2 * idx], data[2 * idx + 1]);
        }
    }

    long long range_min(int l, int r) {
        long long res = INF;
        l += n;
        r += n;
        while (l <= r) {
            if (l & 1) res = min(res, data[l++]);
            if (!(r & 1)) res = min(res, data[r--]);
            l >>= 1;
            r >>= 1;
        }
        return res;
    }
};

void solve() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    int n, m;
    cin >> n >> m;

    vector<int> h(n + 1);  // 1-based
    for (int i = 1; i <= n; ++i) {
        cin >> h[i];
    }

    // 前缀和
    vector<long long> pref(n + 1, 0);
    for (int i = 1; i <= n; ++i) {
        pref[i] = pref[i - 1] + h[i];
    }

    // DP 数组
    vector<long long> dp(n + 1, INF);
    dp[0] = 0;

    // 线段树
    SegTree seg(n + 1);
    seg.build(dp);

    // 单调栈，保存 (start_index, height_value)
    vector<pair<int, int>> stack;

    int L = 1;  // 滑动窗口左边界

    for (int i = 1; i <= n; ++i) {
        // 更新滑动窗口左边界
        while (L <= i && (pref[i] - pref[L - 1]) > m) {
            L++;
        }

        if (L > i) {
            dp[i] = INF;
            seg.point_update(i, dp[i]);
            stack.clear();
            continue;
        }

        // 维护单调栈
        int start = i;
        while (!stack.empty() && stack.back().second <= h[i]) {
            start = stack.back().first;
            stack.pop_back();
        }
        stack.emplace_back(start, h[i]);

        // 计算最佳值
        long long best = INF;
        int sz = stack.size();
        for (int idx = 0; idx < sz; ++idx) {
            int s_k = stack[idx].first;
            int val_k = stack[idx].second;
            int left = s_k;
            int right = (idx + 1 < sz) ? (stack[idx + 1].first - 1) : i;

            int Lk = max(L, left);
            int Rk = right;

            if (Lk <= Rk) {
                int ql = Lk - 1;
                int qr = Rk - 1;
                long long min_dp = seg.range_min(ql, qr);
                long long cand = min_dp + val_k;
                if (cand < best) {
                    best = cand;
                }
            }
        }

        dp[i] = best;
        seg.point_update(i, dp[i]);
    }

    cout << dp[n] << '\n';
}

int main() {
    solve();
    return 0;
}
```

### Answer9
```C++
#include <iostream>
#include <vector>
#include <queue>
#include <unordered_map>
using namespace std;

using ll = long long;
const ll INF = (ll)4e18;

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    int n;
    ll m;
    if (!(cin >> n >> m)) return 0;

    vector<ll> a(n + 1);
    for (int i = 1; i <= n; ++i) cin >> a[i];

    // 如果有某个数本身就大于 m，必然无解（如果题目保证 max(h_i) <= m，可以不用）
    for (int i = 1; i <= n; ++i) {
        if (a[i] > m) {
            cout << -1 << '\n';
            return 0;
        }
    }

    vector<ll> f(n + 1, 0); // f[i] = 前 i 个数最小代价

    // 单调队列：存的是下标，队列里 a[idx] 严格递减
    vector<int> q(n + 5);
    int hh = 0, tt = -1;

    // 小根堆 + 计数，用来维护一堆候选值的最小值（类似 multiset）
    priority_queue<ll, vector<ll>, greater<ll>> heap;
    unordered_map<ll, int> cnt;

    auto push_val = [&](ll v) {
        if (++cnt[v] == 1) heap.push(v);
    };
    auto erase_val = [&](ll v) {
        auto it = cnt.find(v);
        if (it == cnt.end()) return;
        if (--(it->second) == 0) cnt.erase(it);
    };
    auto get_min = [&]() -> ll {
        while (!heap.empty()) {
            ll x = heap.top();
            auto it = cnt.find(x);
            if (it == cnt.end() || it->second == 0) {
                heap.pop();
                continue;
            }
            return x;
        }
        return INF;
    };

    ll sum = 0;
    int L = 1;      // 当前能作为段左端点的最小位置（保证区间和 ≤ m）
    f[0] = 0;

    for (int i = 1; i <= n; ++i) {
        sum += a[i];

        // 双指针右移 L，使得 [L..i] 的和 ≤ m
        while (sum > m) {
            sum -= a[L];

            // 队头如果已经在 L 左边，就要从队列中删除
            while (hh <= tt && q[hh] < L) {
                if (hh < tt) {
                    // 原来有一项候选：f[q[hh]] + a[q[hh+1]]
                    ll val = f[q[hh]] + a[q[hh + 1]];
                    erase_val(val);
                }
                ++hh;
            }
            ++L;
        }

        // 维护单调队列：从后往前删掉 <= a[i] 的元素
        while (tt >= hh && a[q[tt]] <= a[i]) {
            if (hh < tt) {
                // 删掉一项候选：f[q[tt-1]] + a[q[tt]]
                ll val = f[q[tt - 1]] + a[q[tt]];
                erase_val(val);
            }
            --tt;
        }

        // 把当前下标 i 加到队尾
        q[++tt] = i;

        // 如果队列中至少有两个元素，就新增一项候选：f[前一个最大值位置] + 当前最大值
        if (tt > hh) {
            ll val = f[q[tt - 1]] + a[q[tt]];
            push_val(val);
        }

        // 先考虑最后一段从 L 开始，到 i 结束，段最大值就是 a[q[hh]]
        ll ans = f[L - 1] + a[q[hh]];

        // 再从所有候选里取更小的（对应“最大值变化点”分成的块）
        ll cand = get_min();
        if (cand < ans) ans = cand;

        f[i] = ans;
    }

    cout << f[n] << '\n';
    return 0;
}
```

### Answer10
```C++
#include <bits/stdc++.h>
using namespace std;

const long long INF = (long long)4e18;

struct SegTree {
    int n;
    vector<long long> seg, lazy;

    SegTree(int n = 0) { init(n); }

    void init(int n_) {
        n = n_;
        seg.assign(4 * n + 4, INF);
        lazy.assign(4 * n + 4, 0);
    }

    void apply(int p, long long v) {
        seg[p] += v;
        lazy[p] += v;
    }

    void push(int p) {
        if (lazy[p] != 0) {
            long long v = lazy[p];
            apply(p << 1, v);
            apply(p << 1 | 1, v);
            lazy[p] = 0;
        }
    }

    // 区间加
    void range_add(int p, int l, int r, int ql, int qr, long long val) {
        if (ql > r || qr < l) return;
        if (ql <= l && r <= qr) {
            apply(p, val);
            return;
        }
        push(p);
        int mid = (l + r) >> 1;
        range_add(p << 1, l, mid, ql, qr, val);
        range_add(p << 1 | 1, mid + 1, r, ql, qr, val);
        seg[p] = min(seg[p << 1], seg[p << 1 | 1]);
    }

    // 单点赋值
    void point_set(int p, int l, int r, int pos, long long val) {
        if (l == r) {
            seg[p] = val;
            lazy[p] = 0; // 清掉懒标记
            return;
        }
        push(p);
        int mid = (l + r) >> 1;
        if (pos <= mid) point_set(p << 1, l, mid, pos, val);
        else point_set(p << 1 | 1, mid + 1, r, pos, val);
        seg[p] = min(seg[p << 1], seg[p << 1 | 1]);
    }

    // 区间最小
    long long range_min(int p, int l, int r, int ql, int qr) {
        if (ql > r || qr < l) return INF;
        if (ql <= l && r <= qr) return seg[p];
        push(p);
        int mid = (l + r) >> 1;
        return min(range_min(p << 1, l, mid, ql, qr),
                   range_min(p << 1 | 1, mid + 1, r, ql, qr));
    }
};

struct Node {
    int l, r;
    long long mx;
};

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    int n;
    long long m;
    if (!(cin >> n >> m)) return 0;

    vector<long long> h(n + 1);
    for (int i = 1; i <= n; ++i) cin >> h[i];

    vector<long long> dp(n + 1, INF);
    dp[0] = 0;

    SegTree st(n);

    vector<Node> stck;
    stck.reserve(n);

    long long sum = 0;
    int L = 1;

    for (int i = 1; i <= n; ++i) {
        sum += h[i];
        while (sum > m) {
            sum -= h[L];
            ++L;
        }

        long long hi = h[i];
        int newL = i;

        // 处理单调栈：弹出 mx <= hi 的组，并对这些组做区间加
        while (!stck.empty() && stck.back().mx <= hi) {
            Node cur = stck.back();
            stck.pop_back();
            // 这些起点 j ∈ [cur.l, cur.r] 的最大值从 cur.mx 变成 hi
            // best_j 需要加上 (hi - cur.mx)
            st.range_add(1, 1, n, cur.l, cur.r, hi - cur.mx);
            newL = cur.l;
        }

        // 当前元素形成的新组 [newL, i]，最大值 hi
        stck.push_back({newL, i, hi});

        // 新起点 j = i，best[i] = dp[i-1] + h[i]
        st.point_set(1, 1, n, i, dp[i - 1] + hi);

        // dp[i] = min_{j ∈ [L..i]} best_j
        dp[i] = st.range_min(1, 1, n, L, i);
    }

    cout << dp[n] << '\n';
    return 0;
}
```


# 改错题（1）
## 最⼤路径权重问题

### Answer1
```python
import sys

# md想到了预处理，但是我是以行列预处理，应该按照对角线

def main():
    input = sys.stdin.read().split()
    # input = "2 2 3 3 6 4".split()
    n, m = int(input[0]), int(input[1])
    a = [[0] * (m + 2) for _ in range(n + 2)]
    
    idx = 2
    for i in range(1, n + 1):
        for j in range(1, m + 1):
            a[i][j] = int(input[idx])
            idx += 1

    # f[i][j]: 从(1,1)到(i,j)的最大路径权重
    f = [[-10**18] * (m + 2) for _ in range(n + 2)]
    f[1][1] = a[1][1]
    for i in range(1, n + 1):
        for j in range(1, m + 1):
            if i == 1 and j == 1:
                continue
            f[i][j] = max(f[i-1][j], f[i][j-1]) + a[i][j]

    # g[i][j]: 从(i,j)到(n,m)的最大路径权重
    g = [[-10**18] * (m + 2) for _ in range(n + 2)]
    g[n][m] = a[n][m]
    for i in range(n, 0, -1):
        for j in range(m, 0, -1):
            if i == n and j == m:
                continue
            g[i][j] = max(g[i+1][j], g[i][j+1]) + a[i][j]
            
    diag_max1 = [0] * (n + m + 2)
    diag_max2 = [0] * (n + m + 2)
    for i in range(1, n + 1):
        for j in range(1, m + 1):
            diagv = f[i][j] + g[i][j] - a[i][j]
            if diag_max1[i+j] < diagv:
                diag_max2[i+j] = diag_max1[i+j]
                diag_max1[i+j] = diagv
            else:
                diag_max2[i+j] = max(diag_max2[i+j], diagv)

    ans = 10**18

    for p in range(1, n + 1):
        for q in range(1, m + 1):
            # 经过(p,q)并把它变0的最大分数
            pass_zero = f[p][q] + g[p][q] - 2 * a[p][q]
            
            # 不经过(p,q)的最大分数
            max1, max2 = diag_max1[p+q], diag_max2[p+q]
            if max1 == f[p][q] + g[p][q] - a[p][q]:
                no_pass = max2
            else:
                no_pass = max1
            
            cur_max = max(no_pass, pass_zero)
            
            ans = min(ans, cur_max)

    print(ans)

if __name__ == "__main__":
    main()

```

### Answer2
```python
def solve():
    n, m = map(int, input().split())
    a = []
    for i in range(n):
        row = list(map(int, input().split()))
        a.append(row)
    
    def max_path_weight(grid):
        n, m = len(grid), len(grid[0])
        dp = [[0] * m for _ in range(n)]

        dp[0][0] = grid[0][0]

        for j in range(1, m):
            dp[0][j] = dp[0][j-1] + grid[0][j]

        for i in range(1, n):
            dp[i][0] = dp[i-1][0] + grid[i][0]

        for i in range(1, n):
            for j in range(1, m):
                dp[i][j] = max(dp[i-1][j], dp[i][j-1]) + grid[i][j]
        
        return dp[n-1][m-1]
    
    min_result = float('inf')

    for p in range(n):
        for q in range(m):
            modified = [row[:] for row in a]
            modified[p][q] = 0

            result = max_path_weight(modified)

            min_result = min(min_result, result)
    
    print(min_result)

solve()
```

### Answer3
```python
import sys


def main():
    data = sys.stdin.read().split()
    if not data:
        return

    n = int(data[0])
    m = int(data[1])
    a = []
    index = 2
    for i in range(n):
        row = list(map(int, data[index:index + m]))
        a.append(row)
        index += m

    INF = -10 ** 18
    dp1 = [[INF] * m for _ in range(n)]
    dp2 = [[INF] * m for _ in range(n)]

    dp1[0][0] = a[0][0]
    for i in range(n):
        for j in range(m):
            if i == 0 and j == 0:
                continue
            up = INF
            left = INF
            if i > 0:
                up = dp1[i - 1][j] + a[i][j]
            if j > 0:
                left = dp1[i][j - 1] + a[i][j]
            dp1[i][j] = max(up, left)

    dp2[n - 1][m - 1] = a[n - 1][m - 1]
    for i in range(n - 1, -1, -1):
        for j in range(m - 1, -1, -1):
            if i == n - 1 and j == m - 1:
                continue
            down = INF
            right = INF
            if i < n - 1:
                down = dp2[i + 1][j] + a[i][j]
            if j < m - 1:
                right = dp2[i][j + 1] + a[i][j]
            dp2[i][j] = max(down, right)

    S = dp1[n - 1][m - 1]

    candidates = []
    for i in range(n):
        for j in range(m):
            if dp1[i][j] + dp2[i][j] - a[i][j] == S:
                candidates.append((i, j, a[i][j]))

    ans = S
    for (i, j, val) in candidates:
        option1 = INF
        option2 = INF
        if i > 0 and j < m - 1:
            option1 = dp1[i - 1][j] + a[i - 1][j + 1] + dp2[i][j + 1]
        if j > 0 and i < n - 1:
            option2 = dp1[i][j - 1] + a[i + 1][j - 1] + dp2[i + 1][j]

        if option1 == INF and option2 == INF:
            F_val = S - val
        else:
            U = max(option1, option2)
            F_val = max(U, S - val)

        if F_val < ans:
            ans = F_val

    print(ans)


if __name__ == "__main__":
    main()
```

### Answer4
```python
import sys
import heapq

# 增加递归深度以防万一，虽然本解法主要用迭代
sys.setrecursionlimit(20000)

def solve():
    # 1. 读取输入
    input_data = sys.stdin.read().split()
    if not input_data:
        return
    
    iterator = iter(input_data)
    try:
        n = int(next(iterator))
        m = int(next(iterator))
    except StopIteration:
        return

    a = [[0] * (m + 2) for _ in range(n + 2)]
    for i in range(1, n + 1):
        for j in range(1, m + 1):
            a[i][j] = int(next(iterator))

    # 2. DP 计算 f 和 g
    # f[i][j]: (1,1) 到 (i,j) 的最大和
    f = [[-1] * (m + 2) for _ in range(n + 2)]
    f[1][1] = a[1][1]
    
    for i in range(1, n + 1):
        for j in range(1, m + 1):
            if i == 1 and j == 1: continue
            val = -1
            if i > 1 and f[i-1][j] != -1: val = max(val, f[i-1][j])
            if j > 1 and f[i][j-1] != -1: val = max(val, f[i][j-1])
            if val != -1:
                f[i][j] = val + a[i][j]

    # g[i][j]: (i,j) 到 (n,m) 的最大和
    g = [[-1] * (m + 2) for _ in range(n + 2)]
    g[n][m] = a[n][m]
    
    for i in range(n, 0, -1):
        for j in range(m, 0, -1):
            if i == n and j == m: continue
            val = -1
            if i < n and g[i+1][j] != -1: val = max(val, g[i+1][j])
            if j < m and g[i][j+1] != -1: val = max(val, g[i][j+1])
            if val != -1:
                g[i][j] = val + a[i][j]

    max_total = f[n][m]
    
    # 3. 提取一条关键路径 (Critical Path)
    # path 存储坐标 (r, c)，path_indices 存储坐标对应的路径索引(0-based)
    path = []
    curr_i, curr_j = 1, 1
    path.append((1, 1))
    
    while curr_i != n or curr_j != m:
        # 贪心寻找关键路径的下一个点
        # 下一个点必须满足: f[next] + g[next] - a[next] + a[curr] == f[curr] + g[curr]
        # 简化判断：只需要 f[next] + g[next] - a[next] == max_total 且 next 是邻居
        moved = False
        # 尝试向下
        if curr_i < n:
            ni, nj = curr_i + 1, curr_j
            if f[ni][nj] != -1 and g[ni][nj] != -1 and f[ni][nj] + g[ni][nj] - a[ni][nj] == max_total:
                curr_i, curr_j = ni, nj
                path.append((ni, nj))
                moved = True
        
        if not moved and curr_j < m:
            ni, nj = curr_i, curr_j + 1
            if f[ni][nj] != -1 and g[ni][nj] != -1 and f[ni][nj] + g[ni][nj] - a[ni][nj] == max_total:
                curr_i, curr_j = ni, nj
                path.append((ni, nj))
                moved = True
    
    path_len = len(path)
    path_map = {pos: idx for idx, pos in enumerate(path)} # (r,c) -> path_index

    # 4. 计算 L[i][j] 和 R[i][j]
    # L[i][j]: 能够到达(i,j)的关键路径节点的最大索引
    # R[i][j]: 从(i,j)出发能到达的关键路径节点的最小索引
    
    L = [[-1] * (m + 2) for _ in range(n + 2)]
    R = [[path_len + 1] * (m + 2) for _ in range(n + 2)]

    # 计算 L (DP 从左上到右下)
    for i in range(1, n + 1):
        for j in range(1, m + 1):
            if (i, j) in path_map:
                L[i][j] = path_map[(i, j)]
            else:
                val = -1
                if i > 1: val = max(val, L[i-1][j])
                if j > 1: val = max(val, L[i][j-1])
                L[i][j] = val
                
    # 计算 R (DP 从右下到左上)
    for i in range(n, 0, -1):
        for j in range(m, 0, -1):
            if (i, j) in path_map:
                R[i][j] = path_map[(i, j)]
            else:
                val = path_len + 1
                if i < n: val = min(val, R[i+1][j])
                if j < m: val = min(val, R[i][j+1])
                R[i][j] = val

    # 5. 收集“绕行”事件
    # 对于每个点 (i,j) (不一定要在路径外，路径上的点计算出来区间为空，不影响)
    # 它构成的路径权值为 val = f[i][j] + g[i][j] - a[i][j]
    # 它可以作为 bypass 覆盖关键路径上下标范围 (L[i][j] + 1) 到 (R[i][j] - 1)
    
    # events[k] 存储在索引 k 处“开始”生效的绕行路径: (权值, 结束索引)
    events = [[] for _ in range(path_len)]
    
    for i in range(1, n + 1):
        for j in range(1, m + 1):
            if f[i][j] == -1 or g[i][j] == -1: continue
            
            # 优化：如果在关键路径上，无需作为bypass（因为去掉它自己时，不能经过它自己）
            if (i, j) in path_map:
                continue

            u = L[i][j]
            v = R[i][j]
            
            # 有效区间 (u < k < v)
            start_idx = u + 1
            end_idx = v - 1
            
            if start_idx <= end_idx:
                path_val = f[i][j] + g[i][j] - a[i][j]
                events[start_idx].append((path_val, end_idx))

    # 6. 扫描线处理 + 堆维护最大值
    min_max_path = max_total # 初始设为 S (相当于删除路径外点的结果)
    
    # 最大堆，存储 (-val, end_index) 因为heapq是最小堆
    pq = []
    
    for k in range(path_len):
        # k 是当前我们要删除的关键路径节点的索引
        
        # 1. 加入所有在这里开始有效的 bypass
        for val, end_idx in events[k]:
            heapq.heappush(pq, (-val, end_idx))
            
        # 2. 移除过期的 bypass (end_idx < k)
        # 只要堆顶元素的 end_idx 小于 k，就弹出
        while pq and pq[0][1] < k:
            heapq.heappop(pq)
            
        # 3. 当前 k 处的最大绕行路径值
        max_bypass = 0
        if pq:
            max_bypass = -pq[0][0]
            
        # 4. 计算删除 node k 后的结果
        # 结果是 max(原来的路径去掉node k, 最大的绕行路径)
        node_r, node_c = path[k]
        current_res = max(max_total - a[node_r][node_c], max_bypass)
        
        min_max_path = min(min_max_path, current_res)

    print(min_max_path)

if __name__ == "__main__":
    solve()
```

### Answer5
```python
import sys

INF_NEG = -10**9

def read_ints():
    while True:
        line = sys.stdin.readline()
        if not line:
            return []
        if line.strip() != "":
            return list(map(int, line.strip().split()))

def solve():
    nm = read_ints()
    n, m = nm[0], nm[1]

    a = []
    for _ in range(n):
        a.append(read_ints())

    # forward DP
    L = [[INF_NEG]*m for _ in range(n)]
    cntF = [[0]*m for _ in range(n)]
    for i in range(n):
        for j in range(m):
            if i==0 and j==0:
                L[i][j]=a[i][j]; cntF[i][j]=1
            else:
                best=INF_NEG; ways=0
                if i>0 and L[i-1][j]!=INF_NEG:
                    v=L[i-1][j]+a[i][j]
                    if v>best: best=v; ways=cntF[i-1][j]
                    elif v==best: ways+=cntF[i-1][j]
                if j>0 and L[i][j-1]!=INF_NEG:
                    v=L[i][j-1]+a[i][j]
                    if v>best: best=v; ways=cntF[i][j-1]
                    elif v==best: ways+=cntF[i][j-1]
                L[i][j]=best
                cntF[i][j]=ways

    # backward DP
    R = [[INF_NEG]*m for _ in range(n)]
    cntB = [[0]*m for _ in range(n)]
    for i in reversed(range(n)):
        for j in reversed(range(m)):
            if i==n-1 and j==m-1:
                R[i][j]=a[i][j]; cntB[i][j]=1
            else:
                best=INF_NEG; ways=0
                if i+1<n and R[i+1][j]!=INF_NEG:
                    v=R[i+1][j]+a[i][j]
                    if v>best: best=v; ways=cntB[i+1][j]
                    elif v==best: ways+=cntB[i+1][j]
                if j+1<m and R[i][j+1]!=INF_NEG:
                    v=R[i][j+1]+a[i][j]
                    if v>best: best=v; ways=cntB[i][j+1]
                    elif v==best: ways+=cntB[i][j+1]
                R[i][j]=best; cntB[i][j]=ways

    Smax=L[n-1][m-1]; cnt_total=cntF[n-1][m-1]

    # forbid DP
    def max_path_forbid(x,y):
        if x==0 and y==0: return INF_NEG
        DP=[[INF_NEG]*m for _ in range(n)]
        for i in range(n):
            for j in range(m):
                if i==x and j==y: DP[i][j]=INF_NEG; continue
                if i==0 and j==0: DP[i][j]=a[i][j]; continue
                best=INF_NEG
                if i>0 and DP[i-1][j]!=INF_NEG: best=max(best, DP[i-1][j]+a[i][j])
                if j>0 and DP[i][j-1]!=INF_NEG: best=max(best, DP[i][j-1]+a[i][j])
                DP[i][j]=best
        return DP[n-1][m-1]

    ans=10**9
    for i in range(n):
        for j in range(m):
            max_through=L[i][j]+R[i][j]-a[i][j]
            if max_through<Smax:
                Fij=Smax
            else:
                ways_through=cntF[i][j]*cntB[i][j]
                if ways_through<cnt_total:
                    Fij=Smax
                else:
                    S_avoiding=max_path_forbid(i,j)
                    Fij=max(S_avoiding,Smax-a[i][j])
            ans=min(ans,Fij)
    print(ans)

if __name__=="__main__":
    solve()

```

### Answer6
```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;
const ll NEG = LLONG_MIN / 4;

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    int n, m;
    if (!(cin >> n >> m)) return 0;
    vector<vector<ll>> a(n+2, vector<ll>(m+2, 0));
    for (int i = 1; i <= n; ++i)
        for (int j = 1; j <= m; ++j)
            cin >> a[i][j];

    // DP L: max sum from (1,1) to (i,j)
    vector<vector<ll>> L(n+2, vector<ll>(m+2, NEG));
    L[1][1] = a[1][1];
    for (int i = 1; i <= n; ++i) {
        for (int j = 1; j <= m; ++j) {
            if (i==1 && j==1) continue;
            ll v1 = (i>1 ? L[i-1][j] : NEG);
            ll v2 = (j>1 ? L[i][j-1] : NEG);
            L[i][j] = max(v1, v2) + a[i][j];
        }
    }

    // DP R: max sum from (i,j) to (n,m)
    vector<vector<ll>> R(n+2, vector<ll>(m+2, NEG));
    R[n][m] = a[n][m];
    for (int i = n; i >= 1; --i) {
        for (int j = m; j >= 1; --j) {
            if (i==n && j==m) continue;
            ll v1 = (i < n ? R[i+1][j] : NEG);
            ll v2 = (j < m ? R[i][j+1] : NEG);
            R[i][j] = max(v1, v2) + a[i][j];
        }
    }

    // through and global max
    int Kmin = 2, Kmax = n + m;
    vector<vector<ll>> through(n+1, vector<ll>(m+1, NEG));
    ll G = NEG;
    for (int i = 1; i <= n; ++i) {
        for (int j = 1; j <= m; ++j) {
            through[i][j] = L[i][j] + R[i][j] - a[i][j];
            G = max(G, through[i][j]);
        }
    }

    // For each diagonal k = i+j, find:
    // count of cells with through == G,
    // maximum through on that diagonal,
    // second maximum through on that diagonal.
    int K = n + m;
    vector<int> cnt(K+1, 0);
    vector<ll> max1(K+1, NEG), max2(K+1, NEG);

    for (int i = 1; i <= n; ++i) {
        for (int j = 1; j <= m; ++j) {
            int k = i + j;
            ll val = through[i][j];
            if (val == G) cnt[k]++;

            if (val > max1[k]) {
                max2[k] = max1[k];
                max1[k] = val;
            } else if (val > max2[k]) {
                max2[k] = val;
            }
        }
    }

    // Collect candidate mandatory cells: those with through==G and cnt[k]==1
    vector<pair<int,int>> mandatory;
    for (int i = 1; i <= n; ++i) {
        for (int j = 1; j <= m; ++j) {
            int k = i + j;
            if (through[i][j] == G && cnt[k] == 1) {
                mandatory.emplace_back(i,j);
            }
        }
    }

    if (mandatory.empty()) {
        cout << G << "\n";
        return 0;
    }

    // For each mandatory cell, compute F = max( second largest through on same diag, G - a[i][j] )
    // If no second largest (i.e. max2[k] == NEG) then treat it as NEG so first term won't dominate.
    ll ans = LLONG_MAX;
    for (auto &pr : mandatory) {
        int i = pr.first, j = pr.second;
        int k = i + j;
        ll second = max2[k]; // maybe NEG
        ll term1 = second;   // best path that avoids this cell (might be NEG => impossible)
        ll term2 = G - a[i][j];
        ll F = max(term1, term2);
        // If term1 is NEG (no path avoiding) we just take term2 (which is >= -inf)
        ans = min(ans, F);
    }

    cout << ans << "\n";
    return 0;
}

```

### Answer7
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

vector<vector<unsigned long>> f, g, A;
unsigned long n = 0u, m = 0u;

long init(void) {
  unsigned long i, j, d;

  cin >> n >> m;
  // init f[][] and g[][]
  f.resize(n + 2u);
  g.resize(n + 2u);
  A.resize(n + 1u);

  for (i = 0u; i <= n + 1u; ++i)
    for (j = 0u; j <= m + 1u; ++j) {
      f.at(i).push_back(0u);
      g.at(i).push_back(0u);
    }

  for (i = 1u; i <= n; ++i) {
    A.at(i).push_back(0u);
    for (j = 1u; j <= m; ++j) {
      cin >> d;
      A.at(i).push_back(d);
    }
  }
  return 0l;
}

long dp_fg(void) {
  unsigned long i, j;
  for (i = 1u; i <= n; ++i)
    for (j = 1u; j <= m; ++j)
      f.at(i).at(j) = max(f.at(i - 1u).at(j), f.at(i).at(j - 1u)) + A.at(i).at(j);
  for (i = n; i >= 1u; --i)
    for (j = m; j >= 1u; --j)
      g.at(i).at(j) = max(g.at(i + 1u).at(j), g.at(i).at(j + 1u)) + A.at(i).at(j);
  return 0l;
}

unsigned long calczeromod(void) {
  unsigned long p, q, i;
  unsigned long max_pq, max_up, max_dn;
  unsigned long min_max = ~0ul;
  // For every (p, q), calculate the max value over modification A[p][q] = 0
  for (p = 1u; p <= n; ++p)
    for (q = 1u; q <= m; ++q) {
      // The max value of the path going through (p, q)
      max_pq = f.at(p).at(q) + g.at(p).at(q) - 2u * A.at(p).at(q);
      // The max value of the path going bypass (p, q) above (^) it
      max_up = max_dn = 0u;
      if (q < m)
        for (i = 1u; i < p; ++i)
          max_up = max(f.at(i).at(q) + g.at(i).at(q + 1u), max_up);
      if (q > 1)
        for (i = p + 1u; i <= n; ++i)
          max_dn = max(f.at(i).at(q - 1u) + g.at(i).at(q), max_dn);
      // The max value of all paths
      max_pq = max(max(max_pq, max_up), max_dn);
      min_max = min(max_pq, min_max);
    }
  return min_max;
}



int main() {
  init();
  dp_fg();
  cout << calczeromod() << endl;
  return 0;
}

```

### Answer8
```cpp
#include <bits/stdc++.h>

using namespace std;
using ll = long long;

const ll INF = 1e18;

bool check(int x, int y, int n,  int m) {
    return x >= 1 && x <= n && y >= 1 && y <= m;
}
ll solve(int x, int y, int n, int m, const vector<vector<ll>> &a, const vector<vector<ll>> &f, const vector<vector<ll>> &g) {
    set<pair<int,int>> S, T;
    for (int i = x - 1; i <= x + 1; ++i) {
        for (int j = y - 1; j <= y + 1; ++j) {
            if (i + j <= x + y && check(i, j, n, m)) S.insert(make_pair(i, j));
            if (i + j >= x + y && check(i, j, n, m)) T.insert(make_pair(i, j));
        }
    }
    ll ans = f[x][y] + g[x][y] - 2 * a[x][y];
    for (auto [x, y] : S) {
        vector<vector<ll>> h(3, vector<ll>(3, 0));
        h[0][0] = a[x][y];
        for (int i = 0; i <= 2; ++i) {
            for (int j = 0; j <= 2; ++j) {
                if (i == 0 && j == 0) {
                    if (T.count(make_pair(x + i, y + j))) {
                        ll ans_tmp = f[x][y] + g[x + i][y + j] - a[x][y] - a[x + i][y + j];
                        ans = max(ans, ans_tmp);
                    }
                    continue;
                }
                h[i][j] = -INF;
                if (i > 0) h[i][j] = max(h[i][j], h[i - 1][j]);
                if (j > 0) h[i][j] = max(h[i][j], h[i][j - 1]);
                h[i][j] += a[x + i][y + j];
                if (T.count(make_pair(x + i, y + j))) {
                    ll ans_tmp = f[x][y] + g[x + i][y + j] - a[x][y] - a[x + i][y + j];
                    ans = max(ans, ans_tmp);
                }
            }
        }
    }
    return ans;
}
int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    int n, m;
    cin >> n >> m;
    vector<vector<ll>> a(n + 5, vector<ll>(m + 5, -INF)); 
    vector<vector<ll>> f(n + 5, vector<ll>(m + 5, -INF)); 
    vector<vector<ll>> g(n + 5, vector<ll>(m + 5, -INF)); 
    for (int i = 1; i <= n; ++i) {
        for (int j = 1; j <= m; ++j) {
            cin >> a[i][j];
        }
    }
    f[1][1] = a[1][1];
    for (int i = 1; i <= n; ++i) {
        for (int j = 1; j <= m; ++j) {
            if (i == 1 && j == 1) continue;
            f[i][j] = max(f[i - 1][j], f[i][j - 1]) + a[i][j];
        }
    }
    g[n][m] = a[n][m];
    for (int i = n; i >= 1; --i) {
        for (int j = m; j >= 1; --j) {
            if (i == n && j == m) continue;
            g[i][j] = max(g[i + 1][j], g[i][j + 1]) + a[i][j];
        }
    }
    vector<vector<ll>> down_left(n + 5, vector<ll>(m + 5, -INF)); 
    vector<vector<ll>> up_right(n + 5, vector<ll>(m + 5, -INF)); 
    for (int i = 1; i <= n; ++i) {
        for (int j = 1; j <= m; ++j) {
            down_left[i][j] = up_right[i][j] = f[i][j] + g[i][j] - a[i][j];
        }
    }
    for (int i = n; i >= 1; --i) {
        for (int j = 1; j <= m; ++j) {
            down_left[i][j] = max(max(down_left[i][j - 1], down_left[i + 1][j]), down_left[i][j]);
        }
    }
    for (int i = 1; i <= n; ++i) {
        for (int j = m; j >= 1; --j) {
            up_right[i][j] = max(max(up_right[i - 1][j], up_right[i][j + 1]), up_right[i][j]);
        }
    }
    ll ans = 1e18;
    for (int i = 1; i <= n; ++i) {
        for (int j = 1; j <= m; ++j) {
            f[i][j] -= a[i][j];
            g[i][j] -= a[i][j];
            ll tmp = a[i][j];
            a[i][j] = 0;
            ll ans_tmp = solve(i, j, n, m, a, f, g);
            a[i][j] = tmp;
            f[i][j] += a[i][j];
            g[i][j] += a[i][j];
            ans_tmp = max(ans_tmp, down_left[i + 1][j - 1]);
            ans_tmp = max(ans_tmp ,up_right[i - 1][j + 1]);
            ans = min(ans, ans_tmp);
        }
    }
    printf("%lld\n", ans);
    return 0;
}
```

### Answer9
```cpp
#include <bits/stdc++.h>
using namespace std;
const long long NEG = -4e18;

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    int n, m;
    cin >> n >> m;
    vector<vector<long long>> a(n + 1, vector<long long>(m + 1));
    for (int i = 1; i <= n; ++i)
        for (int j = 1; j <= m; ++j)
            cin >> a[i][j];

    long long ans = LLONG_MAX;

    // 对每一个格子 (p,q) 作为修改点
    for (int p = 1; p <= n; ++p) {
        for (int q = 1; q <= m; ++q) {
            // 暂时修改
            long long old = a[p][q];
            a[p][q] = 0;

            // 重新计算从 (1,1) 到 (n,m) 的最大路径和
            vector<vector<long long>> dp(n + 1, vector<long long>(m + 1, NEG));
            dp[1][1] = a[1][1];
            for (int i = 1; i <= n; ++i) {
                for (int j = 1; j <= m; ++j) {
                    if (i == 1 && j == 1) continue;
                    dp[i][j] = max(dp[i-1][j], dp[i][j-1]) + a[i][j];
                }
            }

            ans = min(ans, dp[n][m]);

            // 恢复原值
            a[p][q] = old;
        }
    }

    cout << ans << "\n";
    return 0;
}

```

### Answer10
```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <climits>

using namespace std;

// 解决最大路径权重问题的动态规划函数
// A: 矩阵
// n, m: 矩阵维度
// p, q: 被修改为 0 的点坐标 (1-based)
// 返回: 修改后的矩阵的最大路径权重
long long max_path_weight_after_modification(
    const vector<vector<int>>& A, 
    int n, int m, 
    int p, int q
) {
    // DP 数组，存储从 (1, 1) 到 (i, j) 的最大路径权重
    // 使用 long long 防止路径权重过大溢出
    vector<vector<long long>> dp(n + 1, vector<long long>(m + 1, 0));

    // 处理边界
    for (int i = 1; i <= n; ++i) {
        for (int j = 1; j <= m; ++j) {
            // 获取当前点的值。如果当前点是要修改的点(p, q)，则值为 0。
            long long current_val = A[i-1][j-1]; // 矩阵A是0-based
            if (i == p && j == q) {
                current_val = 0;
            }

            // (1, 1) 是起点
            if (i == 1 && j == 1) {
                dp[i][j] = current_val;
            } 
            // 只有上边有路径
            else if (i == 1) {
                dp[i][j] = dp[i][j-1] + current_val;
            } 
            // 只有左边有路径
            else if (j == 1) {
                dp[i][j] = dp[i-1][j] + current_val;
            } 
            // 左右都有路径，取较大者
            else {
                dp[i][j] = max(dp[i-1][j], dp[i][j-1]) + current_val;
            }
        }
    }

    // 终点 (n, m) 的值即为最大路径权重
    return dp[n][m];
}


void solve() {
    int n, m;
    if (!(cin >> n >> m)) return;

    // A: 存储 n x m 矩阵 (使用 0-based 索引)
    vector<vector<int>> A(n, vector<int>(m));
    for (int i = 0; i < n; ++i) {
        for (int j = 0; j < m; ++j) {
            cin >> A[i][j];
        }
    }

    // 初始化最小的最大路径权重为一个极大值
    long long min_max_weight = LLONG_MAX;

    // 遍历所有可能的修改方案 M (即选择一个位置 (p, q) 将其值置为 0)
    // p, q 使用 1-based 索引，以匹配 DP 函数中的习惯
    for (int p = 1; p <= n; ++p) {
        for (int q = 1; q <= m; ++q) {
            
            // 1. 计算修改 A[p-1][q-1] = 0 后的最大路径权重 F(M_{p,q})
            long long current_max_weight = max_path_weight_after_modification(A, n, m, p, q);
            
            // 2. 更新 min F(M)
            min_max_weight = min(min_max_weight, current_max_weight);
        }
    }

    // 输出最终结果
    cout << min_max_weight << endl;
}

int main() {
    // 加快输入/输出速度
    ios_base::sync_with_stdio(false);
    cin.tie(NULL);

    solve();

    return 0;
}
```


# 改错题（2）
## 合法的h序列

### Answer1
```C++
// solve.cpp
// 编译: g++ -O2 -std=c++17 solve.cpp -o solve
#include <bits/stdc++.h>
using namespace std;
using int64 = long long;
const int64 MOD = 1000000007LL;

// count number of sequences A_1..A_n (with A_0=0, A_n=0) satisfying
//  - for each k: A_k >= 0
//  - for each k: 0 <= A_k + A_{k-1} <= M[k]  (k from 1..n; M indexed 0..n-1)
// M is vector<long long> length n
int64 count_even(const vector<int64>& M) {
    int n = (int)M.size();
    // dp_prev[a] = number ways with A_{k-1} = a after processing up to k-1
    // Initially k=0: only A_0=0
    vector<int64> dp_prev(1, 1); // dp_prev[0]=1
    int prev_max = 0; // max index available in dp_prev
    for (int k = 1; k <= n; ++k) {
        int64 Mk = M[k-1];
        if (Mk < 0) return 0; // impossible if any M < 0
        // A_k can range 0..Mk
        int cur_size = (int)min<int64>(Mk, INT_MAX - 5) + 1;
        vector<int64> dp_cur(cur_size, 0);
        // build prefix sums of dp_prev
        vector<int64> pref(prev_max + 1, 0);
        pref[0] = dp_prev[0] % MOD;
        for (int i = 1; i <= prev_max; ++i) {
            pref[i] = (pref[i-1] + dp_prev[i]) % MOD;
        }
        // transition:
        // dp_cur[a] = sum_{prev = 0 .. min(prev_max, Mk - a)} dp_prev[prev]
        for (int a = 0; a < cur_size; ++a) {
            int limit = (int)min<int64>(prev_max, Mk - a);
            if (limit >= 0) {
                dp_cur[a] = pref[limit];
            } else {
                dp_cur[a] = 0;
            }
        }
        // move
        dp_prev.swap(dp_cur);
        prev_max = cur_size - 1;
    }
    // after k=n, we need A_n == 0
    if (dp_prev.size() == 0) return 0;
    return dp_prev[0] % MOD;
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    int N;
    if (!(cin >> N)) return 0;
    vector<int64> H(N);
    for (int i = 0; i < N; ++i) cin >> H[i];

    // quick check: if any H[i] < 0 -> 0
    for (int i = 0; i < N; ++i) if (H[i] < 0) { cout << 0 << '\n'; return 0; }

    if (N % 2 == 0) {
        // n even: compute once
        vector<int64> M = H; // M_k = H_k
        int64 ans = count_even(M) % MOD;
        cout << (ans % MOD + MOD) % MOD << "\n";
        return 0;
    } else {
        // n odd: sum over t = 0..minH count_even(H - t)
        int64 minH = H[0];
        for (int i = 1; i < N; ++i) minH = min(minH, H[i]);
        int64 ans = 0;
        // For speed: we iterate t from 0..minH. Note each count_even uses reduced M_k = H_k - t.
        // Complexity ~ sum_{t=0..minH} (sum_k (H_k - t)) in worst case, so if H very large this may be heavy.
        for (int64 t = 0; t <= minH; ++t) {
            vector<int64> M(N);
            bool ok = true;
            for (int i = 0; i < N; ++i) {
                M[i] = H[i] - t;
                if (M[i] < 0) { ok = false; break; }
            }
            if (!ok) break;
            int64 add = count_even(M);
            ans += add;
            if (ans >= MOD) ans -= MOD;
        }
        cout << (ans % MOD + MOD) % MOD << "\n";
        return 0;
    }
}
```

### Answer2
```C++
#include <iostream>
#include <vector>
#include <algorithm>
#include <cstring>

using namespace std;

const int MOD = 1e9 + 7;

// 预分配最大内存以避免反复开辟空间的开销
// 假设 H 最大为 3000 左右，根据题目通常约束调整
const int MAX_VAL = 3005; 
int dp[MAX_VAL];
int pref[MAX_VAL];

// 核心计算函数：计算在给定 limits 下，从 0 到 0 的合法路径数
// 使用引用传递避免拷贝
int solve_dp_linear(int n, const vector<int>& limits) {
    // 检查是否有负数限制（快速剪枝）
    // 在外部循环中 limits 会不断减小
    for (int h : limits) {
        if (h < 0) return 0;
    }

    int current_max = 0;
    
    // 初始化：dp[0] = 1, 其余为 0
    // 由于复用全局数组，需要手动清理
    // 只需清理到 current_max 即可
    // 这里为了安全，初始清理一次，或者维护 current_max
    dp[0] = 1;
    // 注意：后续循环会覆盖用到 dp 值，但为了逻辑严密，
    // 第一步前应该确保 dp[1...current_max] 为 0。
    // 由于每次函数调用 limits 不同，这里简单重置：
    // 实际优化：上一轮 limits 更大，残留数据会被本次 limits 截断，
    // 但为了正确性，建议清理。
    // 考虑到调用频率，我们只清理上一轮用到的范围。
    // 但为了代码简洁且不影响大局，这里不做过度微操，依赖逻辑覆盖。

    for (int i = 1; i < MAX_VAL; ++i) dp[i] = 0;

    for (int i = 0; i < n; ++i) {
        int limit = limits[i];
        
        // 1. 计算前缀和
        // pref[x] = sum(dp[0]...dp[x-1])
        // 优化：pref[0] = 0, pref[j+1] = pref[j] + dp[j]
        pref[0] = 0;
        for (int j = 0; j <= current_max; ++j) {
            int val = pref[j] + dp[j];
            if (val >= MOD) val -= MOD;
            pref[j+1] = val;
        }
        
        // 2. 计算下一层 dp
        // next_dp[j] = sum(dp[u]) where u + j <= limit => u <= limit - j
        // 即 next_dp[j] = pref[ min(limit - j, current_max) + 1 ]
        
        int next_max = limit; // 下一层最大可能的 j
        // 注意：如果 limit < 0，循环不执行，current_max 变 0，符合预期
        
        for (int j = 0; j <= next_max; ++j) {
            int max_u = limit - j;
            if (max_u < 0) {
                dp[j] = 0;
            } else {
                // 取有效范围。如果 max_u > current_max，则取 pref[current_max + 1] (即所有和)
                int idx = (max_u > current_max) ? (current_max + 1) : (max_u + 1);
                dp[j] = pref[idx];
            }
        }
        
        // 清理超过 next_max 的部分，避免污染下一轮
        for (int j = next_max + 1; j <= max(current_max, next_max); ++j) {
            dp[j] = 0;
        }
        
        current_max = next_max;
    }

    return dp[0];
}

int main() {
    // I/O 优化
    ios_base::sync_with_stdio(false);
    cin.tie(NULL);

    int n;
    if (cin >> n) {
        vector<int> H(n);
        int min_h = 2000000000;
        for (int i = 0; i < n; ++i) {
            cin >> H[i];
            if (H[i] < min_h) min_h = H[i];
        }

        if (n % 2 == 0) {
            // 偶数情况：直接计算一次
            cout << solve_dp_linear(n, H) << endl;
        } else {
            // 奇数情况：枚举 k
            // 为了速度，我们手动在循环内进行 limits 的减法，
            // 而不是每次构造新 vector。
            long long total_ans = 0;
            
            // k 的范围是从 0 到 min(H)
            // 我们直接修改 solve_dp_linear 逻辑，或者传入偏移量
            // 为了极致性能，我们将 solver 内联或稍微修改一下接口。
            // 这里为了代码清晰，我们传入 vector 副本的引用? 
            // 不，拷贝 vector 太慢。我们修改 solver 接收一个 decrement 参数。
            
            // 这种 O(N*H^2) 的做法在 N,H=3000 时比较危险，
            // 但通过上面的 solve_dp_linear 内部非常紧凑的数组操作，
            // 能够最大程度利用 CPU 缓存。
            
            // 优化策略：
            // 不重新分配内存，不拷贝 vector。
            // 在 solve_dp 内部计算时，使用的 limit 为 (H[i] - k)。
            
            for (int k = 0; k <= min_h; ++k) {
                // 内联逻辑开始 ---
                int current_max = 0;
                // 重置 dp
                // 这里只需要重置 dp[0]=1, 其他之前用过的清零
                // 但简单起见我们假定 H 递减导致范围缩小，
                // 只要每次循环开始前 dp 干净即可。
                // 我们只清空前一次用到的范围。
                // 初始状态只有 dp[0] 有值。
                // 为安全起见：
                memset(dp, 0, sizeof(int) * (min_h + 2)); 
                dp[0] = 1;
                
                bool possible = true;
                
                for (int i = 0; i < n; ++i) {
                    int limit = H[i] - k; // 核心偏移
                    // 理论上 k <= min_h，所以 limit >= 0
                    
                    // 计算前缀和
                    pref[0] = 0;
                    for (int j = 0; j <= current_max; ++j) {
                        int val = pref[j] + dp[j];
                        if (val >= MOD) val -= MOD;
                        pref[j+1] = val;
                    }
                    
                    int next_max = limit;
                    for (int j = 0; j <= next_max; ++j) {
                        int max_u = limit - j;
                        int idx = (max_u > current_max) ? (current_max + 1) : (max_u + 1);
                        dp[j] = pref[idx];
                    }
                    
                    // 归零多余部分
                    // 实际上下一轮 current_max 将是 next_max，
                    // 我们只需要保证 next_max 之外的是 0。
                    // 由于 next_max 可能会比 current_max 小，也要处理
                    // 但上面循环只填充到 next_max。
                    // 如果 current_max > next_max，需要清空尾部吗？
                    // 下一轮的计算依赖 pref，pref 依赖 dp。
                    // 如果 dp[next_max+1] 有脏数据，会影响下一轮 pref。
                    // 所以必须清空。
                    for (int j = next_max + 1; j <= current_max; ++j) dp[j] = 0;
                    
                    current_max = next_max;
                }
                // 内联逻辑结束 ---
                
                total_ans = (total_ans + dp[0]) % MOD;
            }
            cout << total_ans << endl;
        }
    }
    return 0;
}
```

### Answer3
```C++
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

const int MOD = 1e9 + 7;

int main() {
    int N;
    cin >> N;
    vector<int> H(N + 1);
    for (int i = 1; i <= N; i++) {
        cin >> H[i];
    }

    int minH = *min_element(H.begin() + 1, H.end());
    long long ans = 0;

    for (int t = 0; t <= minH; t++) {
        vector<vector<long long>> f(N + 1, vector<long long>(1001, 0));
        f[0][0] = 1;

        for (int i = 1; i <= N; i++) {
            vector<long long> prefix(1002, 0);
            for (int j = 0; j <= 1000; j++) {
                prefix[j + 1] = (prefix[j] + f[i - 1][j]) % MOD;
            }

            for (int x_i = 0; x_i <= H[i] - t; x_i++) {
                int max_x_prev = H[i] - t - x_i;
                if (max_x_prev < 0) break;
                f[i][x_i] = prefix[max_x_prev + 1] % MOD;
            }
        }
        
        // 剪枝：当N为偶数时，直接输出当前结果并退出
        if (N % 2 == 0) {
            cout << f[N][0] % MOD << endl;
            return 0;
        }
        ans = (ans + f[N][0]) % MOD;
    }

    cout << ans << endl;
    return 0;
}
```

### Answer4
```C++
#include<cstdio>
#include<iostream>
#include<cstring>
#include<algorithm>
using namespace std;

const int N=110,mod=1e9+7;
int n,mn=0x3f3f3f3f;
int a[N]; 
int f[N][N*10],sum[N][N*10],ans; 

int main() {
    cin>>n;
    for(int i=1;i<=n;i++) {
        cin>>a[i];
        mn=min(a[i],mn);
        a[i]++;
    }
    
    for(int y=0;y<=mn;y++) {
        memset(f,0,sizeof f);
        memset(sum,0,sizeof sum);
        f[0][0]=1;
        sum[0][0]=1;
        for(int i=1;i<=n;i++) a[i]--;
        for(int i=1;i<=n;i++)
            for(int k=0;k<=a[i];k++) {
                if(sum[i-1][a[i]-k]) f[i][k]=sum[i-1][a[i]-k];
                else f[i][k]=sum[i-1][a[i-1]];
                sum[i][k]=f[i][k];
                if(k) sum[i][k]=(sum[i][k]+sum[i][k-1])%mod; 
            }

        
        int tmp=0;
        tmp=f[n][0]%mod;
        if(!(n%2)) {
            cout<<tmp<<'\n';
            return 0;
        }
        ans=(ans+tmp)%mod;
    }
    
    cout<<ans<<'\n';    
    return 0;
}
```

### Answer5
```C++
#include<algorithm>
#include<cstdio>
#include<cstring>
#include<iostream>
#include<queue>
#include<vector>
using namespace std;
inline int mmin(register int x, register int y) {
    return x > y ? y : x;
}
const long long mod = 1000000007;
int n;
int a[107], mn = 1000;
long long ans, g[107][1007];
signed main() {
    cin >> n;
    for (int i = 1; i <= n; ++i) {
        cin >> a[i];
        mn = mmin(mn, a[i]);
    }
    for (int i = 0; i <= a[1]; ++i) g[1][i] = i + 1; //这里省略了求和
    if (n % 2 == 0)mn = 0;
    for (int d = 0; d <= mn; ++d) {
        for (int i = 2; i <= n; ++i) {
            g[i][0] = g[i - 1][mmin(a[i], a[i - 1]) - d];
            for (int j = 1; j <= a[i] - d; ++j) {
                g[i][j] = g[i][j - 1] + g[i - 1][mmin(a[i] - j, a[i - 1]) - d];
                if (g[i][j] >= mod) g[i][j] -= mod;
            }
        }
        ans += g[n][0]; if (ans >= mod) ans -= mod;
    }
    cout << ans << endl;
    return 0;
}
```

### Answer6
```C++
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

const int MOD = 1e9 + 7;

int main() {
    int N;
    cin >> N;
    vector<int> H(N + 1);
    for (int i = 1; i <= N; i++) {
        cin >> H[i];
    }

    int minH = *min_element(H.begin() + 1, H.end());
    long long ans = 0;

    if (N % 2 == 0)minH = 0;

    for (int t = 0; t <= minH; t++) {
        vector<vector<long long>> f(N + 1, vector<long long>(1001, 0));
        f[0][0] = 1;

        for (int i = 1; i <= N; i++) {
            // 前缀和优化
            vector<long long> prefix(1002, 0);
            for (int j = 0; j <= 1000; j++) {
                prefix[j + 1] = (prefix[j] + f[i - 1][j]) % MOD;
            }

            for (int x_i = 0; x_i <= H[i] - t; x_i++) {
                int max_x_prev = H[i] - t - x_i;
                if (max_x_prev < 0) break;
                f[i][x_i] = prefix[max_x_prev + 1] % MOD;
            }
        }
        ans = (ans + f[N][0]) % MOD;
    }

    cout << ans << endl;
    return 0;
}
```

### Answer7
```C++
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

const int MOD = 1e9 + 7;

int main() {
    // 优化输入效率
    ios_base::sync_with_stdio(false);
    cin.tie(NULL);

    int N;
    if (!(cin >> N)) return 0;

    vector<int> H(N + 1);
    int minH = 2000000000; // 初始化为一个大数
    
    for (int i = 1; i <= N; i++) {
        cin >> H[i];
        minH = min(minH, H[i]);
    }

    long long ans = 0;

    // 核心修正：
    // 如果 N 是偶数，同一个 h 序列可能对应多个 t。
    // 但它们通过变换都能规约到 t=0 的情况。
    // 所以 N 为偶数时只计算 t=0 即可避免重复计数。
    // 如果 N 是奇数，t 是唯一的，必须枚举所有可能的 t。
    int max_t_loop = (N % 2 == 0) ? 0 : minH;

    for (int t = 0; t <= max_t_loop; t++) {
        // dp[i][j] 表示考虑到第 i 个数，且第 i 个位置的操作数 x_i 为 j 时的方案数
        // 根据题目约束，x 的最大值不会超过 max(H)，这里取 1005 足够
        // 使用滚动数组或复用 vector 也可以，但为了清晰保留原结构逻辑
        
        // f[curr_val] = sum(f[prev_val])
        // 初始化：第0个位置操作数为0，方案数为1
        // 这里的dp数组大小取决于 H 的最大值，题目给出的 H 可达 1000
        int max_val = 1005; 
        vector<long long> dp(max_val, 0);
        dp[0] = 1;

        for (int i = 1; i <= N; i++) {
            vector<long long> next_dp(max_val, 0);
            vector<long long> prefix(max_val + 1, 0);

            // 计算前缀和优化转移：prefix[k] = sum(dp[0]...dp[k-1])
            for (int k = 0; k < max_val; k++) {
                prefix[k + 1] = (prefix[k] + dp[k]) % MOD;
            }

            // 转移方程：
            // h[i] = x[i-1] + x[i] + t
            // => x[i-1] = h[i] - t - x[i]
            // 约束：0 <= x[i-1] 且 x[i-1] <= H[i-1] (这个在上一轮DP已经隐式包含)
            // 本轮约束：0 <= h[i] <= H[i] (即输入限制)
            // 以及 x[i] >= 0, x[i-1] >= 0
            
            // 对于当前的 x_i (即 j)，合法的 x_{i-1} 的范围是：
            // x_{i-1} = h_i - t - j
            // 由于 h_i 是变量，但在DP中我们只关心计数。
            // 实际上题目约束是 h_i <= H[i]。
            // 等价于 x_{i-1} + x_i + t <= H[i]
            // => x_{i-1} <= H[i] - t - x_i
            
            // 修正DP含义：dp[j] 代表 x_i = j 的方案数。
            // x_{i-1} 的取值范围是 [0, H[i] - t - j]
            
            int limit = H[i] - t; 
            for (int j = 0; j <= limit; j++) {
                // j 代表当前的 x_i
                // 我们需要 sum(dp[prev_x]) 其中 prev_x <= H[i] - t - j
                int max_prev_x = H[i] - t - j;
                
                // max_prev_x 必须非负，且不能超过上一轮数组的界限
                if (max_prev_x >= 0) {
                    int sum_idx = min(max_prev_x + 1, max_val);
                    next_dp[j] = prefix[sum_idx]; 
                }
            }
            dp = next_dp;
        }
        
        // 最后要求 x_n = 0，所以累加 dp[0]
        ans = (ans + dp[0]) % MOD;
    }

    cout << ans << endl;

    return 0;
}
```

### Answer8
```Rust
use std::io::{self, BufRead};

const MODULUS: i64 = 1_000_000_007;

fn main() {
    let stdin = io::stdin();
    let mut lines = stdin.lock().lines();

    let n: usize = lines.next().unwrap().unwrap().trim().parse().unwrap();

    let mut a = vec![0i32; n + 1];
    let mut mn: i32 = 1000;

    let input_line = lines.next().unwrap().unwrap();
    let nums: Vec<i32> = input_line
        .split_whitespace()
        .map(|x| x.parse().unwrap())
        .collect();

    for i in 1..=n {
        a[i] = nums[i - 1];
        mn = mn.min(a[i]);
    }

    let mut g = vec![vec![0i64; 1007]; 107];
    let mut ans: i64 = 0;

    // Initialize first row
    for i in 0..=a[1] as usize {
        g[1][i] = (i + 1) as i64;
    }

    if n % 2 == 0 {
        mn = 0;
    }

    for d in 0..=mn {
        for i in 2..=n {
            g[i][0] = g[i - 1][a[i].min(a[i - 1]) as usize - d as usize];

            for j in 1..=(a[i] - d) as usize {
                g[i][j] =
                    g[i][j - 1] + g[i - 1][(a[i] - j as i32).min(a[i - 1]) as usize - d as usize];
                if g[i][j] >= MODULUS {
                    g[i][j] -= MODULUS;
                }
            }
        }
        ans += g[n][0];
        if ans >= MODULUS {
            ans -= MODULUS;
        }
    }

    println!("{ans}");
}
```

### Answer9
```C
#include <stdio.h>

#define MOD 1000000007

int main() {
    int n;
    scanf("%d", &n);
    
    int a[101];
    int mn = 1000;
    
    for (int i = 1; i <= n; i++) {
        scanf("%d", &a[i]);
        if (a[i] < mn) {
            mn = a[i];
        }
    }
    
    if (n % 2 == 0) {
        mn = 0;
    }
    
    long long ans = 0;
    
    for (int d = 0; d <= mn; d++) {
        long long g[101][1007] = {0};
        
        // 初始化第一行
        for (int i = 0; i <= a[1]; i++) {
            g[1][i] = i + 1;
        }
        
        // 动态规划
        for (int i = 2; i <= n; i++) {
            g[i][0] = g[i-1][(a[i] < a[i-1] ? a[i] : a[i-1]) - d];
            
            for (int j = 1; j <= a[i] - d; j++) {
                g[i][j] = g[i][j-1] + g[i-1][(a[i]-j < a[i-1] ? a[i]-j : a[i-1]) - d];
                if (g[i][j] >= MOD) {
                    g[i][j] -= MOD;
                }
            }
        }
        
        ans = (ans + g[n][0]) % MOD;
    }
    
    printf("%lld\n", ans);
    
    return 0;
}
```

### Answer10
```C++
#include <bits/stdc++.h>
using namespace std;

static const int MOD = 1'000'000'007;

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    int n;
    cin >> n;
    vector<int> H(n + 1);
    int maxH = 0, minH = INT_MAX;
    for (int i = 1; i <= n; i++) {
        cin >> H[i];
        maxH = max(maxH, H[i]);
        minH = min(minH, H[i]);
    }

    auto solve_for_t = [&](int t) -> int {
        // 需要满足 H[i] - t >= 0，否则该层 t 无解
        for (int i = 1; i <= n; i++) {
            if (H[i] - t < 0) return 0;
        }

        vector<int> dp(maxH + 1, 0), ndp(maxH + 1, 0);
        dp[0] = 1; // x0 = 0

        for (int i = 1; i <= n; i++) {
            int lim = H[i] - t; // x_{i-1} + x_i <= lim
            // 前缀和
            vector<int> pref(maxH + 2, 0);
            for (int j = 0; j <= maxH; j++) {
                pref[j + 1] = pref[j] + dp[j];
                if (pref[j + 1] >= MOD) pref[j + 1] -= MOD;
            }

            fill(ndp.begin(), ndp.end(), 0);
            for (int xi = 0; xi <= lim; xi++) {
                int max_prev = lim - xi;
                // sum dp[0..max_prev] = pref[max_prev+1]
                ndp[xi] = pref[max_prev + 1];
            }
            dp.swap(ndp);
        }

        // 要求 x_n = 0
        return dp[0];
    };

    long long ans = 0;

    if (n % 2 == 0) {
        // 偶数：只需 t = 0（避免对同一 h 的多次计数）
        ans = solve_for_t(0);
    } else {
        // 奇数：t 唯一，不会重复计数，正常枚举
        for (int t = 0; t <= minH; t++) {
            ans += solve_for_t(t);
            ans %= MOD;
        }
    }

    cout << ans % MOD << "\n";
    return 0;
}
```