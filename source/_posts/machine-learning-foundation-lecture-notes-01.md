---
title: 机器学习基石学习笔记01
date: 2019-06-16 17:02:46
tags: [Machine Learning Foundation, ML]
description: 《机器学习基石》第01-02课学习笔记
mathjax: true
---

## Lecture 01

人类学习:

```flow
s=>start: Observations
o=>operation: Learning
e=>end: Skill

s->o->e
```

机器学习:

```flow
s=>start: Data
o=>operation: ML
e=>end: Skill

s->o->e
```



机器学习目的:

unknown target function:
$$
f : \mathcal{X} \rightarrow \mathcal{Y}
$$

机器学习过程:
$$
\mathcal{A} \text { takes } \mathcal{D} \text { and } \mathcal{H} \text { to get } g
$$

$\mathcal{A}$ 是机器学习算法

$\mathcal{D}$ 是数据

$\mathcal{H}$ 是公式集合

 $g$   $\approx f$ 的假设



## Lecture 02

Vector Form of Perceptron Hyponthesis

$$
\begin{aligned} h(\boldsymbol{x}) &=\operatorname{sign}\left(\left(\sum_{i=1}^{d} w_{i} x_{i}\right)-\text { threshold }\right) \\ &=\operatorname{sign}\left(\left(\sum_{i=1}^{d} w_{i} x_{i}\right)+\underbrace{(-\text { threshold })}_{w_{0}} \cdot \underbrace{(+1)}_{x_{0}}\right) \\ &=\operatorname{sign}\left(\sum_{i=0}^{d} w_{i} x_{i}\right) \\ &=\operatorname{sign}\left(\boldsymbol{w}^{T} \boldsymbol{x}\right) \end{aligned}
$$

Perceptrons in $\mathbb{R}^{2}$

$\boldsymbol{x}$: points on the plane（ or points in $\mathbb{R}^{d}$）, ${h}$: lines ( or hyperplanes in $\mathbb{R}^{d}$)

$$
h(\boldsymbol{x})=\operatorname{sign}\left(w_{0}+w_{1} x_{1}+w_{2} x_{2}\right)
$$

label $y$: +1 or -1

$$
\text { perceptrons } \Leftrightarrow \text { linear (binary) classifiers } 
$$

Select ${g}$ from $\mathcal{H}$  

- want: $g$   $\approx f$ 
- difficult: $\mathcal{H}$ is of infonite size
- idea: start from some $g_{0}$

Perceptron Learning Algorithm

For t=0, 1,…


$$
\operatorname{sign}\left(\mathbf{w}_{t}^{T} \boldsymbol{x}_{n(t)}\right) \neq y_{n(t)}
$$


$$
\boldsymbol{w}_{t+1} = \boldsymbol{w}_{t}+y_{n(t)} \boldsymbol{x}_{n(t)}
$$

$$
- \text {A fault confessed is half redressed. :-) }
$$

**Definition 1.1**: $\boldsymbol{w}_f$  satisfies $y_{n}=\operatorname{sign}\left(\boldsymbol{w}_{f}^{T} \boldsymbol{x}_{n}\right)$

**Then**: $\boldsymbol{w}_t$ get more aligned with $\boldsymbol{w}_f$

(i)
$$
y_{n(t)} \boldsymbol{w}_{f}^{T} \boldsymbol{x}_{n(t)} \geq \min _{n} y_{n} \boldsymbol{w}_{f}^{T} \boldsymbol{x}_{n}>0
$$

$$
\begin{aligned} \boldsymbol{w}_{f}^{T} \boldsymbol{w}_{t+1} &=\boldsymbol{w}_{f}^{T}\left(\boldsymbol{w}_{t}+y_{n(t)} \boldsymbol{x}_{n(t)}\right) \\ & \geq \boldsymbol{w}_{f}^{T} \boldsymbol{w}_{t}+\min _{n} y_{n} \boldsymbol{w}_{f}^{T} \boldsymbol{x}_{n} \\ &>\boldsymbol{w}_{f}^{T} \boldsymbol{w}_{t}+0 \end{aligned}
$$

(ii)

$$
\begin{array}{c}{\boldsymbol{w}_t \text { changed only when mistake }} \\ \Leftrightarrow \operatorname{sign}\left(\mathbf{w}_{t}^{T} \boldsymbol{x}_{n(t)}\right) \neq y_{n(t)} \Leftrightarrow y_{n(t)} \boldsymbol{w}_{t}^{T} \boldsymbol{x}_{n(t)} \leq 0 \end{array}
$$

$$
\begin{aligned}\left\|\boldsymbol{w}_{t+1}\right\|^{2} &=\left\|\boldsymbol{w}_{t}+y_{n(t)} \boldsymbol{x}_{n(t)}\right\|^{2} \\ &=\left\|\boldsymbol{w}_{t}\right\|^{2}+2 y_{n(t)} \boldsymbol{w}_{t}^{T} \boldsymbol{x}_{n(t)}+\left\|y_{n(t)} \boldsymbol{x}_{n(t)}\right\|^{2} \\ & \leq\left\|\boldsymbol{w}_{t}\right\|^{2}+0+\left\|y_{n(t)} \boldsymbol{x}_{n(t)}\right\|^{2} \\ & \leq\left\|\boldsymbol{w}_{t}\right\|^{2}+\max _{n}\left\|y_{n} \boldsymbol{x}_{n}\right\|^{2} \end{aligned}
$$

start from $\boldsymbol{w}_0 = \boldsymbol{0}$, after T mistake corrections,

$$
\begin{aligned} \frac{\boldsymbol{w}_{f}^{T}}{\left\|\boldsymbol{w}_{f}\right\|} \frac{\boldsymbol{w}_{T}}{\left\|\boldsymbol{w}_{T}\right\|} & \geq \frac{T \cdot \min _{n} y_{n} \boldsymbol{w}_{f}^{T} \boldsymbol{x}_{n(t)}}{\left\|\boldsymbol{w}_{f}\right\| \left\|\boldsymbol{w}_{T}\right\|} \\ & \geq  \frac{T \cdot \min _{n} y_{n} \boldsymbol{w}_{f}^{T} \boldsymbol{x}_{n(t)}}{\left\|\boldsymbol{w}_{f}\right\| \cdot \sqrt{T} \cdot \max _{n} \left\|\boldsymbol{x}_{n}\right\|}  \\ & \geq \sqrt{T} \cdot \frac{ \min _{n} \boldsymbol{x}_{n(t)} }{\max _{n} \left\|\boldsymbol{x}_{n}\right\|}   \\ & \geq \sqrt{T} \cdot \text { constant } \end{aligned}
$$


