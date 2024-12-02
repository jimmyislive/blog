---
title: "Vector DBs for Data Protection"
date: 2024-11-28
draft: false
tags: ["tech"]
---
I was reading up some information on vector embeddings recently and started thinking about different use cases of it. One interesting use case was for Data Detection and Response. The thought exercise I had was that if we are able to track data access patters or [data lineage](https://en.wikipedia.org/wiki/Data_lineage) and represent them as embeddings, then the embeddings of any anomalous / nefarious access pattern would have very little similarity with other embeddings.

Lets say our files have attributes like: `channel` and `owner`
Let's say some valid actions on files could be: `copy`, `rename`, `zip` etc

We could represent a legitimate file copy via a json e.g.

`Copying file on desktop from one dir to another`

{{< highlight json "linenos=table" >}}

{
	"source": {
		"channel": "desktop", 
		"owner": "corporate"
	}
	"action": "copy",
	"dest": {
		"channel": "desktop", 
		"owner": "corporate"	
	}
}

{{< / highlight >}}

Here are a few more legitimate scenarios captured in json format:

`Download a file from salesforce and upload to a corporate drive`

{{< highlight json "linenos=table" >}}

{
	"source": {
		"channel": "salesforce", 
		"owner": "corporate"
	}
	"action": "upload",
	"dest": {
		"channel": "gdrive", 
		"owner": "corporate"	
	}
}

{{< / highlight >}}

`Download a file from personal email and upload to personal drive`

{{< highlight json "linenos=table" >}}
{
	"source": {
		"channel": "email", 
		"owner": "personal"
	}
	"action": "upload",
	"dest": {
		"channel": "gdrive", 
		"owner": "personal"	
	}
}
{{< / highlight >}}

You get the idea...

The next step would be to convert the above into vector embeddings and store it in a vector DB like [Pinecone](https://www.pinecone.io) or something similar.

Then, should a suspicious action happen, we could create an embedding for it and search via cosine similarity in a vector DB if it is an outlier. If so, alert on it. e.g.

`Download a file from salesforce, rename it then zip it and upload to a personal drive`

{{< highlight json "linenos=table" >}}
{
	"source": {
		"channel": "salesforce", 
		"owner": "corporate"
	}
	"action": ["rename", "zip"],
	"dest": {
		"channel": "gdrive", 
		"owner": "personal"	
	}
}
{{< / highlight >}}

I see products such as [Cyberhaven](https://www.cyberhaven.com) doing similar things but, as far as I can tell, they seem to be using graph DBs. Curious if there are any products out there that do something similar via embeddings/vector DBs.
