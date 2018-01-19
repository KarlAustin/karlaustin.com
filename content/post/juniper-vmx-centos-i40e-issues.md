+++
author = "Karl Austin"
categories = [ "Internet" ]
tags = ["juniper", "vmx", "internet"]
description = "A known issue with the Juniper supplied i40e NIC driver stops installation on CentOS"
title = "Juniper vMX CentOS i40e Driver Issues"
date = "2018-01-19"
draft = false
+++

# vMX - Nice if you can make it work
I like Juniper's vMX product, I like JunOS and vMX makes it much more affordable to run inside your network.

What I don't like is the rather sketchy documentation that gives the impression they'd rather you don't run vMX.  It's clear that Ubuntu is their primary platform along with VMware now.  They list support for Redhat and CentOS, but with CentOS at least there are some issues that the documentation doesn't cover.

# The i40e Showstopper
If you want to use Intel i40e NICs, which at this moment in time means Intel X710 and XL710 for your vMX and you're running CentOS then you're going to hit an issue with everything up to vMX 17.4 (and possibly higher).
```
Check I40E drivers................................
[Command] cd /usr/local/vmx/live/drivers/i40e-1.3.46/src

[Command] rm -f i40e.ko

[Command] make install
make[1]: Entering directory `/usr/src/kernels/3.10.0-327.el7.x86_64'
CC [M]  /usr/local/vmx/live/drivers/i40e-1.3.46/src/i40e/i40e_main.o
/usr/local/vmx/live/drivers/i40e-1.3.46/src/i40e/i40e_main.c: In function ‘i40e_ndo_bridge_getlink’:
/usr/local/vmx/live/drivers/i40e-1.3.46/src/i40e/i40e_main.c:9086:2: error: too few arguments to function ‘ndo_dflt_bridge_getlink’
return ndo_dflt_bridge_getlink(skb, pid, seq, dev, veb->bridge_mode);
^
In file included from include/net/dst.h:13:0,
from include/net/sock.h:68,
from include/linux/tcp.h:23,
from include/net/tcp.h:24,
from /usr/local/vmx/live/drivers/i40e-1.3.46/src/i40e/i40e.h:30,
from /usr/local/vmx/live/drivers/i40e-1.3.46/src/i40e/i40e_main.c:28:
include/linux/rtnetlink.h:87:12: note: declared here
extern int ndo_dflt_bridge_getlink(struct sk_buff *skb, u32 pid, u32 seq,
^
/usr/local/vmx/live/drivers/i40e-1.3.46/src/i40e/i40e_main.c:9088:1: warning: control reaches end of non-void function [-Wreturn-type]
}
^
make[2]: *** [/usr/local/vmx/live/drivers/i40e-1.3.46/src/i40e/i40e_main.o] Error 1
make[1]: *** [_module_/usr/local/vmx/live/drivers/i40e-1.3.46/src/i40e] Error 2
make[1]: Leaving directory `/usr/src/kernels/3.10.0-327.el7.x86_64'
make: *** [i40e/i40e.ko] Error 2
[Failed]
```

The first thing you might think to do is go and fetch the i40e drivers from the Intel site - **don't** do that, your router won't work properly.  The i40e drivers are modified by Juniper, hence why they ship their own with vMX.

# The Solution
Fortunately the solution is pretty trivial, annoyingly so.

Open up:
`drivers/i40e-1.3.46/src/i40e/kcompat.h`

Search for:
`#define NDO_DFLT_BRIDGE_GETLINK_HAS_BRFLAGS`

Add the following on the line after:
`#define NDO_BRIDGE_GETLINK_HAS_FILTER_MASK_PARAM`

Re-run your install and it should happily compile the i40e drivers for you.

# Questions
You'll probably then do like I did, and wonder why on Earth Juniper didn't just apply a patch to the driver when it detects i40e NICs in a CentOS system if this is a known issue.  The only answer I have for that is, *router vendors*.

You might also ask why the documentation doesn't mention it as well, as following Juniper's CentOS install guide it's impossible to get vMX up and running; Again, I have no answer for that or why other items are wrong.

It's almost like they don't want you to use it isn't it?