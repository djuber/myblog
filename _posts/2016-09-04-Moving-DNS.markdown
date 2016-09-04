---
layout: post
date: 2016-09-04 00:17 -0500
title: Moving DNS
categories: 
---
So I've been using the nameservers at my employer for the past three years. In preparation for leaving, I'll be migrating them. Since the first and most important thing about
internet hobbies is that they cost very little, I was looking for the best way to manage these. Of course, I can use the registrar to manage the zones. That doesn't sound totally painful. I could use Digital Ocean. I think they're not very picky about your dns setup, and don't enforce the traffic goes to their nodes. I could use cloudflare, which is what I do for my wife's domains, but I get the feeling cloudflare is looking out for someone else.

I will be testing namecheap's FreeDNS service. I already have an account with them, so that helps. I registered my wife's domains there, after feeling like I had paid too much to do this with Hover. I'm still 100% happy with hover, and if they get an extra penny a day from me for my laziness, congratulations. I have never felt like they were sneaking deals in to entice me to spend money, and I'll pay a premium to not look at ads.

Once you sign up, they "add to cart" the domain service at $0.00 and print out your receipt:

Congratulations! Your domain/ sub-domain is added to our DNS service.
Please set the nameservers of your domain/ sub-domain to

*    freedns1.registrar-servers.com
*    freedns2.registrar-servers.com
*    freedns3.registrar-servers.com
*    freedns4.registrar-servers.com
*    freedns5.registrar-servers.com

Before I pull the trigger and move the dns nameservers, let's setup the dns (so we don't go dark).