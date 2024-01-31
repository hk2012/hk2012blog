---
title: 线性代数（数学二）
category: 数学
math: true
abbrlink: 17654
date: 2023-09-01 16:45:53
---

# 行列式

- 概念：行列式是一个[数]{.rainbow}，它是不同行不同列元素乘积的[代数和]{.red}

:::info
- 两行两列行列式 （主对角线相乘 - 副对角线相乘）
:::

- 高中时的写法
    $$
    \left|\begin{array}{c}
    a & b \\
    c & d \\
    \end{array}\right| = ad - bc
    $$ 

- 大学的写法
    $$
    \left|\begin{array}{c}
    a_{11} & a_{12} \\
    a_{21} & a_{22} \\
    \end{array}\right| = a_{11}a_{22} - a_{12}a_{21}
    $$ 
:::info
三行三列行列式 （主对角线相乘 - 副对角线相乘）
:::
$$
\left|\begin{array}{c}
a_{11} & a_{12} & a_{13} \\
a_{21} & a_{22} & a_{23} \\
a_{31} & a_{32} & a_{33} \\
\end{array}\right| = a_{11}a_{22}a_{33} + a_{12}a_{23}a_{31} + a_{13}a_{21}a_{32} - a_{13}a_{22}a_{31} - a_{23}a_{32}a_{11} - a_{33}a_{12}a_{21}
$$ 

- 逆序数: 如果一个大的数排在小的数之前，就称这两个数构成了一个逆序。一个排列的逆序总数则称为这个排列的逆序数
    - $N(54123) = 4 + 3 + 0 + 0 + 0 = 7$
- 偶排列: 逆序数为偶数的排列
- 奇排列: 逆序数为奇数的排列

- 对换: 交换两个数，如果排列作偶数次，排列[性质不变]{.red}；排列作奇数次，排列性质则会[发生改变]{.red}
    - $N(54213) = 4 + 3 + 1 + 0 + 0 = 8$

- n阶行列式
    - 按行排列：行标取标准排列，列标取排列的所有可能，从不同行不同列取出n个元素相乘。符号由列标排列的奇偶性决定
    - 主对角线的上三角下三角行列式等于[主对角线相乘]{.red}
    - 负对角线的上三角下三角行列式等于$(-1^{\frac{n(n-1)}{2}})$[负对角线相乘]{.red}
    - 按列排列：列标取标准排列，行标取排列的所有可能，从不同行不同列取出n个元素相乘。符号由行标排列的奇偶性决定

:::info
行列式五大性质
:::

1. 经过转置行列式的值不变 [(行列式的行和列的性质相同)]{.rainbow}

2. 两行行列式互换位置，行列式变号

    - 两行行列式相同，行列式的值为0 

3. 某行(或列)行列式如有公因子K,则可把K提出行列式记号外（亦即用数K乘列式|A|等于用K乘它的的某行或列）

    - 某行（或列）的元素都为0，行列式的值为0

    - 若两行（或列）的元素对应成比例，行列式的值为0

4. 如果行列式某行（或列）是两个元素之和，则可把行列式拆成拆成两行列之和。
5. 把某行（或列）的K倍加到另一行（或列），行列式的值不变。

:::info
转置
:::
- 将行列式的行变成列，列变成行，符号 $D^T$

:::info
余子式
:::

- 去掉某个元素所在行和所在列的元素去掉，所形成的新的行列式

:::info
代数余子式
:::

- 在原来余子式的前面加上（-1）的行列下标和的次方数。 $(-1^{i+j})$

- 异乘变零定理

- 拉普拉斯
    - 取定k行，由K行元素组成的所有K阶子式与代数余子式乘积之后 = Determinant

# 矩阵
- Markdown 矩阵写法

```java
- 常规写法
    $$\begin{matrix}
    1&1&1&1\\
    2&2&2&2\\
    4&4&4&4\\
    \end{matrix}$$

- 在起始、结束标记用下列词替换 matrix
    pmatrix：小括号边框
    bmatrix：中括号边框
    Bmatrix：大括号边框
    vmatrix：单竖线边框
    Vmatrix：双竖线边框
```

