---
layout: post
title:  "Object Orientation"
date:   2021-03-28 12:50:00
categories:
---

I sometimes wonder if I, having spent a fair amount of effort to 
understand how to work effectively with lisp, have become brain damaged
by ruby. When I was in college, I remember getting an assignment dealing
with a SAT solver, and the people who came from a java background asked
if there was a library to ingest the clause document format.

I just wrote a dirty parser to pull in lines and generate clauses instead
and wondered if this dependence on other code was a problem tied to their
education or to the habits their language gives them.

I've worked a lot with ruby lately, and will continue working with a mix 
of ruby and javascript for the foreseeable future. I don't know what that
means but I feel like I am needing to make a concerted effort to either
get really deep into fundamentals or work in really different languages.

I'd love to love clojure, but I can't care enough about it to overcome the difference in tooling. I think I'll try to use ocaml to solve either euler or advent of code problems, and I have access to the competitive programming books (which assume c++ generally) and might take a look at those. I need to be more comfortable with javascript than I am, but I wonder if the kind of language javascript is becoming (declarative, modular, strict, typed) and the kind of language javascript was (function and value based, prototypical, permissive) are at odds with one another. 

I had exposure to typescript at my last job. I didn't make use of that and treated it as a funny tooling in front of JS. That's probably fine for some kinds of maintenance, I understand you can't work or think that way in development.

I feel as though 5 years ago I thought of programming in terms of building enough definitions (the way a math proof flows) and the functions really just as definitions, and message sending paradigms have changed the way I _think_ about code, the way I think about problem decomposition, and I'm not sure it's for the better. 

One thing I will say for Ruby though, they did go all in on aesthetics (don't look too closely at some of the standard library), user programs are typically really good at being close to the domain and clutter free. 

I think languages that allow users to move the boilerplate/annotations out of the source files, and encourage small code artifacts (in ruby that's a class or module, but in other languages it could be a module or package or something). I'm alluding to the distinction between declaration/definition - forward declarations of functions and their type information, while repeated within the definition in C, is shared via the header files, and typedefs are almost only ever in headers, I think ML may use interfaces to describe this, and common lisp at least settled on a common pattern of "package information goes local to the package, system information goes elsewhere" to ensure a file is a compilable unit without sequencing (definitely not needed, if you wanted to spread a package over multiple files you could, put an `in-package` at the beginning of each file, and ensure the file defining the package and its exports is loaded before the others).

