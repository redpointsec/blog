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
In Redpoint Security's existence as an application security firm, we have seen a range of clientele who manifest a whole host of differences: development teams and applications that range remarkably small and large, different levels of security maturity, compliance requirements that are relatively lax as opposed to highly demanding with a rapidly rushing SLA (Service-Level Agreement) clock. Given this range of differences, we wanted to reflect on the Customer Journeys of some repeat customers, seeing how security develops in concert with active application developments.

![Customer-Security-Journey-1]({{ site.baseurl }}{{ '/assets/images/client-vulns-1.png' }})
_<span style="color:red">Customer 1:</span> A smaller application used established authentication schemes to address early issues_

Our first customer, represented by the above bar graph, had a few interesting developments over a regular review-remediate-retest cycle. This company was a relatively small shop and had several security-conscious people in positions that could affect security decision-making for the company and its culture. We felt there was evidence of these advantages from the get-go, with higher-rated findings mostly reflecting edge-case discoveries. 

After the second round of dynamic reviews, the overall security position of the application had improved and we felt that an enhancement of our understanding of the application (and its overall security) was warranted. As such, further reviews were done as hybrid assessments that pair manual secure-code review with dynamic validation of any code vulnerabilities. 

This hybrid review resulted in the discovery of additional security vulnerabilities that were remediated quickly. Because the client was happy with these results, the hybrid assessment became our standard engagement for this application going forward. This additional access to the source code gave us the opportunity to locate other, less critical vulnerabilities. Overall, we have found that an application we evaluated as moderately or averagely secure in initial reviews, is now regularly evaluated as one of the more secure applications we see in the course of our work.

![Customer-Security-Journey-2]({{ site.baseurl }}{{ '/assets/images/client-vulns-2.png' }})
_<span style="color:red">Customer 2:</span> Initial tests revealed critical issues that have been buttoned down over time even with a significant migration to a new underlaying framework_

Our second customer is one we have engaged with frequently over the years doing dynamic application assessments, API testing, and cloud configuration reviews, among a range of other consultation work. In initial assessments, we discovered some critical vulnerabilities, some of which required pretty tricky remediations. A general assessment with them has proceeded to find fewer vulnerabilities in general and certainly not many high or critical findings after our initial assessments. This has remained to be true even as the client has implemented some fairly complex framework migrations over the past few years. In that regard, it has been encouraging to see development teams eliminate whole classes of vulnerabilities as well as use libraries and safeguards to prevent newly-developed vulnerabilities from reaching levels of highest concern. 

# <span style="color:red">Where do our assessments fit in your SDLC?</span>

![RDPT-SDLC]({{ site.baseurl }}{{ '/assets/images/SDLC-RDPT.png' }})
_SDLC in a waterfall process. Where does your organization need the most help?_

There can be a feeling of frustration at times when red teams are pulled into engagements only to find that the yearly compliance check is perfunctory and the relatively risky bugs we discover remain in an application after we are invited back the following year to re-conduct a review. Our goal with report write-ups is to provide a level of detail in the report recommendations that makes fixes as straightforward as possible. We really do not want to see authorization issues (such as IDOR) on the same endpoint year after year. Discovering these conditions usually reflects an internal company culture conflict, where security is seen either as an antagonistic force or, alternatively, developers who want to fix the security issues but have neither the time nor support to address tickets added to their backlogs.

In these cases, we try to encourage clients to use us more in the planning and design phases of their SDLC. Training developers in security practices or helping work up Security Champion programs are often prerequisites to effectively using the results from an assessment to improve the security of a company's application.

![Customer-Security-Journey-3]({{ site.baseurl }}{{ '/assets/images/client-vulns-3.png' }})
_<span style="color:red">Customer 3:</span> An organization with high security maturity that returned with widely shifting Secure-Code Review scopes, interesting to see rapid turnaround on mitigations from the dev teams_

With some clients, the maturity of the organization's security program is readily apparent in the responses to findings from an assessment. Tickets get assigned and remediated with sometimes astonishing speed. We expect, given the SLAs for some large-scale organizations, to see high- and medium-level findings to be addressed often in time for the re-testing we offer as part of our application review engagements. Clients, like the one reflected in our bar graph above, are known to address low findings and even often informational ones within a couple weeks' time. We have noted often how this team takes security seriously and have resources to put clever solutions in place for tricky problems. While it is nice to see this brand of security culture, we have also observed clients create security controls as effective as this well-resourced client by addressing security issues at any point in the SDLC.

Overall, we are encouraged and fortunate to have repeat clients that reveal how security grows over time. If you or your company are curious to find out more about our process or to see if there are ways we can help support your company's security culture, don't hesitate to reach out: info@redpointsecurity.com 
