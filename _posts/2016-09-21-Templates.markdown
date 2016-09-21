---
title: Templates and Me
layout: post
---

I often receive a lot of interesting group updates and invitations from meetup.com. 
A lot of the groups seem like they exist because meetup makes it attractive, 
as a basic part of their business, to create these. Since I work
weekdays second shift, that's basically been an impossible proposition. 
I can go to things on Saturdays, but the mainstay of meetup, and most users groups, 
has been the 6PM weekday monthly meeting. I don't think I've been to a chipy meeting 
since I left the 9-5 training period in 2013, and have made it to the Chicago C/C++ 
Users Group meeting exactly once, when they hosted RMS at Loyola. 

I signed up for the 'Chicago SQL' group, and received an email from meetup.com welcoming me. 
It was interesting mainly because the group owner seems to have only checked the html portion. 
I saw a preview/notification from thunderbird that showed 'Welcome to {CHAPTER NAME} ' in it, 
and suddenly I was interested.

View source sadly had two base64 encoded blocks, but just hoping emacs would have eveything I needed, 
I grabbed the one marked ```Content-Type: text/plain; charset=UTF-8``` and pasted it into a new buffer 
called 'unbase'. 
Immediately after pasting, I hit M-x and typed base. 
Tab completion gave me just what I wanted, base64-decode-region, which revealed the secret message:

 Welcome to {CHAPTER_NAME} (http://www.meetup.com/Chicago-SQL/?rv=wm1)

Well, score 1 for emacs already having text conversion when I wanted it. 
And as I had just been reading through the action mailer section of the Rails 4 in Action book
(which I returned the paper copy this afternoon before work) 
it stuck out that this looked like a simple template that might have been missed.

If you're more interested in meetup.com's internals, the Message-ID header showed this:
 177993518.1474437178687.JavaMail.root@jobs2.meetup.com
 
 One of the Received headers suggests they're using [power mta](https://port25.com/), 
 the message starts in New York and is passed to Philadelphia with this hostname: pmta1.phi.meetup.com
 
 
