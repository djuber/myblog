---
layout: post
title:  "It Took Them Long Enough"
date:   2021-01-10 18:50:00
categories: web2py
---

For years I've been operating a [web2py](https://www.web2py.org) based site with a few contact request forms. I used Recaptcha, then upgraded to
recaptcha2 when the v1 api was discontinued by google.

In the last few weeks only, we started to see abuse of the form (unsolicited spam, more or less). Experimenting a little,
I realized that while I was presenting the captcha prompt (I'm not a robot challenge button), I was failing to validate it. The form would
submit, process, send me an email, and then display the "Invalid" error next to the recaptcha element.

The fix was adding a conditional check for form.process().accepted, where I previously only had form.process().

It's amusing that I had this issue on some linked but non-primary pages for years, and the spam only started lately.

It's depressing that I probably had been sending human users through this hoop for no effect for years.

