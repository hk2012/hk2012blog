---
title: 高等数学(数学二)
category: 数学
math: true
abbrlink: 5070
date: 2023-08-18 15:48:53
---

# 概述

## 官方解释
- 高等数学是指相对于初等数学和中等数学而言, 数学的对象及方法较为繁杂的一部分, 中学的代数、几何以及简单的集合论初步、逻辑初步称为中等数学, 将其作为中小学阶段的初等数学与大学阶段的高等数学的过渡。

## 通俗解释
- 通常认为, 高等数学是由微积分学, 较深入的代数学、几何学以及它们之间的交叉内容所形成的一门基础学科。主要内容包括：数列、极限、微积分、空间解析几何与线性代数、级数、常微分方程。工科、理科、财经类研究生考试的基础科目。

# 第一节 函数与极限
- 大部分人都知道高等数学的第一章节就是函数与极限了, 其实函数在中学时期就已经学过了, 所以大部分人把重心放在了极限上面。但是数学是需要有他的完整性, 而且之前学过了不代表现在的你还是记得的, 至少在细节方面或者在做题方面和当时的自己是没法比的, 接下来我们来一一介绍。

## 函数介绍

### 教材定义
- 有两个变量 x 和 y , 当 x 取其变化范围中的每一个特定值时, 相应地有唯一的 y 与它对应, 则称 y 是 x 的函数。记为 y = f（ x ）, 其中 x 为自变量,  y 为因变量。（之所以看不下去教材是有原因的）

### 通俗理解
- 其实函数本质上就是数学描述对应关系的一种特殊集合, 用映射与函数的关系去解释再清晰不过了。
  - 有两个非空集合 分别为 X Y,通过法则f (function),对 X 集合里的每个元素 x 都有唯一的 y 与之对应。
  
  ![映射介绍图](/img/math/1.映射.png)

  -  X 集合实际上就是定义域 记作 D (Domain).
  -  Y 集合实际上就是值域, 记作 R (Range).
### 函数特性
  1. 有界性:
      - 有上界: 如果 $\exists k_1$ , 使得 $f(x) \leq k_1$ , 则称$k_1$ 是 $f(x)$的一个上界
      - 有下界: 如果 $\exists k_2$ , 使得 $f(x) \geq k_2$ , 则称$k_2$ 是 $f(x)$的一个下界
      - 有界  : 如果 $\exists$ 正数M , 使得 $|f(x)|$ $\leq M$ ,即 $-M \leq f(x) \leq M$
      - 无界  :  $\forall$ 正数M.如果 $\exists x_1 \in x$, 使得 $|f(x)| \leq M$
  2. 单调性:
      - $x_1 \leq x_2$ , 则$f(x_1) \leq f(x_2)$
      - $x_1 \leq x_2$ , 则$f(x_1) \geq f(x_2)$
  3. 奇偶性:
      - 前提 $D_f$ (Domain)定义域必须是关于原点对称的
      - 如果 $f(-x) = f(x)$,则称为偶函数,该函数在直角坐标系中, 所形成的图像是关于Y轴对称
      - 如果 $f(-x) = -f(x)$,则称为奇函数,该函数在直角坐标系中, 所形成的图像是关于原点对称
  4. 周期性:
      - $\exists$ 正数$l$ , $f(x + l) = f(x)$ 
        - $y = \sin(x)$ 周期为2 $\pi$
        - ps:
          - 任何正有理数都是周期, (有理数 + 有理数 = 有理数, 无理数 + 有理数 = 无理数)
          - 因为没有最小的正有理数(0.000...1), 所以不存在最小的周期
          - 并非每个周期函数都有最小周期
### 反函数
  - $f: D -> f(D)$ 必须是单射,$f^{-1}:f(D) -> D$
  - $f$ 单调、单射, $f^{-1}$ 单调、单射
  - $f$ 单调增 $f^{-1}$ 单调增,$f$ 单调减 $f^{-1}$ 单调减
  - $f$ 与 $f^{-1}$, 关于 $y = f(x)$ 对称
