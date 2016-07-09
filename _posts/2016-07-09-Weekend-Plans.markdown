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

Cursory inspection of the file tree suggests that most of the time the archive contained a directory 'wordpress' at the head of the tree, but in some cases (mu releases before 3.0, and the named releases platinum, blakey, and miles, each had wordpress-mu (2.7 to 2.9.2) or wordpress-mu-VERSION (up to 2.6.5) or wordpress-version-NAME (platinum, miles), or the weirder wordpress-VERSION (blakey). I'll fix all of these, since they bother me. Now, no matter how it was packaged, we can have a single expected directory name, wordpress-VERSION/wordpress/

```bash
 for file in *.tar.gz
 do
   wpver=$(echo $file | sed 's/.tar.gz//')
   pushd $wpver
   if [ ! -d ./wordpress ]
   then
	dirname=$(/bin/ls)
	mv -v $dirname wordpress
   fi
   popd
done
```

Okay, so we now have what I think we want. We want to map these into a release version string ("4.5.2" or "0.7.1-gold"), and into a release date. I see ```ls -ld */wordpress``` gives a workable list of dates, lets fiddle just a bit with that. Rather than trying to force ls to be nice (there is a command line option for that), I took stat's output for the modify time:

```bash
[~/wp-files]# for dirname in */wordpress
 do
    details="$(stat $dirname)"
    basename=$(echo "$details" |
    		    grep File  |
		    cut -f 2 -d \‘ |
		    cut -f 1 -d / |
		    cut -f 2- -d -  )
    dateline=$(echo "$details" |
    		    grep Modify |
		    awk '{print $2}' )
    echo $basename $dateline;
done |
sort -k 2 -n > versions-release.txt
```

This gives a file looking like something I can use for importing into the database:

```
4.2.9 2016-06-21
4.3.2 2016-01-06
4.3.3 2016-02-02
4.3.4 2016-05-06
4.3.5 2016-06-21
4.4.1 2016-01-06
4.4.2 2016-02-02
4.4.3 2016-05-06
4.4.4 2016-06-21
4.5.1 2016-04-26
4.5 2016-04-12
4.5.2 2016-05-06
4.5.3 2016-06-21
```