---
title: "Does code matter anymore ?"
date: 2026-09-14
draft: false
tags: ["musings", "tech", "human-created"]
---

Code has always been considered as Craft. You could tell "well crafted" code from the alternative just by looking at it. It required time, patience, an opinionated outlook and effort, amongst other things. Developers took pride in their code. Just as an artisan would take pride in theirs. The tools and ecosystem around the craft of coding has evolved and improved making it easier for us to comprehend and maintain larger and more complex codebases. 

And for good reason too. The primary consumers of code were other humans. The vast majority of the cost of software is in its maintenance, not its first authorship. So everyone had a vested interest in having good design docs, coding conventions, design patterns, documentation etc We were comfortable making those upfront investments so that the future toil is minimal.

AI is changing that. (especially for greenfield code. A lot of concerns I had in [Challenges in an AI First world](https://jimmyislive.dev/posts/ai-first-challenges/) still apply for legacy systems)

When code, conventions, documentation etc is automated to a large extent a lot is gained in that process. The primary consumers of this code are now no longer humans. It's the next coding agent / model that is consuming this code. What we now care about is velocity and correctness. So we are now spending time writing evals / tests to make sure that what AI is generating solves the actual problem.

Let's consider a design pattern like DRY (don't repeat yourself). Yes, its a good idea not to repeat yourself and if there is one function performing a task, put it in one place and call it from wherever you want. If you need a slight variation of that function, parametrize it so that now all related functionality is in one place. It's very useful to isolate functionality like this and test it. For humans.

An agent might decide that it is not that useful and just write a new function for it (justifying it by thinking perhaps it is different functionality). 

But does this matter now ? Do/should we care ?

If we are only concerned about the result and solving customer problems, such implementation details should not matter. Yes it's more code, but a machine is consuming all of this. They can just as easily write a whole bunch of unit tests for the new function as well. And when a bug is found, an agent is fixing it as well. At astonishing speed.

When the cost of creation trends towards zero, our efforts should gravitate towards ensuring that what is created is what is needed.

Does the code prompted by your favourite LLM matter ? We should provide guidelines, guardrails etc to guide the LLM, but everything after that is created by a machine. For consumption by other machines. 

We are nearing the point that the vast majority of our time as engineers will now be spent on :

- Deciding what to build
- Ensuring what is built adheres to quality and customer goals

And part of the reason for this is that the nature of startup "moats" have changed. Code is no longer a moat. Speed, distribution and scale are the new moats. While an agent is not a replacement for a specialized skill (you still need someone to interpret results and know how and where to apply them), it nullifies a lot of the upper hand that larger corporations had in the past as only they could afford the smartest programmers.

We are entering a new phase wherein the code per see is secondary. Correctness, speed and judgement are defining the new winners, irrespective of how uncomfortable that makes us.
