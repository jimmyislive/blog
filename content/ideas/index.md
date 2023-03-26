---
title: Ideas
description: ideas
date: 2022-12-15T20:54:45-08:00
publishdate: 
lastmod: 
categories: []
keywords: [tech ideas]
draft: false
---
Here are a list of ideas that I think would be great to build out as standalone products. (NOTE: I have not researched if they already exist in the wild or not). At different points in time I have needed something like this, so there is definitely a need. If any entrepreneur wants to adopt these ideas and run with it, go for it ! (If you are a VC and want to fund it, ping me :) )

* **Visualizing System Operations**

    As products we build become more and more complex, it becomes hard to explain all the different components and their dependencies. Failure modes becomes difficult to understand. And since systems just grow organically, documentation is woefully out of sync. 
    But what if, the system visualization was part of code, "**architecture as code**" so as to speak. So lets say that you are building a component. It has some inputs, outputs and dependencies. Lets document this in a yaml file, along with the code. This yaml is then used to populate say a graph db as vertices, dependency links, hierarchies etc. Throw a visualization library over it and you have a real time view of your system architecture. Operational metrics can then be tied into this easily. e.g. if your input is from kafka and there is a large lag on that topic in kafka, display this component as red and the downstream dependencies as orange. The on-call person then has a quick way of diagnozing the problem thereby reducing MTTR. There is a whole range of additional features you can build onto this e.g. tie this to spinnaker for latest updates on deploys, use it for quick onboarding of new hires, use it for capacity planning etc etc etc

 
* **Product Sandbox**

    How many times have you had to decide which third party product to purchase and had to spend multiple man weeks on evaluating them ? The big players seem to drown out the smaller ones (even though they might have better capabilities but smaller marketting budgets). What if there was an easy way for vendors to compete and you to grade them yourself ? A Product Sandbox is what you need ! Lets use a real life example, web page analytics. There are a whole bunch of vendors. What if we create a static page and add all those vendors to that page in order to visualize the metrics they provide. In this product sandbox then, you can compare the capabilities of each of these vendors (maybe something as simple as iframing that vendors product in). You can now compare the analytics provided by multiple vendors of the same asset and make a decision based on merits rather than hype. New entrats will jump on this offer as founders always love to compete on the merits of their product. End users benefit by saving time not having to set up n different environments to evaluate these vendors.

* **TIL (Today I Learnt)**

    We keep learning new stuff. Almost on a daily basis. I do (or at least I try to). This comes from tech articles, blogs, newsletters, podcasts etc. If there were a reddit / twitter style site wherein people just posted things they learnt about it would become a repository of very useful info. Quick snippets of facts / learnings (along with sources) that can be voted on. Not news or social media. An oasis of learnings where users can come to satisfy their curiosity. The true fountain of youth is, after all, knowledge.
