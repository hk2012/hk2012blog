---
title: 高等数学积分学
category: 数学
math: true
tag: 总结
abbrlink: 48197
date: 2023-08-30 16:58:53
---

# 不定积分

   1. 定义
      - $F'(x) = f(x) \quad$ $F(x)$ 是 $f(x)$ 的一个原函数 
   2. 原函数存在定理
      - 连续一定有原函数 $[F(x) + C]' = f(x)$
   3. 几何含义
      - 一组平行的曲线簇 
   4. 性质
      - $\int [f(x) \pm g(x)] dx = \int f(x) dx \pm \int g(x) dx$
      - $\int kf(x) dx = k \int f(x) dx$

## 积分表

   0. $\int 0 dx = C$
   1. $\int k dx = kx + C$
   2. $\int x^\mu dx = \dfrac{x^{\mu + 1}}{\mu + 1} + C$
   3. $\int \dfrac{1}{x} dx = \ln|x| + C$
   4. $\int \dfrac{1}{1 + x^2} = \arctan(x) + C$ 
   5. $\int \dfrac{1}{\sqrt{1 - x^2}} dx = \arcsin(x) + C$
   6. $\int \cos(x) dx = \sin(x) + C$
   7. $\int \sin(x) dx = -\cos(x) + C$
   8. $\int \dfrac{1}{\cos^2(x)} dx =\int \sec^2(x) dx = \tan(x) +C$
   9. $\int \dfrac{1}{sin^2(x)} dx = \int \csc^2(x) dx = -\cot(x) + C$
   10. $\int \sec(x)\tan(x) dx = \sec(x) + C$
   11. $\int \csc(x)\cot(x) dx = -\csc(x) + C$
   12. $\int e^x dx = e^x + C$
   13. $\int a^x dx = \dfrac{a^x}{\ln(a)} + C$
   14. $\int \csc^2(x) = -\cot(x) + C$
   15. $\int \sec^2(x) = \tan^2(x) + C$
   16. $\int \dfrac{1}{\sqrt{a^2 - x^2}} dx = \arcsin(\dfrac{x}{a}) + C$
   17. $\int \dfrac{1}{x^2 + a^2} dx = \dfrac{1}{a}\arctan(\dfrac{x}{a}) + C$
   18. $\int \dfrac{1}{x^2 - a^2} dx = \dfrac{1}{2a}\ln|\dfrac{x - a}{x + a}| + C$
   19. $\int \dfrac{1}{\sqrt{x^2 + a^2}} dx = \ln(x + \sqrt{x^2 + a^2}) + C$
   20. $\int \dfrac{1}{\sqrt{x^2 - a^2}} dx = \ln(x + \sqrt{x^2 - a^2}) + C$
   21. $\int \tan(x) dx = -\ln|\cos(x)| + C$
   22. $\int \cot(x) dx = \ln|sin(x)| + C$
   23. $\int \sec(x) = \ln|\sec(x) + \tan(x)| + C$

# 换元积分法

   - 核心思想 (凑),把 d 前面的某一部分，求原函数拿到 d 的里面去
      - $\int 1 dx = x + C$
      - $\int 1 du = u + C$ 
      - $\int 1 dF(u) = F(u) + C$ 
      - $\int 1 d(x^2 - 3) = x^2 - 3 + C$ 
   - $x = \psi(t)$,先积分出来，然后再换回 $x$
      - 第二类换元思路 (常用是前三项)
      1. ${\sqrt{a^2 - x^2}} \qquad {x = a\sin(t)} \rightarrow {\sqrt{a^2 - a^2\sin^2(t)}} = {\sqrt{a^2\cos^2(t)}}$ 
      2. ${\sqrt{x^2 - a^2}} \qquad {x = a\sec(t)} \rightarrow {\sqrt{a^2\sec^2(t) - a^2}} = {\sqrt{a^2\tan^2(t)}}$ 
      3. ${\sqrt{x^2 + a^2}} \qquad {x = a\tan(t)} \rightarrow {\sqrt{a^2\tan^2(t) + a^2}} = {\sqrt{a^2\sec^2(t)}}$ 
      4. ${\sqrt{a^2 - x^2}} \qquad {x = a\cos(t)} \rightarrow {\sqrt{a^2 - a^2\cos^2(t)}} = {\sqrt{a^2\sin^2(t)}}$ 
      5. ${\sqrt{x^2 - a^2}} \qquad {x = a\csc(t)} \rightarrow {\sqrt{a^2\dfrac{\cos^2(t)}{\sin^2(t)}}} = {\sqrt{a^2\cot^2(t)}}$ 
      6. ${\sqrt{x^2 + a^2}} \qquad {x = a\cot(t)} \rightarrow {\sqrt{a^2\cot^2(t) + a^2}} = {\sqrt{a^2\csc^2(t)}}$ 

# 分部积分法
   - $\int x e^x dx = \int x de^x = xe^x - \int e^x dx = xe^x - e^x + C$
   - $\int \ln(x) dx = x\ln(x) - x + C$ 

## 往里拿的优先级
   1. $e^x$
   1. $\sin(x) , \cos(x)$
   1. $x^n$

# 有理函数的积分

   - 
   - 待定系数法
   - 

# 定积分

## 概念与性质
   - 设函数$f(x)$在区间[a,b]上有定义且有界，然后分割，求和，取极限。
   - PS: 只与被积函数$f(x)$ 被积区域$[a,b]$有关，与积分变量无关
   - 定理1: 连续，可积
   - 定理2: 有界、有限个间断点，可积
   - 几何意义:  

## 定积分性质
   
   - $\int^a_a f(x) dx= 0$
   - 性质一
      - $\int^b_a f(x) dx= -\int^a_b f(x) dx$
   - 性质二
      - $\int^b_a (\alpha f(x) + \beta g(x)) dx = \alpha \int^b_a f(x) dx + \beta \int^b_a g(x) dx$
   - 性质三
      - $\int^b_a f(x) dx = \int^c_a f(x) dx +  \int^b_c f(x) dx$
   - 性质四
      - $f(x) \equiv 1 \quad \int^b_a 1 dx = b - a \quad \int^b_a k dx = b - a$
   - 性质五
      - $f(x) \geq 0 \qquad \int^b_a f(x) dx \geq 0$
      - $f(x) \leq 0 \qquad \int^b_a f(x) dx \leq 0$
   - 推理一
      - $f(x) \geq g(x) \qquad \int^b_a f(x) dx \geq \int^b_a g(x) dx$
      - $g(x) - f(x) \geq 0 \qquad \int^b_a [g(x) - f(x)] dx \geq 0$