---
layout: post
title:  "ggplot2 refresher"
date:   2017-11-24 16:16:01 -0600
categories: ggplot refresher
---

In this refresher, we will produce the following plot using `ggplot2` and `dplyr`:

![Square Plot](/assets/img/squareplot.png){:class="img-responsive"}


This picture is defined as representing the following data:

- (x, y) are both [iid](https://en.wikipedia.org/wiki/Independent_and_identically_distributed_random_variables)
about a regular normal distribution
- Points within .1 of an axes are **not** included
- Points are colored according to their distance to the origin (darker will be closer)

First, let's import libraries that we're going to need if a library isn't installed,
you can install by running the following on the R console once: `install.packages('packagename')`

Import ggplot2 (for plotting) and dplyr (for data manipulation)

```R
library(ggplot2)
library(dplyr)
```

Basic variable assignment, notice how variable names can have the "." character

```R
axis.min <- -4
axis.max <- 4
n <- 10000
tolerance <- .1
```

Create a data frame with 10,000 observations `rnorm()` is a built in function that
takes an integer n and outputs n normally distributed numbers. This line has the
R "pipe" operator, with similar thinking as the Unix pipe. `%>%` is actually syntatic
sugar to just composing functions `a %>% b() %>% c()` is logically equivalent to
`c(b(a))`. A helpful way of thinking about it is that we're writing in order of
execution, so the calls are done "inside out".

```R
tibble(x = rnorm(n), y = rnorm(n)) %>%
```

Filter out those inside the tolerance of the axes

```R
  filter(x < -tolerance | x > tolerance) %>%
  filter(y < -tolerance | y > tolerance) %>%
```

Mutate tibble so that is has a column that is the distance to the center.
NB: Because sqrt(abs(x * y)) is nested as such,
it is the same as writing: distance = (x * y) %>% abs() %>% sqrt()

```R
mutate(distance = sqrt(abs(x * y))) %>%
```

The data is now piped into a plotting object, notice the "+" indicate
that we're no longer piping, as we're now building the actual graphic.

```R
  ggplot() +
```

Add an aesthetic that defines x position, y position, and the color

```R
    aes(x = x, y = y, colour = distance) +
```

Add a point layer, with each point size = 1

```R
    geom_point(size = 1) +
```

Modify the colour scale so that the points range from black to white

```R
    scale_colour_gradient(low = "00000", high = "FFFFF") +
```

Expand both of the the axes to be given min and max

```R
    xlim(axis.min, axis.max) + ylim(axis.min, axis.max)
```

Notice that above call did not have a "+", meaning that this singular call will
at the point output a plot to the either Quartz or the "Plots" tab, depending
how you're running this file.

Finally, we can sve the generated plot to a file with size 10x10 inches (yes, inches).
A good frame of reference is that 1 inch = 300 pixels.

```R
ggsave('squareplot.png', width = 10, height = 10)
```

Without any comments, here is the entire block.

```R
library(ggplot2)
library(dplyr)

axis.min <- -4
axis.max <- 4
n <- 10000
tolerance <- .1

tibble(x = rnorm(n), y = rnorm(n)) %>%
  filter(x < -tolerance | x > tolerance) %>%
  filter(y < -tolerance | y > tolerance) %>%
  mutate(distance = sqrt(abs(x * y))) %>%

  ggplot() +
    aes(x = x, y = y, colour = distance) +
    geom_point(size = 1) +
    scale_colour_gradient(low = "#000000", high = "#FFFFFF") +
    xlim(axis.min, axis.max) + ylim(axis.min, axis.max)

ggsave('squareplot.png', width = 10, height = 10)
```
