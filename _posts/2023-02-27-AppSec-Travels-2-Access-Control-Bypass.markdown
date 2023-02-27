---
layout: single
title:  "AppSec Travels: Part 2"
date:   2023-02-27 10:29:00 -0600
categories: posts research vulnerabilities
tags: appsec edge-case vulnerability research review findings 
permalink: /AppSec-Travels-2-Access-Control-Bypass/
author: Seth Law
author_profile: true
classes: wide
---

# <span style="color:red">Access Control Bypass</span>

This is another in Redpoint's blog series AppSec Travels where we walk you through interesting findings we've discovered in vulnerability assessments. AppSec Travels is on ongoing series without a regular cadence because frankly some assessments are perfunctory security checks lacking in exciting findings and we sort of doubt anyone wants to read findings write-ups on lack of _HTTPONLY_ flags. As a consequence, AppSec Travels will be reserved for findings that are interesting to us and hopefully to you as well. We don't claim that these findings will be in every assessment or that they need to added to your general security checklists, but hopefully they help in thinking differently about your individual approach to security testing. We do _a lot_ of secure code reviews here at Redpoint Security so most of these blog posts will focus on code along with dynamic testing. Some names and data have been changed to protect the innocent (and our customers' NDAs).  

## Access Control via URL Path

### Overview

This instance of access control failure was discovered during a recent assessment on a commercial off-the-shelf (COTS) dashboard application that served identity functionality for both external clients and internal users. Access restrictions were implemented by a web application firewall (WAF) that limited access to sensitive endpoints (e.g. `/manage`,` /UserSearch`) from IP addresses outside to the company's networks. The application itself did not differentiate between user roles, so any user with an account and access to the application via the corporate network could interact with restricted endpoints

### Vulnerability Details

You may already see where this is going, but anytime a single application serves multiple users of different access levels, especially when those users are on different networks or comming from other locations, there is a risk that access control failure could result in exposure of application data or sensitive functionality.

In this case, external interaction with an access-restricted page from outside the corporate networks resulted in a redirect to a notice that the page and functionality was limited to company networks. The following is a screenshot of this behaviour from Burp Suite.

![Normal Behavior]({{ site.baseurl }}{{ '/assets/images/appsec-travels/2/normal.png' }})
_Normal Redirect when accessing URL from an Unauthorized Network_

The HTTP Response header gives indicator that an F5 Big-IP WAF was the technology that was sending the redirect instruction when attempting to access restricted URLs.

![HTTP Response]({{ site.baseurl }}{{ '/assets/images/appsec-travels/2/response-header.png' }})

_HTTP Response shows that the response is controlled by BipIP via the Server header_

The combination of an application server behind a web application firewall that implements a security control introduced an edge case into our testing methodology where we need to make sure both technologies understand HTTP requests in the same way. One of the methods we used to test this control was to replace sensitive URL characters with their URL-encoded equivalent. Use of these payloads determine if both inline server technologies are interpreting URL-encoded characters properly.

For the above instance, we modify `/UserSearch` to `/%55serSearch` and attempt to access the application.

![Access Control Bypass]({{ site.baseurl }}{{ '/assets/images/appsec-travels/2/bypass.png' }})
_Access Control Bypass resulting from URL-encoding characters in the URL path_

Success! The implemented WAF rule only matches it's regular expression against the requested characters in the URL path and does not normalize the URL path before sending the access control redirect. Notice also that the successful bypass response headers indicate that the application server is Apache (as seen in the `Server` header).

To sum up the vulnerability, access control to network-restricted portions of the reviewed application was enforced by web application firewall rules. These rules were implemented using regular expressions based on a list of sensitive paths within the WAF configuration. Bypass was possible due to the a mismatch in the interpretation of the URL path between the WAF and Apache. Apache allowed for URL-encoded characters in the path, whereas the WAF did not take this possibility into account before allowing access.

### Mitigation

Our suggested mitigation in this case was twofold. First, rebuild the WAF detection to account for alternate encodings. Specifically, URL-decode path strings and characters before matching sensitive paths against a regular expression. Second, implement access control mechanisms closer to the actual application using web server configurations to mitigate future bypasses that exploit differences in HTTP Request decoding. 

Thanks for coming along on another journey in AppSec Travels. Stay tuned for our next excursion into the unexpected world of Application Security and Code Review.