- 伴随矩阵：只有方阵才有伴随矩阵
    - 求所有元素的代数余子式
    - 按行求的代数余子式，按列放构成的矩阵
- 逆矩阵
    - 未必所有的方阵都可逆
    - 若可逆，逆矩阵必唯一
    - 定理：
        - A可逆充要条件 ：$|A| \neq 0 ,A^{-1} = \frac{1}{|A|}A^*$ 
        - A可逆必要条件：$|AA^{-1}| = |E| |A||A^{-1} = 1| |A| \neq 0$
:::info
初等变换
:::
        
- 本质：是对矩阵的一种变换，矩阵与矩阵之间用符号 $\rightarrow$，不能使用 =

分别呈现为：
- 交换两行
    - 例：将第一行和第二行交换
    - $$\begin{bmatrix}
        1&1&1&1\\
        2&2&2&2\\
        4&4&4&4\\
        \end{bmatrix}
        \longrightarrow
        \begin{bmatrix}
        2&2&2&2\\
        1&1&1&1\\
        4&4&4&4\\
        \end{bmatrix}
        $$
    - 当该矩阵为方阵时 $|A| \rightarrow |B|$，该矩阵的行列式为$|A| = -|B|$
- 用$k \quad(k \neq 0)$乘某一行 
    - 例：将第一行乘以6
    - $$\begin{bmatrix}
        1&1&1&1\\
        2&2&2&2\\
        4&4&4&4\\
        \end{bmatrix}
        \longrightarrow
        \begin{bmatrix}
        6&6&6&6\\
        2&2&2&2\\
        4&4&4&4\\
        \end{bmatrix}
        $$
    - 当该矩阵为方阵时 $|A| \rightarrow |B|$，该矩阵的行列式为$|A| = k|B|$
- 某一行的 $l$ 倍加到另一行上去 
    -  例：将第一行的-4倍加到第三行上去
    - $$\begin{bmatrix}
        1&1&1&1\\
        2&2&2&2\\
        4&4&4&4\\
        \end{bmatrix}
        \longrightarrow
        \begin{bmatrix}
        1&1&1&1\\
        2&2&2&2\\
        0&0&0&0\\
        \end{bmatrix}
        $$
    - 当该矩阵为方阵时 $|A| \rightarrow |B|$，该矩阵的行列式为$|A| = |B|$

- 任意矩阵可以通过初等变换化为标准型
    - 标准型
        - $A_{44}$的标准型为5种
        - $$\begin{bmatrix}
            0&0&0&0\\
            0&0&0&0\\
            0&0&0&0\\
            0&0&0&0\\
            \end{bmatrix}
            \begin{bmatrix}
            1&0&0&0\\
            0&0&0&0\\
            0&0&0&0\\
            0&0&0&0\\
            \end{bmatrix}
            \begin{bmatrix}
            1&0&0&0\\
            0&1&0&0\\
            0&0&0&0\\
            0&0&0&0\\
            \end{bmatrix}
            \begin{bmatrix}
            1&0&0&0\\
            0&1&0&0\\
            0&0&1&0\\
            0&0&0&0\\
            \end{bmatrix}
            \begin{bmatrix}
            1&0&0&0\\
            0&1&0&0\\
            0&0&1&0\\
            0&0&0&1\\
            \end{bmatrix}
            $$
    - 例：将矩阵变化为上面5种的一种
    - $$\begin{bmatrix}
        1&2&1\\
        1&-1&0\\
        0&1&1\\
        1&3&1\\
        \end{bmatrix}
        \longrightarrow
        \begin{bmatrix}
        1&2&1\\
        0&1&1\\
        0&1&1\\
        0&1&1\\
        \end{bmatrix}
        \longrightarrow
        \begin{bmatrix}
        1&0&-1\\
        0&1&1\\
        0&0&0\\
        0&0&0\\
        \end{bmatrix}
        \longrightarrow
        \begin{bmatrix}
        1&0&0\\
        0&1&0\\
        0&0&0\\
        0&0&0\\
        \end{bmatrix}
        $$

