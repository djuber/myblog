---
layout: post
date: 2017-09-01 22:07 -0500
title: Reinventing the Wheel... again
categories: web
---

I saw an issue with div tags on a file that did not line up. I normally would fix this issue by aggressively reindenting the entire file, and looking for the problem. The other way to usually get close to this is to just view source and look for the red tags (they're closing the wrong thing). I hesitated to do this in todays case since the commit logs would be a mess, I didn't configure my indent to match the company standard yet, and emacs sometimes needs hints about what gets an indent (does the `a` body of an `li` tag on a separate line need to be indented like a tag or flush like a paragraph?).

I ended up hacking together this monster. May the gods forgive me.

```
balance () 
{ 
    TAG=$1;
    OTAG="<$TAG";
    CTAG="</$TAG";
    for file in $(find . -name "*.html.erb");
    do
        OPENS=$(grep "$OTAG" $file | wc -l);
        CLOSES=$(grep "$CTAG" $file | wc -l );
        if [[ "$OPENS" != "$CLOSES" ]]; then
            echo $file $OPENS $CLOSES;
        fi;
    done
}
```