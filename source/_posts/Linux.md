---
title: 四旋翼无人机的数学模型
date: 2019-12-24 18:22:56
tags: 
categories: 
mathjax: true
---


四旋翼的动力学模型包括平移动力学和旋转动力学两部分.
以大地为参考系，模型可以表示为

<center>
    $$
    \begin{align*}
    \dot{\mathbf{p}} &= \mathbb{v} \\
    m \dot{\mathbf{v}} &= -\frac{f}{m}\mathbf{Re_3} + g\mathbf{e_3} \\
    \dot{\mathbf{\Theta}} &= W(\mathbf{\Theta})\omega \\
    \dot{\mathbf{\omega}} &= -\mathbf{\omega} \times \mathbf{J \omega} + \mathbf{\tau}
    \end{align*}
    $$
</center>
其中，$m$是四旋翼的质量...
