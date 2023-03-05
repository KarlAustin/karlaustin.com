+++
description = "IPv6 is 21 years old this year, it shouldn't be this hard to set it up at home."
categories = ["Internet"]
date = "2017-03-05T09:50:46Z"
title = "IPv6 at Home - It Should Not Be This Hard"
tags = ["internet","ipv6","networking"]
+++

## 5Ghz Here We Come

A few months ago I changed my home router from a [Mikrotik RB2011](https://www.amazon.co.uk/gp/product/B00I4QFQDI/ref=as_li_tl?ie=UTF8&camp=1634&creative=6738&creativeASIN=B00I4QFQDI&linkCode=as2&tag=karlaustincom-21) router to a [Linksys WRT1900ACSv2](https://www.amazon.co.uk/gp/product/B01562PSIU/ref=as_li_tl?ie=UTF8&camp=1634&creative=6738&creativeASIN=B01562PSIU&linkCode=as2&tag=karlaustincom-21) - Not a particularly cheap router, it's still &pound; even now.  I changed because I wanted 5Ghz wireless as the 2.4Ghz around here is massively congested, plus I wanted some faster speeds for copying photos to my NAS.  I've got Gigabit wired to most rooms, but I just can't be bothered to find a cable most of the time to hook up to - I don't usually have one long enough at home, work tends to stay at work these days.

## How Hard Can It Be To Get IPv6 Working?

I didn't really like the Linksys interface, so I did what any self respecting IT geek does, I put DD-WRT firmware on it straight away, which it turns out was a mistake.  It has very poor support for IPv6 out of the box, as in virtually non-existent if you just want to put a few details in to the interface and have it work.

After a couple of hours of googling and faffing I'd had enough and went back to the stock Linksys firmware to see what the IPv6 support in that was like, after all it does claim to support IPv6.

## Liar Liar, Pants On Fire

Whilst it's true that the WRT1900ACSv2 does support IPv6, it's a bodge.  The only thing it supports is 6rd, which is tunneling mechanism and not native IPv6.  Tunneling is a bodge at the end of the day and high end routers shouldn't just be limiting you to a bodge, they should have proper native IPv6 dual-stack support.  There's no excuse these days. None.

## OpenWRT, Come On Down

After a few minutes ranting at Linksys I switched over to OpenWRT, what a revelation compared to DD-WRT.  The web interface, Luci, was virtually instant with anything asked of it and had proper IPv6 support built right in there; that's not to say I didn't have problems though.

I'm on an FTTC connection from [Goscomb](http://goscomb.net) and for v6 I have a /48 subnet assigned to me - unfortunately this doesn't get pushed to the router by Router Advertisement (RA) or DHCPv6, only the /64 linknet.  Getting the /48 on to the router wasn't an issue, a quick bit of googling gave me the simple and Ubuntu style config file format for the network, within 10 minutes of flashing the router I'd got IPv6 addresses out to the clients.  That's when the fun began.

## No IPv6 Connectivity

Once the clients had got an IPv6 address, they couldn't actually get to anything out on the net.  That's when a couple of hours of fiddling around started, I could either:

* Have IPv6 addresses on the clients

or

* Have the router connect out via IPv6

but not both.  After a while I did get both happening, but still no IPv6 from clients to the net.

## The Solution

It turns out the default route that was being configured to send IPv6 from the router, to the other end (Goscomb) of the Link-Local v6 subnet wasn't actually doing it's job and I had to manually configure an IPv6 default route to just aim all v6 traffic at the wan interface.  A very simple thing to do, but one that still took too long to find and work out late at night.

## My Config (Redacted)

/etc/config/network
~~~~
config interface 'loopback'
        option ifname 'lo'
        option proto 'static'
        option ipaddr '127.0.0.1'
        option netmask '255.0.0.0'

config interface 'lan'
        option ifname 'eth1'
        option force_link '1'
        option type 'bridge'
        option proto 'static'
        option ipaddr '192.168.X.1'
        option netmask '255.255.255.0'
        option ip6assign '60'
        option ip6hint '1'

config interface 'wan'
        option ifname 'eth0'
        option _orig_ifname 'eth0'
        option _orig_bridge 'false'
        option proto 'pppoe'
        option username 'user@example.org'
        option password 'APasswordHere'

config interface 'wan6'
        option ifname '@wan'
        option proto 'dhcpv6'
        option reqaddress 'try'
        option reqprefix 'no'
        option defaultroute '0'
        option ip6prefix '2a01:X:Y::/48'

config 'route6'
        option interface 'wan'
        option target '::/0'
~~~~

/etc/config/dhcp
~~~~
config dnsmasq
        option domainneeded '1'
        option boguspriv '1'
        option filterwin2k '0'
        option localise_queries '1'
        option rebind_protection '1'
        option rebind_localhost '1'
        option local '/lan/'
        option domain 'lan'
        option expandhosts '1'
        option nonegcache '0'
        option authoritative '1'
        option readethers '1'
        option leasefile '/tmp/dhcp.leases'
        option resolvfile '/tmp/resolv.conf.auto'
        option localservice '1'

config dhcp 'lan'
        option interface 'lan'
        option start '100'
        option limit '150'
        option leasetime '48h'
        option ra_maxinterval '30'
        option ra 'server'
        option dhcpv6 'server'
        option ra_default '1'
        option ra_management '1'

config dhcp 'wan'
        option interface 'wan'
        option ignore '1'

config odhcpd 'odhcpd'
        option maindhcp '1'
        option leasefile '/tmp/hosts/odhcpd'
        option leasetrigger '/usr/sbin/odhcpd-update'
~~~~


## This Is My Day Job - It Shouldn't Be This Hard!

Bearing in mind this is my day job and I work with routers that are insanely complex compared to your average home router and cost well in to 5 figures at the low end, it just shouldn't be this difficult.  I get that most people use a router from their ISP so that all of this is hidden from them, bt there are still an awful lot of people who don't because their ISP doesn't provide one, or the one it does provide is awful.

It's 2017, IPv6 was drafted in 1996, it's 21 years old.  In that time it's been tweaked a bit along the way, but there's really no excuse for any home router to not have it, other than saving a few pence on a bill of materials and that's just irresponsible to their users and the internet as a whole.  Without widespread IPv6 adoption for end users we end up with nasty solutions such as CG-NAT, when NAT should have been put out of its misery years ago, not adding more layers of complexity to it.
