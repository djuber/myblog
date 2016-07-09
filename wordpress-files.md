---
layout: page
title:  Wordpress Files
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

## download

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

## extract

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

## tidy

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

## versions-release

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

So now what. Rails aside, we want to get these version strings, and release dates, into the database. Directly concerning rails, while Active Record wants an integer/serial id column, we want our urls to look like /wordpress/4.5.3/core_files/index.php and not like /wordpress/201/core_files/89, which is okay if you are making a lot more round trips -- fetch available versions, get id number for the one you need, fetch core files, and get details for the ones you need. For a human friendly view, wordpress/4.5/core_files/index.php looks correct 

## populating data by trial and error

The next step was to fire up a development copy on the server, since running this from my desk was useful, until all the data I want is on the server. So I'll be using the server (where I have all the files) as the development instance. It's in [github](ttps://github.com/djuber/wpfiles) now, so shuttling between the desk and server should be okay. And if you're me and are whitelisted in the firewall, it's [online](http://beta-reduction.com:3000/).

So how to put all this into the database? As a test I inserted the first version, and checking the logs, I see this was the transaction:

```
started POST "/wordpress" for 216.80.113.156 at 2016-07-09 14:58:27 -0500
Processing by WordpressesController#create as HTML

Parameters: {"utf8"=>"✓", "authenticity_token"=>"2V1t4hTnMM8smG2YBzRH4eUNLhU8nS3wzspJxoWjnohnQg0iqiRKvAknd99RfeK+0IgJLZ+7VNwM/aOt/yrzfw==", "wordpress"=>{"version"=>"0.71-gold", "release_date(1i)"=>"2003", "release_date(2i)"=>"6", "release_date(3i)"=>"9"}, "commit"=>"Create Wordpress"}
```

Let's try to skip the utf8 and authenticity_token, and just put this into curl. My first attempt obviously was a failure:

```bash
[~/wp-files]# for line in $(sed 's/ /=/' versions-release.txt)
do
   version=$(echo $line | cut -f 1 -d =);
   year=$(echo $line | cut -f 2 -d = | cut -f 1 -d -);
   month=$(echo $line | cut -f 2 -d = | cut -f 2 -d -);
   day=$(echo $line | cut -f 2 -d = | cut -f 3 -d - );
   curl -v -X POST -d "version=$version" \
   -d "release_date(1i)=$year" \
   -d "release_date(2i)=$month" \
   -d "release_date(3i)=$day" \
   beta-reduction.com:3000/wordpress/
done
```

Rails console messages were pretty clear it did not want anything to do with these random POST requests:

```
Started POST "/wordpress/" for 192.138.20.248 at 2016-07-09 15:05:30 -0500
Cannot render console from 192.138.20.248! Allowed networks: 216.80.113.156, 127.0.0.0/127.255.255.255, ::1
Processing by WordpressesController#create as */*
  Parameters: {"version"=>"4.5.3", "release_date(1i)"=>"2016", "release_date(2i)"=>"06", "release_date(3i)"=>"21"}
Can't verify CSRF token authenticity.
Completed 422 Unprocessable Entity in 1ms (ActiveRecord: 0.0ms)



ActionController::InvalidAuthenticityToken (ActionController::InvalidAuthenticityToken):

actionpack (5.0.0) lib/action_controller/metal/request_forgery_protection.rb:195:in `handle_unverified_request'
actionpack (5.0.0) lib/action_controller/metal/request_forgery_protection.rb:223:in `handle_unverified_request'
actionpack (5.0.0) lib/action_controller/metal/request_forgery_protection.rb:218:in `verify_authenticity_token'
```


So inspecting the /wordpress/new page, there are those fields in the request, utf8, authenticity_token, and commit="Create Wordpress", the first two as hidden inputs , the last as the commit action (submit button). Do I need to grab this authenticity token for each post? Should I be using seeds.rb instead for this (you might say yes, you might be right). Let's continue down the resisting path and loop this curl one more time.

Grab the token:

```bash
[~/wp-files]# curl -s beta-reduction.com:3000/wordpress/new | grep authenticity_token
    <meta name="csrf-param" content="authenticity_token" />
<form class="new_wordpress" id="new_wordpress" action="/wordpress" accept-charset="UTF-8" method="post"><input name="utf8" type="hidden" value="&#x2713;" /><input type="hidden" name="authenticity_token" value="DHgA5Wynz0ULvKYaHoFs8T6pcHBqNJ5MoLlFmoacf7dS+q2yDGAhQPWAft1ZN1eqjdCP7tdp/UWZleVPoHvwng==" />

get_authenticity_token() {
    curl -s beta-reduction.com:3000/wordpress/new |
    grep authenticity_token |
    cut -d \" -f 22 |
    grep =
}

for line in $(sed 's/ /=/' versions-release.txt) ;
do
   version=$(echo $line | cut -f 1 -d =);
   year=$(echo $line | cut -f 2 -d = | cut -f 1 -d -);
   month=$(echo $line | cut -f 2 -d = | cut -f 2 -d -);
   day=$(echo $line | cut -f 2 -d = | cut -f 3 -d - );
   curl -v -X POST \
   -d "version=$version" \
   -d "release_date(1i)=$year" \
   -d "release_date(2i)=$month" \
   -d "release_date(3i)=$day" \
   -d "authenticity_token=$(get_authenticity_token)" \
   -d "utf8=&#x2713;" \
   beta-reduction.com:3000/wordpress/
done
```

Rails can't be fooled? Did the cookie disappear or some similar silliness? 

```
Started POST "/wordpress/" for 192.138.20.248 at 2016-07-09 15:24:28 -0500
Cannot render console from 192.138.20.248! Allowed networks: 216.80.113.156, 127.0.0.0/127.255.255.255, ::1
Processing by WordpressesController#create as */*
  Parameters: {"version"=>"3.7.13", "release_date(1i)"=>"2016", "release_date(2i)"=>"02", "release_date(3i)"=>"02", "authenticity_token"=>
"WzbIiE7lBzvKbU4vb4P61CCc1uc563 wuM mQU9Erha8RYUburGdnSsRsmxaJGqkc1LBIoiuSx8VB6X8XAoqow==", "utf8"=>"", "#x2713;"=>nil}
Can't verify CSRF token authenticity.
Completed 422 Unprocessable Entity in 1ms (ActiveRecord: 0.0ms)



ActionController::InvalidAuthenticityToken (ActionController::InvalidAuthenticityToken):
```

Redoing my test via the browser, and checking the console:

```
Started POST "/wordpress" for 216.80.113.156 at 2016-07-09 15:27:12 -0500
Processing by WordpressesController#create as HTML
  Parameters: {"utf8"=>"✓", "authenticity_token"=>"yKlW3YPN+OS6RrSDeXaV4Y4i7TVEMtIo2/ZIN3dPCQicBiqJSNSPc76lVq6YDaYv1aNtsGxd+FtgGm4u0xXC9g==", "wordpress"=>{"version"=>"0.71-gold", "release_date(1i)"=>"2003", "release_date(2i)"=>"6", "release_date(3i)"=>"9"}, "commit"=>"Create Wordpress"}
   (0.1ms)  begin transaction
  SQL (0.4ms)  INSERT INTO "wordpresses" ("version", "release_date", "created_at", "updated_at") VALUES (?, ?, ?, ?)  [["version", "0.71-gold"], ["release_date", Mon, 09 Jun 2003], ["created_at", 2016-07-09 20:27:12 UTC], ["updated_at", 2016-07-09 20:27:12 UTC]]
   (17.9ms)  commit transaction
Redirected to http://beta-reduction.com:3000/wordpress/2
Completed 302 Found in 24ms (ActiveRecord: 18.4ms)
````

So that worked. Right now I have two options. I can figure out how to get around "Request Forgery Protection" from the trace:
```
actionpack (5.0.0) lib/action_controller/metal/request_forgery_protection.rb:223:in `handle_unverified_request'
```
or I can go back to seeds.rb, which seems like it's going to be so much easier. So lets rewrite our loop to output a seeds.rb style list of version and dates, rather than watching the stack traces continue. So here's a cheap (and [horribly unattractive](https://raw.githubusercontent.com/djuber/wpfiles/146219198801985d1f9fc8926c61e05c9cc58876/db/seeds.rb) seeds generation:

## populating data with seeds.rb

```bash
 [~/wp-files]# for line in $(sed 's/ /=/' versions-release.txt) ;
 do
    version=$(echo $line | cut -f 1 -d =);
    date=$(echo $line | cut -f 2 -d = );
    echo Wordpress.create\!\(version: \""$version"\", release_date: \""$date"\"\)
done >> ../src/wpfiles/db/seeds.rb
```

This did work, and ```rails db:seed``` populated the versions at my [development site](http://beta-reduction.com:3000/wordpress). Now we can focus on nesting the core_files resource into the view, and controller.

Since I'm about 3 hours in right now, time to get some weekend beach hours in.