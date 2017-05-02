# Postfix PCRE Bot Spam Killer
*(no longer available at www.hardwarefreak.com/fqrdns.pcre)*

**v2015-11-18**

The original version of the `fqrdns.pcre` file was originally created and maintained by Stan Hoeppner.

If you have suggested modifications to this file, please create a new issue or a pull request here on GitHub so I can consider the proposed changes after others have had a chance to comment. You can use the History function here on GitHub to see the most recent changes.

There are now **three** separate PCRE files in the `fqrdns.pcre` project. They are:

* **fqrdns.pcre** - the primary list of bot spam killer rules. The goal of this list is to have virtually zero chance of false positives.

* **fqrdns-plus.pcre** - *slightly* more restrictive rules that have been moved from the original fqrdns.pcre file due to a slightly increased risk of false positives. Overall, however, this file still has a low chance of false positives. This file should be used *in addition to* the original `fqrdns.pcre` file.

* **fqrdns-max.pcre** - new rules that were never part of the original fqrdns.pcre file and which use more general patterns to take more a aggressive approach to blocking bot spam, including a maximum number of dynamic hosts and/or misconfigured senders. These patterns help enforce a more strict and "forensically sound" route of static addresses from sender to recipient, but with an increased risk of blocking potentially legitimate mail from misconfigured senders. This file should be used *in addition to* the original `fqrdns.pcre` file and the `fqrdns-plus-pcre` file.

Mail admins should deploy the primary `fqrdns.pcre` first, then **add** the `fqrdns-plus.pcre` and `fqrdns-max.pcre` files to their setup as desired. The three separate files work together to create a **tiered** approach to progressively blocking spam and misconfigured mailers.

The following paragraphs were included in Stan's original `README`:

*The following set of regular expressions attempt to stop bot spam connections by matching the FQrDNS name of the sending IP against known consumerish rDNS patterns or other rDNS patterns likely to be sources of bot spam and not legit
email.  This methodology is not without error, but time has proven it to be very accurate.  If it blocks a sender you know to be legit and from whom you need to receive mail I recommend whitelisting that source instead of removing an expression
below as a means to solve the problem.  The expressions are POSIX regular expressions and can be used with either the PCRE or REGEXP table type.  The PCRE engine is typically faster.

This file is provided AS IS with no WARRANTY.  It is free software, without attribute
or copyright, and without license.  As such, you are completely free to use it
and modify it as you see fit, for your purposes, with absolutely no strings attached.*

# Usage
Save any of the pcre files included in this project in `/etc/postfix/`.

Add them to the `smtpd_client_restrictions` section of `main.cf`. When using multiple files from this project, it is recommended to include them in your `main.cf` file in order of *most* aggressive to *least* aggressive:

    smtpd_client_restrictions =
    	...
    	check_reverse_client_hostname_access pcre:/etc/postfix/fqrdns-max.pcre
        check_reverse_client_hostname_access pcre:/etc/postfix/fqrdns-plus.pcre
    	check_reverse_client_hostname_access pcre:/etc/postfix/fqrdns.pcre

    	...

If using Postfix 2.5 or earlier, use `check_client_access` instead of `check_reverse_client_hostname_access`.

    	check_client_access pcre:/etc/postfix/fqrdns.pcre

If you use the "everything under smtp_recipient_restrictions" approach in your `main.cf`, add `fqrdns.pcre`
toward the very top of your restrictions list, with your IP whitelist first, such as:

    smtpd_recipient_restrictions =
    	permit_mynetworks
    	permit_sasl_authenticated
    	reject_unauth_destination
    	check_client_access hash:/etc/postfix/whitelist
    	check_reverse_client_hostname_access pcre:/etc/postfix/fqrdns.pcre
    	...

# Additional Patterns
If you would like to propose a pattern which isn't currently covered, please create a new issue or pull request here on GitHub so it it can be considered for inclusion.

# Downloading Updates
The file doesn't change very often so a wget every two weeks is usually sufficient. Each file change is normally one or more new expressions or minor fixes.
