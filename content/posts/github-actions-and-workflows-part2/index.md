+++
date = "2023-03-06"
draft = false
title = "Github Actions and Workflows - Part 2"
categories = [ "Development" ]
tags = ["me", "dev", "git", "github"]
series = "Github Workflows"
+++

## The new state of play
I've had my workflow working now, building the site quite nicely, although with a few more little gotchas along the way, and with some new added features for my situation.

## Gotchas
### Git Submodules
This one caught me out, whilst I was developing some shortcodes I made a fork of the [Poison](URL "https://github.com/lukeorth/poison") theme which, as per the original theme, I include using git submodules:
```bash
git submodule add https://github.com/lukeorth/poison theme/poison
```
I noticed that my site as deployed life didn't include the changes in my fork.  This was down to my workflow checkout not including recursive submodule checks.
```yaml {linenos=table,hl_lines=3,linenostart=20}
- uses: actions/checkout@v3
  with:
  submodules: recursive  # Fetch Hugo themes (true OR recursive)
  fetch-depth: 0    # Fetch all history for .GitInfo and .Lastmod
```
Once I've made that change, the submodule updates were included properly.

### Caching
This one isn't really Hugo's fault, but mine as this site sits behind Cloudflare and caches everything.  Fortunately there's an [action for dealing with cache purging already](URL "https://github.com/marketplace/actions/cloudflare-purge-cache")!

A quick bit of config in my workflow:
```yaml {linenos=table,linenostart=51}
- name: Purge cache
  uses: jakejarvis/cloudflare-purge-action@master
  env:
    # Zone is required by both authentication methods
    CLOUDFLARE_ZONE: ${{ secrets.CLOUDFLARE_ZONE }}
    CLOUDFLARE_TOKEN: ${{ secrets.CLOUDFLARE_TOKEN }}
```
Some secrets added to my GitHub environment and it's job done.