- 初等变换的性质
    - 反身性 $A \cong A$
        
    - 对称性 $A \cong B \rightarrow B \cong A$
    
    - 传递性 $A \cong B \quad B \cong C  \rightarrow A \cong C$

- 特征值

- 特征向量

- 行列式

# 向量
:::info
定义
:::

- n个数 $a_1 .... a_n$ 组成的有序数组 $(a_1 , a_2 , ... , a_n)$

- 行向量

- 列向量

- $k\alpha = 0 \Rightarrow k = 0 \quad or \quad \alpha = 0$

:::info
向量间的线性关系
:::

- 线性组合
    - $\beta,\alpha_1,\alpha_2,\dots,\alpha_n$是m维向量，若存在 $k_1,k_2,\dots,k_n$,使$\beta = k_1\alpha_1 + k_2\alpha_2 + \dots +k_n\alpha_n$
        - $$\binom{1}{2} = 1 \times \binom{1}{0} + 2 \times \binom{0}{1}$$
    - 系数表示可以全取0
        - $$\binom{0}{0} = 0 \times \binom{1}{0} + 0 \times \binom{0}{1}$$
:::info
性质
:::
- 零向量可由任意向量组表示
    - $$0 = 0 \times \alpha_1 + 0 \times \alpha_2 + \dots + 0 \times \alpha_n$$
- 向量组中任一向量可由向量组表示
    - $$\alpha_3 = 0 \times \alpha_1 + 0 \times \alpha_2 + 1 \times \alpha_3 + 0 \times \alpha_4$$
- 任意向量可由$\epsilon_1 = (1,0,\dots,0),\epsilon_2 = (0,1,\dots,0),\dots,\epsilon_n = (0,0,\dots,1),$表示 
    - $$(1,2,3) = 1 \times (1,0,0) + 2 \times (0,1,0) + 3 \times (0,0,1)$$

