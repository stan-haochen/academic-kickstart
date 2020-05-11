---
title: The EM Algorithm
summary: Chapter 5 of Non-convex Optimization.
authors: []
tags: []
categories: []
date: "2020-04-27T00:00:00Z"
slides:
  # Choose a theme from https://github.com/hakimel/reveal.js#theming
  theme: white
  # Choose a code highlighting style (if highlighting enabled in `params.toml`)
  #   Light style: github. Dark style: dracula (default).
  highlight_style: github
---

# The EM Algorithm

---

## Appearance

- {{% fragment %}} Gaussian mixture models {{% /fragment %}}
- {{% fragment %}} the Baum-Welch algorithm for HMM {{% /fragment %}}
- {{% fragment %}} mixed regression {{% /fragment %}}
- {{% fragment %}} close to Lloyd's algorithm for k-means clustering {{% /fragment %}}

---

## Notation


{{% fragment %}} *distribution* with parameter $\boldsymbol \theta$: $f(\cdot|\boldsymbol \theta)$ or $f_{\boldsymbol\theta}$ {{% /fragment %}}

{{% fragment %}} *parametric family*: $\mathcal F=\\\{f_\theta:\boldsymbol{\theta}\in\Theta\\\}$ {{% /fragment %}}

{{% fragment %}} *random variable* : $X\sim f(\cdot|\boldsymbol\theta)$ or $X\sim f_{\boldsymbol\theta}$ {{% /fragment %}}

{{< speaker_note >}}
- The random variable is usually a joint of input and label
{{< /speaker_note >}}

---

## Probabilistic Machine Learning
We observe i.i.d. samples $\mathbf x_1, \mathbf x_2, \dots, \mathbf x_n\sim\mathbb P$.

Assume $\mathbb P$ belongs to $\mathcal F$ and estimate the opptimal $\theta^*$.

{{< speaker_note >}}
- $\mathbb P$ is a data-generating distribution we don't see
{{< /speaker_note >}}

---

### Likelihood

$$
\begin{aligned}
\mathcal L(\mathbf \theta;\mathbf x_1, \mathbf x_2, \dots, \mathbf x_n) & := f(\mathbf x_1, \mathbf x_2, \dots, \mathbf x_n|\boldsymbol \theta)\\\\
 & = \prod_{i=1}^n f(\mathbf x_i\vert\boldsymbol \theta)
\end{aligned}
$$

---

### Maximum Likelihood Estimate

$$
\hat{\mathbf \theta}_{MLE}:=\underset{\mathbf \theta\in\Theta}{\text{arg max }}\mathcal L(\mathbf \theta;\mathbf x_1, \mathbf x_2, \dots, \mathbf x_n)
$$

{{< speaker_note >}}
- Maximum a Posteriori
{{< /speaker_note >}}

---

## Latent Variable

some hidden variable $Z$ that affect the observation $Y$

$(Y,Z)\sim f_{\theta^*} = f(\cdot, \cdot|\mathbf \theta^*)$


{{< speaker_note >}}
- Topics of an article
{{< /speaker_note >}}

---

### Marginalized likelihood

$\mathcal L(\theta; y) = \prod_{i=1}^n\sum_{z_i\in\mathcal Z} f(y_i, z_i|\theta)$

{{% fragment %}} Summing $z$ is usually intractable. {{% /fragment %}}

---

### AM-LVM

Alternatively estimate $\theta^t$ and $z^t$ at time step $t$

