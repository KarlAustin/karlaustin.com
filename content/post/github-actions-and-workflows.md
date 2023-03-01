7+++
date = "2023-02-28"
draft = false
title = "Github Actions and Workflows"
categories = [ "Development" ]
tags = ["me", "dev", "git", "github"]
+++

As per my [Hello Again]({{< ref "hello-again.md" >}} "Hello Again") post I'm looking at automating some of my content creation.

## The current state of play
If you've looked before, you'll know I use {{< url-link "Hugo" "https://gohugo.io" >}} to build my site.

That means I need to ask Hugo to build my site whenever I add some more content, which currently means:

 1. Get Hugo to build the content
 2. Update my git repo with the new content
 3. Push the repo to GitHub
 4. GitHub fires a webhook that tells my site to pull down new content

Don't shoot me, this was all build pre-workflow days.

## The plan
Have a workflow to do all of the above for me

### Step 1
Get it to build and just fire the current webhook.

Wish me luck with it.

#### Attempt 1
Fail.  I forgot my repo is so old that it was stil called master and not main, and I'd put main in my workflow action.

#### Attempt 2
Fail.  A few gotchas it seems with the examples given online for running Hugo builds as workflows.  I needed to add:

```
permissions:
  packages: read
```

To my workflow file for it to be able to pull down the docker image.

#### Attempt 3
Fail-ish.  Ooops, only had it build an artefact to attach to the build, not actually commit the public directory back to the repo.

#### Attempt 4
Success-ish.  All built and committed back to the repo, but for some reason my webhook for deploy is failing :/
