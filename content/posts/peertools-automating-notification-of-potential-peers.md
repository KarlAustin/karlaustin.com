+++
draft = false
description = "An automated tool for sending email notifications to ISPs sending BGP Open requests to your routers on a peering point."
date = "2017-03-04T18:41:30Z"
title = "Peertools: Automated notification of unsolicited BGP Open requests at peering points"
categories = ["Internet"]
tags = ["internet","peering","linx","bgp"]
+++

## The Problem
When you connect to an internet peering exchange, the IP addresses you're given for your interfaces will most likely have been used by someone else prior to you, that has left the exchange.

This means, because we're not all good at housekeeping, that you'll end up with ISPs trying to open up BGP sessions with your routers and if you're not firewalling them out then you'll end up with a lot of messages about BGP Opens being received from unconfigured neighbours.  If you're firewalling (and you should be) and you're logging you'll end up with a lot of hits logged against port 179.

## The Solution
Ideally you'd like everyone to keep their peering neat and tidy, but we're not in an ideal world unfortunately.  So I came up with a tool that will:

* Scan your message log for BGP opens from unconfigured neighbours
* Scan your message log for TCP with unexpected MD5
* Scan your firewall log for connections to port 179 that have been dropped

This catches most instances and you end up with a list of IP address, the system then:

* Looks at the peering point data to see what ASN the IP address is assigned to
* Checks PeeringDB for a peering contact for the ASN
* Emails the peering contact based on the email template, which by default shows the peering LAN and IP addresses trying to open sessions

### What It Supports Right Now

* JunOS 12.x - It may work with other versions of JunOS, but that's what it has been tested with.
* SSH - Key based SSH for connecting to JunOS
* LINX - Currently it is hard coded to work with LINX, The London Internet Exchange.
* PeeringDB - Only PeeringDB is supported at the moment for fetching an AS numbers peering contacts
* Email - Email is the current form of contact

## What It Can Support Right Now If You Want To Write a Module?

The router code is modularised and self contained, so any router or switch can be supported.

The router class includes what commands to execute to fetch and parse the data in to an IP address.  Additional commands are added in to an array.

## The Future

The aim is to make a simple suite of tools for small ISPs without their own staff to better manage their peering relationships.  The next evolution will hopefully add tracking support, so it will log when you last emailed a given peer and allow you to set a time period for them to respond before it emails again.

## Where Can I Find It?

[https://github.com/KarlAustin/peertools](https://github.com/KarlAustin/peertools)

The code is currently still a little rough in places, as it was quickly built to deal with the 30 or so IPs hitting our peering routers every few seconds.  So far we've had a couple of ISPs stop and several agree to peer with us.  Please feel to contribute or comment.
