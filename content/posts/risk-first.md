---
title: "Risk First Assessment"
date: 2020-05-21
draft: false
tags: ["process", "risk"]
---
We are all familiar with the usual way how software project deliverables work: requirements, design, development, testing, deployment, monitoring...

However, how many times do we do a risk assessment of our projects? We always seem to focus on the steady state where most of the things work. We don't spend as much time in thinking about failure scenarios / risks to our project.

I differentiate this from say a threat analysis / matrix. A threat matrix is one where an adversary is trying to take control of your system or gain unauthorized access into it. Hence threat matrices are focused on minimizing attack surface and such controls.

A Risk First Assessment assumes no malicious intent. It's just a comprehensive look at the end-to-end project to identify things that may go wrong (their severity and possible mitigations). A risk could be something technical (server unable to keep up with load) or a dependency (are you dependent on a third party or team to deliver something) or resourcing (are your resources multitasking on several projects).

Obviously, you can go overboard with such an assessment. What if there is a natural calamity in the data center? What if the business landscape changes etc. The key is to restrict the risk assessment to a micro level. Focus on just the project of interest (making reasonable assumptions about the rest). Think of it as layers of an onion. Each subsequent layer will concern itself with a broader macro picture, say at the business or company or geo level.

Coming back to our software project. If every design doc is followed by a comprehensive risk assessment, I think it will uncover many weaknesses earlier and will allow us to mitigate against it. The goal is to not only think about "why this design/process/deliverable will work" but to actively think about and minimize reasons as to "why the design/process/deliverable may not work". A forcing function of sorts that lets us view things through a different lens.
