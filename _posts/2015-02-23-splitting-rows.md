---
layout: post
title: "R question: Splitting rows inside a matrix"
date: 2015-02-23
category: how-to
tags: [split]
image: alleles.png
---

In this post I'll describe a problem for manipulating data in R, that I think might be useful for those working on genetics and related fields.

<!--more-->

![](/images/blog/alleles.png)

<hr/>

### Motivation

Some days ago I received an email from a student of University of Buenos Aires, Argentina, asking me a question about a problem in R, and requesting some help. Although I usually cannot answer to this type of emails (mostly for lack of time), her question was interesting enough, and worth a blog post.

So here's the problem. Imagine that you are working with a file containing [allele](http://en.wikipedia.org/wiki/Allele) copies for several individuals. Specifically, consider you have a toy matrix like the following one:

{% highlight r %}
# matrix of alleles
alleles = rbind(c("B_B", "B_B", "A_A"), c("B_B", "A_B", "A_B"), c("B_B", "B_B", 
    "A_A"), c("A_B", "A_B", "A_A"), c("B_B", "A_B", "A_A"), c("B_B", "A_B", 
    "A_A"))

# adding names
rownames(alleles) = c(160, 301, 303, 311, 312, 314)
colnames(alleles) = paste("X", c(9718, 3574, 3547), sep = "_")

# toy allele's data
alleles
{% endhighlight %}



{% highlight text %}
##     X_9718 X_3574 X_3547
## 160 "B_B"  "B_B"  "A_A" 
## 301 "B_B"  "A_B"  "A_B" 
## 303 "B_B"  "B_B"  "A_A" 
## 311 "A_B"  "A_B"  "A_A" 
## 312 "B_B"  "A_B"  "A_A" 
## 314 "B_B"  "A_B"  "A_A"
{% endhighlight %}


but you need to transform the data such that each allele copy appears in a separate column, like this:




{% highlight r %}
# transformed allele's data
new_alleles
{% endhighlight %}



{% highlight text %}
##     X_9718 X_9718 X_3574 X_3574 X_3547 X_3547
## 160 "B"    "B"    "B"    "B"    "A"    "A"   
## 301 "B"    "B"    "A"    "B"    "A"    "B"   
## 303 "B"    "B"    "B"    "B"    "A"    "A"   
## 311 "A"    "B"    "A"    "B"    "A"    "A"   
## 312 "B"    "B"    "A"    "B"    "A"    "A"   
## 314 "B"    "B"    "A"    "B"    "A"    "A"
{% endhighlight %}


That is, the transformed data involves splitting each element of the matrix to obtain an unfolded matrix with the same number of rows (i.e. individuals) but with twice the number of columns.


### Solution

Here's one possible solution to the previous problem. The idea is to take each row of the matrix, unfold the alleles (i.e. splitting them by removing the underscore symbol '_'), and then store the output in a new matrix which will have the double of columns as the original data.


{% highlight r %}
# how many observations
num_obs = nrow(alleles)

# how many columns
num_col = ncol(alleles)

# create matrix where unfolded alleles will be placed
alleles2 = matrix("", num_obs, 2 * num_col)

# function to split alleles of one observation (one row)
split_allele <- function(allele) {
    unlist(strsplit(allele, split = "_"))
}

# unfolding alleles (row by row)
for (i in 1:num_obs) {
    alleles2[i, ] = split_allele(alleles[i, ])
}

# add names (it's better if you add row and column names later)
rownames(alleles2) = rownames(alleles)
colnames(alleles2) = rep(colnames(alleles), each = 2)

alleles2
{% endhighlight %}



{% highlight text %}
##     X_9718 X_9718 X_3574 X_3574 X_3547 X_3547
## 160 "B"    "B"    "B"    "B"    "A"    "A"   
## 301 "B"    "B"    "A"    "B"    "A"    "B"   
## 303 "B"    "B"    "B"    "B"    "A"    "A"   
## 311 "A"    "B"    "A"    "B"    "A"    "A"   
## 312 "B"    "B"    "A"    "B"    "A"    "A"   
## 314 "B"    "B"    "A"    "B"    "A"    "A"
{% endhighlight %}



