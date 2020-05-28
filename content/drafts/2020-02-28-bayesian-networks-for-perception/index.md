---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "Bayesian Networks for Perception"
subtitle: ""
summary: "What's going on with uncertainty in deep learning and how to apply them to object detection."
authors: []
tags: []
categories: []
date: 2020-02-28T17:31:22+10:30
lastmod: 2020-02-28T17:31:22+10:30
featured: false
draft: false

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

Finally I got a chance to talk about Bayesian Deep Learning. Although being an important topic and a classic task, we haven't seen enough attentions here following the breakthoughs in deep learning field. Probably because of its shortcommings in inference and difficulties in explanation, many people a still standing by. Recently I am very happy to find this field moving in a healthy direction and here are some very useful papers discussing related stuffs.

* [Pitfalls of In-Domain Uncertainty Estimation and Ensembling in Deep Learning](https://openreview.net/forum?id=BJxI5gHKDr)
* [The Case for Bayesian Deep Learning](https://arxiv.org/abs/2001.10995)
* [How Good is the Bayes Posterior in Deep Neural Networks Really?](https://arxiv.org/abs/2002.02405)

In this post I would like to first give a quick recap to Bayesian Deep Learning in general and discuss what can be done in object detection.

## Introduction

[Kendall][Kendall2017] introduced a general practice for uncertainty estimation in computer vision. The uncertainity of models can be categorized into two sources. *Aleatoric* uncertainty captures noise inherent in the observations and *epistemic* uncertainty accounts for uncertainty in the model.

We can estimate epistemic uncertainty by placing a prior on model weights, or aleatoric uncertainty by putting a prior on model outputs. Bayesian Neural Networks replace neural network weights with the posterior distribution of them.

## Epistemic Uncertainty with Bayesian Neural Networks

### MCMC Approaches

Performing this inference often includes a Monte Carlo approximation. These methods samples the model weights from the estimated distribution and ensemble the predictions of multiple samples. Thus, evaluating the posterior requires evaluating an ensemble of models:

$$
\hat p(y_i|x_i)\approx\int p(y_i|x_i,\omega)q_m(\omega) d_\omega\simeq \frac1K\sum_{k=1}^K p(y_i|x_i, \omega_k),\qquad \omega_k\sim q_m(\omega).
$$

We can also use a variational distribution $q(\omega; \theta)$ to approximate the posterior $p(\omega|x, y)$. Introducing a prior $p(\omega)$ and applying Bayesian rules, we get the objective known as the evidence lower bound (ELBO):

$$
\theta^* = \underset{\theta}{\mathrm{arg\,max}} \{\mathbb E_{\omega\sim q}[\log p(y|x, \omega)] - D_{\text{KL}}[q(\omega;\theta)\|p(\omega)]\}.
$$

The first term is reconstruction and the second regularization. The KL term can be represented explicitly if we choose simple form for the variational distribution. And we can use MC approximations for the reconstruction term:

$$
\mathbb E_{\omega\sim q}[\log p(y|x, \omega)]\simeq \frac1K\sum_{k=1}^K \log p(y|x, \omega_k), \qquad \omega_k\sim q(\omega; \theta)
$$

|    Method     |  Sampling distribution $q_m$     |
|---------------|:--------------------------------:|
| Deep Ensemble |  $\frac1S\sum_{s=1}^S\delta(\omega - \omega_s)$      |
| Mean-Field VI |  $\mathcal N(\omega\vert\mu, \text{diag}(\sigma^2))$ |
| MC dropout    |  Dropout distribution |
| SWAG          |   |
| FGE           |   |
| cSGLD         |   |
| TDA           |   |
| Multi-SWAG    |  SWAG + Deep Ensemble |

such as in [MC dropout](https://arxiv.org/abs/1506.02142 "Dropout as a Bayesian Approximation: Representing Model Uncertainty in Deep Learning"), [SWA-Gaussian](https://arxiv.org/abs/1902.02476 "A Simple Baseline for Bayesian Uncertainty in Deep Learning") etc

Approximation and acceleration of ensembling has been studied.

### Deterministic VI Approaches
SVI such as in [[Blundell 2015]][Blundell2015] is difficult to get to work for large dataset such as ImageNet and complex models.

[[Wu et al. 2018]][Wu2018] assume the pre-ReLU activations are Gaussian and deduce closed form posterior of the network output when the nonlinearity is Heaiside or ReLU. Even though sometimes we can only afford to compute the diagonal entries $\operatorname{Cov}(h_j, h_j)$. The empirical result is acceptable.

### Yes, but Did It Work?
[[Wenzel et al. 2020]][Wenzel2020] shows that Bayesian posterior can give an inferior prediction than MAP or VI. [[Yao]][Yao2020] suggests that such models could benefits from informative priors instead of treating networks as a black box.

The exact sampling from a posterior in a deep neural network is infeasible. Current sampling methods can be inaccurate and hard to defend. Even HMC cannot serve as a gold standard because it suffers from all multimodality and non-log-convexity [[Yao]][Yao2020].

Also, it is hard to say whether functional diversity can be captured by sampling around one mode [[Wilson 2020]][Wilson2020].

Generally, I don't consider these methods scalable enough for more challenging perception tasks on large scale dataset. The uncertainty consistency for these methods can be degraded for different datasets [[]].

## Aleatoric Uncertainty for Panoptic Segmentation
We consider an object detection problem with a dataset

For real-time applications, we cannot afford expensive Monte Carlo estimations or covariance estimation.

The only thing we can afford for epistemic uncertainty is probably making only the last layer Bayesian ([[Riquelme 2018]][Riquelme2018]). So I would prefer not to touch Bayesian Neural Network for object detection.

Instead I will just cover a simple model for aleatoric uncertainty.

I choose to adopt heteroscedastic aleatoric uncertainty introduced in [[Kendall 2017]][Kendall2017]. For a regression problem given input pair $\mathbf X$, $\mathbf Y$.


[Blundell2015]: https://arxiv.org/abs/1505.05424 "Weight Uncertainty in Neural Networks"
[Kendall2017]: https://papers.nips.cc/paper/7141-what-uncertainties-do-we-need-in-bayesian-deep-learning-for-computer-vision.pdf "What Uncertainties Do We Need in Bayesian Deep Learning for Computer Vision?"
[Ovadia2019]: http://papers.nips.cc/paper/9547-can-you-trust-your-models-uncertainty-evaluating-predictive-uncertainty-under-dataset-shift "Can You Trust Your Model’s Uncertainty? Evaluating Predictive Uncertainty Under Dataset Shift"
[Riquelme2018]: https://openreview.net/forum?id=SyYe6k-CW "An Empirical Comparison of Bayesian Deep Networks for Thompson Sampling"
[Wenzel2020]: https://arxiv.org/abs/2002.02405 "How Good is the Bayes Posterior in Deep Neural Networks Really?"
[Wilson2020]: https://arxiv.org/abs/2002.08791 "Bayesian Deep Learning and a Probabilistic Perspective of Generalization"
[Wu2018]: https://arxiv.org/abs/1810.03958 "Deterministic Variational Inference for Robust Bayesian Neural Networks"
[Yao2020]: https://statmodeling.stat.columbia.edu/2020/02/13/how-good-is-the-bayes-posterior-for-prediction-really/ "How good is the Bayes posterior for prediction really?"
