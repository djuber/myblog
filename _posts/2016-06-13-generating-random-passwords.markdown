---
layout: post
title:  "Generating lots of random passwords"
date:   2016-06-13 00:55 -0500
categories:  labnotes
---

I've seen some people that have a pwgen program that makes a pile of passwords for them. I always just use openssl, so I threw this solution together to emulate what I saw.

## passwords.sh
{% highlight bash %}
#!/bin/bash
 
LINES=10
COLS=4
STRENGTH=5
 
RANDLENGTH=$(( $STRENGTH * 3))
for line in $(seq 1 $LINES);
do
  OUTSTRING=""
  for col in $(seq 1 $COLS)
  do
    ADDSTRING=$(openssl rand -base64 $RANDLENGTH)
    OUTSTRING="$OUTSTRING $ADDSTRING"
  done
  echo $OUTSTRING
done
{% endhighlight %}

So this obvious example is really slow, or noticeable slow. Why is that? Lots of calls to openssl. We can do better I think.

{% highlight bash %}
time openssl rand -base64 1000 > /dev/null
real    0m0.007s

time for i in $(seq 1 100); do openssl rand -base64 10; done > /dev/null
real    0m1.333s
{% endhighlight %}

Lets instead call rand once, fill a big buffer, and read from it. Then any time spent is just IO.

## pwgen.sh
{% highlight bash %}
#!/bin/bash
LINES=10
COLS=4
STRENGTH=5
# end configuration area.         
                                                                     
RANDLENGTH=$(( $STRENGTH * 3))
BUFFERSIZE=$(( $RANDLENGTH * $COLS * $LINES))
BUFFER=$(openssl rand -base64 $BUFFERSIZE)
STRINGLENGTH=$(( $STRENGTH * 4 ))
 
echo $BUFFER |
sed 's; ;;g'  |
for line in $(seq 1 $LINES);
do
  OUTSTRING=""
  for col in $(seq 1 $COLS)
  do
    read -r -N$STRINGLENGTH ADDSTRING
    OUTSTRING="$OUTSTRING $ADDSTRING"
  done
  echo $OUTSTRING
done
{% endhighlight %}

Well, testing shows I did save some time here (passwords.sh is first script, pwgen is the second):

{% highlight bash }
time sh passwords.sh > /dev/null
real    0m0.611s
user    0m0.149s
sys    0m0.115s

time sh pwgen.sh > /dev/null
real    0m0.029s
user    0m0.008s
sys    0m0.017s

{% endhighlight %} 

If you're ever interested in speeding things up, not repeatedly seeding
an RNG is a really good start. Calling rand in a loop is painful.