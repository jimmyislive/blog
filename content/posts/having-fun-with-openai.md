---
title: "Having Fun with OpenAI"
date: 2024-11-23
draft: false
tags: ["tech"]
---
AI is all the rage these days. New capabilities, that seemed impossible just a few months ago, show up almost weekly. So I decided to take it for a spin and see what different tasks I could code up quickly.

I chose [OpenAI](https://openai.com), but you should be able to replicate these using any of the other models as well.

- [Printing the text of a html page](#text-to-html)
- [Book Recommendations](#book-recommendations)
- [Converting csv to json using json_schema](#csv-to-json)

## Printing the text of a html page {#text-to-html}

The first quick task I tried was to ask AI to print out the text of a html page i.e. without tags or images etc.

I chose a [post](https://jimmyislive.dev/posts/tailscale/) I had written a while back which had images.

First, get the content of that page which contains all the html tags:

{{< highlight python "linenos=table" >}}
def download_page(url: str) -> str:
    response = requests.get(url)
    html_content = response.content

    html_content = str(html_content).replace('"', '\\"').replace("'", "\\'")

    return html_content
{{< / highlight >}}

Don't forget to escape the text as bove.

Once I got the text, all I had to do was send it to openAI with the aprpopriate prompt.

{{< highlight python "linenos=table" >}}
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
{{< / highlight >}}

The first is the `system` prompt telling the model what it is supposed to do. The second is the `user` prompt which provides the model with the actual task.

lo and behold !

{{< highlight plaintext "linenos=table" >}}
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
{{< / highlight >}}

## Book Recommendations {#book-recommendations}

I enjoy reading. My [books](https://jimmyislive.dev/books/) page lists a lot of the books I have read recently. So I decided to ask OpenAI to recommend me some new books based on the ones I liked.

The books I enjoyed typically have a (:thumbsup:) emoji next to it. So we'll first ask OpenAI to extract only those books. Then we'll feed this list into the model and ask it to suggest new ones based on my previous likes.

*First lets pull down the books I liked*

{{< highlight python "linenos=table" >}}
client = OpenAI()
completion = client.chat.completions.create(
    model="gpt-4o-mini",
    messages=[
        {"role": "system", "content": "You will be provided with html content of books I have read. Remove tags and output as csv without quotes in one line. I am interested only in books that have a thumbs up icon."},
        {
            "role": "user",
            "content": "<!DOCTYPE html>\n<html lang=\"en\">\n    <head>\n\t\t<meta charset=\"UTF-8\">\n\t\t<meta name=\"viewport\" content=\"width=device-width, initial-scale=1.0\">\n\t\t\t<meta name=\"description\" content=\"books\">\n\t\n\t\t<title>\n\t\t\t\tBooks &middot; @jimmyislive\n\t\t</title>\n\t\n\t\t\n  \t\t<link rel=\"stylesheet\" href=\"/css/style.css\">\n\t\t<link rel=\"stylesheet\" href=\"https://fonts.googleapis.com/css?family=Libre+Baskerville:400,400i,700\">\n\t\t<link rel=\"stylesheet\" href=\"https://use.fontawesome.com/releases/v5.7.2/css/all.css\" integrity=\"sha384-fnmOCqbTlWIlj8LyTjo7mOUStjsKC4pOpQbqyi7RrhN7udi9RwhKkMHpvLbHG9Sr\" crossorigin=\"anonymous\">\n\n\t\t\n\t\t<link rel=\"icon\" type=\"image/png\" sizes=\"64x64\" href=\"/images/favicon-64x64.png\">\n\t\t<link rel=\"apple-touch-icon\" sizes=\"180x180\" href=\"/images/apple-touch-icon.png\">\n\t\n\t\t\n\t\t<link href=\"\" rel=\"alternate\" type=\"application/rss+xml\" title=\"@jimmyislive\" />\n\n\n                <script async src=\"https://www.googletagmanager.com/gtag/js?id=G-SDJ0D81L5C\"></script>\n\n                <script>\n                  window.dataLayer = window.dataLayer || [];\n                  function gtag(){dataLayer.push(arguments);}\n                  gtag(\\'js\\', new Date());\n\n                  gtag(\\'config\\', \\'G-SDJ0D81L5C\\');\n                </script>\n\n\t</head>\n\t\n\n    <body>\n        \t\t<nav class=\"nav\">\n\t\t\t<div class=\"nav-container\">\n\t\t\t\t<a href=\"/\">\n\t\t\t\t\t<h2 class=\"nav-title\">@jimmyislive</h2>\n\t\t\t\t</a>\n\t\t\t\t<ul>\n    <li><a href=\"/\">Posts</a></li>\n    <li><a href=\"/books\">Books</a></li>\n    \n    <li><a href=\"/projects\">Projects</a></li>\n    <li><a href=\"/about\">About</a></li>\n</ul>\n\n\t\t\t</div>\n\t\t</nav>\n\n        \n\n<main>\n\t<div class=\"post\">\n\t\t<div class=\"post-info\">\n    <span>Written by</span>\n        Jimmy John\n        <br>\n        <span>on&nbsp;</span><time datetime=\"2019-03-06 20:54:45 -0800 -0800\">March 6, 2019</time>\n</div>\n\t\t<h1 class=\"post-title\">Books</h1>\n<div class=\"post-line\"></div>\n<div class=\"post-info\">\n    \n</div>\n\n\n\t\t\n\n\t\t<p>Some of the books I have recently read. (Items marked with a \xf0\x9f\x91\x8d are ones that I really enjoyed.)</p>\n\n<ul>\n<li><p><a href=\"https://www.amazon.com/Man-Technics-Contribution-Philosophy-Life/dp/1910524174\" target=\"_blank\">Man and Technics - Oswald Spengler</a> - <sup>11</sup>&frasl;<sub>2024</sub></p></li>\n\n<li><p><a href=\"https://www.amazon.com/Being-Mortal-Illness-Medicine-Matters/dp/1250081246\" target=\"_blank\">Being Mortal - Atul Gawande</a> - 08/2024 (\xf0\x9f\x91\x8d)</p></li>\n\n<li><p><a href=\"https://www.amazon.com/War-Below-Lithium-Copper-Global/dp/1668011808\" target=\"_blank\">The War Below: Lithium, Copper, and the Global Battle to Power Our Lives - Ernest Scheyder</a> - 06/2024 (\xf0\x9f\x91\x8d)</p></li>\n\n<li><p><a href=\"https://www.amazon.com/Price-We-Pay-American-Care/dp/1635574110\" target=\"_blank\">The Price We Pay - Marty Makary</a> - 03/2024 (\xf0\x9f\x91\x8d)</p></li>\n\n<li><p><a href=\"https://www.amazon.com/Lessons-History-Will-Durant/dp/143914995X\" target=\"_blank\">The Lessons of History - Will &amp; Ariel Durant</a> - 01/2024</p></li>\n\n<li><p><a href=\"https://us.macmillan.com/books/9781250266774/recodingamerica\" target=\"_blank\">Recoding America - Jennifer Pahlka</a> - 01/2024</p></li>\n\n<li><p><a href=\"https://www.thecoddling.com\" target=\"_blank\">The coddling of the american mind - Greg Lukianoff / Jonathan Haidt</a> - <sup>12</sup>&frasl;<sub>2023</sub> (\xf0\x9f\x91\x8d)</p></li>\n\n<li><p><a href=\"https://www.amazon.com/Obesity-Code-Unlocking-Secrets-Weight/dp/1771641258\" target=\"_blank\">The Obesity Code - Jason Fung</a> - <sup>10</sup>&frasl;<sub>2023</sub></p></li>\n\n<li><p><a href=\"https://www.amazon.com/Omon-Ra-Victor-Pelevin/dp/0811213641\" target=\"_blank\">Omon Ra - Victor Pelevin</a> - <sup>10</sup>&frasl;<sub>2023</sub></p></li>\n\n<li><p><a href=\"https://www.amazon.com/National-Geographic-Backyard-Guide-Night/dp/1426220154\" target=\"_blank\">Backyard Guide to the Night Sky - Andrew Fazekas</a> - <sup>8</sup>&frasl;<sub>2023</sub> (\xf0\x9f\x91\x8d)</p></li>\n\n<li><p><a href=\"https://www.amazon.com/Factfulness-Reasons-World-Things-Better/dp/1250107814\" target=\"_blank\">Factfulness - Hans Rosling</a> - <sup>8</sup>&frasl;<sub>2023</sub></p></li>\n\n<li><p><a href=\"https://www.amazon.com/Evicted-Poverty-Profit-American-City/dp/0553447459\" target=\"_blank\">Evicted - Matthew Desmond</a> - <sup>5</sup>&frasl;<sub>2023</sub> (\xf0\x9f\x91\x8d)</p></li>\n\n<li><p><a href=\"https://www.amazon.com/Decoding-Reality-Universe-Quantum-Information/dp/0199695741\" target=\"_blank\">Decoding Reality: The universe as quantum information - Vlatko Vedral</a> - <sup>4</sup>&frasl;<sub>2023</sub></p></li>\n\n<li><p><a href=\"https://www.amazon.com/Rough-Ride-Future/dp/B01GOE8HDW/\" target=\"_blank\">A rough ride to the future - James Lovelock</a> - <sup>3</sup>&frasl;<sub>2023</sub> (\xf0\x9f\x91\x8d)</p></li>\n\n<li><p><a href=\"https://www.amazon.com/End-World-Just-Beginning-Globalization/dp/006323047X\" target=\"_blank\">The end of the world is just the beginning - Peter Zeihan</a> - 02/2023 (\xf0\x9f\x91\x8d)</p></li>\n\n<li><p><a href=\"https://www.amazon.com/Zen-Art-Motorcycle-Maintenance-Inquiry/dp/0060589469\" target=\"_blank\">Zen and the art of motorcycle maintenance - Robert M Persig</a> - <sup>10</sup>&frasl;<sub>2022</sub> (\xf0\x9f\x91\x8d)</p></li>\n\n<li><p><a href=\"https://www.amazon.com/Greenlights-Matthew-McConaughey/dp/0593139135\" target=\"_blank\">Greenlights - Matthew McConaughey</a> - 09/2022</p></li>\n\n<li><p><a href=\"https://www.amazon.com/1776-David-McCullough/dp/0743226720\" target=\"_blank\">1776 - David McCullough</a> - 09/2022 (\xf0\x9f\x91\x8d)</p></li>\n\n<li><p><a href=\"https://www.amazon.com/Walden-Henry-David-Thoreau/dp/1505297729\" target=\"_blank\">Walden - Henry David Thoreau</a> - 07/2022</p></li>\n\n<li><p><a href=\"https://www.amazon.com/Little-Often-Memoir-Trent-Preszler/dp/0062976648\" target=\"_blank\">Little and Often - Trent Preszler</a> - 05/2022 (\xf0\x9f\x91\x8d)</p></li>\n\n<li><p><a href=\"https://www.amazon.com/Ikigai-Japanese-Secret-Long-Happy/dp/0143130722\" target=\"_blank\">Ikigai - Hector Garcia / Francesc Miralles</a> - 05/2022</p></li>\n\n<li><p><a href=\"https://www.thriftbooks.com/w/geography-of-nowhere-the-rise-and-decline-of-americas-man-made-landscape_james-howard-kunstler/252343/?resultid=6c9389f2-18c9-4548-b6fa-e50a72cf5f44#edition=2264395&amp;idiq=2862438\" target=\"_blank\">The Geography of Nowhere - James Howard Kunstler</a> - 02/2022 (\xf0\x9f\x91\x8d)</p></li>\n\n<li><p><a href=\"https://www.amazon.com/Dumbing-Down-Curriculum-Compulsory-Schooling-dp-0865718547/dp/0865718547\" target=\"_blank\">Dumbing Us Down - John Taylor Gatto</a> - 01/2022</p></li>\n\n<li><p><a href=\"https://www.thriftbooks.com/w/into-the-wild_jon-krakauer/245729/\" target=\"_blank\">Into The Wild - Jon Krakauer</a> - <sup>10</sup>&frasl;<sub>2021</sub> (\xf0\x9f\x91\x8d)</p></li>\n\n<li><p><a href=\"https://www.amazon.com/Alchemist-Paulo-Coelho/dp/0061122416\" target=\"_blank\">The Alchemist - Paulo Coelho</a> - 09/2021</p></li>\n\n<li><p><a href=\"https://www.amazon.com/Peace-Every-Step-Mindfulness-Everyday/dp/0553351397\" target=\"_blank\">Peace Is Every Step - Thich Nhat Hanh</a> - 09/2021 (\xf0\x9f\x91\x8d)</p></li>\n\n<li><p><a href=\"https://www.thriftbooks.com/w/into-thin-air-a-personal-account-of-the-mt-everest-disaster_jon-krakauer/250322/item/3718623/\" target=\"_blank\">Into Thin Air - Jon Krakauer</a> - 07/2021 (\xf0\x9f\x91\x8d)</p></li>\n\n<li><p><a href=\"https://www.amazon.com/Simple-Path-Wealth-financial-independence/dp/1533667926\" target=\"_blank\">The Simple Path to Wealth - J.L. Collins</a> - 06/2021 (\xf0\x9f\x91\x8d)</p></li>\n\n<li><p><a href=\"https://www.amazon.com/End-Overeating-Insatiable-American-Appetite/dp/1605294578\" target=\"_blank\">The End of Overeating - David A. Kessler</a> - 05/2021</p></li>\n\n<li><p><a href=\"https://www.amazon.com/Price-Civilization-Reawakening-American-Prosperity/dp/0812980468\" target=\"_blank\">The Price of Civilization - Jeffrey Sachs</a> - 04/2021</p></li>\n\n<li><p><a href=\"https://www.amazon.com/Small-Beautiful-Economics-Mattered-Perennial/dp/0061997765\" target=\"_blank\">Small is Beautiful - E. F. Schumacher</a> - 02/2021</p></li>\n\n<li><p><a href=\"https://www.amazon.com/Strong-Towns-Bottom-Up-Revolution-Prosperity/dp/1119564816\" target=\"_blank\">Strong Towns - Charles L. Marohn Jr.</a> - 01/2021 (\xf0\x9f\x91\x8d)</p></li>\n\n<li><p><a href=\"https://www.amazon.com/Gang-Leader-Day-Sociologist-Streets/dp/014311493X\" target=\"_blank\">Gang Leader for a Day - Sudhir Venkatesh</a> - <sup>12</sup>&frasl;<sub>2020</sub> (\xf0\x9f\x91\x8d)</p></li>\n\n<li><p><a href=\"https://www.amazon.com/gp/product/1941129420\" target=\"_blank\">On the shortness of life - Seneca</a> - <sup>12</sup>&frasl;<sub>2020</sub> (\xf0\x9f\x91\x8d)</p></li>\n\n<li><p><a href=\"https://www.amazon.com/Freakonomics-Economist-Explores-Hidden-Everything/dp/0060731338/\" target=\"_blank\">Freakonomics - Steven Levitt / Stephen Dubner</a> - <sup>12</sup>&frasl;<sub>2020</sub></p></li>\n\n<li><p><a href=\"https://www.amazon.com/Joy-Missing-Out-Self-Restraint-Excess/dp/1509531572\" target=\"_blank\">The Joy of Missing Out - Svend Brinkmann</a> - <sup>12</sup>&frasl;<sub>2020</sub></p></li>\n\n<li><p><a href=\"https://www.amazon.com/Skin-Game-Hidden-Asymmetries-Daily/dp/042528462X\" target=\"_blank\">Skin in the Game - Nassim Taleb</a> - <sup>11</sup>&frasl;<sub>2020</sub> (\xf0\x9f\x91\x8d)</p></li>\n\n<li><p><a href=\"https://www.amazon.com/Salt-Fix-Experts-Wrong-Eating-ebook/dp/B01GBAJR9C\" target=\"_blank\">The Salt Fix - Dr. James DiNicolantonio</a> - 09/2020</p></li>\n\n<li><p><a href=\"https://www.amazon.com/How-Money-Became-Dangerous-Relationship/dp/0062684752\" target=\"_blank\">How Money Became Dangerous - Christopher Varelas</a> - 07/2020</p></li>\n\n<li><p><a href=\"https://www.amazon.com/Prisoners-Geography-Explain-Everything-Politics/dp/1501121472/\" target=\"_blank\">Prisoners of Geography - Tim Marshall</a> - 06/2020 (\xf0\x9f\x91\x8d)</p></li>\n\n<li><p><a href=\"https://www.amazon.com/Art-War-Sun-Tzu/dp/1599869772\" target=\"_blank\">The Art of War - Sun Tzu</a> - 06/2020</p></li>\n\n<li><p><a href=\"https://www.amazon.com/AI-Superpowers-China-Silicon-Valley/dp/132854639X\" target=\"_blank\">AI Superpowers: China, Silicon Valley and the New World Order - Kai-Fu Lee</a> - 05/2020</p></li>\n\n<li><p><a href=\"https://www.amazon.com/Gene-Intimate-History-Siddhartha-Mukherjee/dp/1432837818\" target=\"_blank\">The Gene: An Intimate History - Siddhartha Mukherjee</a> - 04/2020 (\xf0\x9f\x91\x8d)</p></li>\n\n<li><p><a href=\"https://www.amazon.com/Misguided-Medicine-Second-Colin-Champ/dp/0692629300\" target=\"_blank\">Misguided Medicine - Colin E Champ</a> - <sup>12</sup>&frasl;<sub>2019</sub></p></li>\n\n<li><p><a href=\"https://www.amazon.com/Free-Choose-Statement-Milton-Friedman/dp/0156334607\" target=\"_blank\">Free to Choose - Milton/Rose Friedman</a> - <sup>11</sup>&frasl;<sub>2019</sub></p></li>\n\n<li><p><a href=\"https://www.amazon.com/Siddhartha-Novel-Hermann-Hesse/dp/0553208845\" target=\"_blank\">Siddhartha - Hermann Hesse</a> - <sup>10</sup>&frasl;<sub>2019</sub> (\xf0\x9f\x91\x8d)</p></li>\n\n<li><p><a href=\"https://www.amazon.com/Why-We-Sleep-Unlocking-Dreams/dp/1501144316\" target=\"_blank\">Why We Sleep - Matthew Walker</a> - 09/2019 (\xf0\x9f\x91\x8d)</p></li>\n\n<li><p><a href=\"https://www.amazon.com/Change-Your-Mind-Consciousness-Transcendence/dp/1594204225\" target=\"_blank\">How to Change Your Mind - Michael Pollan</a> - 07/2019 (\xf0\x9f\x91\x8d)</p></li>\n\n<li><p><a href=\"https://www.amazon.com/Influence-Psychology-Persuasion-Robert-Cialdini/dp/006124189X\" target=\"_blank\">Influence: The Psychology of Persuation - Robert Cialdini</a> - 06/2019</p></li>\n\n<li><p><a href=\"https://www.amazon.com/dp/B07BVF1Z6Z/ref=dp-kindle-redirect?_encoding=UTF8&amp;btkr=1\" target=\"_blank\">The Circadian Code - Satchin Panda</a></p>\n\n<blockquote>\n<p>A very revealing book about all the biological clocks in your body and how they work, which has a direct effect on your overall health. Anecdotally, I can confirm several of my suspicions related to my health after reading this book e.g. bad sleeping habits which sometimes made me feel tired even after waking up in the morn. This book does a good job of revealing the science behind the way our body clocks work and how with relatively simple changes in our schedule / habits we can make vast improvements in health. Will be trying out several of the recommendations in this book.</p>\n</blockquote></li>\n\n<li><p><a href=\"https://www.amazon.com/Tender-Bar-J-R-Moehringer/dp/0786888768\" target=\"_blank\">Tender Bar - J. R Moehringer</a> (\xf0\x9f\x91\x8d)</p>\n\n<blockquote>\n<p>One of the best books I have read. It&rsquo;s a memoir of J. R Moehringer. Beautifully written. Found it very hard to put this book down !</p>\n</blockquote></li>\n\n<li><p><a href=\"https://www.amazon.com/Billionaire-Raj-Journey-Through-Indias/dp/1524760064\" target=\"_blank\">The Billionaire Raj - James Crabtree</a></p>\n\n<blockquote>\n<p>All about the corruption / crony capitalism in India. A sad state of affiars indeed. To most folks born and raised in India though, it&rsquo;s nothing new. But to see it defining the future of the country is depressing.</p>\n</blockquote></li>\n\n<li><p><a href=\"https://www.amazon.com/gp/product/0984755128/ref=ppx_yo_dt_b_asin_title_o07_s00?ie=UTF8&amp;psc=1\" target=\"_blank\">Death by Food Pyramid - Denise Minger</a></p>\n\n<blockquote>\n<p>An insightful and well researched book about the politics of nutrition. Being healthy today has become confusing owing to the contradictory information coming out of various institutions. Not to mention how special interests and lobbyists of the food industry hijack the health agenda with flimsy science. Read this book, do your own research and make decisions about your health !</p>\n</blockquote></li>\n\n<li><p><a href=\"https://www.amazon.com/Shoe-Dog-Memoir-Creator-Nike-ebook/dp/B0176M1A44\" target=\"_blank\">Shoe Dog - Phil Knight</a> (\xf0\x9f\x91\x8d)</p>\n\n<blockquote>\n<p>Memoir of the creator of Nike. Great stroy of grit and passion. Essential reading for any entrepreneur. (Incidently, it was ghost written by J. R Moehringer)</p>\n</blockquote></li>\n\n<li><p><a href=\"https://www.amazon.com/gp/product/0062316095/ref=ppx_yo_dt_b_asin_title_o06_s01?ie=UTF8&amp;psc=1\" target=\"_blank\">Sapiens - Yuval Noah Harari</a></p>\n\n<blockquote>\n<p>Great read about the history of mankind. He goes through a chronological sequence of historical events starting from the early agricultutal era to the present one. Intutive insights into why we are the way we are.</p>\n</blockquote></li>\n\n<li><p><a href=\"https://www.amazon.com/gp/product/0312430000/ref=ppx_yo_dt_b_asin_title_o03_s00?ie=UTF8&amp;psc=1\" target=\"_blank\">The Checklist Manifesto - Atul Gawande</a> (\xf0\x9f\x91\x8d)</p>\n\n<blockquote>\n<p>I started making checkists after reading this book !</p>\n</blockquote></li>\n\n<li><p><a href=\"https://www.amazon.com/gp/product/1784701998/ref=ppx_yo_dt_b_asin_title_o07_s00?ie=UTF8&amp;psc=1\" target=\"_blank\">When Breadth Becomes Air - Paul Kalanithi</a> (\xf0\x9f\x91\x8d)</p>\n\n<blockquote>\n<p>Poignant read about the fragility of life.</p>\n</blockquote></li>\n</ul>\n\n\n\t\t\n\t</div>\n\n\t<div class=\"pagination\">\n\t\t<a href=\"/about/\" class=\"left arrow\">&#8592;</a>\n\t\t<a href=\"/posts/gitattributes/\" class=\"right arrow\">&#8594;</a>\n\n\t\t<a href=\"#\" class=\"top\">Top</a>\n\t</div>\n</main>\n\n\n        \t\t<footer>\n\t\t\t<div>\n\t\t\t\t<a href=\"https://twitter.com/jimmyislive\" target=\"_blank\">\n\t\t\t\t\t<i class=\"fab fa-twitter\"/></i>\n\t\t\t\t</a>&nbsp;\n\t\t\t\t<a href=\"mailto:jimmyislive@gmail.com\">\n\t\t\t\t\t<i class=\"fas fa-envelope\"/></i>\n\t\t\t\t</a>&nbsp;\n\t\t\t\t<a href=\"https://github.com/jimmyislive\" target=\"_blank\">\n\t\t\t\t\t<i class=\"fab fa-github\"/></i>\n\t\t\t\t</a>&nbsp;\n\t\t\t\t<a href=\"https://www.linkedin.com/in/jimmyislive/\" target=\"_blank\">\n\t\t\t\t\t<i class=\"fab fa-linkedin\"/></i>\n\t\t\t\t</a>&nbsp;\n\t\t\t\t<a href=\"/index.xml\" target=\"_blank\">\n\t\t\t\t\t<i class=\"fas fa-rss\"></i>\n\t\t\t\t</a>\n\t\t\t\t\n\t\t\t</div>\n\t\t\t<span>\n\t\t\t&copy; <time datetime=\"2024-11-23 20:39:32.958656993 &#43;0000 UTC m=&#43;0.090932629\">2024</time> . Made with <a href=\\'https://gohugo.io\\'>Hugo</a> using the <a href=\\'https://github.com/EmielH/tale-hugo/\\'>Tale</a> theme.\n\t\t\t</span>\n\t\t</footer>\n\n    </body>\n</html>"
        }
    ]
)

print(completion.choices[0].message.content)
{{< / highlight >}}

The above extracted the books I liked i.e. the ones with the (:thumbsup:) emoji.

*Now to feed this into the model for new recommendations*

{{< highlight python "linenos=table" >}}
client = OpenAI()
completion = client.chat.completions.create(
    model="gpt-4o-mini",
    messages=[
        {"role": "system", "content": "You are provided with some books the user likes. Recommend some new books based on this. The liked books and author are: Being Mortal - Atul Gawande,The War Below: Lithium, Copper, and the Global Battle to Power Our Lives - Ernest Scheyder,The Price We Pay - Marty Makary,The coddling of the american mind - Greg Lukianoff / Jonathan Haidt,Backyard Guide to the Night Sky - Andrew Fazekas,Evicted - Matthew Desmond,A rough ride to the future - James Lovelock,The end of the world is just the beginning - Peter Zeihan,Zen and the art of motorcycle maintenance - Robert M Persig,1776 - David McCullough,Little and Often - Trent Preszler,The Geography of Nowhere - James Howard Kunstler,Into The Wild - Jon Krakauer,Peace Is Every Step - Thich Nhat Hanh,Into Thin Air - Jon Krakauer,The Simple Path to Wealth - J.L. Collins,Strong Towns - Charles L. Marohn Jr.,Gang Leader for a Day - Sudhir Venkatesh,On the shortness of life - Seneca,Skin in the Game - Nassim Taleb,Prisoners of Geography - Tim Marshall,The Gene: An Intimate History - Siddhartha Mukherjee,Siddhartha - Hermann Hesse,Why We Sleep - Matthew Walker,How to Change Your Mind - Michael Pollan,Tender Bar - J. R Moehringer,Shoe Dog - Phil Knight,The Checklist Manifesto - Atul Gawande,When Breadth Becomes Air - Paul Kalanithi"},
        {
            "role": "user",
            "content": "Make me some new book recommendations"
        }
    ]
)

print(completion.choices[0].message.content)
{{< / highlight >}}

Boom...

{{< highlight markdown "linenos=table" >}}
Based on your interests in thought-provoking non-fiction, insightful explorations of society and human behavior, as well as introspective narratives, here are some book recommendations you might enjoy:

1. **The Second Machine Age** by Erik Brynjolfsson and Andrew McAfee - A deep dive into how digital technology is transforming our economy and society.

2. **The Body Keeps the Score** by Bessel van der Kolk - An exploration of how trauma affects the body and mind, offering hope for recovery through innovative therapies.

3. **Sapiens: A Brief History of Humankind** by Yuval Noah Harari - A compelling overview of the history of our species and how we've shaped the world.

4. **Range: Why Generalists Triumph in a Specialized World** by David Epstein - A look at why being a jack-of-all-trades can lead to better outcomes and innovation than specialization.

5. **How to Avoid a Climate Disaster** by Bill Gates - An exploration of the solutions we need to put in place to avoid the catastrophic effects of climate change.

6. **The Invisible Man** by Ralph Ellison - A powerful novel that addresses race, identity, and individuality in America.

7. **Educated** by Tara Westover - A memoir about a woman who grows up in a strict and abusive household in rural Idaho but eventually escapes to learn about the wider world through education.

8. **The Comfort Crisis** by Michael Gervais - Discusses the importance of embracing discomfort to enrich our lives and promote resilience and personal growth.

9. **The Nature of Nature** by Enric Sala - Explores the importance of preserving our natural world and the impact humans have on it.

10. **Atomic Habits** by James Clear - A practical guide on how to build good habits and break bad ones through small and sustainable changes.

These selections span themes of technology, society, health, and personal development, offering a variety of insights that align with your existing interests. Enjoy your reading!
{{< / highlight >}}

*NOTE*: Book recommendations are just that, recommendations. And hence can be very subjective. But these results are way better than I got when I did not prompt the model with past books I liked. e.g. without my past books, the model recommended books in the fiction / fantasy genre which I typically don't read.

## Converting csv to json using json_schema {#csv-to-json}
I pulled some csv data about traffic deaths from the [National Safety Council](https://injuryfacts.nsc.org/motor-vehicle/historical-fatality-trends/deaths-and-rates/). I then asked OpenAI to convert this into json while specifying a schema of how the output should look like.

{{< highlight python "linenos=table" >}}
client = OpenAI()
completion = client.chat.completions.create(
    model="gpt-4o-mini",
    messages=[
        
        {"role": "system", "content": "You will be provided with csv data. Convert this into json. "},
        {
            "role": "user",
            "content": "The following is csv: Year,'Number of deaths','Vehicles (millions)','Vehicle miles (billions)','Drivers (millions)','Death rates Per 10,000 motor vehicles','Death rates Per 100,000,000 vehicle miles','Death rates Per 100,000 population'\n1970,'54,633',111.2,'1,120',111.5,4.92,4.88,26.8 \n1971,'54,381',116.3,'1,186',114.4,4.68,4.57,26.3 \n1972,'56,278',122.3,'1,268',118.4,4.60,4.43,26.9 \n1973,'55,511',129.8,'1,309',121.6,4.28,4.24,26.3 \n1974,'46,402',134.9,'1,290',125.6,3.44,3.59,21.8 \n1975,'45,853',137.9,'1,330',129.8,3.33,3.45,21.3 \n1976,'47,038',143.5,'1,412',133.9,3.28,3.33,21.6 \n1977,'49,510',148.8,'1,477',138.1,3.33,3.35,22.5 \n1978,'52,411',153.6,'1,548',140.8,3.41,3.39,23.6 \n1979,'53,524',159.6,'1,529',143.3,3.35,3.50,23.8 \n1980,'53,172',161.6,'1,521',145.3,3.29,3.50,23.4 \n1981,'51,385',164.1,'1,556',147.1,3.13,3.30,22.4 \n1982,'45,779',165.2,'1,592',150.3,2.77,2.88,19.8 \n1983,'44,452',169.4,'1,657',154.2,2.62,2.68,19.0 \n1984,'46,263',171.8,'1,718',155.4,2.69,2.69,19.6 \n1985,'45,901',177.1,'1,774',156.9,2.59,2.59,19.3 \n1986,'47,865',181.4,'1,835',159.5,2.63,2.60,19.9 \n1987,'48,290',183.9,'1,924',161.8,2.63,2.51,19.9 \n1988,'49,078',189.0,'2,026',162.9,2.60,2.42,20.1 \n1989,'47,575',191.7,'2,107',165.6,2.48,2.26,19.3 \n1990,'46,814',192.9,'2,148',167.0,2.43,2.18,18.8 \n1991,'43,536',192.5,'2,172',169.0,2.26,2.00,17.3 \n1992,'40,982',194.4,'2,240',173.1,2.11,1.83,16.1 \n1993,'41,893',198.0,'2,297',173.1,2.12,1.82,16.3 \n1994,'42,524',201.8,'2,360',175.4,2.11,1.80,16.3 \n1995,'43,363',205.3,'2,423',176.6,2.11,1.79,16.5 \n1996,'43,649',210.4,'2,486',179.5,2.07,1.76,16.5 \n1997,'43,458',211.5,'2,562',182.7,2.05,1.70,16.2 \n1998,'43,501',215.0,'2,632',185.2,2.02,1.65,16.1 \n1999,'42,401',220.5,'2,691',187.2,1.92,1.58,15.5 \n2000,'43,354',225.8,'2,747',190.6,1.92,1.58,15.8 \n2001,'43,788',235.3,'2,797',191.3,1.86,1.57,15.4 \n2002,'45,380',234.6,'2,856',194.3,1.93,1.59,15.8 \n2003,'44,757',236.8,'2,890',196.2,1.89,1.55,15.4 \n2004,'44,933',243.0,'2,965',199.0,1.85,1.52,15.3 \n2005,'45,343',247.4,'2,989',200.5,1.83,1.52,15.3 \n2006,'45,316',250.8,'3,014',202.8,1.81,1.50,15.2 \n2007,'43,945',254.4,'3,032',205.7,1.73,1.45,14.6 \n2008,'39,790',255.9,'2,976',208.3,1.55,1.34,13.1 \n2009,'36,216',254.2,'2,957',209.6,1.42,1.22,11.8 \n2010,'35,332',250.3,'2,967',210.1,1.41,1.19,11.4 \n2011,'35,303',253.2,'2,950',211.9,1.39,1.20,11.3 \n2012,'36,415',253.6,'2,969',211.8,1.44,1.23,11.6 \n2013,'35,369',255.9,'2,988',212.2,1.38,1.18,11.2 \n2014,'35,398',260.3,'3,026',214.1,1.36,1.17,11.1 \n2015,'37,757',263.6,'3,095',218.1,1.43,1.22,11.8 \n2016,'40,327',268.8,'3,174',221.7,1.50,1.27,12.5 \n2017,'40,231',272.5,'3,212',225.3,1.48,1.25,12.4 \n2018,'39,404',276.6,'3,240',227.6,1.42,1.22,12.0 \n2019,'39,107',276.5,'3,262',228.7,1.41,1.20,11.9 \n2020,'42,338',275.9,'2,904',228.2,1.53,1.46,12.9 \n2021,'46,980',282.4,'3,140',232.8,1.53,1.38,14.2 \n2022,'46,027',283.4,'3,196',235.1,1.50,1.33,13.8 \n"
        }
    ],
    response_format={
        "type": "json_schema",
        "json_schema": {
            "name": "highway_taffic_safety_schema",
            "schema": {
                "type": "object",
                "properties": {
                    "year": {
                        "description": "Year",
                        "type": "integer"
                    },
                    "num_of_deaths": {
                        "description": "Number of deaths",
                        "type": "number"
                    },
                    "vehicle_count": {
                        "description": "Vehicles (millions)",
                        "type": "number"
                    },
                    "venhicle_miles": {
                        "description": "Vehicle miles (billions)",
                        "type": "number"
                    },
                    "driver_count": {
                        "description": "Drivers (millions)",
                        "type": "number"
                    },
                    "death_rate_vehicles": {
                        "description": "Death rates Per 10,000 motor vehicles",
                        "type": "number"
                    },
                    "death_rate_vehicle_miles": {
                        "description": "Death rates Per 100,000,000 vehicle miles",
                        "type": "number"
                    },
                    "death_rate_population": {
                        "description": "Death rates Per 100,000 population",
                        "type": "number"
                    }

                },
                "additionalProperties": False
            }
        }
    }
)

print(completion.choices[0].message.content)
{{< / highlight >}}

And the result...

{{< highlight json "linenos=table" >}}
{
  "highway_traffic_safety_data": [
    {
      "year": 1970,
      "num_of_deaths": 54633,
      "vehicle_count": 111.2,
      "venhicle_miles": 1120,
      "driver_count": 111.5,
      "death_rate_vehicles": 4.92,
      "death_rate_vehicle_miles": 4.88,
      "death_rate_population": 26.8
    },
    {
      "year": 1971,
      "num_of_deaths": 54381,
      "vehicle_count": 116.3,
      "venhicle_miles": 1186,
      "driver_count": 114.4,
      "death_rate_vehicles": 4.68,
      "death_rate_vehicle_miles": 4.57,
      "death_rate_population": 26.3
    },
    {
      "year": 1972,
      "num_of_deaths": 56278,
      "vehicle_count": 122.3,
      "venhicle_miles": 1268,
      "driver_count": 118.4,
      "death_rate_vehicles": 4.60,
      "death_rate_vehicle_miles": 4.43,
      "death_rate_population": 26.9
    },
    {
      "year": 1973,
      "num_of_deaths": 55511,
      "vehicle_count": 129.8,
      "venhicle_miles": 1309,
      "driver_count": 121.6,
      "death_rate_vehicles": 4.28,
      "death_rate_vehicle_miles": 4.24,
      "death_rate_population": 26.3
    },
    {
      "year": 1974,
      "num_of_deaths": 46402,
      "vehicle_count": 134.9,
      "venhicle_miles": 1290,
      "driver_count": 125.6,
      "death_rate_vehicles": 3.44,
      "death_rate_vehicle_miles": 3.59,
      "death_rate_population": 21.8
    },
    {
      "year": 1975,
      "num_of_deaths": 45853,
      "vehicle_count": 137.9,
      "venhicle_miles": 1330,
      "driver_count": 129.8,
      "death_rate_vehicles": 3.33,
      "death_rate_vehicle_miles": 3.45,
      "death_rate_population": 21.3
    },
    {
      "year": 1976,
      "num_of_deaths": 47038,
      "vehicle_count": 143.5,
      "venhicle_miles": 1412,
      "driver_count": 133.9,
      "death_rate_vehicles": 3.28,
      "death_rate_vehicle_miles": 3.33,
      "death_rate_population": 21.6
    },
    {
      "year": 1977,
      "num_of_deaths": 49510,
      "vehicle_count": 148.8,
      "venhicle_miles": 1477,
      "driver_count": 138.1,
      "death_rate_vehicles": 3.33,
      "death_rate_vehicle_miles": 3.35,
      "death_rate_population": 22.5
    },
    {
      "year": 1978,
      "num_of_deaths": 52411,
      "vehicle_count": 153.6,
      "venhicle_miles": 1548,
      "driver_count": 140.8,
      "death_rate_vehicles": 3.41,
      "death_rate_vehicle_miles": 3.39,
      "death_rate_population": 23.6
    },
    {
      "year": 1979,
      "num_of_deaths": 53524,
      "vehicle_count": 159.6,
      "venhicle_miles": 1529,
      "driver_count": 143.3,
      "death_rate_vehicles": 3.35,
      "death_rate_vehicle_miles": 3.50,
      "death_rate_population": 23.8
    },
    {
      "year": 1980,
      "num_of_deaths": 53172,
      "vehicle_count": 161.6,
      "venhicle_miles": 1521,
      "driver_count": 145.3,
      "death_rate_vehicles": 3.29,
      "death_rate_vehicle_miles": 3.50,
      "death_rate_population": 23.4
    },
    {
      "year": 1981,
      "num_of_deaths": 51385,
      "vehicle_count": 164.1,
      "venhicle_miles": 1556,
      "driver_count": 147.1,
      "death_rate_vehicles": 3.13,
      "death_rate_vehicle_miles": 3.30,
      "death_rate_population": 22.4
    },
    {
      "year": 1982,
      "num_of_deaths": 45779,
      "vehicle_count": 165.2,
      "venhicle_miles": 1592,
      "driver_count": 150.3,
      "death_rate_vehicles": 2.77,
      "death_rate_vehicle_miles": 2.88,
      "death_rate_population": 19.8
    },
    {
      "year": 1983,
      "num_of_deaths": 44452,
      "vehicle_count": 169.4,
      "venhicle_miles": 1657,
      "driver_count": 154.2,
      "death_rate_vehicles": 2.62,
      "death_rate_vehicle_miles": 2.68,
      "death_rate_population": 19.0
    },
    {
      "year": 1984,
      "num_of_deaths": 46263,
      "vehicle_count": 171.8,
      "venhicle_miles": 1718,
      "driver_count": 155.4,
      "death_rate_vehicles": 2.69,
      "death_rate_vehicle_miles": 2.69,
      "death_rate_population": 19.6
    },
    {
      "year": 1985,
      "num_of_deaths": 45901,
      "vehicle_count": 177.1,
      "venhicle_miles": 1774,
      "driver_count": 156.9,
      "death_rate_vehicles": 2.59,
      "death_rate_vehicle_miles": 2.59,
      "death_rate_population": 19.3
    },
    {
      "year": 1986,
      "num_of_deaths": 47865,
      "vehicle_count": 181.4,
      "venhicle_miles": 1835,
      "driver_count": 159.5,
      "death_rate_vehicles": 2.63,
      "death_rate_vehicle_miles": 2.60,
      "death_rate_population": 19.9
    },
    {
      "year": 1987,
      "num_of_deaths": 48290,
      "vehicle_count": 183.9,
      "venhicle_miles": 1924,
      "driver_count": 161.8,
      "death_rate_vehicles": 2.63,
      "death_rate_vehicle_miles": 2.51,
      "death_rate_population": 19.9
    },
    {
      "year": 1988,
      "num_of_deaths": 49078,
      "vehicle_count": 189.0,
      "venhicle_miles": 2026,
      "driver_count": 162.9,
      "death_rate_vehicles": 2.60,
      "death_rate_vehicle_miles": 2.42,
      "death_rate_population": 20.1
    },
    {
      "year": 1989,
      "num_of_deaths": 47575,
      "vehicle_count": 191.7,
      "venhicle_miles": 2107,
      "driver_count": 165.6,
      "death_rate_vehicles": 2.48,
      "death_rate_vehicle_miles": 2.26,
      "death_rate_population": 19.3
    },
    {
      "year": 1990,
      "num_of_deaths": 46814,
      "vehicle_count": 192.9,
      "venhicle_miles": 2148,
      "driver_count": 167.0,
      "death_rate_vehicles": 2.43,
      "death_rate_vehicle_miles": 2.18,
      "death_rate_population": 18.8
    },
    {
      "year": 1991,
      "num_of_deaths": 43536,
      "vehicle_count": 192.5,
      "venhicle_miles": 2172,
      "driver_count": 169.0,
      "death_rate_vehicles": 2.26,
      "death_rate_vehicle_miles": 2.00,
      "death_rate_population": 17.3
    },
    {
      "year": 1992,
      "num_of_deaths": 40982,
      "vehicle_count": 194.4,
      "venhicle_miles": 2240,
      "driver_count": 173.1,
      "death_rate_vehicles": 2.11,
      "death_rate_vehicle_miles": 1.83,
      "death_rate_population": 16.1
    },
    {
      "year": 1993,
      "num_of_deaths": 41893,
      "vehicle_count": 198.0,
      "venhicle_miles": 2297,
      "driver_count": 173.1,
      "death_rate_vehicles": 2.12,
      "death_rate_vehicle_miles": 1.82,
      "death_rate_population": 16.3
    },
    {
      "year": 1994,
      "num_of_deaths": 42524,
      "vehicle_count": 201.8,
      "venhicle_miles": 2360,
      "driver_count": 175.4,
      "death_rate_vehicles": 2.11,
      "death_rate_vehicle_miles": 1.80,
      "death_rate_population": 16.3
    },
    {
      "year": 1995,
      "num_of_deaths": 43363,
      "vehicle_count": 205.3,
      "venhicle_miles": 2423,
      "driver_count": 176.6,
      "death_rate_vehicles": 2.11,
      "death_rate_vehicle_miles": 1.79,
      "death_rate_population": 16.5
    },
    {
      "year": 1996,
      "num_of_deaths": 43649,
      "vehicle_count": 210.4,
      "venhicle_miles": 2486,
      "driver_count": 179.5,
      "death_rate_vehicles": 2.07,
      "death_rate_vehicle_miles": 1.76,
      "death_rate_population": 16.5
    },
    {
      "year": 1997,
      "num_of_deaths": 43458,
      "vehicle_count": 211.5,
      "venhicle_miles": 2562,
      "driver_count": 182.7,
      "death_rate_vehicles": 2.05,
      "death_rate_vehicle_miles": 1.70,
      "death_rate_population": 16.2
    },
    {
      "year": 1998,
      "num_of_deaths": 43501,
      "vehicle_count": 215.0,
      "venhicle_miles": 2632,
      "driver_count": 185.2,
      "death_rate_vehicles": 2.02,
      "death_rate_vehicle_miles": 1.65,
      "death_rate_population": 16.1
    },
    {
      "year": 1999,
      "num_of_deaths": 42401,
      "vehicle_count": 220.5,
      "venhicle_miles": 2691,
      "driver_count": 187.2,
      "death_rate_vehicles": 1.92,
      "death_rate_vehicle_miles": 1.58,
      "death_rate_population": 15.5
    },
    {
      "year": 2000,
      "num_of_deaths": 43354,
      "vehicle_count": 225.8,
      "venhicle_miles": 2747,
      "driver_count": 190.6,
      "death_rate_vehicles": 1.92,
      "death_rate_vehicle_miles": 1.58,
      "death_rate_population": 15.8
    },
    {
      "year": 2001,
      "num_of_deaths": 43788,
      "vehicle_count": 235.3,
      "venhicle_miles": 2797,
      "driver_count": 191.3,
      "death_rate_vehicles": 1.86,
      "death_rate_vehicle_miles": 1.57,
      "death_rate_population": 15.4
    },
    {
      "year": 2002,
      "num_of_deaths": 45380,
      "vehicle_count": 234.6,
      "venhicle_miles": 2856,
      "driver_count": 194.3,
      "death_rate_vehicles": 1.93,
      "death_rate_vehicle_miles": 1.59,
      "death_rate_population": 15.8
    },
    {
      "year": 2003,
      "num_of_deaths": 44757,
      "vehicle_count": 236.8,
      "venhicle_miles": 2890,
      "driver_count": 196.2,
      "death_rate_vehicles": 1.89,
      "death_rate_vehicle_miles": 1.55,
      "death_rate_population": 15.4
    },
    {
      "year": 2004,
      "num_of_deaths": 44933,
      "vehicle_count": 243.0,
      "venhicle_miles": 2965,
      "driver_count": 199.0,
      "death_rate_vehicles": 1.85,
      "death_rate_vehicle_miles": 1.52,
      "death_rate_population": 15.3
    },
    {
      "year": 2005,
      "num_of_deaths": 45343,
      "vehicle_count": 247.4,
      "venhicle_miles": 2989,
      "driver_count": 200.5,
      "death_rate_vehicles": 1.83,
      "death_rate_vehicle_miles": 1.52,
      "death_rate_population": 15.3
    },
    {
      "year": 2006,
      "num_of_deaths": 45316,
      "vehicle_count": 250.8,
      "venhicle_miles": 3014,
      "driver_count": 202.8,
      "death_rate_vehicles": 1.81,
      "death_rate_vehicle_miles": 1.50,
      "death_rate_population": 15.2
    },
    {
      "year": 2007,
      "num_of_deaths": 43945,
      "vehicle_count": 254.4,
      "venhicle_miles": 3032,
      "driver_count": 205.7,
      "death_rate_vehicles": 1.73,
      "death_rate_vehicle_miles": 1.45,
      "death_rate_population": 14.6
    },
    {
      "year": 2008,
      "num_of_deaths": 39790,
      "vehicle_count": 255.9,
      "venhicle_miles": 2976,
      "driver_count": 208.3,
      "death_rate_vehicles": 1.55,
      "death_rate_vehicle_miles": 1.34,
      "death_rate_population": 13.1
    },
    {
      "year": 2009,
      "num_of_deaths": 36216,
      "vehicle_count": 254.2,
      "venhicle_miles": 2957,
      "driver_count": 209.6,
      "death_rate_vehicles": 1.42,
      "death_rate_vehicle_miles": 1.22,
      "death_rate_population": 11.8
    },
    {
      "year": 2010,
      "num_of_deaths": 35332,
      "vehicle_count": 250.3,
      "venhicle_miles": 2967,
      "driver_count": 210.1,
      "death_rate_vehicles": 1.41,
      "death_rate_vehicle_miles": 1.19,
      "death_rate_population": 11.4
    },
    {
      "year": 2011,
      "num_of_deaths": 35303,
      "vehicle_count": 253.2,
      "venhicle_miles": 2950,
      "driver_count": 211.9,
      "death_rate_vehicles": 1.39,
      "death_rate_vehicle_miles": 1.20,
      "death_rate_population": 11.3
    },
    {
      "year": 2012,
      "num_of_deaths": 36415,
      "vehicle_count": 253.6,
      "venhicle_miles": 2969,
      "driver_count": 211.8,
      "death_rate_vehicles": 1.44,
      "death_rate_vehicle_miles": 1.23,
      "death_rate_population": 11.6
    },
    {
      "year": 2013,
      "num_of_deaths": 35369,
      "vehicle_count": 255.9,
      "venhicle_miles": 2988,
      "driver_count": 212.2,
      "death_rate_vehicles": 1.38,
      "death_rate_vehicle_miles": 1.18,
      "death_rate_population": 11.2
    },
    {
      "year": 2014,
      "num_of_deaths": 35398,
      "vehicle_count": 260.3,
      "venhicle_miles": 3026,
      "driver_count": 214.1,
      "death_rate_vehicles": 1.36,
      "death_rate_vehicle_miles": 1.17,
      "death_rate_population": 11.1
    },
    {
      "year": 2015,
      "num_of_deaths": 37757,
      "vehicle_count": 263.6,
      "venhicle_miles": 3095,
      "driver_count": 218.1,
      "death_rate_vehicles": 1.43,
      "death_rate_vehicle_miles": 1.22,
      "death_rate_population": 11.8
    },
    {
      "year": 2016,
      "num_of_deaths": 40327,
      "vehicle_count": 268.8,
      "venhicle_miles": 3174,
      "driver_count": 221.7,
      "death_rate_vehicles": 1.50,
      "death_rate_vehicle_miles": 1.27,
      "death_rate_population": 12.5
    },
    {
      "year": 2017,
      "num_of_deaths": 40231,
      "vehicle_count": 272.5,
      "venhicle_miles": 3212,
      "driver_count": 225.3,
      "death_rate_vehicles": 1.48,
      "death_rate_vehicle_miles": 1.25,
      "death_rate_population": 12.4
    },
    {
      "year": 2018,
      "num_of_deaths": 39404,
      "vehicle_count": 276.6,
      "venhicle_miles": 3240,
      "driver_count": 227.6,
      "death_rate_vehicles": 1.42,
      "death_rate_vehicle_miles": 1.22,
      "death_rate_population": 12.0
    },
    {
      "year": 2019,
      "num_of_deaths": 39107,
      "vehicle_count": 276.5,
      "venhicle_miles": 3262,
      "driver_count": 228.7,
      "death_rate_vehicles": 1.41,
      "death_rate_vehicle_miles": 1.20,
      "death_rate_population": 11.9
    },
    {
      "year": 2020,
      "num_of_deaths": 42338,
      "vehicle_count": 275.9,
      "venhicle_miles": 2904,
      "driver_count": 228.2,
      "death_rate_vehicles": 1.53,
      "death_rate_vehicle_miles": 1.46,
      "death_rate_population": 12.9
    },
    {
      "year": 2021,
      "num_of_deaths": 46980,
      "vehicle_count": 282.4,
      "venhicle_miles": 3140,
      "driver_count": 232.8,
      "death_rate_vehicles": 1.53,
      "death_rate_vehicle_miles": 1.38,
      "death_rate_population": 14.2
    },
    {
      "year": 2022,
      "num_of_deaths": 46027,
      "vehicle_count": 283.4,
      "venhicle_miles": 3196,
      "driver_count": 235.1,
      "death_rate_vehicles": 1.50,
      "death_rate_vehicle_miles": 1.33,
      "death_rate_population": 13.8
    }
  ]
}
{{< / highlight >}}

