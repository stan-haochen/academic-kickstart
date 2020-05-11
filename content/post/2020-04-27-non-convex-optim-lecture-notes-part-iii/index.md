---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "Non-convex Optimizatin Lecture Notes Part III"
subtitle: "Part III of the lecture notes on non-convex optimization for machine learning. Planned for AIM paper reading group."
summary: ""
authors: []
tags: []
categories: []
date: 2020-04-27T09:08:33+08:00
lastmod: 2020-04-27T09:08:33+08:00
featured: false
draft: true

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: ""
  focal_point: ""
  preview_only: false

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: []
---

This is the third post of a planned four-part series building from the necessary basics focusing on the FTML survey paper [Non-convex Optimization for Machine Learning](https://www.prateekjain.org/publications/all_papers/JainK17_FTML.pdf). The first blog will include a overview and introduce projected gradient descent.

## Introduction

Finding approximated solutions for non-convex problems in polynomial time is important for many machine learning tasks, such as sparse/low-rank recovery and robust regression. It often requires heuristics like projected gradient descent and alternating minimization. The (not so) recent monograph has provided an in depth analysis of those algorithms and their applications.

### Sparse recovery
Sparse recovery seeks to fit a sparse model vector to the data

$$
\hat \mathbf w_{sp} = \argmin_{\mathbf w\in \mathbb R^p}\sum_{i=1}^n(y_i-\mathbf x_i^T\mathbf w)^2\\
\text{s.t.} \quad \mathbf w\in \mathcal B_0(s)
$$

Sparse recovery solves the two problems of discarding irrelevant covariates adn countering data-starvation. Typically, only $n\ge s\log p$ (as opposed to $n\ge p$) data points are required for sparse recovery to work.

### Recommendation systems
Collaborative filtering algorithms makes a low rank structural assumption on the *preference matrix* $A$.

$$
\hat A_{lr} = \argmin_{\mathbf w\in \mathbb R^{m\times n}}\sum_{(i, j)\in\Omega}(X_{i,j}-A_{ij})^2\\
\text{s.t.} \quad \text{rank}(X)\le r,
$$

This formulation also has a convex objective but a non-convex rank constraint. Assuming the ratings matrix to have rank at most $r$ is equivalent to assuming that the matrix $A$ can be written as $A=UV^T$. With the matrices having at most $r$ columns, we have the following alternative formulation

$$
\hat A_{lv} = \argmin_{U\in \mathbb R^{m\times r}, V\in\mathbb R^{n\times r}}\sum_{(i, j)\in\Omega}(U_i^TV_j-A_{ij})^2.
$$

The matrices $U$ and $V$ can be seen as encoding $r$-dimensional descriptions of users and items respectively.

### Convex Relaxation vs Non-Convex Approach

For sparse linear regression, the relaxation approach gives us the popular LASSO formulation. If the problem possesses certain nice structure, then under careful relaxation, relaxation gap is absent and solutions can be optimal for the original problem. Suprisingly, in some nice caes, non-convex approaches are usually also able to avoid NP-hardness.

One big challange for this approach is to solve relaxed problems efficiently for large-scale problems. Relaxation-based methods such as LASSO, SVT can be more time consuming comparing to the non-convex methods such as IHT, SVP.

## Mathematical Tools

### Convex Analysis

Convex Combination
: A convex combination of a set of $n$ vectors $\mathbf x_i\in \mathbb R^p$, $i=1\dots n$ in an arbitrary real space is a vector $\mathbf x_\theta:=\sum_{i=1}^n\theta_i\mathbf x_i$ where $\mathbf \theta = (\theta_1, \theta_2,\dots, \theta_n)$, $\theta_i\ge 0$ and $\sum_{i=1}^n\theta_i=1$.

Stongly Convex/Smooth Function
: A continuously differentiable function $f:\mathbb R^p\rightarrow\mathbb R$ is considered $\alpha$-strongly convex (SC) and $\beta$-strongly smooth (SS) if for every $\mathbf x, \mathbf y\in\mathbb R^p$, we have
$$
\frac{\alpha}{2}\|\mathbb x- \mathbb y\|^2_2\le f(\mathbf y) - f(\mathbf x) - \langle\nabla f(\mathbf x), \mathbf y - \mathbf x\rangle\le \frac{\beta}{2}\|\mathbf x - \mathbf y\|_2^2
$$

Lipschitz Function
: A function $f:\mathbb R^p\rightarrow \mathbb R$ is $B$-Lipschitz if for every $\mathbf x, \mathbf y\in\mathbb R^p$, we have
$$
|f(\mathbf x)-f(\mathbf y)|\le B\cdot\|\mathbf x - \mathbf y\|_2
$$

Differentiable functions with bounded gradients are always Lipschitz.

Jensen's Inequality
: If $X$ is a random variable taking values in the domain of a convex function $f$, then $\mathbb E[f(X)]\ge f(\mathbb E[X])$


### Convex Projections

Given any closed set $\mathcal C\subset\mathbb R^p$, the projection operator $\Pi_{\mathcal C}(\cdot)$ is defined as
$$
\Pi_{\mathcal C}(\mathbf z):=\argmin_{x\in\mathcal C}\|\mathbf x - \mathbf z\|_2.
$$

For instance, if $\mathcal C=\mathcal B_2(1)$ i.e., the unit $L_2$ ball, then projection is equivalent to a normalziation step. For the case $\mathcal C=\mathcal B_1(1)$, the projection step reduces to the popular soft thresholding operation.