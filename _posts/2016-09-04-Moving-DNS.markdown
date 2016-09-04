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

Before I pull the trigger and move the dns nameservers, let's setup the dns (so we don't go dark). So I clicked on the 'Manage Domain' link on the same page. Unfortunately, I did not see anything about setting up DNS records (some nonsense about redirecting and forwarding domain, parking page, etc). After some poking, I found the "Advanced DNS" section, that allows you to select actual DNS records. It's a one at a time sort of editor, reminiscent of many registrar's Host Records section for creating glue records. There is a filter optio n for the various entries, and they separate A from MX records, so if you have very limited needs it should be fine. I think I'll keep looking, as this looks painful to use.

Rackspace's cloud dns service advertises import from bind db files. That might be good. Heroku does not have these options, which would have been okay too. "Please note that Amazon Route 53 is not currently available on the AWS Free Tier." says [AWS](https://aws.amazon.com/route53/). That's a shame.

Searching a bit, it looks like Cloudflare is still a safe bet. Cloudns.net has a free offering that doesn't look too crippled (you can't change SOA, but if you're hosting dns there, what do you need?). They limit their free offering to three domains (I'll keep beta-reduction.com and djuber.tk so I think I'm fine). It looks viable. I'll probably setup cloudflare, and setup cloudns.net, and experiment. But that's starting to feel like at daytime activity.

