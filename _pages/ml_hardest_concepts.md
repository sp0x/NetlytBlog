---
layout: post
title:  "What are the hardest concepts to grasp in machine learning?"
date:   2019-08-19 18:27:42 +0200
categories: ML Theory, ML
category: ML Theory
description: Whats the hardest thing in ML to wrap your head around?
cover: "/assets/concept.jpg"
author: Tony Petrov
---


<i>[As answered on Quora: What are the hardest concepts to grasp in machine learning?](https://qr.ae/TWvDYQ)</i>

Since everyone’s experience with ML is different I’ll list the concepts that I’ve had trouble with and that I’ve seen other data scientists struggle with (especially beginners):

<h2>Solving problems the ML way</h2>

Since I come from a developer background one of the hardest things to wrap my head around was the whole idea of feeding data into an algorithm and having it magically predict things. This is very confusing especially when you work with neural networks. In traditional software development, you write your code you can debug it and at every step of the way you know what it's doing. 

With ML it's a whole different story. First, you have to think in terms of inputs and outputs and not in terms of devising a set of instructions on how to solve a problem. Then you collect some data and you pick a learning algorithm over which you have very little control. Yes, you can tune it through its hyperparameters but you can’t really say if it's doing the right thing until it's done learning.

<h2>How neural networks “store” information</h2>

This is a question I get asked very often by beginners and sometimes more experienced data scientists. If you try to think about how a classical algorithm works you know that all algorithms rely on instructions and stored information. Yes, neural networks also use the same storage devices as other algorithms but the instructions they use to process that information is stored in a completely different way.

You basically have information being stored in the weights of connections between neurons. That’s really hard to imagine when you think about it because you can never be sure what each neuron actually represents. So trying to understand how a neural network is deciding on what the output should be is pretty challenging.

<h2>Why data is more important than the algorithm you use to solve your problem</h2>

Again if you’re coming from a developer background this can seem super weird. In normal software development, the algorithm you use is the single most important part of your application. It's the application itself! Data is just the stuff you feed into your amazing algorithm to produce an output. The better your algorithm the better your results. 

With ML, however, the learning algorithm doesn’t matter as much as the quality of the data. Which to a software developer makes no sense. Since we’re used to working with noisy often broken data we don’t really stop and think that data can actually be useful.

In ML good data can give you decent results even if you’re using an average algorithm. This shift in mindset is often quite difficult for engineers who aren’t used to having data do their work for them. It took me a fairly long time to wrap my head around this idea and to stop looking for better algorithms and to start looking into my data and trying to improve its quality instead of my algorithm.

The way I got around to grasping these concepts was through lots of practice. So to all beginners, I’d say don’t let ML scare you and keep on experimenting until you’ve mastered it.

Netlyt is an AI-only research and development company. We tackle non-trivial problems using the latest technologies and our own solutions. We've built systems to <a href=netlyt.io><b>prevent corruption in the police force, track and predict air pollution, reduce natural gas distribution costs, detect faults in the electricity grids in Africa</b></a> and others.