### 复合函数
  - $y = f(t) && t = g(x)$ ==> $y = f(g(x))$ 其中 t 为中间变量
  - $g(x)$的值域$R_g$必须落在$f(t)$的定义域$D_f$里面
### 函数运算
  - $f(x),g(x),\quad D_f,D_g \quad D=D_f \cap D_g \neq \emptyset$
  - $(f \pm g)(x) = f(x) \pm g(x)$
  - $(f * g)(x) = f(x) * g(x)$
  - $(\frac{f}{g})(x) = \frac{f(x)}{g(x)} \quad g(x) \neq 0$

### 初等函数
  - 幂函数
  - 指数函数
  - 对数函数
  - 三角函数
  - 由基本初等函数经过有限次的运算、复合, 都称为基本初等函数
# 常见三角函数公式
## 基本三角函数公式
  - 正割余割
    - $\sec(\alpha) = {\dfrac{1}{\cos(\alpha)}}$
    - $\csc(\alpha) = {\dfrac{1}{\sin(\alpha)}}$
  - 商数关系
    - $\tan(\alpha) = {\dfrac{\sin(\alpha)}{\cos(\alpha)}}$
    - $\cot(\alpha) = {\dfrac{\cos(\alpha)}{\sin(\alpha)}}$
  - 倒数关系
    - $\tan(\alpha)\cot(\alpha) = 1$
    - $\sec(\alpha)\cos(\alpha) = 1$
    - $\csc(\alpha)\sin(\alpha) = 1$
  - 平方关系
    - $\sin^2(\alpha) + \cos^2(\alpha) = 1$
    - $\sec^2(\alpha) = 1 + \tan^2(\alpha)$
    - $\csc^2(\alpha) = 1 + \cot^2(\alpha)$   


## 倍角公式
  - $\sin(2x) = 2\sin(x)\cos(x)$
  - $\cos(2x) = 1 - 2sin^2(x) = 2cos^2(x) - 1$
## 半角公式
  - $\tan(\dfrac{x}{2}) = \csc(x) - \cot(x)$
## 两角和差公式
  - $\cos(\alpha + \beta) = \cos\alpha\cos\beta - \sin\alpha\sin\beta$
  - $\cos(\alpha - \beta) = \cos\alpha\cos\beta + \sin\alpha\sin\beta$
  - $\sin(\alpha \pm \beta) = \sin\alpha\cos\beta \pm \cos\alpha\sin\beta$


## 积化和差公式
  - $\sin\alpha\cos\beta = \dfrac{1}{2} [\sin(\alpha + \beta) + \sin(\alpha - \beta)]$
  - $\cos\alpha\sin\beta = \dfrac{1}{2} [\sin(\alpha + \beta) - \sin(\alpha - \beta)]$
  - $\cos\alpha\cos\beta = \dfrac{1}{2} [\cos(\alpha + \beta) + \cos(\alpha - \beta)]$
  - $\sin\alpha\sin\beta = {-}\dfrac{1}{2} [\cos(\alpha + \beta) - \cos(\alpha - \beta)]$
  
## 和差化积公式
  - $\sin\alpha + \sin\beta = {2}\sin(\frac{\alpha+\beta}{2})\cos(\frac{\alpha - \beta}{2})$
  - $\sin\alpha - \sin\beta = {2}\sin(\frac{\alpha-\beta}{2})\cos(\frac{\alpha + \beta}{2})$
  - $\cos\alpha + \cos\beta = {2}\cos(\frac{\alpha+\beta}{2})\cos(\frac{\alpha - \beta}{2})$
  - $\cos\alpha - \cos\beta = {-2}\sin(\frac{\alpha+\beta}{2})\sin(\frac{\alpha - \beta}{2})$

  - 反三角函数
      - 正弦：$y = \sin(x)$的反函数 $y = \arcsin(x)$
      - 余弦：$y = \cos(x)$的反函数 $y = \arccos(x)$
      - 正切：$y = \tan(x)$的反函数 $y = \arctan(x)$
      - 余切：$y = \cot(x)$的反函数 $y = arccot(x)$
# 第二节数列极限

