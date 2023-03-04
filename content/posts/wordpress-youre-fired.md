+++
date = "2016-03-27"
draft = false
title = "Wordpress, you're fired!"
categories = [ "Websites" ]
tags = ["website", "cms", "wordpress", "software", "development"]
+++

## The Honeymoon Is Over
Let's get this out of the way now. I like Wordpress, I like how quick it is to get a site up and running and how easy it is to add functionality to a site.  It's going though.

I've been using Wordpress since around version 1.5, as have many of our customers at KDA.  Whilst Wordpress is supposed to make things easier, it doesn't really.  It just swaps some things you need to do, for another set of things you need to do instead.

Instead of needing to know how to write some HTML, you now need to know how to use all the features of Wordpress, which plugins work with other plugins and how to tweak everything to get the best out of it - or you need to pay someone to do it for you.

### Popularity Has It's Problems
Wordpress powers roughly  25% off all websites on internet and due to that popularity it has every hacker out there looking at it and trying to find a weakness.  When you consider the volume of plugins and themes as well as the core of Wordpress that's an awful lot of places to find a way in to your website.

If you don't remember to keep Wordpress up to date plus any plugins and themes (even if you're not using them) then you will be hacked.  It isn't *if* you will be hacked it is **when** you will be hacked, even if you do try and keep everything up to date by the time you know there's an update it could be to late already.  Been there, *done* that, got the t-shirt.

Using some obscure system that no one has heard of is no better, security by obscurity is not security at all.  It's the software equivalent of putting your fingers in your ears and shouting, "LA LA LAAA I CAN'T HEAR YOU".

### Install And Forget
The big problem we find at KDA is that a lot of web designers will setup a website for their customers using Wordpress, because the customer wants a site they can update themselves, then they don't provide any training or advice on how to maintain the website going forwards though, so most people get their shiny new website and then that's it.

The day it is handed over is usually the last time it ever gets updated right up until the point it is hacked and we have to restore the account from a backup, get it upgraded and hope the designer didn't do anything stupid that will break it.

#### No Sane Defaults
Unfortunately Wordpress doesn't come with any sane defaults that would stop 99% of hacks in their tracks.  More often than not, a Wordpress site is exploited by someone uploading a PHP or Perl script to the server via the upload functionality, so you end up with some PHP or Perl code inside the `wp-content/uploads` directory which of course is publicly accessible so the hacker just pops along to your site and executes the code.  50ms later they've got control of your site and any others in the same account.

It wouldn't take much for Wordpress to fix this, I can't think of single legitimate reason for PHP or Perl code to be in the uploads folder.  So they could generate a `.htaccess` on install (or prompt to create access controls on other platforms) that does something like:

```
<FilesMatch ".(php[45]?|phtml|pl|cgi)$">
 Deny from all
</FilesMatch>
```

Which would stop access to any files with common extensions for code.  Better still, they could create a wizard that asks you what type of content you'll be uploading (and create an API for plugins to register what content they allow) and then create something like this instead:

```
<FilesMatch ".*">
 Deny from all
</FilesMatch>

<FilesMatch ".(jpe?g|gif|pdf|png)$">
 Allow from all
</FilesMatch>
```

Which would only allow access to `.jpg`, `.jpeg`, `.gif`, `.png`, `.pdf` files.  It should still of course raise a red flag if you try and allow `.php`, `.pl` etc.

#### A Plugin Could Do That
I'm sure someone could implement a plugin to do this, but again it comes down to something lacking in the core of Wordpress.  There isn't enough emphasis on stopping stupid security blunders from happening and shutting down 99% of exploits.

It's *incredibly* frustrating.

### But Surely You Update
Yes, I can keep on top of my sites, as I see the pain it brings pretty much every week to customers, but even I've been caught out before by being a couple of hours too slow.  I am getting fed up of keeping on top of it all for multiple sites when more often than not it is overkill.

Yes Wordpress makes it easy to add all sorts of features to a site, but all those features inevitably slow the site down and add to the maintenance burden.  I don't know about you, but I just want to write content for my sites, not have to keep updating everything every five minutes.

### Won't Somebody Think Of The Children?
A little thought project for you.  If you assume 75% (I bet it's more) of all website running Wordpress are nothing more than what you'd typically call Brochure-ware then how much server processing is being wasted on making these sites available?  How many extra servers does that add up to? The electricity?  It's quite scary really when you start to think about it.

## My Solution
I'm going back to a static HTML/CSS website for several reasons:

* I can write content offline easily
* It's simpler to maintain the site from a security perspective
* No constant updates to watch out for and install
* It's much much faster for end users without resorting to extra software such as Varnish
* I can keep track of edits and versions with git, something I use every day, no 3rd party Wordpress plugin required - that might stop working!

### Not Completely Technology Free
I'm not going back to the dark ages completely, I am using a static site generator that supports themes etc. to help me generate the content on my laptop.  I am using {{< url-link "Hugo" "https://gohugo.io" >}} which is written in {{< url-link "Go" "https://golang.org" >}} so it isn't for the feint hearted I suppose - Although I don't think it'd take all that much training to be able to teach someone to use it once installed on their PC.

I picked Hugo as I already have Go installed on my system, I like Go and I like the simplicity of it all as well as it being actively worked on.  To help with content creation it has it's own built in web server so I can preview changes as soon as I hit save in my editor - by the time I've switched back to my browser it's already refreshing with the latest content.

I'm using WebStorm from JetBrains as my editor as it's what I already use for my coding.  All I have to do to get my site live is tell it to upload any changes from my `public` folder (that's where I've told Hugo to save a copy of the generated site to) and it gets pushed out to here.
