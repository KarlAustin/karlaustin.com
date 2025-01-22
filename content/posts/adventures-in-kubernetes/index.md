+++
date = "2025-01-19"
draft = false
title = "Adventures in Kubernetes"
categories = [ "Dev Ops" ]
tags = ["k8s", "devops"]
series = ["Adventures in Kubernetes"]
+++

## Background

It's come to that point of the 21st century where I no longer ignore Kubernetes and pretend it doesn't exist.  The simple fact is that pretty much every useful tool now relies on Kuberenetes, it's such a large ecosystem that everyone supports it.

I've looked at lots of simpler alternatives, some of which I'd prefer to use but they usually fall into one or all of:
 - Poor documentation - making it hard to get going
 - Won't integrate with other tools we (want to) use
 - Small community of public users - meaning if we need to find an answer to something we're stuck or it's *'Enterprise Support'* empty the wallet time

### Current Stack

As you'd expect, we've got a current stack that it would be good to play nice with, which means our OpenNebula + Storpool platform.

### Everything Else

We can't really talk DevOps without talking about a few other things that tend to go hand-in-hand with them, which means everything else is pretty much fair game:
 - Automation tools - we've played but never stuck to anything
 - Monitoring - we've got a mix of tools
 - Metrics - we only collect limited metrics at the moment, but we're going to need to expand on that
 - Logs - We need to be better at this
 - Reporting - Need to build tooling to give reports to partners

## The Requirements

1. Secure

    People entrust us with their data, this needs to be secure for hosting multiple clients and projects. It also needs to be secure because dealing with incidents is a pain.  This doesn't just mean the underlying infrastructure, but the deployments on top as well.

2. Resilient

    It needs to be robust and protected from hardware failures. Preferably it needs to be self healing.

3. Maintainable

    We're a small team, we need to be able to easily maintain this and keep it up to date so it's secure.  Also so that we're getting the future features we may need.

4. Repeatable

    We need to be able to reproduce this infrastructure easily in other locations or if a client wants their own dedicated infrastructure. 

5. Auditable

    We need to be able to prove what we've done and that we've followed processes e.g. ISO 9001/27001

6. Persistence

    We need to know that what we're planning to use is going to be around for some time as our projects are usually 5+ years.

7. Performant

    This will be hosting online apps and sites, users will not tolerate per performance, and neither will we.

How hard can it be?