:::info
$$\beta = (-3,2,-4),\alpha_1 = (1,0,1),\alpha_2 = (2,1,0),\alpha_3 = (-1,1,-2)$$
:::
- 解：
$$\quad \beta = k_1\alpha_1 + k_2\alpha_2 + k_3\alpha_3$$
$$(-3,2,-4) = k_1(1,0,1) + k_2(2,1,0) + k_3(-1,1,-2)$$
$$\left\{
\begin{array}{c}       
    k_1 + 2k_2 - k_3 = -3 \\
    k_2 + k_3 = 2 \\
    k_1 - 2k_3 = -4
\end{array}
\right.$$
$$\left\{
\begin{array}{c}       
    k_1 = 2 \\
    K_2 = -1 \\
    k_3 = 3
\end{array}
\right.$$
$$\therefore \beta = 2\alpha_1 - \alpha_2 + 3\alpha_3$$

:::info
向量组的等价
:::
- 若 $\alpha_1,\alpha_2,\dots,\alpha_m$ 和 $\beta_1,\beta_2,\dots,\beta_n$ 同维度
- 两个向量组可以相互线性表示 
    - 反身性：{$\alpha_1,\alpha_2,\dots,\alpha_m$} $\cong$ {$\beta_1,\beta_2,\dots,\beta_n$}
    - 对称性：{$\alpha_1,\alpha_2,\dots,\alpha_m$} $\cong$ {$\beta_1,\beta_2,\dots,\beta_n$} $\Leftrightarrow$  {$\beta_1,\beta_2,\dots,\beta_n$} $\cong$ {$\alpha_1,\alpha_2,\dots,\alpha_m$}
    - 传递性: {$\alpha_1,\alpha_2,\dots,\alpha_m$} $\cong$ {$\beta_1,\beta_2,\dots,\beta_n$} 、{$\beta_1,\beta_2,\dots,\beta_n$} $\cong$ {$\gamma_1,\gamma_2,\dots,\gamma_s$} $\Longrightarrow$ {$\alpha_1,\alpha_2,\dots,\alpha_m$} $\cong$ {$\gamma_1,\gamma_2,\dots,\gamma_s$}
:::info
向量间线性相关
:::
- $a_1,a_2,\dots,a_n$是n个m维向量，若[存在]{.rainbow}一组不全为0的$k_1,k_2,\dots,k_n$使得
    $$k_1\alpha_1 + k_2\alpha_2 + \dots +k_n\alpha_n = 0$$
    就称$a_1,a_2,\dots,a_n$是线性相关的。
- $$1 \times \binom{2}{0} - 2 \times \binom{1}{0} + 0 \times \binom{8}{9} = 0$$
- $$2 \times \binom{1}{0}+2 \times \binom{0}{1} - 1 \times \binom{2}{3} = 0$$
:::info
向量间线性无关
:::
- $a_1,a_2,\dots,a_n$是n个m维向量，若[找不到]{.rainbow}一组不全为0的$k_1,k_2,\dots,k_n$使得
    $$k_1\alpha_1 + k_2\alpha_2 + \dots +k_n\alpha_n = 0$$
    就称$a_1,a_2,\dots,a_n$不是线性相关的。
- $a_1,a_2,\dots,a_n$是n个m维向量，[必全为0]{.rainbow}的$k_1,k_2,\dots,k_n$使得
    $$k_1\alpha_1 + k_2\alpha_2 + \dots +k_n\alpha_n = 0$$
    就称$a_1,a_2,\dots,a_n$不是线性相关的。
:::info
线性相关或无关的性质
:::
1. 向量组中两向量成比例，必定线性相关
$$-1 \times \binom{1}{2} + \frac{1}{2} \times \binom{2}{4} = 0$$
2. 含零向量的任意向量组必线性相关
$$0\alpha_1 + 0\alpha_2 + 1 \times 0 = 0$$
3. 任意一个零向量必线性相关
$$1 \times 0 = 0$$
4. 任意一个非零向量必线性无关
$$\alpha \neq 0 , k\alpha = 0 \Rightarrow k = 0$$
5. 一个向量$\alpha$线性相关 $\Leftrightarrow \alpha = 0$ 
:::info
线性相关证明
:::

- $\alpha_1,\alpha_2,\dots, \alpha_r$线性相关 , $\alpha_1,\alpha_2,\dots,\alpha_r, \alpha_{r + 1}, \dots ,\alpha_{s}$ 也线性相关

- $\alpha_1,\alpha_2,\alpha_3$线性相关,$\alpha_1,\alpha_2,\alpha_3,\alpha_4,\alpha_5$也线性相关
- 证明：
$$k_1\alpha_1 + k_2\alpha_2 + k_3\alpha_3 = 0$$
$$k_1\alpha_1 + k_2\alpha_2 + k_3\alpha_3 + 0\alpha_4 + 0\alpha_5 = 0$$
$\because \quad k_1,k_2,k_3,0,0$不全为0
$\therefore \quad \alpha_1,\alpha_2,\alpha_3,\alpha_4,\alpha_5$是线性相关的
- 结论
    - 部分组线性相关 $\rightarrow$ 整体组线性相关
    - 整体组线性无关 $\rightarrow$ 部分组线性无关
    - 线性无关的向量组，接长向量组也线性无关
    - 线性相关的向量组，截短向量组也线性相关
    - n个n维向量组，若构成的行列式$D \neq 0$,则该向量组线性无关
    - n个n维向量组，若构成的行列式$D = 0$,则该向量组线性相关
:::info
向量组的秩
:::
- 极大线性无关组：
    - $\alpha_1,\alpha_2,\alpha_3,\alpha_4,\alpha_5$的部分组$\alpha_1,\alpha_2$
        1. $\alpha_1,\alpha_2$线性无关
        2. 每个向量均可由$\alpha_1,\alpha_2$表示
# 线性方程组



# 特征值和特征向量
- 设 A 是 n 阶矩阵，$\lambda$ 是一个数，若存在n维非零向量