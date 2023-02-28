+++
date = "2023-02-28"
draft = false
title = "Github Actions and Workflows"
categories = [ "Development" ]
tags = ["me", "dev", "git", "github"]
+++

As per my [Hello Again]({{< ref "hello-again.md" >}} "Hello Again") post I'm looking at automating some of my content creation.

# The current state of play
If you've looked before, you'll know I use {{< url-link "Hugo" "https://gohugo.io" >}} to build my site.

That means I need to ask Hugo to build my site whenever I add some more content, which currently means:

 1. Get Hugo to build the content
 2. Update my git repo with the new content
 3. Push the repo to GitHub
 4. GitHub fires a webhook that tells my site to pull down new content

Don't shoot me, this was all build pre-workflow days.

# The plan
Have a workflow to do all of the above for me

# Step 1
Get it to build and just fire the current webhook.

Wish me luck with it.

# Attempt 1
Fail.  I forgot my repo is so old that it was stil called master and not main, and I'd put main in my workflow action.
