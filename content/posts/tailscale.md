---
title: "Setting up a private network with Tailscale"
date: 2022-12-30T17:00:00-08:00
draft: false
tags: ["tailscale"]
---

I came across [Tailscale](https://tailscale.com/) recently. It's a tool that lets you build a private mesh network between your devices easily.

My use case was to transfer data from my laptop(macbook) to my android phone. Many times I have a pdf doc that I have on my laptop that I wish to read on the go. I usually just email it to myself and download it to my phone, but wanted to see if I can use Tailscale for it. 

To start off, first install tailscale on both your android device and macbook. (Installation instructions are on the tailscale website)

As soon as tailscale is installed on android, it gets assigned a unique 100.x.y.z IP (by a centralized coordination server at login.tailscale.com) and is called a _node_.

You can actually test that this IP works:

`ping 100.x.y.z -t 4`

Now install tailscale on your macbook. If you open the admin console you will see a second IP appear i.e. both _nodes__ are now on a private network.

{{< figure src="/images/admin_console_tailscale.jpg">}}

I then created a dummy file called `hello_tailscale.txt` on the macbook. You can use [Taildrop](https://tailscale.com/kb/1106/taildrop/) to copy that file between your devices. Just right click > share. Boom ! The file is now on my android device !

{{< figure src="/images/hello_tailscale.jpg">}}

Under the hood, after authentication, when each device is added to the tail network, the auto generated public key is added to the tailscale co-ordination server. This public key is used to identify the device i.e node on the private network. After that, data is directly transferred peer-to-peer between nodes using the auto generated private key. (Note that the private key never leaves the node and data transfer happens peer-to-peer). This is also called zero trust networking. The control pane i.e. coordination server is used only to store public keys of the nodes, domains and some other metadata which is downloaded by all the nodes.

There is a lot more you can do with Tailscale. Head over to the [docs](https://tailscale.com/kb/) if you are curious.