## 数列的形式
  - 数列: $\dfrac{1}{2},\dfrac{1}{4},\dfrac{1}{8},\dfrac{1}{16}...$
  - $x_1,x_2,x_3,x_4,....x_n,...${$x_n$} 数列
  - 数列里的每个数称为项, $x_n$:一般项（通项）
## 数列极限
  - {$x_n$}是数列, $\forall \epsilon > 0 , \exists N$ , 使得 n > N , |$x_n$ - a| < $\epsilon$ , 其中 a 是极限
  - 任意小的距离 $\epsilon$ , 都存在某一项 N ,这项后面的所有项, 都落在小区间 $\epsilon$ 的里面
  - 证明: |$x_n$ - 1| = |$\frac{n + {(-1)}^{n-1}}{n}$ - 1| = |$\frac{n + {(-1)}^{n-1}}{n}$| = $\frac{1}{n}$ < $\epsilon$
  - 证明： |$q < 1, 1,q,q^2,q^3,...q^{n-1}$|,极限是 0

### 收敛数列的性质
  - 唯一性
  - 有界性
  - 保号性
  - 收敛数列的任一子数列收敛于同一极限
  - 若有两个子数列收敛于不同的极限, 则原数列一定发散

# 第三节函数极限

## 函数极限定义
  - $f(x)$ 在$x_0$的去心邻域内有定义（在$x_0$处可以没有定义）
  - $x\rightarrow x_0 ,  \forall \epsilon > 0$ , $\exists \delta > 0$ , 使得 $0 < |x - x_0| < \delta$ ,  有 $|f(x) - A| < \epsilon$
  - 左极限
    - $$\lim\limits_{x\rightarrow{x_0^-}}f(x) = A$$
  - 右极限
    - $$\lim\limits_{x\rightarrow{x_0^+}}f(x) = A$$
  - $x\rightarrow\infty , \forall \epsilon > 0 , \exists X . |x| > X , |f(x) - A| < \epsilon$

### 无穷小与无穷大 
  
  - 无穷小
    - 趋于 0
    - 无穷小+无穷小 (有限个无穷小的和是无穷小) , 无穷小-无穷小 , 无穷小 $\times$ 无穷小 (有限个无穷小的乘积是无穷小), C $\times$ 无穷小 (C为常数), (有界与无穷小的乘积是无穷小)
  - 无穷大: 
    - +$\infty$ , -$\infty$
    - 无穷大 $\times$ 无穷大 

## 函数极限性质
  - 唯一性
  - 局部有界性
  - 局部保号性

### 函数定理
 
  -  $$\lim\limits f(x) = A { , } \lim\limits g(x) = B$$
  1. $$\lim\limits [f(x) \pm g(x)] = \lim\limits f(x) \pm \lim\limits g(x)$$
  2. $$\lim\limits [f(x) \times g(x)] = \lim\limits f(x) \times \lim\limits g(x)$$
  3. $$\lim\limits [f(x) \div g(x)] = \lim\limits f(x) \div \lim\limits g(x) (B 	\not= 0)$$
  4. $$\lim\limits [cf(x)] = c\lim\limits f(x)$$
  5. $$\lim\limits [f(x)]^n = [\lim\limits f(x)]^n$$
  5. $$f(x) \geq g(x) , \lim\limits f(x) \geq \lim\limits g(x)$$

### 两个准则, 两个重要极限
  - 夹逼准则
  - 单调有界准则（单调有界数列必有极限）, 收敛必有界, 有界不一定收敛
  - $$\lim\limits_{x\rightarrow{0}}\frac{x}{sinx} = 1$$
  - $$\lim\limits_{x\rightarrow\infty}(1 + \frac{1}{x})^x = e$$
  - $$\lim\limits_{x\rightarrow 0}(1+ {x})^\frac{1}{x} = e$$
  - $$\lim\limits_{x\rightarrow 0}(\frac{\sqrt[n]{1 + x}}{\frac{x}{n}}) = 1$$
  - 若 $\alpha$ 与 $\beta$ 是等价无穷小 <==> $\beta = \alpha + \omicron (\alpha)$

