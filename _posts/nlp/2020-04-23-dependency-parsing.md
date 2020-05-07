---
layout: post
title: "cs224n: Dependency Parsing"
comments: true
author: "Yi"
header-style: text
mathjax: true
tags:
  - nlp
---
Forwarded from my zhihu blog post.
Notes for [Lecture 5](http://web.stanford.edu/class/cs224n/slides/cs224n-2020-lecture05-dep-parsing.pdf).

##  Two Views of Linguistic Structure

###  1\. Constituency = Phrase Structure Grammar = Context-free Grammars(CFG)

Phrase structure organizes words into nested constituents. e.g. unit words ->
phrases -> bigger phrases:

![](/img/in_post/v2-73d9c3bbfe0154186f7da42f0b7b9b68_b.jpg)

###  2\. Dependency Structure

It shows which words depend on(modify or are arguments of) which other words.

![](/img/in_post/v2-c6466792e13324d8b8c90f278bc232cb_b.jpg)

Such relationships could be used to build a dependency tree for finding
semantic relations.

##  Dependency Grammar and Treebanks

Dependency grammar postulates that the syntactic structure consists of
relations between lexical items, normally binary asymmetric relations called
dependencies.

For root word -> dependent word, the arrow shows what dependent is on the root
word. e.g. submitted were, 'were' is an auxiliary of 'submitted'.

![](/img/in_post/v2-c683248b3343afa98e57029ba82c6731_b.jpg)

We never make use of the labels, we only use the arrows. For the arrows, we
should be able to interpret e.g. what is the prepositional object modifies, by
checking whether the arrow is connected to some words and whether it is
correct or wrong.

Usually, add a fake ROOT so every word is dependent on precisely 1 other node.

Universal dependencies treebank - the rise of annotated data. Human annotated
dependency structure for many sentences(the 1990s), before that, people often
write grammar and use it to parse sentences. It turns out treebank is more
useful because it has reusability of the labor, broad coverage,
frequencies/distributional information, a way to evaluate the system.

###  How to Build Dependency Parses?

We want models to capture what is the right parse. Parse means choosing for
each word what other word is it a dependent of.

Constraint to make the dependency a tree:

1) only one word is the dependent of ROOT

2) No cycle, e.g. A->B, B->A

3) whether arrows can cross(non-projective) or not

###  Transition-based Dependency Parser

The transition-based dependency parse works like a state machine. The
initialized state is a stack with ROOT word in it, a buffer with all words in
the sentence with their order in the sentence, an empty arc set
$\displaystyle A$  .

  * Shift: Pop a word from the buffer and push it in the stack. 
  * Left-Arc: Set the second newly pushed word as the dependent of the newly pushed word, i.e. add  $\displaystyle r(w_j, w_i)$  in  $\displaystyle A$  . 
  * Right-Arc: Add  $\displaystyle r(w_j, w_i)$  in  $\displaystyle A$  . 

An example:

![](/img/in_post/v2-8e74c82616494df0a5082f42ee091108_b.jpg)

How to predict each transition action? Discriminative classifier!

Features: {top word in the stack, top word in the buffer}. If there are
$\displaystyle R$  arcs to predict, then this is a  $\displaystyle 2|R| + 1$
classification problem(for each state we have left arc, right arc, and shift
action to predict).

To evaluate the prediction, we can check  $\displaystyle \text{UAS(unlabeled
accuracy loss) or AS } = \frac{\\#\text{correct deps}}{\\# dependencies}$

Features: Indicator features would be very sparse. For more efficient
computation, we take the top word in the stack, first word in the buffer,
these position's part-of-speech tags, dependency labels that already been
built; concatenate these vectors into a single one. These vectors are
distributed representation.

![](/img/in_post/v2-2080d74de4dede04bcb616426a95ee19_b.jpg)

The final model structure:

![](/img/in_post/v2-8e3af18acf14fa251272ed30cabbc3f7_b.jpg)

