---
layout: post
title:  Weekend Plans
date:  2016-07-09 13:00:00 -0500
categories: journal
---

Working from my desk at home today, since Beth has a shop day. One fun project, make
a restful service that gives diffs of all versions of wordpress files. For any install,
for any file, determine if it's core, give the md5 sum, and optionally give the fulltext
so a diff can be made (to patch changes back to original state, or identify malware).

First, let's gather all the release files (there are only so many because this includes betas):

```bash
lynx -dump https://wordpress.org/download/release-archive/ |
grep tar.gz$ |
grep [0-9]\.\ htt
```

Maybe we don't care about the betas.

```bash
lynx -dump https://wordpress.org/download/release-archive/ |
grep tar.gz$ |
grep [0-9]\.\ htt |
grep -Ev 'beta|RC'
```

That gives 211 dotted versions from 0.71-gold to the current 4.5.3. That's a manageable data set, I think. Lets grab all the links for those. We'll process them as part of the data acquisition later.

```bash
mkdir wp-files
cd wp-files
for url in $(lynx -dump https://wordpress.org/download/release-archive/ |
    	   	  grep tar.gz$ |
		  grep [0-9]\.\ htt |
		  grep -Ev 'beta|RC' |
		  awk '{print $2}')
do
  wget $url
done
```

Hint: This is much faster on the server than on your desk.

Not terribly bad, not terribly light either:

```
[~/wp-files]# du -h
 736M	.
```

So what will this all look like? I'd like to use this as an opportunity to do something useable in rails, and [rails 5](http://weblog.rubyonrails.org/2016/6/30/Rails-5-0-final/) came out this week, so I think I'll use rails 5 to make this api. The client code will probably be python, because, well, python is already installed. But the server code could be in cobol for all anyone knows, as long as the product works.

My model will be that there are wordpress versions ```rails g scaffold wordpress version release_date:date```, and these versions have core files ```rails g scaffold core_file file_name md5sum size:integer content:text```. Lucky for me, these are nested resources, so the following route looks correct (in config/routes.rb):

```ruby
  resources :wordpresses, path: "wordpress" do
    resources :core_files
  end
```

So I added a release date, I could fudge and grab this from the readme, or grab the dates from [WP's Roadmap](https://wordpress.org/about/roadmap/) (for major releases only), or for 2.3 and later, the mtime from the releases download link matches the release date of the file (September 24, 2007). It looks like all of the earlier releases were added to the file server at that time, so the mtime on the archives is not usuable more than 9 years back. Well, I've seen much bigger projects lose information on a grander scale than this, so wordpress looks remarkably stable.

```
-rw-r--r--  1 sysadmin wheel  870766 Sep 24  2007 wordpress-2.3.tar.gz
```

Let's peel these open to get more information from them. This runs super fast upto about 2.8, until it slowed down I had imagined a bug in here was skipping the tar step and just creating directories.

```bash
for file in *.tar.gz
do
   wpver=$(echo $file | sed 's/.tar.gz//');
   mkdir $wpver
   pushd $wpver
   tar xf ../$file
   popd
done
```

```bash
[~/wp-files]# du -hs
3.4G	.
```