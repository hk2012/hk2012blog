---
title: 高等数学数学图像作图
category: 数学
tag: 总结
math: true
abbrlink: 23292
date: 2023-08-27 15:48:53
---

# 函数图像

## 基本图像
  1. 心形线
  2. 玫瑰线
  3. 阿基米德螺线
  4. 摆线 （外摆线）
  5. 星形线 （内摆线）
## 单调性
  - 单调增 $\qquad f'(x) > 0 \qquad \nearrow$  
  - 单调减 $\qquad f'(x) < 0 \qquad \searrow$
  - 讨论单调性注意点
    - 一阶导数为0的点 (驻点)
    - 一阶导数不存在的点
## 凹凸性

  1. 凹曲线:  $\qquad f'(x)$ 递增 $\qquad f''(x) > 0 \qquad \uparrow$
  2. 凸曲线:  $\qquad f'(x)$ 递减 $\qquad f''(x) < 0 \qquad \downarrow$

### 凹凸区间分界点
  1. 二阶导数为零的点
  2. 二阶导数不存在的点

## 间断点
  
  - 第一类间断点 (可去 , 跳跃)
  - 第二类间断点 (无穷 , 振荡)

## 极值
### 定义
  - 邻域内(局部)，最大(小)值 (不唯一)

### 定理
  
  1. (必要条件) $f(x)$ 在 $x_0$ 可导，在 $x_0$ 处取极值，$f'(x_0) = 0$
  2. (充分条件) $f(x)$ 在 $x_0$ 连续，左边 $f'(x_0) > 0$ , 右边 $f'(x_0) < 0 \quad$ 取极大值
  3. (充分条件) $f(x)$ 在 $x_0$ 连续，左边 $f'(x_0) < 0$ , 右边 $f'(x_0) > 0 \quad$ 取极大值
  3. (充分条件) $x \in \mathring{U}(x_0)$ 在 $f'(x_0)$ 符号不变， $x_0$ 不是极值

### 判断方式
  1. 求$f'(x)$
  2. 求驻点和不可导的点
  3. 考察左右导数符号是否变化
      - 左边 $f'(x_0) > 0$ , 右边 $f'(x_0) < 0 \quad$ 取极大值
      - 左边 $f'(x_0) < 0$ , 右边 $f'(x_0) > 0 \quad$ 取极大值
  4. 二阶可导 $f'(x_0) = 0 \quad f''(x_0) \neq 0$
      - $f''(x_0) < 0 \quad$ 在 $x_0$ 处取极大值
      - $f''(x_0) > 0 \quad$ 在 $x_0$ 处取极小值

## 最值

  - 整个区域内(全局)，最大(小)值 (唯一)

# 函数图形的描绘

## 步骤
  1. 确定定义域，奇偶性，周期性 $f'(x), f''(x)$
  2. $f'(x) = 0, f''(x) = 0$ , $f(x)$间断点，$f'(x)$不存在的点，$f''(x)$不存在的点 , 分开定义域。
  3. $f'(x)$ 确定增减性，$f''(x)$ 确定凹凸拐点
  4. 渐近线 (3种)
  5. $f'(x) = 0, f''(x) = 0$ ,以及不存在的点，函数值
  6. 极值，最值

## 例题一作图

  1. 原函数: $y = x^3 - x^2 - x + 1 \qquad  x \in(-\infty,+\infty)$ 
  2. 一阶导数: $f'(x) = 3x^2 - 2x - 1$
  3. 二阶导数: $f''(x) = 6x - 2$
  4. 令 $f'(x) = 0 \Rightarrow x = -{\cfrac{1}{3}}$ 或 $x = 1$
  5. 令 $f''(x) = 0 \Rightarrow x = {\cfrac{1}{3}}$  
  6. 表格
      - |  $x$      | $(-\infty,-\dfrac{1}{3})$  | $-{\dfrac{1}{3}}$ | $(-\dfrac{1}{3},\dfrac{1}{3})$ | ${\dfrac{1}{3}}$ | $(\dfrac{1}{3},1)$ | $1$ | $(1,+\infty)$ |
        | :----:    | :----:  | :----:  | :----:  | :----:  |  :----:  | :----:  | :----:  | 
        | $f'(x)$ | $+$ | $0$ | $-$ | $-$ | $-$ | $0$ | $+$ |
        | $f''(x)$ | $-$ | $-$ | $-$ | 0 | $+$ | $+$ | $+$ |
        | $f(x)$   | $\nearrow \uparrow$ | 极大值  | $\searrow \uparrow$ | 拐点 | $\searrow 	\downarrow$  | 极小值| $\nearrow 	\downarrow$ |
  7. 分析定义域
      - $x \rightarrow -\infty  \quad y \rightarrow -\infty$ 
      - $x \rightarrow +\infty  \quad y \rightarrow +\infty$ 
  8. 计算特殊值
      - $f(-\frac{1}{3}) = \dfrac{32}{27} \qquad f(\frac{1}{3}) = \dfrac{16}{27} \qquad f(1) = 0$
  9. 该函数最终图像
      - ![函数图像示例图1](/img/math/函数图像示例图1.png) 