### 函数的连续性

  1. 有极限 
  2. 有定义 
  3. 极限 = 函数值 
  4. 左连续 = 右连续

### 函数间断点
  
  - 函数的间断实际上就是不连续
  1. 在 $x_0$ 处无定义
  2. 极限不存在
  3. 极限 不等于 函数值
  - $y = \tan(x)$ 在 $x = \frac{\pi}{2}$ 时, 无定义,无穷间断点、$y= \frac{1}{x}$ 在 $x = 0$, 无定义，无穷间断点
  - $y = \sin(\frac{1}{x})$ 在 $x = 0$ 时, 振荡间断点
  - $y = \frac{x^2-1}{x-1}$ 在 $x = 1$ 时, 可去间断点
  - $$y = 
      \begin{cases}
      x = x + 1 \quad x > 0 \\
      x = 0 \quad\qquad x = 0 \\
      x = x -1 \quad x <   0\\
      \end{cases}
      \tag{跳跃间断点}
    $$
  - 第一类间断点: 左右极限都存在，(可去间断点 , 跳跃间断点)
  - 第二类间断点: 左右极限至少有一个不存在 (振荡间断点 , 无穷间断点)

### 连续函数的定理
  - 零点定理
   - 令 $f(x) = 0$ ,(使用频率较高)
  - 介值定理

### 做题总结
  - 当 $x\rightarrow\infty$ 时 , 取最高次数项的系数进行计算, (抓大放小)
  - 当 $x\rightarrow {0}$ 时 , 取最高次数项的系数进行计算然后取倒数 , (抓大放小)
  - 两个无穷小比的极限时，分子或分母用等价无穷小替换
  - 分子或分母是若干因子的乘积时，可对其中一个或多个因子做等价无穷小替换
  - $$ a^b = e^{(blna)} $$
  - $$ a = e^{(lna)} = lne^a = alne $$

# 导数与微分

## 导数定义
  
  1. 速度: 匀速、非匀速 计算 $t_0$ 到 t的平均速度
  2. 切线: $f(x)$ 变化量和 $x$ 变化量的比值
  3. 定义: $y = f(x)$在 $x_0$ 的领域内有定义，$x_0$处取 $\delta x$

### 常见导数公式
 
  - 基本导数求导公式
   
    - $f(x) = c$  ,  $(c)' = 0$ , C为常数
    - $f(x) = x^n$  ,  $f'(x) = n{x^{n-1}}$
    - $f(x) = a^x$  ,  $f'(x) = a^xlna$
    - $f(x) = e^x$  ,  $f'(x) = e^x$
    - $f(x) = \log_{a}{x}$  ,  $f'(x) = {\frac{1}{xlna}}$
    - $f(x) = \ln{x}$  ,  $f'(x) = {\frac{1}{x}}$
  - 三角函数求导公式
   
    - $f(x) = sinx$  ,  $f'(x) = cosx$
    - $f(x) = cosx$  ,  $f'(x) = -sinx$
    - $f(x) = tanx$  ,  $f'(x) = sec^2x$ 
    - $f(x) = cotx$  ,  $f'(x) = -csc^2x$ 
    - $f(x) = secx$  ,  $f'(x) = secxtanx$ 
    - $f(x) = cscx$  ,  $f'(x) = -cscxcotx$
  - 反三角函数求导公式
    
    - $f(x) = arcsinx$  ,  $f'(x) = {\frac{1}{\sqrt{1 - x^2}}}$
    - $f(x) = arccosx$  ,  $f'(x) = -{\frac{1}{\sqrt{1 - x^2}}}$
    - $f(x) = arctanx$  ,  $f'(x) = {\frac{1}{1 + x^2}}$
    - $f(x) = arccotx$  ,  $f'(x) = -{\frac{1}{1 + x^2}}$

### 单侧导数

  - 在某点可导，左右导数存在且相等

