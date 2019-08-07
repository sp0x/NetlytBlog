---
layout: post
title:  "Why are Natural Language Processing (NLP) researchers trying to abandon RNNs and design new architectures that are not recurrent?"
date:   2019-08-19 18:27:42 +0200
categories: nlp
category: NLP
description: Why are AI researchers running away from RNNs?
cover: "/assets/rnn.png"
author: Tony Petrov
---
<i>[As answered on Quora: Why are Natural Language Processing (NLP) researchers trying to abandon RNNs and design new architectures that are not recurrent?](https://qr.ae/TWvDyP)</i>

For quite some time now new CNN based models have been pushing RNNs out of use. Here are a few reasons why this is happening:

<h2>RNNs are slow</h2>
Due to the way they’re designed RNNs can’t be parallelized. Since the computation of the current step depends on the result from the previous one you just have to do everything in a serial fashion.

This makes it really difficult to train RNNs on GPUs since GPU optimizations rely on the task being parallelizable. CNNs, for instance, are fantastic for this job and hence why most RNN replacements are based around convolutions.

Furthermore, RNNs don’t really give you any performance boost in tasks like machine translation where adding attention to a feedforward network is usually enough to get great results at a much lower computational cost.

<h2>Vanishing Gradients And Shallowness</h2>

Since RNNs get inputs from the previous time steps as well as the current one, this means that their error gradients need to be calculated across time as well.

These calculations are both the reason why RNNs are so slow, to begin with, and also lead to a problem known as vanishing/exploding gradients. Due to the large number of updates that these gradients undergo their values tend to shoot up to infinity or completely vanish by the time they reach the top layers of the network. This makes it extremely difficult to train deep RNNs as the top layers end up not learning.

<h2>Should I dump all of my RNNs and switch to CNNs?</h2>

As with everything AI related the answer isn’t all that simple. After all RNNs aren’t all downsides and no benefit. 

For one RNNs are more intuitive and easy to understand. When you’re trying to solve a problem that no one has tackled before (like we did [with the world’s first English to braille translator powered by AI](https://netlyt.io/case/braille)) it is better to use an architecture that you’re familiar with. 

The early stages of the development of a new algorithm are very difficult. First you have to understand the problem before you start optimizing your solution. RNNs are a great start for any sequence based problem. They allow you to quickly experiment and start learning about the problem in more depth.

Once you have a working RNN based solution that you understand then you can move onto using CNNs.  

<br>
<br>
<em style="font-size: 18px; color: rgba(0, 0, 0, 0.62);">
    <i>Netlyt is an AI-only research and development company. We tackle non-trivial problems using the latest technologies and our own solutions. We've built systems to <b>[prevent corruption in the police force, track and predict air pollution, reduce natural gas distribution costs, detect faults in the electricity grids in Africa](https://netlyt.io)</b> and others.</i>
</em>