---
layout: post
title:  "testing the new-post function"
date:  2016-06-25 14:08 -0500
categories: emacs
---


{% highlight lisp %}
(defun new-post ()
  (interactive)
  (let ((title (new-post-read-args "Post Title: ")))
  (switch-to-buffer
   (find-file
    (concat "/home/djuber/src/myblog/_posts/"
	    (new-post-make-file-name title))))
  (beginning-of-buffer)
  (insert "---")
  (newline)
  (insert "layout: post")
  (newline)
  (insert "date: " (new-post-today-string))
  (newline)
  (insert "title: " title)
  (newline)
  (insert   "categories: ")
  (newline)
  (insert "---")))
{% endhighlight %}