## 导数的几何含义

  - 切线的斜率就是导数的几何含义 (图像是光滑的，且切线斜率不可垂直X轴)
  - 切线: $y - y_0 = f'(x_0)(x - x_0)$
  - 法线: $y - y_0 = -\frac{1}{f'(x_0)}(x - x_0)$

### 可导与连续的关系
  
  - 可导一定连续，但是连续不一定可导。(可导的图像是光滑的，连续的图像是不间断的)

### 求导法则
  
  - 函数求导法则
    - $(u + v)' = u' + v'$
    - $(u - v)' = u' - v'$
    - $(uv)' = u'v + uv'$
    - $(uvw)' = u'vw + uv'w + uvw'$
    - $(cv)' = cv'$ (c为常数)
    - $(\frac{u}{v})' = {\frac{u'v - uv'}{v^2}}$
  
  - 反函数求导法则
    
    - 反函数的导数是原来导数的导数: $[f^{-1}(x)]' = {\frac{1}{f'(g)}}$
  
  - 复合函数求导法则 (链式法则)
    
    - $y = f[g(x)] \qquad \qquad f[g(x)]' = f(u)'g(x)'$

  - 隐函数求导法则

    - 两边对 $x$ 求导,如果带着 $y$ 也没关系

### 高阶导数
  - $y = ax + b \qquad y' = a \qquad y'' = 0$
  - $y = sin\omega x \qquad y' = wcos\omega x \qquad y'' = -\omega^2sinwt$
  - $y = e^x \qquad y' = e^x \qquad y'' = e^x$
  - $y = \sin(x) \qquad y' = \cos(x) \qquad y'' = \cos(x + \frac{\pi}{2}) = \sin(x + \frac{\pi}{2} + \frac{\pi}{2}) \qquad y^{(n)} = \sin(x + n \frac{\pi}{2})$
  
  - $y = \cos(x) \qquad y' = - \sin(x) \qquad y'' = - \cos(x) = \cos(x + \frac{\pi}{2} + \frac{\pi}{2}) \qquad y^{(n)} = \cos(x + n \frac{\pi}{2})$   
  
  - $y = \ln(1 + x) \qquad$  $y' = {\frac{1}{1 + x}} \qquad$  $y'' = {-\frac{1}{(1 + x)^2}}\qquad$  $y^{(n)} = {(-1)^{n-1} \frac{(n - 1)!}{(1 + x)^n}}$
  
  - $y = {x^{\mu}} \quad$ $y' = {\mu x^{\mu - 1}} \quad$ $y'' = {(\mu)(\mu - 1)x^{(\mu - 2)}} \quad$ $y^{(n)} = {(\mu)(\mu - 1)…(\mu - n + 1)x^{(\mu - n)}}$

  - $(u + v)^{(n)} = {u^{(n)} + v^{(n)}}$
  - $(uv)^{(n)} = {\displaystyle\sum_{k = 0}^n{C_n^k}u^{(n-k)}v^k}$
  - $(u + v)^n = {\displaystyle\sum_{k = 0}^n{C_n^k}u^{(n-k)}v^k}$    (这里n没有括号,表示次方,而不是导数次方)

# 微分

## 微分的定义

### 定义
  - $y = f(x) \qquad x_0 \rightarrow x_0 + \delta x \qquad \delta y = f(x_0 + \delta x) - f(x_0)$
  - $\delta y = A\delta x + o(\delta x)$ (A相对于 $\delta x$ 是常数)
### 充要条件
  - 可微 <==> 可导
### 基本微分公式与法则
  - $dy = f'(x)dx$
  - $f(x_0 + \delta x) = f'(x)dx + f(x_0)$
### 公式 ( ${x \rightarrow 0}$ )
  - $(1 + x)^\alpha$ ~ 1 + $\alpha x$
  - $\sin(x)$ ~ $x$
  - $\tan(x)$ ~ $x$
  - $e^x$ ~ 1 + $x$
  - $\ln(1 + x)$ ~ $x$

## 微分中值定理

### 费马引理
  - 定义
    - $f(x)$ 在 $x_0$ 邻域 $U(x_0)$ 有定义, 在 $x_0$处可导,如 $f(x) \leq f(x_0) \quad \forall x \in U(x_0)$ ,则 $f'(x_0) = 0$
     - 驻点: 导数为 0 的点 (又称 稳定点 , 零界点  )

