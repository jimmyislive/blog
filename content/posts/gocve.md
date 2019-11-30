---
title: "GoCVE"
date: 2019-10-21
draft: true
tags: ["golang", "cybersecurity", "cve"]
---
Just released a new tool I have been working on: [GoCVE](https://github.com/jimmyislive/gocve)

GoCVE is a simple golang based command line tool to query [CVE](https://cve.mitre.org/) data. 

All you have to do is download the CVE data and insert it into a local database you run (currently sqlite and postgres are supported). The tool helps you to download CVE data and also populate the DB. You can then use GoCVE to conveniently get, search or list CVE data.

## Example
To test this, I set up an ubuntu EC2 instance and a RDS instance on AWS.



For more details of usage, see [github](https://github.com/jimmyislive/gocve).

Enjoy !

