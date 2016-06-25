---
layout: post
title:  "testing the new-post function"
date:  2016-06-25 14:08 -0500
categories: emacs
---

So this is the first time I've tried to make a "new file from template" type function.
Pretty ugly. Couple the fact that I had to look up in info or apropos every elisp
function other than "concat" with my unwillingness to experiment with
newlines as data and you get a fairly unattractive text builder.


{% highlight lisp %}
(defun new-post-read-args (prompt)
  (read-string prompt))
{% endhighlight %}


{% highlight lisp %}
(defun new-post-today-string ()
   (format-time-string "%Y-%m-%d" (current-time)))
{% endhighlight %}

{% highlight lisp %}
(defun new-post-make-file-name (title)
  (concat
   (new-post-today-string)
   "-"
   (replace-regexp-in-string " " "-" title)
   ".markdown"))
{% endhighlight %}

{% highlight lisp %}
(defun new-post-date-string ()
  (concat "date: "
	  (new-post-today-string)
	  (format-time-string "%H:%M -0500" (current-time))))
{% endhighlight %}

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
  (insert "date: " (new-post-date-string) 
  (newline)
  (insert "title: " title)
  (newline)
  (insert   "categories: ")
  (newline)
  (insert "---")))
{% endhighlight %}