### 罗尔定理
  - $f(x)$ 满足
    - 在 $[a,b]$ 连续
    - 在 $(a,b)$ 可导
    - $f(a) = f(b)$
  则至少 $\exist$ $\xi \in (a,b)$ $f'(\xi) = 0$

### 拉格朗日中值定理
  - $f(x)$ 满足
    - 在 $[a,b]$ 连续
    - 在 $(a,b)$ 可导
  $(a , b)$ 至少有一点 $\xi$ $f(b) - f(a) = f'(\xi)(b -a)$
  - $f(x)$ 在区间 I 连续 I 内可导且导数恒为 0 , $f(x) = C$

### 柯西中值定理
  - 若 $f(x)$ 和 $F(x)$
    - 在 $[a,b]$ 连续
    - 在 $(a,b)$ 可导
    - $\forall x \in (a , b) F'(x) \neq 0$
    至少有一点 $\displaystyle\frac{f(b)- f(a)}{F(b)- F(a)}$ = ${\displaystyle\frac{f'(\xi)}{F'(\xi)}}$

### 洛必达法则
  - 形式
    - ${\displaystyle\frac{0}{0}} \qquad {\displaystyle\frac{\infty}{\infty}} \qquad 0 {\cdot} \infty \qquad \infty - \infty \qquad 0^0 \qquad 1^\infty \qquad \infty^0$ 
  
  - 定理1
    - $x \to a$ 时 $\qquad f(x) \to 0 \qquad  F(x) \to 0$
    - 在 $a$ 的去心邻域内 $f'(x) F'(x)$ 存在,且 $F'(x) \neq 0$
    - $\lim\limits_{x \to a}\frac{f(x)}{F(x)}$ 存在 或 $\infty$
  则 ${\lim\limits_{x \to a}\frac{f(x)}{F(x)}} = {\lim\limits_{x \to a}\frac{f'(x)}{F'(x)}}$
  
  - 定理2
    - $x \to \infty$ 时 $\qquad f(x) \to 0 \qquad  F(x) \to 0$
    - 在 $a$ 的去心邻域内 $f'(x) F'(x)$ 存在,且 $F'(x) \neq 0$
    - $\lim\limits_{x \to a}\frac{f(x)}{F(x)}$ 存在 或 $\infty$
  则 ${\lim\limits_{x \to \infty}\frac{f(x)}{F(x)}} = {\lim\limits_{x \to a}\frac{f'(x)}{F'(x)}}$

## 泰勒公式

  - 通用
    - 一次性表达
      - $f(x) = f(x_0) + f'(x_0)(x - x_0)$
    - 多次求导
      - $f(x) = f(x_0) + \frac{f'(x_0)}{1!}(x - x_0) + \frac{f''(x_0)}{2!}(x - x_0)^2+ ... + \frac{f^{(n)}(x_0)}{n!}(x - x_0)^n$ + o$((x - x_0)^n)$

  1. sinx = $x -\displaystyle\frac{x^3}{3!}$ + o(x^3^)

  2. cosx = 1 - $\displaystyle\frac{x^2}{2!}$ + $\displaystyle\frac{x^4}{4!}$ + o(x^4^)

  3. tanx = x + $\displaystyle\frac{x^3}{3}$ + o(x^3^)

  4. arcsinx = x + $\displaystyle\frac{x^3}{3!}$ + o(x^3)
    
  5. arcsinx = x + $\displaystyle\frac{1}{2} {*} \displaystyle\frac{x^3}{3} {+} \displaystyle\frac{1}{2} {*} \displaystyle\frac{3}{4} {*} \displaystyle\frac{x^5}{5} + o(x^5)$

  6. arctanx = $x - \displaystyle\frac{x^3}{3} + o(x^3)$

  7. $\ln(1 + x) = x - \displaystyle\frac{x^2}{2} + \displaystyle\frac{x^3}{3} + o(x^3)$

  8. $e^x = 1 + x + \displaystyle\frac{x^2}{2!} + x^ o(x^2)$

  9. $(1 + x)^α = 1 + αx + \displaystyle\frac{α(α -1)x^2}{2!} + o(x^2)$

