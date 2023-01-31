---
title: "Trap in bash scripts"
date: 2018-05-05T21:54:45-08:00
draft: false
tags: ["tech"]
---

You may be familiar with the “set -e” you typically see in bash scripts. It means that the script should exit on error (default behaviour of bash is to keep going)

Recently I needed a script to do some cleanup action. i.e. I wanted the script to exit on error, but I also wanted some cleanup action to be performed. A use case might be that if something fails, then before exiting, ship log files to an S3 bucket for troubleshooting. 

I used [trap](http://tldp.org/LDP/Bash-Beginners-Guide/html/sect_12_02.html) for this. It essentially traps signals of interest and then performs cleanup actions specified by the user before exiting. Here is an example:

{{< highlight bash >}}
#!/usr/bin/env bash
set -e

function finish {
   aws s3 sync /tmp/logs/ s3://some-bucket-name
}
trap finish EXIT

script1 arg1 &> /tmp/logs/script1.log
script2 arg2 &> /tmp/logs/script2.log
script3 arg3 &> /tmp/logs/script3.log
{{< /highlight >}}

By using trap, even if any of the script1/script2/script3 fails, the logs will be shipped to S3 so you can troubleshoot it.
