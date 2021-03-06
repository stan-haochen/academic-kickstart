---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "NAS - Where Are We Now"
subtitle: ""
summary: "The reasons why people are either running towards or away from Neural Architecture Search."
authors: []
tags: []
categories: []
date: 2019-12-04T20:06:00+10:30
lastmod: 2019-12-04T20:06:00+10:30
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
> First off this ain't no diss record<br/>
> This for some of my homies that were misrepresented
>
> -- <cite> Nas, Where Are They Now. Hip Hop is Dead, 2006. </cite>

For the past year and a half, I have been working on Neural Architecture Search (NAS). The idea of automatically designing neural networks for specific tasks is enticing for both practitioners and theorists. In production, NAS extends the scope of network pruning/compression and can benefits on chip energy saving modeling, etc. In research, NAS has raised new questions and challenges for convergence and generalization analysis, since it requires rapid and accurate structure evaluation.

To quickly recap what's going on with NAS, I suggest reading [Vladimir's post](https://drsleep.github.io/NAS-at-CVPR-2019/). A curated list of literature on NAS is maintained [here](https://www.automl.org/automl/literature-on-neural-architecture-search/).

In this post, I will cast NAS as a bi-level optimization problem. We want to minimize some function $f$, to achieve optimal accuracy or some complex objective considering speed-accuracy tradeoff, with respect to some hyperparameter $h$, in our case, the network structure. To simplify the analysis, we assume $h$ takes form of a sequence with length $L$ and vocabulary size $K$.

$$
\min_{h, z} f(z;h)\qquad s.t. \quad z = \operatorname{argmax}_{\theta_h} f(\theta_h;h).
$$
Two major problems NAS deals with are

1. Inner loop is slow. We have to train a network with structure $h$.
2. Since there is no explicit derivative, we cannot optimize $f(h)$ directly.

## NAS with Variational Optimization

Straightforwardly, we can solve these two problems one by one. First, we minimize the upper bound of our objective:

$$
\min_h f(h)\le \min_\alpha \mathbb E_{h\sim p_{\alpha}(h)}[f(h)],
$$

where $p(h|\alpha)$ can be parametrized by a sequential network, of which the gradient becomes tractable:
$$
\nabla_\alpha \mathbb E_{p_\alpha(h)}[f(h)] = \mathbb E_{p_\alpha (h)}[f(h)\nabla_\alpha \log {p_\alpha}(h)].
$$

This is the REINFORCE algorithm used by [Zoph and Le](https://arxiv.org/abs/1611.01578). The gradient estimation can be made more efficient with PPO as in [their later work](https://arxiv.org/abs/1707.07012).

In NAS, sample efficiency is a bigger issue than in normal reinforcement learning tasks. Because training a network can be as costly as it can get to evaluate a single action. In other words, we prefer lower variance searching algorithms than lower bias ones. This is the reason I don't consider using evolutionary strategy or random search (such as hyperband) for NAS, which ususally requires more samples. According to my experience, to find a good architecture with length $L=20$ and $K=7$ takes about 3,000 samples with REINFORCE and 1,500 with PPO.

Speeding up sample evaluation is definitely important. Typically, a proxy task is designed, which includes training a smaller model with smaller input resolution and less iterations. Some other tricks are analyzed by [Nekrasove et al.](https://arxiv.org/abs/1810.10804) However, all these tricks introduce biases to the evaluation. *It is a good practice to analyse the generalization quality of the proxy tasks to the target task.*

## NAS with Discrete Structure Learning
Another solution to the two problems is to consider them as one and solve them in one shot. The idea is to consider the structure parameters $h$ as a part of the network and one-shot the search by performing a network optimization, usually with SGD.

[DARTS](https://arxiv.org/abs/1806.09055) uses a continous relaxation $h\approx \sigma(\alpha)$ on the operations, 
$$
\nabla_\alpha \mathbb E_{p_\alpha(h)}[f(h)]\approx\nabla_\alpha f(\sigma(\alpha))
$$
where $\sigma$ is softmax activation. Although biased, This is reasonable considering the popular [Lottery Ticket Hypothesis](https://arxiv.org/abs/1803.03635). (I will comeback to this part later.) However, I consider the connection learning part to be ad hoc, simply selecting the highest two activations, to follow the cell-based search space in [[Zoph and Le]((https://arxiv.org/abs/1611.01578))].

There are still a lot of unanswered questions. Is this approximation error bounded? How can we avoid overfitting? We don't even bother developing more accurate gradient computation including inverse Hessian for the second-order optimization, probably because of the accurate gradient does not leads to better result because of this bias.

This challenging questions require better understanding of the optimization mechanisms and properties, e.g. how to early stop? how does training affect generalization?

Another possible fix to this biased estimation is discrete latent structure learning. [[Xie et al.](https://openreview.net/forum?id=rylqooRqK7)] uses Gumbel-softmax trick to reduce this bias.
$$
\nabla_\alpha \mathbb E_{p_\alpha(h)}[f(h)]\approx \mathbb E_{p(u)}\nabla_\alpha f(\sigma(z/t));\quad z:=\log\frac{\alpha}{1-\alpha} + \log\frac{u}{1-u};\quad u\sim\operatorname{Uniform}(0, 1).
$$
A problem with this trick is that the variance goes to infinity as bias gets closer to $0$, which is controlled by the temperature $t$. I am interested to see someone combine this trick with control variates, such as in [relax](https://github.com/duvenaud/relax).
