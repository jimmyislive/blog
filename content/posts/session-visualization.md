---
title: "Session Visualization with Obsidian"
date: 2026-08-15
draft: false
tags: ["tech", "human-created"]
---
When working with coding agents such as Claude, Codex etc. we provide the prompts and context until we get to the final result. However a lot happens behind the scenes. The model constantly makes calls to tools, MCP servers, commands etc until it arrives at the output. By knowing what path it took, we can begin to understand its reasoning better.

One way I have found useful is to use pre/post hooks to log these messages into files and then use [Obsidian](https://obsidian.md) to visualize them.

Obsidian is a note taking app which stores notes as files on your local disk. You can link notes together and then visualize them as a graph.

In order to demonstrate this, I used claude to create a sample todo app. I had claude generate a [script](https://gist.github.com/jimmyislive/883238a86c4f8ffe5603c7e3e8d1e903) which would log events for me into a folder of my choice (In Obsidian lingo, this folder is called a "[vault](https://obsidian.md/help/vault)"). 

Before you start prompting Claude, you will have to update its `settings.json` file to call this script in the pre/post hooks:

{{< highlight json "linenos=table" >}}
"hooks": {
        "UserPromptSubmit": [{ "hooks": [{ "type": "command", "command": "python3 ~/.claude/obsidian-tracker/log_event.py prompt" }] }], 
        "PreToolUse": [{ "matcher": "mcp__.*", "hooks": [{ "type": "command", "command": "python3 ~/.claude/obsidian-tracker/log_event.py tool_call" }] }],
        "PostToolUse": [{ "matcher": "mcp__.*", "hooks": [{ "type": "command", "command": "python3 ~/.claude/obsidian-tracker/log_event.py result" }] }]
}
{{< / highlight >}}

Once this is ready, just prompt claude as you would. It will log all the events into the folder configured in your script. The events are logged into numbered files representing the thought process of the model. 

{{< figure src="/images/ai-session-obsidian.png" alt="AI Session visualization">}}

You can now see the visual representation of all the steps as to how the model arrived at the final result. You can also go sequentially through the files to understand them and see how the graph builds up.

And here is the final todo app:

{{< figure src="/images/todo-app.png" alt="AI Session visualization">}}

There may be other uses of keeping your session history around, but at least now you have them in an easy visualizable format !
