---
title: Universal Differential Equations for Scientific Machine Learning
summary: The exact slides copied from [Chris' Talk](https://www.youtube.com/watch?v=bBH8HVEr0-A) on his new research.
authors: []
tags: []
categories: []
date: "2020-04-01T00:00:00Z"
slides:
  # Choose a theme from https://github.com/hakimel/reveal.js#theming
  theme: serif
  # Choose a code highlighting style (if highlighting enabled in `params.toml`)
  #   Light style: github. Dark style: dracula (default).
  highlight_style: github
---

## Universal Differential Equations for Scientific Machine Learning

Chris Rackauckas<br/>
Massachusetts Institute of Technology, Department of Mathematics<br/>
University of Maryland, Baltimore, School of Pharmacy, Center for Translational Medicine

---

### The major advances in machine learning were due to encoding more structure into the model

More structure = Faster and better fits from less data

---

### Convolutional Neural Networks Are Structure Assumptions

{{< figure src="img/cnn.png" >}}

---

### What is the structure of science?

---

### Ecology Example: Lotka-Volterra Equation

$$
\frac{d🐇}{dt} = \alpha 🐇 - \beta 🐇 🐺
$$

$$
\frac{d🐺}{dt} = \delta 🐇 🐺 - \gamma 🐺
$$

---

### Scientific ML/AL is Domain Models with Integrated Machine Learning

{{< speaker_note >}}
- Models are these almost correct differential equations
- We have to augment the models with the data we have
{{< /speaker_note >}}

---

### Mechanistic vs Non-Mechanistic Models

- __Differential equations__ describe mechanisms/structure and let the equations naturally evolve from this description
  - $🐇'(t) = \alpha 🐇(t)$ encodes "the rate at which the population is growing depends on the current number of rabbits".
- __Machine learning models__ specify a learnable black box, where with the right parameters they can fit any nonlinear function.

---

### Universal Approximation Theorem

Neural networks can get $\epsilon$ close to any $R^n\rightarrow R^m$ function

__Neural networks are just function expansions, fancy Taylor Series like things which are good for computing and bad for analysis__
___

### Neural Networks = Nonlinear Function Approximation

- Polynomial: $e^x = a_1 + a_2x + a_3x^2 + \cdots$
- Nonlinear: $e^x = 1 + \frac{a_1\tanh(a_2)}{a_3x-\tanh(a_4x)}$
- Neural Network: $e^x\approx W_3\sigma(W_2\sigma(W_1x+b_1) + b_2) + b_3$

Others: Fourier/Chebyshev Series, Tensor product spaces, sparse grid, RBFs, etc.
___

### Neural Networks are universal approximators which work well in high dimensions

Neural networks overcome "the curse of dimensionality"

---

### Universal Differential Equations

- Replace the user-defined structure with a neural network, and learn the nonlinear function for the structure
- Neural ordinary differential equation: $u' = f(u, p, t)$. Let $f$ be a neural network.

---



## Fragments

Make content appear incrementally

```
{{%/* fragment */%}} One {{%/* /fragment */%}}
{{%/* fragment */%}} **Two** {{%/* /fragment */%}}
{{%/* fragment */%}} Three {{%/* /fragment */%}}
```

Press `Space` to play!

{{% fragment %}} One {{% /fragment %}}
{{% fragment %}} **Two** {{% /fragment %}}
{{% fragment %}} Three {{% /fragment %}}

---

A fragment can accept two optional parameters:

- `class`: use a custom style (requires definition in custom CSS)
- `weight`: sets the order in which a fragment appears

---

## Speaker Notes

Add speaker notes to your presentation

```markdown
{{%/* speaker_note */%}}
- Only the speaker can read these notes
- Press `S` key to view
{{%/* /speaker_note */%}}
```

Press the `S` key to view the speaker notes!

{{< speaker_note >}}
- Only the speaker can read these notes
- Press `S` key to view
{{< /speaker_note >}}

---

## Themes

- black: Black background, white text, blue links (default)
- white: White background, black text, blue links
- league: Gray background, white text, blue links
- beige: Beige background, dark text, brown links
- sky: Blue background, thin dark text, blue links

---

- night: Black background, thick white text, orange links
- serif: Cappuccino background, gray text, brown links
- simple: White background, black text, blue links
- solarized: Cream-colored background, dark green text, blue links

---

{{< slide background-image="/img/boards.jpg" >}}

## Custom Slide

Customize the slide style and background

```markdown
{{</* slide background-image="/img/boards.jpg" */>}}
{{</* slide background-color="#0000FF" */>}}
{{</* slide class="my-style" */>}}
```

---

## Custom CSS Example

Let's make headers navy colored.

Create `assets/css/reveal_custom.css` with:

```css
.reveal section h1,
.reveal section h2,
.reveal section h3 {
  color: navy;
}
```

---

# Questions?

[Ask](https://spectrum.chat/academic)

[Documentation](https://sourcethemes.com/academic/docs/managing-content/#create-slides)