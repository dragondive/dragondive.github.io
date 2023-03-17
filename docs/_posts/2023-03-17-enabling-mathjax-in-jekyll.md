---
layout: post
title:  "Enabling MathJax in Jekyll"
comments: true
share: true
tags:
  - mathjax
  - jekyll
  - self learning
  - reference
  - mathematics
---

For some of my technical blog posts, I may have to include mathematical expressions. In preparation, I decided to figure out today how to enable [MathJax](https://www.mathjax.org/) in Jekyll.

It turned out to be my easiest and simplest task on this blog so far. All I needed to do was include two lines [mentioned in their documentation](https://www.mathjax.org/#gettingstarted) into the blog's `_includes/header.html`.

```html
<script src="https://polyfill.io/v3/polyfill.min.js?features=es6"></script>
<script id="MathJax-script" async src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js"></script>
```

Then enclose the mathematical expressions within `$$ ... $$`:

```
$$
e ^ {\pi i} + 1 = 0 
$$
```

**Result**

$$
e ^ {\pi i} + 1 = 0 
$$
