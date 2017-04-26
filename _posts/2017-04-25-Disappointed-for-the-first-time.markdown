---
layout: post
date: 2017-04-25 21:18 -0500
title: Disappointed for the first time
categories: 
---
I've been playing with Ruby a bit in the last year.
One thing that is never shown in the documentation,
and perhaps is never considered useful style, is
assignment of a block to a variable.
It turns out you can pass a block as an argument to methods that
expect one, but can't use the `{ |vars| actions }` literal syntax,
nor the `do .. end` syntax to define what feels like a lambda
from the block.

I was blissfully unaware of this limitation until reading the pickaxe book
I came across the sentence:

"All you can do with a block is associate it with a call to a method."

I immediately opened an irb session and tried to assign to `aBlock`.
Errors popped up everywhere. It seems like a context sensitive assignment
is happening in the parser when a curly brace is seen in a block context (start
a block) and another one is happening when a curly brace is seen outside (start a
hash/map/dictionary).

Well, I sure don't like magic syntax that's constrained. Blocks are still immensely more comfortable than lambda's in python, but not fully capable, like blocks in Smalltalk, or lambdas in lisp.