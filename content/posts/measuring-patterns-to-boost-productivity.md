---
title: "Measuring Patterns To Boost Productivity"
date: 2024-02-18
draft: false
tags: ["management"]
---
Today the most common way of getting feedback is via peer reviews. Some companies do it annually, some bi-annually. This helps employers to reward the most promising employee through promotions, salary raises etc.

One disadvantage of the above format is that the feedback is essentially an evaluation by peers of past performance. So it's already too late to possibly course correct. The next time around the peer review could be done by someone else, with different internal yardsticks of evaluation.

Typically, 1:1s with your manager is supposed to be a place to get continuous feedback. If there is something obviously missing in terms of performance etc, hopefully the manager is flagging it so that you can course correct in a meaningful way.

But how can your manager know that something is missing or you are performing above/below certain performance thresholds early enough ? Are there any proxy metrics available that can indicate analytically where an employee performance falls compared to the overall team performance. In other words can we use individual metrics which act as proxies for performance that can give both manager and employee a useful gauge ? Are there patterns that can be measured to boost productivity ?

Maybe there is...

Lets say your company uses google docs for PRDs etc. A good proxy engagement metric would be things like author and number of comments / responses by various members of the team. We could write a simple program to extract this data over a quarter and plot this on a per team member basis. The idea is not to pressurize folks to always be the top commenter. The idea is to flag outliers e.g. someone who hardly comments on any docs. Maybe they comment only on PRDs they are involved in. Such a metric could encourage them to get involved in general team discussions.

We could extrapolate this concept to many other proxy metrics like number of git commits, number of PR review comments etc. Even a measure of how active you are on internal slack channels could be a good proxy for productivity. (Highly productive folks tend to answer questions on slack proactively) 

The important thing here is that whatever metrics you choose to measure this way, should be agreed to by the team. It's not some HR or company exec deciding these. Which metrics to use should be debated and agreed to on a team by team basis and perhaps the most common ones elevated to an org level.

**Can these be gamed ?** unlikely. Since these proxy metrics are agreed to by the team members, attempts to game them can be easily seen. e.g. comments like "first", "second" would be all too apparent ! 

**What's the point of this ?** The purpose of such a scheme is not to force people into gaming metrics or do needless work. (A refresher on [Goodhart's law](https://en.wikipedia.org/wiki/Goodhart%27s_law) may be useful here.) The goal is to identify outliers and using this info employees can either self course correct or provide managers with pointers as to which direction to nudge them towards.

The following are some hypothetical examples and what one could learn from them.

{{< figure src="/images/prd_comments_by_employee.png">}}

In the above graph, everyone on the team is within acceptable thresholds on what the team thinks is an acceptable number of feedback comments by team members over a month.


{{< figure src="/images/pr_approval_by_employee.png">}}

In the above graph, most team members approve a reasonable number of PRs, except one. It would be a good conversation to have with them to encourage them to set aside time to review PRs.

{{< figure src="/images/git_commits_by_employee.png">}}

In the above graph, everyone on the team is contributing meaningfully to the codebase in terms of their commits. (Maybe it's time to teach emp5 how they can squash their commits though !)


By keeping a running dashboard of such proxy metrics, it can give the team a convenient way to track and improve productivity. 

Managing teams is hard. Giving and taking feedback is hard. But it's necessary to maintain high levels of productivity else teams / products just wither away. How you use a tool is just as important as the tool itself. Hopefully the approaches described here can give you a practical, unbiased and fair framework to build from.

PS: In case anyone is interested, here is a great book that suggests similar techniques to bring healthcare costs down: [The Price We Pay by Marty Makary](https://www.amazon.com/Price-We-Pay-American-Care/dp/1635574110)