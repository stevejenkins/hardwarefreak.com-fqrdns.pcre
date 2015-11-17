<h1>Postfix PCRE Bot Spam Killer</h2>
*(no longer available at www.hardwarefreak.com/fqrdns.pcre)*

<strong><em>v2015-11-16</em></strong>

<em>This file was originally created and maintained by Stan Hoeppner, and is maintained "by proxy" by Steve Jenkins until Stan is ready to take it over again.</em>

<em>If you have suggested modifications to this file, please create a new issue or a pull request here on GitHub so I can consider the proposed changes. You can use the History function here on GitHub to see the most recent changes.</em>

There are now <strong>three</strong> PCRE files in the fqrdns.pcre project. They are:

<ol>
<li><strong>fqrdns.prec</strong> - the primary list of bot spam killer rules</li>
<li><strong>fqrdns-plus.pcre</strong> - rules that have been moved from the original fqrdns.pcre file due to an increased likelihood of false positives</li>
<li><strong>fqrdns-max.pcre</strong> - new rules that were never part of the original fqrdns.pcre file and which use more general patterns to take more a aggressive approach to blocking bot spam, including a maximum number of dynamic hosts and/or misconfigured senders. These patterns help enforce a more strict and "forensically sound" route of static addresses from sender to recipient, at an increased risk of blocking potentially legitimate mail.</li>
</ol>

Mail admins should start with the primary fqrdns.pcre first, then add the "plus" and "max" files (in conjunction with, not as a replacement) to their setup, as desired.

<em>Stan's original README for the fqrdns.pcre file:</em>

The following set of regular expressions attempt to stop bot spam connections
by matching the FQrDNS name of the sending IP against known consumerish rDNS
patterns or other rDNS patterns likely to be sources of bot spam and not legit
email.  This methodology is not without error, but time has proven it to be very
accurate.  If it blocks a sender you know to be legit and from whom you need to
receive mail I recommend whitelisting that source instead of removing an expression
below as a means to solve the problem.  The expressions are POSIX regular
expressions and can be used with either the PCRE or REGEXP table type.  The PCRE
engine is typically faster.

This file is provided AS IS with no WARRANTY.  It is free software, without attribute
or copyright, and without license.  As such, you are completely free to use it
and modify it as you see fit, for your purposes, with absolutely no strings attached.

Usage:

Save this file as /etc/postfix/fqrdns.pcre
Add to main.cf

    smtpd_client_restrictions =
    	...
    	check_reverse_client_hostname_access pcre:/etc/postfix/fqrdns.pcre
    	...

or

    	check_client_access pcre:/etc/postfix/fqrdns.pcre

Use the first above with Postfix 2.6 or later, the second with 2.5 and ealier.  If you
use the "everything under smtp_recipient_restrictions" style of main.cf add this
toward the very top of your restrictions list, with your IP whitelist first, such as:

    smtpd_recipient_restrictions =
    	permit_mynetworks
    	permit_sasl_authenticated
    	reject_unauth_destination
    	check_client_access hash:/etc/postfix/whitelist
    	check_reverse_client_hostname_access pcre:/etc/postfix/fqrdns.pcre
    	...

If you ~~add~~ like to propose a pattern which isn't currently covered please ~~email it to stan at hardwarefreak.com~~ create a new issue or pull request here so ~~I can add it to the "master" file located at http://www.hardwarefreak.com/fqrdns.pcre~~ it can be considered for inclusion.

The file doesn't change very often so a wget every two weeks is usually sufficient.
Each file change is normally one or more new expressions or minor fixes.
