---
layout: post
date: 2017-09-06 22:49 -0500
title: Reading is Fun-damental
categories: 
---

So I was unhappy with the way the [trailblazer](http://trailblazer.to) [guide](http://trailblazer.to/gems/trailblazer/2.0/rails.html) was introducing pages without proper elements (you know, the doctype and head elements?). While it's admirable that the browser will just give you what a naked `div` should look like maybe, it's something that makes me a little sad. I spent a brief time fixing up my controllers to render the cell to string, pass as a variable to the view, and called raw on the code to insert it.

I read the guide through again, and see I either skipped over reading or did not understand the import of this line:


> You simply invoke cell the way you did it before, and pass it to render. Per default, render will add layout: true to render the ActionView layout. It can be turned off using layout: false.

Well, all of their early examples have layout false (they're probably seeking to draw attention to the Cell output in isolation), I only had to remove the layout override, restoring the default, and all would be well (and I wouldn't need to write a view page for the action in addition to the cell, which was making me feel like I was doing too much work).

I'm back to being happy again.