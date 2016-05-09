+++
date = "2016-05-09"
draft = false
title = "cPanel - Broken FTPS (FTP over TLS) with Java Clients"
categories = [ "Internet" ]
tags = ["internet", "website", "cpanel", "security"]
+++

If you've gone to edit your site and suddenly found that your chosen IDE won't connect to upload any more via FTPS (FTP using TLS encryption) and your web host runs cPanel, then chances are they're running v56 and your IDE is written in Java.

I hit this issue with another website late last week, where the DH params had already been re-generated to 2048 bit, but that change removed.  Your IDE probably just says it can't connect, that's what JetBrains WebStorm and PhpStorm do although the logs did give more information:
<pre>
Caused by: java.security.InvalidAlgorithmParameterException:
Prime size must be multiple of 64, and can only range from 512 to 2048 (inclusive)
</pre>
NetBeans gave the game away sooner by giving the error from Java about DH parameters.

<h2>Weak DH Keys</h2>
The root cause of the issue is a problem with weak Diffie-Hellman key sizes, widely reported as the Logjam attack.  The recommendation is to move to 2048 bit prime number sizes which sounds brilliant, but then....

<h2>Damn you Java</h2>
Only Java 8 supports 2048bit DH params, v7 supports a maximum of 1024bit.

The big problem is that Java 8 supports only a *maximum* of 2048bit, this is where we hit an issue with cPanel v56.

<h2>Oops cPanel</h2>
It seems that on April 20th 2016 cPanel pushed out an update that generated new dhparams for pure-ftpd, but they didn't fully understand the warnings around Java, as they generated 3072 bit DH params, which you can verify with the following command:

<pre>
openssl dh -in /etc/ssl/private/pure-ftpd-dhparams.pem -text -noout
</pre>
You'll see output like the following:

<pre>
Diffie-Hellman-Parameters: (3072 bit)
    prime:
        00:80:26:5e:fe:c9:8e:63:2d:6d:52:3e:ab:4c:49:
        <snip>
</pre>
As you can see, 3072 bit.

<h2>Fixing it</h2>
Fortunately the fix is easy:

<pre>
cp /etc/ssl/private/pure-ftpd-dhparams.pem /etc/ssl/private/pure-ftpd-dhparams.pem.bak
openssl dhparam -out /etc/ssl/private/pure-ftpd-dhparams.pem 2048
service pure-ftpd restart
</pre>
We've backed up the existing params just in case and generated new ones for pure-ftpd to use.

<h2>Will it break again?</h2>
That remains to be seen.  The issue has been reported to cPanel, so hopefully it won't break as we're not just talking a few IDEs here like JetBrains, NetBeans etc. there are also countless ERP systems out there that use Java and a lot of them shuffle files (such as orders, tracking info for shipping) in and out via FTP with their customers and suppliers.