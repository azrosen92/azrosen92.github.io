---
layout: post
title:  Zero to NextCloud
date:   2023-05-10 15:03:34 -0600
categories:
---

How I set up my first home server using Ubuntu Server and Nextcloud.

I started with an empty computer that I'd built a while ago. I built this computer a couple
of years ago as a hobby project. I used to have a custom built that I used for playing
Microsoft Flight Sim and really enjoyed the process of designing and building a machine.
Unfortunately I had no real plans for the computer this time around, and that ended up being my
downfall. It ended up sitting unused for about 2 years before I finally decided to turn
it into a home server.

Starting with an empty, custom built computer, I had to get an OS onto it. Ubuntu is probably
one of the most well-known linux distros, so I figured I would start there. In order to get it
installed on my machine, I had to [create a bootable USB stick, flashed with the Ubuntu Server
installer](https://ubuntu.com/tutorials/create-a-usb-stick-on-macos#1-overview). This is pretty
straightforward with a piece of software called [Balena Etcher](https://www.balena.io/etcher).

Once I had the bootable USB stick ready, I just plugged it into the machine, turned it on, and
held down the F11 key. Once the boot menu comes up, I selected my flash drive and the installer
walks you through the rest of the process from there. Ubuntu has a good overview of what the process
should look like on [their website](https://ubuntu.com/tutorials/install-ubuntu-server#1-overview).

One thing to note here is that you should set up your wifi network from the installer. I only mention
this because I missed this step while I was installing and it was a bit of pain to get working
afterwards. If you do need to set it up after installing, I followed [this article](https://www.makeuseof.com/connect-to-wifi-network-on-ubuntu-server/)
to do that.

At this point, I had a home server. I could find the IP address and ssh into it from my home network,
which was pretty cool! In order to make it a little more useful, I wanted to install Nextcloud so I could
backup all of my photos (Nextcloud has a lot of other cool cloud based applications built in, like calendars, email,
even social media, but I'm not gonna go into those because I only needed to store photos).

NextCloud has pretty comprehensive documentation on how to set up a server
[here](https://www.vultr.com/docs/how-to-install-nextcloud-on-ubuntu-22-04-with-snap/), but their docs aren't
as clear on how to open that server up to connections outside of your local network. I followed [this article
](https://www.vultr.com/docs/how-to-install-nextcloud-on-ubuntu-22-04-with-snap/)
to set up the configuration necessary to open my server up to the internet. I'll pause here to
mention the obligatory warning about opening up your server to connections outside of your home network. This
is inherently dangerous and you potentially open yourself and your devices to infection by malware and
DoS attacks. I'm no security or networking expert, so I'm learning all of this along the way as well. I can't
promise that this blog post will give you the safest server setup, but my goal is to make my own set up as safe
as I can reasonably figure out with my own research and hopefully you can improve upon it with some of your own
research!

At this point you'll likely need a domain name. A lot of people on reddit who set up Nextcloud on their home
servers will use a subdomain like `nextcloud.mydomainname.com`

Set up A Record to new domain name https://www.namecheap.com/support/knowledgebase/article.aspx/9837/46/how-to-connect-a-domain-to-a-server-or-hosting/#viaip

Open up port 80 on your home network to external traffic https://help.nextcloud.com/t/how-to-access-from-outside-your-network/126311

Future plans for this server
- Set up remote dev environment
- Media streaming server/remote torrenting
- Let me know if you have any other cool suggestions!
