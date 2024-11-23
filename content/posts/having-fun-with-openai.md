---
title: "Having Fun with OpenAI"
date: 2024-11-23
draft: false
tags: ["tech"]
---
AI is all the rage these days. New capabilities, that seemed impossible just a few months ago, show up almost weekly. So I decided to take it for a spin and see what different tasks I could code up quickly.

I chose [OpenAI](https://openai.com), but you should be able to replicate these using any of the other models as well.

**Printing the text of a html page**

The first quick task I tried was to ask AI to print out the text of a html page i.e. without tags or images etc.

I chose a [post](https://jimmyislive.dev/posts/tailscale/) I had written a while back which had images.

First, get the content of that page which contains all the html tags:

```python
def download_page(url: str) -> str:
    response = requests.get(url)
    html_content = response.content

    html_content = str(html_content).replace('"', '\\"').replace("'", "\\'")

    return html_content
```

Don't forget to escape the text as bove.

Once I got the text, all I had to do was send it to openAI with the aprpopriate prompt.

```python
client = OpenAI()
completion = client.chat.completions.create(
    model="gpt-4o-mini",
    messages=[
        {"role": "system", "content": "You will be provided with html content. Remove tags and output only the text. Ignore any images or audio. Formatting the output text in paragraphs."},
        {
            "role": "user",
            "content": "<!DOCTYPE html>\n<html lang=\"en\">\n    <head>\n\t\t<meta charset=\"UTF-8\">\n\t\t<meta name=\"viewport\" content=\"width=device-width, initial-scale=1.0\">\n\t\n\t\t<title>\n\t\t\t\tSetting up a private network with Tailscale &middot; @jimmyislive\n\t\t</title>\n\t\n\t\t\n  \t\t<link rel=\"stylesheet\" href=\"/css/style.css\">\n\t\t<link rel=\"stylesheet\" href=\"https://fonts.googleapis.com/css?family=Libre+Baskerville:400,400i,700\">\n\t\t<link rel=\"stylesheet\" href=\"https://use.fontawesome.com/releases/v5.7.2/css/all.css\" integrity=\"sha384-fnmOCqbTlWIlj8LyTjo7mOUStjsKC4pOpQbqyi7RrhN7udi9RwhKkMHpvLbHG9Sr\" crossorigin=\"anonymous\">\n\n\t\t\n\t\t<link rel=\"icon\" type=\"image/png\" sizes=\"64x64\" href=\"/images/favicon-64x64.png\">\n\t\t<link rel=\"apple-touch-icon\" sizes=\"180x180\" href=\"/images/apple-touch-icon.png\">\n\t\n\t\t\n\t\t<link href=\"\" rel=\"alternate\" type=\"application/rss+xml\" title=\"@jimmyislive\" />\n\n\n                <script async src=\"https://www.googletagmanager.com/gtag/js?id=G-SDJ0D81L5C\"></script>\n\n                <script>\n                  window.dataLayer = window.dataLayer || [];\n                  function gtag(){dataLayer.push(arguments);}\n                  gtag(\\'js\\', new Date());\n\n                  gtag(\\'config\\', \\'G-SDJ0D81L5C\\');\n                </script>\n\n\t</head>\n\t\n\n    <body>\n        \t\t<nav class=\"nav\">\n\t\t\t<div class=\"nav-container\">\n\t\t\t\t<a href=\"/\">\n\t\t\t\t\t<h2 class=\"nav-title\">@jimmyislive</h2>\n\t\t\t\t</a>\n\t\t\t\t<ul>\n    <li><a href=\"/\">Posts</a></li>\n    <li><a href=\"/books\">Books</a></li>\n    \n    <li><a href=\"/projects\">Projects</a></li>\n    <li><a href=\"/about\">About</a></li>\n</ul>\n\n\t\t\t</div>\n\t\t</nav>\n\n        \n\n<main>\n\t<div class=\"post\">\n\t\t<div class=\"post-info\">\n    <span>Written by</span>\n        Jimmy John\n        <br>\n        <span>on&nbsp;</span><time datetime=\"2022-12-29 17:00:00 -0800 -0800\">December 29, 2022</time>\n</div>\n\t\t<h1 class=\"post-title\">Setting up a private network with Tailscale</h1>\n<div class=\"post-line\"></div>\n<div class=\"post-info\">\n    \n    \n    <a href=/tags/tech>#tech&nbsp;</a>\n    \n</div>\n\n\n\t\t\n\n\t\t<p>I came across <a href=\"https://tailscale.com/\" target=\"_blank\">Tailscale</a> recently. It&rsquo;s a tool that lets you build a private mesh network between your devices easily.</p>\n\n<p>My use case was to transfer data from my laptop(macbook) to my android phone. Many times I have a pdf doc that I have on my laptop that I wish to read on the go. I usually just email it to myself and download it to my phone, but wanted to see if I can use Tailscale for it.</p>\n\n<p>To start off, first install tailscale on both your android device and macbook. (Installation instructions are on the tailscale website)</p>\n\n<p>As soon as tailscale is installed on android, it gets assigned a unique 100.x.y.z IP (by a centralized coordination server at login.tailscale.com) and is called a <em>node</em>.</p>\n\n<p>You can actually test that this IP works:</p>\n\n<p><code>ping 100.x.y.z -t 4</code></p>\n\n<p>Now install tailscale on your macbook. If you open the admin console you will see a second IP appear i.e. both <em>nodes</em> are now on a private network.</p>\n\n<figure>\n    <img src=\"/images/admin_console_tailscale.jpg\"/> \n</figure>\n\n\n<p>I then created a dummy file called <code>hello_tailscale.txt</code> on the macbook. You can use <a href=\"https://tailscale.com/kb/1106/taildrop/\" target=\"_blank\">Taildrop</a> to copy that file between your devices. Just right click &gt; share. Boom ! The file is now on my android device !</p>\n\n<figure>\n    <img src=\"/images/hello_tailscale.jpg\" height=\"500\"/> \n</figure>\n\n\n<p>Under the hood, after authentication, when each device is added to the tail network, the auto generated public key is added to the tailscale co-ordination server. This public key is used to identify the device i.e node on the private network. After that, data is directly transferred peer-to-peer between nodes using the auto generated private key. (Note that the private key never leaves the node and data transfer happens peer-to-peer). This is also called zero trust networking. The control pane i.e. coordination server is used only to store public keys of the nodes, domains and some other metadata which is downloaded by all the nodes.</p>\n\n<p>There is a lot more you can do with Tailscale. Head over to the <a href=\"https://tailscale.com/kb/\" target=\"_blank\">docs</a> if you are curious.</p>\n\n\n\t\t\n\t</div>\n\n\t<div class=\"pagination\">\n\t\t<a href=\"/posts/noone-knows-anything/\" class=\"left arrow\">&#8592;</a>\n\t\t<a href=\"/posts/diswasher-test/\" class=\"right arrow\">&#8594;</a>\n\n\t\t<a href=\"#\" class=\"top\">Top</a>\n\t</div>\n</main>\n\n\n        \t\t<footer>\n\t\t\t<div>\n\t\t\t\t<a href=\"https://twitter.com/jimmyislive\" target=\"_blank\">\n\t\t\t\t\t<i class=\"fab fa-twitter\"/></i>\n\t\t\t\t</a>&nbsp;\n\t\t\t\t<a href=\"mailto:jimmyislive@gmail.com\">\n\t\t\t\t\t<i class=\"fas fa-envelope\"/></i>\n\t\t\t\t</a>&nbsp;\n\t\t\t\t<a href=\"https://github.com/jimmyislive\" target=\"_blank\">\n\t\t\t\t\t<i class=\"fab fa-github\"/></i>\n\t\t\t\t</a>&nbsp;\n\t\t\t\t<a href=\"https://www.linkedin.com/in/jimmyislive/\" target=\"_blank\">\n\t\t\t\t\t<i class=\"fab fa-linkedin\"/></i>\n\t\t\t\t</a>&nbsp;\n\t\t\t\t<a href=\"/index.xml\" target=\"_blank\">\n\t\t\t\t\t<i class=\"fas fa-rss\"></i>\n\t\t\t\t</a>\n\t\t\t\t\n\t\t\t</div>\n\t\t\t<span>\n\t\t\t&copy; <time datetime=\"2024-11-16 14:34:32.209577043 &#43;0000 UTC m=&#43;0.054812465\">2024</time> . Made with <a href=\\'https://gohugo.io\\'>Hugo</a> using the <a href=\\'https://github.com/EmielH/tale-hugo/\\'>Tale</a> theme.\n\t\t\t</span>\n\t\t</footer>\n\n    </body>\n</html>"
        }
    ]
)

print(completion.choices[0].message.content)
```

The first is the `system` prompt telling the model what it is supposed to do. The second is the `user` prompt which provides the model with the actual task.

lo and behold !

```
Setting up a private network with Tailscale

Written by Jimmy John on December 29, 2022

I came across Tailscale recently. It’s a tool that lets you build a private mesh network between your devices easily.

My use case was to transfer data from my laptop (macbook) to my android phone. Many times I have a pdf doc that I have on my laptop that I wish to read on the go. I usually just email it to myself and download it to my phone, but wanted to see if I can use Tailscale for it.

To start off, first install Tailscale on both your android device and macbook. (Installation instructions are on the Tailscale website)

As soon as Tailscale is installed on android, it gets assigned a unique 100.x.y.z IP (by a centralized coordination server at login.tailscale.com) and is called a node.

You can actually test that this IP works:

ping 100.x.y.z -t 4

Now install Tailscale on your macbook. If you open the admin console you will see a second IP appear i.e. both nodes are now on a private network.

I then created a dummy file called hello_tailscale.txt on the macbook. You can use Taildrop to copy that file between your devices. Just right click > share. Boom! The file is now on my android device!

Under the hood, after authentication, when each device is added to the tail network, the auto generated public key is added to the Tailscale co-ordination server. This public key is used to identify the device i.e node on the private network. After that, data is directly transferred peer-to-peer between nodes using the auto generated private key. (Note that the private key never leaves the node and data transfer happens peer-to-peer). This is also called zero trust networking. The control plane, i.e., coordination server is used only to store public keys of the nodes, domains, and some other metadata which is downloaded by all the nodes.

There is a lot more you can do with Tailscale. Head over to the docs if you are curious.
```