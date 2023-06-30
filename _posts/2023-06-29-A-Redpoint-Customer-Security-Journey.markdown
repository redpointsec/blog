---
layout: single
title:  "The Redpoint Customer Security Journey"
date:   2023-06-29 16:23:00 -0600
categories: posts appsec assessment customer
tags: appsec assessment pentest secure-code webapp
permalink: /Redpoint-Customer-Journey/
author: Aaron Law
author_profile: true
classes: wide
---
In Redpoint Security's existence as an application security firm, we've seen a range of clientele who manifest a whole host of differences: dev teams and applications that range remarkably small and large, different levels of security maturity, compliance requirements that are relatively lax to  the highly demanding and a rapidly rushing SLA (Service Level Agreement) clock. Given this range of difference, we still wanted to reflect on the Customer Journeys of a couple of our repeat customers, seeing how security develops in concert with active application developments.

![Customer-Security-Journey-1]({{ site.baseurl }}{{ '/assets/images/client-vulns-1.png' }})
_<span style="color:red">Customer 1:</span> A smaller application used established authentication schemes to address early issues_

Our first customer represented by the above bar graph had a few interesting developments over the review-remediate-retest cycle we repeated with them. This company was a relatively small shop, and they had several good security-conscious people in positions that could affect security decision-making for the company and its culture. We felt there was evidence of these advantages from the get-go, with higher-rated findings mostly reflecting edge case discoveries we made. 

After our second round of dynamic reviews, we liked the security position of the application, but felt that we could enhance our understanding of the application (and its overall security) by conducting a hybrid assessment by pairing manual secure-code review application with dynamic validation of code vulnerabilities we might find. 
This hybrid review resulted in a helpful security vulnerability discover that was remediated quickly. Because the client was happy with these results, the hybrid assessment became our standard engagement type with this company going forward. Secure-code review gave us the opportunity to locate other, less critical vulnerabilities. Overall, we've found that an application we evaluated as moderately or averagely secure in initial reviews, is regularly evaluated as one of the more secure applications we see in the course of our work.

![Customer-Security-Journey-2]({{ site.baseurl }}{{ '/assets/images/client-vulns-2.png' }})
_<span style="color:red">Customer 2:</span> Initial tests revealed critical issues that have been buttoned down over time even with a significant migration to a new underlaying framework_

Our second customer is one we've worked with frequently over the years with dynamic application assessments, API testing, and cloud configuration reviews, among a range of other consultation work. In initial assessments, we discovered some critical vulnerabilities, some of which required pretty tricky remediations. A general assessment with them has proceeded to find fewer vulnerabilities in general and certainly not many high or critical findings after our initial assessments. This has remained to be true even though our client has taken on some fairly tricky framework migrations over the past few years. In that regard, it's been encouraging to see dev teams eliminate some classes of vulnerabilities entirely as well as use libraries or safeguards that prevent newly developed vulnerabilities to reach levels of highest concern. 

# <span style="color:red">Where do our assessments fit in your SDLC?</span>

![RDPT-SDLC]({{ site.baseurl }}{{ '/assets/images/SDLC-RDPT.png' }})
_SDLC in a waterfall process. Where does your organization need the most help?_

There is a feeling of frustration at times for redteams pulled in on consultations to find that the yearly compliance check is so perfunctory that relatively risky bugs that we discover remain in an application when we're invited back the following year to re-conduct a review. Our goal with our report write-ups is to provide a level of detail in our remediation recommendations that make fixes as straightforward as possible. We really don't want to see IDOR on the same endpoint year after year. Usually discovering conditions like this reflect some internal company culture conflicts, and security is seen either mostly as an antagonistic force or, alternatively, we have sincere people who want to fix the security issues but they have neither the time nor support to address tickets added to their backlogs.

In these cases, we try to encourage our clients to use us more in the plan and design phases of their SDLC. Training developers, helping work up Security Champion programs are often prerequisites to effectively using the results from an application security assessment to improve the security of your application.

![Customer-Security-Journey-3]({{ site.baseurl }}{{ '/assets/images/client-vulns-3.png' }})
_<span style="color:red">Customer 3:</span> An organization with high security maturity that returned with widely shifting Secure-Code Review scopes, interesting to see rapid turnaround on mitigations from the dev teams_

With some clients, the maturity of the organization's security program is readily apparent in the responses we get to our findings. Tickets get assigned and remediated with sometimes astonishing speed. We expect, given the SLAs for some large-scale organizations, to see high- and medium-level findings to be addressed often in time for the re-testing we offer as part of our application review engagements. Clients, like the one reflected in our bar graph above, are known be us to address low findings and even often informational ones within a couple weeks time. We've noted often how these guys take security seriously and have the resources to put clever solutions in place for sometimes tricky problems. It's nice to see that kind of security culture, but we've watched clients create security controls as effective as this well-resourced client through addressing issues that contribute to security laxity at any point in the SDLC.

Overall, we're happy to fortunate enough to have repeat clients that help us see how good security grows over time. If you or your company are curious to find out more about our process or to see if there are ways we can help support your company's security culture, don't hesitate to reach out: info@redpointsecurity.com 
