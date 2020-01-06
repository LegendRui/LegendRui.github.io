---
title: 论文笔记：Backstepping with bounded feedbacks
date: 2020-01-05 22:27:07
tags: 
    - nonlinear control
categories: 读书笔记
mathjax: true
---

### 1 简介
#### 1.1 研究意义
>  "saturation is probably the most commonly encountered nonlinearity in control engineering"

#### 1.2 贡献
> Interestingly enough even when applied to these systems, the technique of control design
of the present work does not coincide with the forwarding approach: for instance, it does not require the determination of the decoupling changes of coordinates used in [8,7] or of the cross terms used in [3], which is an important advantage because in many cases these changes of coordinates or these cross-terms frequently lead to complicated calculations or cannot even be determined due to parameters inaccurately known or the fact that ˙x = f(x; 0) is nonlinear.

#### 1.3 研究对象
此文考虑的系统为
$$\dot x = f(x,z), \quad \dot z = u + h(x,z)$$
其中，$u \in R$是输入，$x \in R^{n_x}, f(0, 0) = 0,h(0,0) = 0$.

### 2 预备知识
#### 2.1 Lyapunov函数
定义在$R^n$上的正定函数$V(\cdot)$是系统
$$\dot x = \phi(x), \quad \phi(0) = 0$$
的Lyapunov函数  
+ 如果有$\frac{\partial V}{\partial x}\phi(x) \le 0, \quad \forall x.$
+ 如果还有$\frac{\partial V}{\partial x}\phi(x) < 0, \quad \forall x \ne 0$，则称为严格Lyapunov函数.

#### 2.2 $\mathcal{K}_\infty$函数
实值函数$k(\cdot)$是$\mathcal{K}_\infty$函数，当它满足
+ $k(0) = 0$
+ 严格增
+ 当自变量趋于无穷时，函数值也趋于无穷

#### 2.3 饱和函数
实值函数$\sigma(\cdot)$是饱和函数，当它满足
+ $\sigma(s)$时非减的
+ $\sigma(s) = \text{sign}(s)\sigma_M$当$|s|\ge 1$，且$\sigma_M \in [\frac{1}{2}, 1]$
+ $\sigma(s) = s$当$|s|\le \frac{1}{2}$

### 3 主要结论
#### 3.1 没有严格Lyapunov函数时的有界反步法
**假设H1:** 函数$V(\cdot)$正定、径向无界、二阶可导的，函数$W(\cdot)$非负、一阶可导的，控制律$z_s(s)$二阶可导、范数有界（界为B）并满足$z_s(0) = 0$，使得  
$$W(x) \le -\frac{\partial V}{\partial x}(x)f(x, z_x(x)).$$
更进一步，系统的解$x(t) = 0$是唯一满足对于所有的$t$，有
$$\dot x(t) = f(x(t), z_s(x(t)),\quad W(x(t)) = 0.$$

**假设H2:** 存在两个定义在$[0, +\infty)$非减、非负的函数$\Gamma_1(\cdot)$与$\Gamma_2(\cdot)$，使得
$$|T(x, z)| \le \Gamma_1(|z|),$$
$$| \frac{\partial z_s}{\partial x}(x)f(x, z) | \le \Gamma_2(|z|)$$
其中，
$$T(x, z) = \frac{(\partial V / \partial x)(x)[f(x, z) - f(x, z_s(x))]}{z - z_s(x)}.$$


**假设H3：**函数$h(x, z)$以常数$H_M$为范数界.

**定理1：**假设系统满足假设H1-H3. 则可以构造一个二阶可导的$\mathcal{K}_\infty$函数$\Omega(\cdot)$，当系统以控制律
$$u_s(x,z) = -h(x, z) + \frac{1}{\Omega ^{\prime}(z)}\left \[-\frac{(z - z_s(x))(\rho(z-z_s(x)) + T(x, z))}{\Omega(z) - \Omega(z_s(x))} + \frac{\partial z_s}{\partial x}(s)f(x, z)\right \]$$
闭环，则正定、径向无界的函数
$$U(x, z) = V(x) + \frac{1}{2}[\Omega(z) - z_s(x)]^2$$
沿系统轨迹的导数是非正定（半负定）的；如果$W(x)$是正定的，则$U(x, z)$的导数是负定的. 更确切地说，系统以范数有界的控制律$u_s(x, z)$闭环后是全局渐近稳定的. 上式中，$\rho(\cdot)$是一阶可导的有界函数，$s\rho(s)$是正定的. 

#### 3.2 有严格Lyapunov函数时的有界反步法