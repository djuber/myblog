---
layout: post
date: 2016-06-25 14:37 -0500
title: Second Test of new-post
categories: emacs
---

Okay, confirmed that newline literals `n` are interpreted as intended:

{% highlight lisp %}
(defun new-post ()
  (interactive)
  (let ((title (new-post-read-args "Post Title: ")))
  (switch-to-buffer
   (find-file
    (concat +blog-path+
	    (new-post-make-file-name title))))
  (beginning-of-buffer)
  (insert
   (concat "---\n" 
	   "layout: post\n"
	   (new-post-date-string)
	   "\n" "title: " title "\n"
	   "categories: \n"
	   "---\n"))))
{% endhighlight %}

So now I just do `M-x new-post Something I am thinking about`, type type type, and
`M-x publish`.