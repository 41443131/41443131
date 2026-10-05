# 資料結構作業一

## 題目一：Ackermann 函數

### 一、遞迴版本

Ackermann 函數的定義如下：

\[
A(m,n)=
\begin{cases}
 n+1, & m=0 \\
 A(m-1,1), & n=0 \\
 A(m-1,A(m,n-1)), & \text{其他情況}
\end{cases}
\]

我依照題目給的三種情況撰寫遞迴函數：

```cpp
#include <iostream>
using namespace std;
int a(int m, int n)
{
    if (m == 0)
    {
        return n + 1;
    }
    else if (n == 0)
    {
        return a(m - 1, 1);
    }
    else
    {
        return a(m - 1, a(m, n - 1));
    }
}
int main()
{
    int m = 0, n = 0;
    cin >> m >> n;
    cout << a(m, n) << endl;
    return 0;
}
```

當 `m == 0` 時，回傳 `n + 1`；當 `n == 0` 時，計算 `a(m - 1, 1)`；其他情況則使用巢狀遞迴計算。

### 二、非遞迴版本

非遞迴版本不能直接呼叫函數自己，因此我使用陣列 `x` 模擬堆疊，並使用 `top` 記錄陣列目前的位置。

```cpp
#include <iostream>
using namespace std;
int a(int m, int n)
{
    int x[10000];
    int top = -1;
    x[++top] = m;
    while (top >= 0)
    {
        m = x[top--];
        if (m == 0)
        {
            n += 1;
        }
        else if (n == 0)
        {
            n = 1;
            x[++top] = m - 1;
        }
        else
        {
            n -= 1;
            x[++top] = m - 1;
            x[++top] = m;
        }
    }
    return n;
}
int main()
{
    int m = 0, n = 0;
    cin >> m >> n;
    cout << a(m, n) << endl;
    return 0;
}
```

在一般情況下：

```cpp
A(m,n) = A(m-1,A(m,n-1))
```

程式先將 `m - 1` 放入陣列，再將 `m` 放入陣列。因為陣列是後進先出，所以會先處理 `m`，再處理 `m - 1`，這樣可以模擬原本的遞迴順序。

### 測試結果

| 輸入 | 輸出 |
|---|---:|
| `0 0` | `1` |
| `1 2` | `4` |
| `2 3` | `9` |
| `3 3` | `61` |
| `4 0` | `65533` |

Ackermann 函數成長速度很快，因此輸入太大的數字可能造成執行時間過久、整數溢位或陣列空間不足。

---

## 題目二：冪集 Powerset

一個集合的冪集是由這個集合的所有子集合組成。例如，集合 `{a, b, c}` 有以下 8 個子集合：

```text
{}
{a}
{b}
{a, b}
{c}
{a, c}
{b, c}
{a, b, c}
```

集合有 3 個元素時，子集合數量為：

\[
2^3=8
\]

### 解題方法

每個元素都有兩種選擇：

1. 不放入目前的子集合；
2. 放入目前的子集合。

程式使用遞迴處理這兩種情況。`answer` 陣列用來暫時儲存目前建立中的子集合，`k` 表示目前已經放入幾個元素。

```cpp
#include <iostream>
using namespace std;
void powerset(char s[], int n, char answer[], int k)
{
    if (n == 0)
    {
        if (k == 0)
        {
            cout << "{}";
        }
        else
        {
            cout << "{";

            for (int i = 0; i < k; i++)
            {
                cout << answer[i];
                if (i < k - 1)
                {
                    cout << ", ";
                }
            }
            cout << "}";
        }
        cout << endl;
        return;
    }
    // 不選目前的元素
    powerset(s, n - 1, answer, k);
    // 選目前的元素
    answer[k] = s[n - 1];
    powerset(s, n - 1, answer, k + 1);
}
int main()
{
    char s[3] = {'a', 'b', 'c'};
    int n = 3;
    char answer[3];
    int k = 0;
    powerset(s, n, answer, k);
    return 0;
}
```

### 程式說明

當 `n == 0` 時，代表所有元素都已經決定是否選入，因此將 `answer` 中的內容印出來。

這一行：

```cpp
powerset(s, n - 1, answer, k);
```

表示不選目前的元素。

這兩行：

```cpp
answer[k] = s[n - 1];
powerset(s, n - 1, answer, k + 1);
```

表示選擇目前的元素，並將它放入 `answer` 中。

### 輸出結果

程式會輸出 8 個子集合，例如：

```text
{}
{a}
{b}
{b, a}
{c}
{c, a}
{c, b}
{c, b, a}
```

雖然部分集合中的元素順序和題目範例不同，例如 `{b, a}` 和 `{a, b}`，但集合中的元素沒有順序，因此兩者代表相同的集合。

---

## 結論

本次作業完成了以下內容：

1. 使用遞迴函數計算 Ackermann 函數。
2. 使用陣列模擬堆疊，完成 Ackermann 函數的非遞迴版本。
3. 使用遞迴方法列出集合的所有子集合。
4. 問題二的冪集程式不使用 `<vector>`，問題一的非遞迴程式也不使用 `<stack>`。
