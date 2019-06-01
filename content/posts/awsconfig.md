---
title: "AWS Config"
date: 2019-05-31T13:00:00
draft: true
tags: ["aws", "config", "cloud"]
---
[AWS Config](https://docs.aws.amazon.com/config/latest/developerguide/WhatIsConfig.html) is a service provided by AWS to keep track of configurations (and their possible drift) of all the resources you bring up in AWS. When your infrastructure setup is small, you probably don't have to be worried about a configuration store for your resources. But as you grow, it would become increasingly difficult to manage and keep track of all the moving pieces, especially if you have a team of engineers continually deploying and changing things. Other benefits of such tracking include auditing, security and compliance and general mental health and well being.

However, when reading their documenation, I found some bits confusing. So I drew out a diagram of what I wish they had to help folks understand it better.

{{< figure src="/images/aws_config.jpg">}}

1. [Resources](https://docs.aws.amazon.com/config/latest/developerguide/resource-config-reference.html)
    Resources are the things whose configurations you want to keep track of. A resource is an aws entity you create via the console, cli or the API. e.g. EC2 instance, EBS etc. Resources have attributes and relationships to other resources. When you talk about tracking configurations, it's these characteristics of a resource that you are tracking.

    The first thing you decide when using AWSConfig is the resources you want to monitor. You could choose to track all, and have aws discover all the resources you are currently using, or choose a specific subset of resources you are interested in.

2. [Configuration Recorder](https://docs.aws.amazon.com/config/latest/developerguide/stop-start-recorder.html)
    A configuration recorder is what records the configuration of resources of interest. You can have only one configuration recorder per region / per account. When you start a configuration recorder, it starts recording the resource characteristics (encapsulated in something called a "configuration item").

    *NOTE:* You are charged usage fees when the configuration recorder starts, on a per configuration item recorded basis.

3. [Delivery Channel](https://docs.aws.amazon.com/config/latest/developerguide/manage-delivery-channel.html)
    A delivery channel acts as a proxy to where the configuration recorder can send the recorded configuration items and configuration history (a collection of configuration items) to. Loose coupling and all. You can have only one delivery channel per region / per account. In the diagram above, the delivery channel is associated with an S3 bucket (where configuration items/history/snapshots are stored) and a SNS topic for alerts of changes.

4. [Config Rules](https://docs.aws.amazon.com/config/latest/developerguide/evaluate-config.html)
    Rules are conditions that are evaluated against configuration items to get a true /false decision. If fired, notifications are sent to the SNS topic with details. There are a wide variety of canned rules you can choose from or even write your own custom rules. e.g. you could have a rule stating that volumes always be encrypted. If an unencrypted volume appears, the rule is in violation. Rules can be triggered either on a schedule or whenever a resource is created, updated or deleted.

    *NOTE:* If you want to write custom rules, you have to put the rule evaluation logic in a lambda function and associate it with the rule.

5. S3 Bucket
    This is where AWSConfig will store the configuration items, history and snapshots of resources. This bucket is specified when configuring the delivery channel, so it knows where to store the configurations. You could also do things like consolidate the configs from different accounts / regions into one bucket for easier management. See [this](https://docs.aws.amazon.com/config/latest/developerguide/aggregate-data.html) for more details.

    *NOTE:* You need to set up the appropriate policy for the bucket so that AWSConfig is allowed to write to it.

6. SNS Topic
    This is a topic on which alerts can be sent out. Whenever a config rule is in violation, details about it are sent out on this topic. You can see many examples of such notifications [here](https://docs.aws.amazon.com/config/latest/developerguide/notifications-for-AWS-Config.html).


