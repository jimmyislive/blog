---
title: "Challenges in an AI First world"
date: 2026-05-17
draft: false
tags: ["musings", "tech", "human-created"]
---
There is an AI race going on. No one knows where or what the finish line is, but it's a race nevertheless. Run or be left out. So everyone is running. Developers, designers. product managers, companies, countries. 

To be clear, there was always a race. A race to get products out first, to get features in time etc. But there were guardrails. There were rules. Now, they seem to be absent/de-prioritized. "Move fast and break things" - then fix them even faster with AI !

This pace introduces several challenges. Understanding some of them will help us to recognize them so we are not caught off guard.

## Too much, too quickly
The cost of implementation is now almost zero. There is no doubt that Claude, Codex etc are much better coders than us (for most parts). So why not, keep building ! Mandates to 2x or 3x developer velocity seem so reasonable.

Turns out the underlying infrastructure and the current model of development has not caught up. Things will move only as fast as the slowest moving part. i.e. the "human" in that AI loop. We have 2xed our PRs, but it's the same number of devs who need to approve those PRs. Your CI/CD team did not get the memo of an order of magnitude more PRs and it kept buckling under the new "agentic" workload. SREs were also caught unaware of the onslaught of new logs they have to deal with. 

Our end-to-end pipelines are very interdependent with multiple humans in multiple loops. Automating all of them takes time. There is no playbook for all this. It's by hit and trial. 

It's almost as if we took away peoples' trusted Honda Accords and handed them Ferraris. Then we promptly disabled all traffic lights !

## Maintenance Nightmare
Armed with Claude / Codex, we all now feel like The Terminator. Everyone is a creator. That's what is encouraged as well. (Not saying it's a bad thing).

But the flip side is that now we have a dizzying array of vibe coded things. Imagine if we decided to create physical things with duct tape and called it "production grade". It "works". But every developer knows that the vast majority of cost is in maintaining apps, not creating them. Those bugs fixes, that production install, the operational overhead. Oh..we've got AI for that too ! Before you know it, your basic CRUD app is now 10K lines plus. Code has been duplicated, formated, reformated, rewritten from scratch...and the problem is now compounded if you have actual paying customers.

Conding is now no longer in Go, Python, Java, Rust etc. It's English. So type away. At least in coding, testing is kinda formalized. Unit tests, integration tests etc. But in skills, vibe coded apps ? In their rush to get it out, did they write tests ? Oh, they should have proper "evals" for all skills. Take a poll and see how many know about evals ? But even then...evals, testing etc is a discipline thing that you learn to do. Many first timers with these sudden creation superpowers don't slow down to write them.

The usual response is that "they are doing it wrong". They should provide proper context, invest in prompt engineering etc etc. All true.

But the problem still remains. Many will still jump head first into it without investing the time needed for it to be less dangerous. It's a race after all. And you have a Ferrari !

## Integration Challenges
There is an existing ecosystem in which your product lives. Auth is done a particular way, data lives in a DB with strict access controls etc Your vibe coded avatar needs to respect those. And that's where a lot of these vibe coded apps live..as demoware. As integration is a pain. You may not be able to write to that production DB as that could be an attack vector, so it is denied. Your dashboard needs to have RBAC controls as not everyone is allowed to see those dashboard you made. 

The bar is (and should be) high for production apps. Dealing with integrations is messy, even when AI does it, as there are a lot of discussions, tradeoffs and justification discussions involved. Many never make it past these.

## Information Overload
How many dashboards can you realistically consume in a day ? There is a new dashboard to see almost every day. New skills are created at incredible rates, never to be used again (maybe only the author uses them). 

There was a survey sent recently. It took me around a minute to fill. Meanwhile, someone made a skill to fill out that survey so they could just answer it in a natural language ! Will this skill ever be used again ? Should I use this skill next time and perhaps save 30s ? What would I do with those 30s ? 

I always assumed a fundamental tenet of AI was "Delegation"..what task is AI worthy and what isin't...but I diagress...

