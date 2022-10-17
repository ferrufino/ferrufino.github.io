---
layout: post
title: Intro to System Design Architecture
tags: sysDesignArch
math: false
toc: true
date: 2022-01-28 05:30 +0800
---

**Motivation:** While designing a scalable architecture we have to keep so many things in mind. To make this learning process easier (and in a incremental manner) I'll be summarizing books/courses I start reading and writing from personal experience.

My goal here is to have in simple words notes I wish someone had taught me before.

Stating the obvious, there is no such a thing as a 'just-do-this recipes'. In real life you'll have to design depending on the needs required. Hopefully this sets a base for creating those designs.

**TLDR:** In this post we start describing key characteristics a system needs to keep in mind, plus also description of key components.\
Notes taken were from Educative.io courses.
(If you want to jump into more advance stuff, check out the next 'Design Arch' post!)

## System Design

Whenever we are designing a large[^fn-sample_footnote] system, we need to consider a few things:

1.  What are the requirements that our system needs to cover? Can we estimate the scale a service will have?
2.  What are the different architectural pieces that can be used?
3.  How do these pieces work with each other?
4.  How can we best utilize these pieces: what are the right tradeoffs?

But before we even do this, we must first understand key characteristics to consider while desigining architectures.

## Key Characteristics of Distributed Systems

First thing we will learn: **SAREM**\
This stands for:

- Scalability
- Availability
- Reliability
- Efficiency
- Manageability

### Scalability

<mark>Scalability is the capability of a system, process, or a network to grow and manage increased demand.</mark> Any distributed system that can continuously evolve in order to support the growing amount of work is considered to be scalable.

There are multiple reason why a system might need to scale, like increased data volume or increased amount of work/transactions. The goal of a scalable system is to achieve this, without performance loss.

Generally, the performance of a system, although designed (or claimed) to be scalable, declines with the system size due to the management or environment cost.

For instance, network speed may become slower because machines tend to be far apart from one another. More generally, some tasks may not be distributed, either because of their inherent atomic nature or because of some flaw in the system design. At some point, such tasks would limit the speed-up obtained by distribution.

A scalable architecture avoids this situation and attempts to balance the load on all the participating nodes evenly.

Now there are two general known forms of scaling, **vertical and horizontal scaling.**

<mark>Vertical Scaling means you scale by adding more power (CPU, RAM, Storage, etc.) to an existing server.</mark> Being a single server though, this would imply shutting things down (downtime) and moving them to a higher performant server.

<mark>Horizontal scaling means that you scale by adding more servers into your pool of resources.</mark> Allowing more nodes available if some end up crashing to provide continous availability of the system.

### Availability

<mark>Availability
refers to the percentage in time the system remains operational, <strong>under normal conditions</strong>, performaing the tasks required.</mark> If have low downtime, or your system rarely crashes, then you have high availability.

### Reliability

Reliability is the probability a system will fail in a given period. In simple terms,<mark> a distributed system is considered reliable if it keeps delivering its services even when one or several of its software or hardware components fail.</mark> Reliability represents one of the main characteristics of any distributed system, since in such systems any failing machine can always be replaced by another healthy one, ensuring the completion of the requested task.

Take the example of a Amazon ecommerce, where one of the primary requirement is that any user transaction should never be canceled due to a failure of the machine that is running that transaction.

<mark>For instance, if a user has added an item to their shopping cart, the system is expected not to lose it. A reliable distributed system achieves this through redundancy of both the software components and data.</mark> If the server carrying the user’s shopping cart fails, another server that has the exact replica of the shopping cart should replace it.

Obviously, redundancy has a cost and a reliable system has to pay that to achieve such resilience for services by eliminating every single point of failure.

<mark> Reliability is availability over time considering the full range of possible real-world conditions that can occur. </mark>

### Efficiency

Efficiency can be measured in many ways regarding ones system. In general though, it can be expressed in a distributed system as two standard measures:

- <mark>The response time (or latency) that denotes the delay to obtain the first item.</mark> Number of messages globally sent by the nodes of the system regardless of the message size.
- <mark>The throughput (or bandwidth) which denotes the number of items delivered in a given time unit (e.g., a second)</mark> Size of messages representing the volume of data exchanges.

### Manageability

<mark>Also known as Serviceability, it refers to how easy it is to operate and maintain the system. <strong> If the time to fix a failed system increases, then availability decreases.</strong></mark>

Things to consider for manageability are the ease of diagnosing and understanding problems when they occur, ease of making updates or modifications, and how simple the system is to operate (i.e., does it routinely operate without failure or exceptions?).

Early detection of faults can decrease or avoid system downtime.

[^fn-sample_footnote]: we'll mainly focus on large systems, but will have a proper comparison between both (small vs large) and bring attention to things that need to be considered.