## 例题二作图

  1. 原函数: $y = \dfrac{1}{\sqrt{2\pi}} e^{-\frac{x^2}{2}}\qquad  x \in (-\infty,+\infty)$ 偶函数，只考虑 $x \geq 0$
  2. 一阶导数: $f'(x) = -x\dfrac{1}{\sqrt{2\pi}} e^{-\frac{x^2}{2}}$
  3. 二阶导数: $f''(x) = \dfrac{1}{\sqrt{2\pi}} e^{-\frac{x^2}{2}}(x^2 - 1)$
  4. 令 $f'(x) = 0 \Rightarrow x = 0$
  5. 令 $f''(x) = 0 \Rightarrow x = \pm 1$  
  6. 表格
      - |  $x$      | $0$  | $(0,1)$ | $1$ | $(1,+\infty)$ |
        | :----:    | :----:  | :----:  | :----:  | :----:  |
        | $f'(x)$ | $0$ | $-$ | $-$ | $-$ |
        | $f''(x)$ | $-$ | $-$ | 0 | $+$ |
        | $f(x)$   | 极大值 | $\searrow \uparrow$ | 拐点 | $\searrow 	\downarrow$  |
  7. 分析定义域
      - $x \rightarrow +\infty  \quad y \rightarrow 0$ ，水平渐近线 $y = 0$
  8. 计算特殊值
      - $f(0) = \dfrac{1}{\sqrt{2\pi}} \qquad f(1) = \dfrac{1}{\sqrt{2\pi}} e^{-\frac{1}{2}}$
  9. 该函数最终图像
      - ![函数图像示例图2](/img/math/函数图像示例图2.png) 


## 例题三作图
  1. 原函数: $y = 1 + \dfrac{36x}{(x + 3)^2} \qquad  x \in(-\infty,-3) \cup (-3,+\infty)$ 
  2. 一阶导数: $f'(x) = {\dfrac{36x(3 - x)}{(x + 3)^3}}$
  3. 二阶导数: $f''(x) = {\dfrac{72(x - 6)}{(x + 3)^4}}$
  4. 令 $f'(x) = 0 \Rightarrow x = 3$ 
  5. 令 $f''(x) = 0 \Rightarrow x = 6$ 
  6. 表格
      - |  $x$      | $(-\infty,-3)$ | $(-3,3)$ | $3$ | $(3,6)$ | $6$ | $(6,+\infty)$ |
        | :----:    | :----:  | :----:  | :----:  | :----:  |  :----:  | :----:  |
        | $f'(x)$ | $-$ | $+$ | $0$ | $-$ | $-$ | $-$ |
        | $f''(x)$ | $-$ | $-$ | $-$ | $-$ | $0$ | $+$ |
        | $f(x)$   | $\searrow \uparrow$ | $\nearrow \uparrow$  | 极大值 | $\searrow \uparrow$ | $\searrow \downarrow$  | 极小值|
  7. 分析定义域
      - $x \rightarrow -3  \quad y \rightarrow -\infty$ 
      - $x \rightarrow \infty  \quad y \rightarrow 1$ 
  8. 计算特殊值
      - $f(3) = 4 \qquad f(6) = \frac{11}{3} \qquad f(0) = 1$
  9. 该函数最终图像
      - ![函数图像示例图3](/img/math/函数图像示例图3.png) 

# 曲率

## 概述
  - 定义: 曲线弯曲的程度

## 常见曲率形式

  - 平均曲率
    - $\bar k = |\dfrac{\delta \alpha}{\delta s}|$
  - 在 $M$处的曲率
    - $\lim\limits_{\delta s \rightarrow{0}}|\dfrac{\delta \alpha}{\delta s}|$
  - 直线的曲率
    - $\lim\limits_{\delta s \rightarrow{0}}|\dfrac{\delta \alpha}{\delta s}| = 0$
  - 圆的曲率 (圆的半径为 $r$)
    - $\lim\limits_{\delta s \rightarrow{0}}|\dfrac{\delta \alpha}{\delta s}| = {\dfrac{1}{r}}$

# 奇怪的图形

- 图像网站： https://www.desmos.com/?lang=zh-CN

## 重要的常考的图象函数

1. $$(x^2 + 2y - 1)^3 - x^2y^3 = 1$$
2. $$e^{sin(x)+cos(y)} = sin(e^{x+y})$$