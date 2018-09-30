---
layout: post
date: 2018-09-29 23:38 -0500
title: macro of the day- whichever
categories: lisp
---

I started reading the sources and docs for [Alexandria](https://common-lisp.net/project/alexandria) - and stumbled across the odd looking `whichever`.

It's a macro that takes a list of forms, and selects only one, at random, from the forms, and evaluates it.

A good example of this is present in the arnesi test suite (alexandria's own tests just assert that one of a b or c are the result, this incf trick expresses the intent that only one path was evaluated):

```lisp
    (let ((result 0))
      (is (member (whichever (progn (incf result) 'a)
                             (progn (incf result) 'b)
                             (progn (incf result) 'c))
                  '(a b c)))
      (is (= 1 result)))
```

The definition is a little less pretty but might be interesting [at gitlab](https://gitlab.common-lisp.net/alexandria/alexandria/blob/ada02b061aac7cbbf43145beaf5d27d78f9e9fcc/control-flow.lisp#L50-68)

```lisp
(defmacro whichever (&rest possibilities &environment env)
  "Evaluates exactly one of POSSIBILITIES, chosen at random."
  (setf possibilities (mapcar (lambda (p) (macroexpand p env)) possibilities))
  (if (every (lambda (p) (constantp p)) possibilities)
      `(svref (load-time-value (vector ,@possibilities)) (random ,(length possibilities)))
      (labels ((expand (possibilities position random-number)
		 (if (null (cdr possibilities))
		     (car possibilities)
		     (let* ((length (length possibilities))
                            (half (truncate length 2))
                            (second-half (nthcdr half possibilities))
			    (first-half (butlast possibilities (- length half))))
		       `(if (< ,random-number ,(+ position half))
                            ,(expand first-half position random-number)
                            ,(expand second-half (+ position half) random-number))))))
	(with-gensyms (random-number)
          (let ((length (length possibilities)))
            `(let ((,random-number (random ,length)))
               ,(expand possibilities 0 random-number)))))))
```


I checked the other sources I have - while I can see what this does (and can speculate how you might use this) - the only places I see this exercised (in the software I have downloaded from the quicklisp dist) is in its test suites. It looks like a simple example of an evenly weighted non-deterministic evaluator - I think that this might present a solution for a "choose" or "amb" case.

One thing to note is that the call to setf wraps each possibility in a lambda calling macroexpand, so we can defer expansion. The manual implementation of nth as expand (using bisection) I assume is built around ensuring we're not evaluating this list - I'm not 100% sure why we can't do that safely. I would be interested to see how macroexpand looks on this.
