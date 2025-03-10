---
title: "Model tests: Recursive and Bootstrap Approaches"
date: 2025-03-05 22:48:00 -0400
---

The recursive and bootstrap approaches are two model tests with the econometrics tool. 

The recursive approach provides a temporal look at the model stability, while the bootstrap approach provides a distributional assessment of coefficient variability and robustness through resampling. These techniques help evaluate a model's performance under different data conditions.

## Recursive
The recursive approach involves sequentially fitting the same regression model over time, and updating the sample by adding new observations. This is done by fitting the model using data up to time t. The add the next observation (t+1) and refit the model. This helps track how coefficients evolve over time, which is useful for identifying model instability. Currently, the recursive approach focuses only on coefficient tracking. 

The code contains 3 main functions, these are (1) recursive model Fitting (recursive.apply), (2) generating the recursive table (get.recursive.table), and (3) plotting the results (get.recursive.chart).





# Headers

Use headers to organize your content. Each header will automatically be part of the Table of Contents generated in tools that support this feature.

## Header 2
### Header 3
#### Header 4

# Text Formatting

Quickly add styles to your text with these Markdown commands.

**bold text**
_italic text_
**_bold and italic_**

# Lists

- Bullet list item 1
- Bullet list item 2
  - Nested bullet list item

1. Numbered list item 1
2. Numbered list item 2

# Links and images

We can link to websites like [this]((https://sop.route1.io/))

And we can also link images like so:

![Detective Pikachu frowning]({{site.url}}{{site.baseurl}}/images/detectivepikachu.jpg)

The image must first be copied into the *images* directory and then referenced by filepath

# Code snippets

{% highlight r %}
# Simple R code for mean calculation
my_vector <- c(1, 2, 3, 4, 5)
mean(my_vector)
{% endhighlight %}

# Mathematical Expressions

Mathematical expressions can be imbedded using LaTeX syntax

$$ x(\theta) = (R - r)cos\theta + d cos(\frac{R - r}{r}\theta) $$
$$ y(\theta) = (R - r)sin\theta - d sin(\frac{R - r}{r}\theta) $$