Agents are working round the clock. Kick off agents at night and come back and see what they did. Detailed reports are generated of what happened and why. OK, now let's make another agent to summarize those agent created reports so we can consume it. 

There seems to be an avalanche of new "insights" that we have to consume and "act" on. At some point this will become white noise and we will miss something important.

## Ownership
I'm not talking about ownership from a legal standpoint, but from a personal integrity standpoint. Do you take ownership of the things you generate (maybe AI did most of the work...but it's still yours now) 

I was watching a presentation recently where most of the slides were very obviously AI generated. The author prefaced the talk by saying all the slides are generated by AI. Not sure why, but it caused me to disengage and I did not pay attention to the remainder of the talk. 

AI is not an owner. AI is a skilled doer. You are still very much the owner.

Ownership is about taking responsibility. It's about accountability. It's about standing behind your product as a stamp of confidence and approval. AI needs you for that. Not the other way around.

## Liability
Every business needs some kind of liability insurance. I'm not an expert on it, but I know it exists (there are perhaps different flavors of it one needs to operate). What happens when you go out to your product launch and announce that this new feature / app has been 100% vibe coded ? Would insurers start asking you to purchase a higher insurance ? For bigger / established companies this may not be that big of a deal. But if these start spiking for startups ?

Are there established protocols, audit trails to prove to an authority that every PR was human reviewed (before this there was not such a big need for this). What if undersigners insist that you have to use a particular LLM (owing to its quality) or for you to disclose what kind of AI was used in its development. 

What if your consumers start demanding this ? Seems far fetched ? Waymo was founded in 2009. It has logged > 200M autonomous driving miles. It's 100% safer than human drivers at this point. But every time an incident occurs with it. It's front page news. Even today, after 17 years, it operates only in 5 to 6 cities. 

We are one incident away from AI development practices to be scrutinized more. I'm not sure we have thought about this as much as we should.

## Cost / ROI
AI is expensive. As all these CEOs doing layoffs where they sugarcoat their message into something totally tangential. Someone needs to pay these costs. Might as well be employees.

We are at that stage now, where we are still "figuring it out". We are all exploring AI to gain those benefits which will surely come..in the future. Seems like the "growth at all costs" mantra of the last few decades.

After all, we have 2xed our velocity and churned out so many new features. But has that resulted in your customers adopting all these new features ? Have they bought those 10 new skews you invented ? 

Measuring ROI in all this is notoriously difficult. So we just avoid it. 

You made a skill that saved you 1 hour every week. Awesome. Well, what did you do with that saved hour ? Did you spend it on something else useful ? If so, then great. If not, you are just tokenmaxing / flexmaxing.

Identifying ROI metrics is a challenging task. For coding, is it the number of PRs ? number of lines of code ? Number of features ? For a team, is it the number of story points ? bugs ? For every +1 argument for a metric there is a counterargument. How about customer / business outcomes ? 

Fair to say, these efforts are complicated as it involves so many variables. Most do not invest the time or energy to evaluate themselves.

## AI Hype
You can't open a site without some AI news on it. [this post is also about AI :)] LinkedIn has become an AI shithole. Doom, gloom, how everyone is using AI in such absolutely awesome ways. 

No one at Antropic codes anymore apparently. 75% of code at Google is AI generated. Things are going so swell at Block, AI just made half their company redundant !

When you live in such an environment, it is but natural to look inward and ask "What's wrong with me/us ?" 

But you are not Google. Google is not Anthropic. Anthropic is not OpenAI. Every company / product is in its own lifecycle and these comparison games are just silly. 

It's almost as if AI has become a checkbox to appease someone. 

AI is a truly powerful and transformational tool. Understanding what you can do with it takes time and patience. Software is still very much a people centered business. We will get more productive and may not need as many, but we need to find that out for ourselves. Ignore the hype.

---

*I'm still very bullish on AI. When we finally reach equilibrium, I truly believe AI will be a net positive. We just need to invent our techniques, processes, ecosystems and ourselves to work in an AI First world. And like everything of value, that takes time.*
