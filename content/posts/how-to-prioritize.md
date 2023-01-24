---
title: "How To Prioritize Tasks ?"
date: 2023-01-22
draft: false
tags: ["prioritization"]
---
Shipping products is hard. What makes it hard is that typical products involve multiple teams and multiple dependencies. Navigating these challenges is non-trivial. There are technical challenges to overcome, but those are typically not the biggest blockers. (There are enough smart people in the room to take care of that, unless its some greenfield domain like AR/VR/Self-Driving etc), Then there are the procedural challenges; what should be done when and by whom. This is where most organizations run into their biggest pain points as there is no set process to follow to fix it. It necessarily involves people and each organization needs to come up with its own flavor of processes that work.

One of the first challenge of any process is prioritization. How do you prioritize your tasks given limited resources, scarce time and conflicting asks. Most try and make intelligent guesses using their "intution". It's a perfectly valid way to do things. Problem is that intution changes over time and people. There should be a better, more standardized way to determine prioritization so everyone can agree on them.

Here are some ideas to help you with task prioritization:

1. What benefit does doing the task have on your customer / product ? (A positive score)
2. What impact does not doing the task have on your customer / product ? (A negative score)
3. What is the effort involved in doing the task ?


## What benefit does doing the task have on your customer / product ?
You should arrive at a score that quantifies the benefits of prioritizing this task. Is it a highly requested feature ? Is it a competitive advantage ? Does it reduce cost ? Does it reduce the time to delivery ?

No doubt, quantifying the benefit is hard (Document assumptions you need to make in order do it)

Use a scoring method like this to arrive at a score:
```
Immediate Benefit  : x
Short Term Benefit : y
Long Term Benefit  : z
```

If there is immediate benefit, assign a high positive score. And if only in the long term, assign a lower score, but positive.

## What impact does not doing the task have on your customer / product ?
You should arrive at a score that quantifies the impact of not prioritizing this task. Will it result in customer churn ? Will it increase toil ? Will it effect productivity negatively ?

Use a scoring method like this to arrive at a score:

```
Immediate Impact  : -a
Short Term Impact : -b
Long Term Impact  : -c
```

If there is immediate impact, assign a low negative score. And if only in the long term, assign a higher negative score.

## What is the effort involved in doing the task ?
Resources are always limited. So ballparking effort is a critical aspect of deciding which task to prioritize. You can use the following broad buckets to characterize effort:

```
High    p
Medium  q
Low     r
```

## Tying them all together

Once you have a quantitative score attached to each item above, add them up. 

`(x|y|z) - (a|b|c) + (p|q|r)`

The tasks whose score is closer to 0, should be prioritized higher and hence done now. Or choose a band: tasks that fall in the +ve X to -ve X band will be done now and the tasks that fall outside that band fall into a Backlog bucket for review later. (The next sprint / quarter, pick out items from this Backlog bucket and run through a similar exercise to prioritize.)

The above gives a standardized process and acts as a forcing function to talk about task priorities and value generated.

Remember though, prioritizing tasks needs to be complemented with a long term roadmap / vision. 