1. {{% fragment %}} assign a $z^t_i$ to each $y_i$ {{% /fragment %}}
2. {{% fragment %}} $\theta^t$: MLE on $\mathcal L(\theta; \\\{(y_i, z^t_i)\\\}^n_{i=1}$ {{% /fragment %}}
3. {{% fragment %}} $z^{t+1}$: MAP on $f(z|y_i, \theta^t)$ {{% /fragment %}}

{{% fragment %}} AM-LVM neglects all affects of $z'$ that is not the most likely ones. {{% /fragment %}}

{{< speaker_note >}}
- Topics of an article
{{< /speaker_note >}}

---

## EM Algorithm

"assigns" $y_i$ to a value $z$ with weight $f(z|y_i, \theta^t)$

---

### Sampling $z$

$$
\log\mathcal L(\theta;y) = \log\sum_zf(y, z|\theta)
$$

{{% fragment %}} $$ =\log\sum_zf(z|y, \theta^0)\frac{f(y, z|\theta)}{f(z|y, \theta^0)} $$ {{% /fragment %}}

{{% fragment %}} $$ =\log\mathbb E_{z\sim f(\cdot|y, \theta^0)}\left[\frac{f(y, z|\theta)}{f(z|y, \theta^0)}\right] $$ {{% /fragment %}}

---

### $Q$-function

\begin{aligned}
& \log\mathbb E_{z\sim f(\cdot|y, \theta^0)}\left[\frac{f(y, z|\theta)}{f(z|y, \theta^0)}\right] \\\\
\ge& \mathbb E_{z\sim f(\cdot|y, \theta^0)}\left[\log\frac{f(y, z|\theta)}{f(z|y, \theta^0)}\right] \qquad \text{(Jensen's inequality)}\\\\
=& \mathbb E_{z\sim f(\cdot|y, \theta^0)}[\log f(y, z|\theta)] \qquad \qquad (Q_y(\theta|\theta^0))\\\\
&- \mathbb E_{z\sim f(\cdot|y, \theta^0)}[\log f(z|y, \theta^0)] \qquad (R_y(\theta^0))
\end{aligned}


{{< speaker_note >}}
- weighted point-wise likelihood
- Variational distribution
- equals iff $\theta^0 = \theta$
{{< /speaker_note >}}

---

### Choosing $\theta$ to improve $Q$

$$
\log\mathcal L(\theta^0;y) = Q_y(\theta^0|\theta^0) - R_y(\theta^0)
$$

$$
\log\mathcal L(\theta;y) - \log\mathcal L(\theta^0;y) \ge Q_y(\theta|\theta^0) - Q_y(\theta^0|\theta^0)
$$

{{% fragment %}}  causes $\log\mathcal L(\theta;y)$ to improve at least as much. {{% /fragment %}}

---

### Pseudo Code

```
theta <- INITIALIZE()
for t = 1, 2, ... do
  Q <- E(theta)  // E-step
  theta <- M(theta, Q)  // M-step
end for
return theta
```

---

### E-step: sampling construction

$$
Q_t(\theta|\theta^t) = \frac1n \sum_{i=1}^n\sum_{z\in\mathcal Z} f(z|y, \theta^t)\cdot \log f(y_i, z|\theta)
$$

{{< speaker_note >}}
- this expression has $n\cdot |\mathcal Z|$ terms instead of $|\mathcal Z|^n$ terms.
{{< /speaker_note >}}

---

### M-step: gradient descent

$$
M(\theta, Q_{Y_t}) = \theta^t + \alpha_t\cdot\nabla Q_{Y_t}(\theta^t|\theta^t)
$$

{{< speaker_note >}}
- this expression has $n\cdot |\mathcal Z|$ terms instead of $|\mathcal Z|^n$ terms.
- if M-step improves $Q$, then EM iteration improves likelihood
{{< /speaker_note >}}

---

## Motivating Applications

Gaussian Mixture Models:

$$
f(\cdot|{\boldsymbol {\theta }})=\sum _{i=1}^{K}\phi _{i}{\mathcal {N}}({\boldsymbol {\mu _{i},\Sigma _{i}}})
$$

{{< speaker_note >}}
EM is of particular appeal for finite normal mixtures where closed-form expressions are possible such as in the following iterative algorithm by Dempster et al. (1977)
{{< /speaker_note >}}

---

### Plate notation

{{< figure src="img/gaussian_mixture.png" title="Non-Bayesian Gaussian mixture model" lightbox="true" >}}

{{< speaker_note >}}
- Smaller squares indicate fixed parameters;
- larger circles indicate random variables. 
- Filled-in shapes indicate known values.
- The indication [K] means a vector of size K.
{{< /speaker_note >}}

---

### E-step

$$
h_{s}^{(j)}(t)={\frac {w_{s}^{(j)}p_{s}(x^{(t)};\mu _{s}^{(j)},\Sigma _{s}^{(j)})}{\sum _{i=1}^{n}w_{i}^{(j)}p_{i}(x^{(t)};\mu _{i}^{(j)},\Sigma _{i}^{(j)})}}.
$$

---

### M-step
$$
w_{s}^{(j+1)}={\frac {1}{N}}\sum _{t=1}^{N}h_{s}^{(j)}(t)
$$
$$
{\displaystyle \mu _{s}^{(j+1)}={\frac {\sum _{t=1}^{N}h_{s}^{(j)}(t)x^{(t)}}{\sum _{t=1}^{N}h_{s}^{(j)}(t)}}}
$$
$$
{\displaystyle \Sigma _{s}^{(j+1)}={\frac {\sum _{t=1}^{N}h_{s}^{(j)}(t)[x^{(t)}-\mu _{s}^{(j+1)}][x^{(t)}-\mu _{s}^{(j+1)}]^{\top }}{\sum _{t=1}^{N}h_{s}^{(j)}(t)}}}
$$