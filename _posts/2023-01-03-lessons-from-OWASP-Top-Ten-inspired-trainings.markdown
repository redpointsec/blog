---
layout: single
title:  "Training Devs for a new OWASP Top Ten (2021)"
date:   2023-01-03 13:43:00 -0600
categories: posts
---
# What we learned from adapting our Application Security 101 course

![]({{ site.baseurl }}{{ '/assets/images/owasp-top-ten-changes.png' }})
_Changing the OWASP Top Ten_

## Background: The OWASP Top Ten - An Awareness Document

The [OWASP Top Ten](https://owasp.org/Top10/) is a widely-used guide developed by the fine folks at the Open Web Application Security Project that provides a list of the common and critical vulnerabilities in web applications. First published in 2003 and updated every three or four years, the guide  reflects developing and ongoing threats and best practices for secure web application development ([For a  historical list of the incarnations of the OWASP Top Ten vulnerabilities over time](https://www.hahwul.com/cullinan/history-of-owasp-top-10/)).

### _Terminology reflects the idea of moving security left._ 
The 2021 version of the Top Ten indulges the _au courant_ security zeitgeist, putting a larger emphasis on development decisions in the security pipeline. The best example of this shift is from the addition of the Insecure Design category to the new Top Ten as well as the linguistic change to describe the sensitive data leakage category. Now, [Cryptographic Failures](https://owasp.org/Top10/A02_2021-Cryptographic_Failures/) underlines the fact that devevlopers most likely erred up in how they protected data, whereas the past description, sensitive data exposure, reflected a vulnerability in passive voice, i.e., "data was exposed, who can say why?" In the estimable Professor [Brian Glas's explanation](https://youtu.be/nq-Igdk0o7s?t=717), sensitive data exposure is the result of other failures (categories like Broken Access Control, Broken Authentication, Security Misconfiguration or, the new Cryptographic Failures are all application development mistakes that preceded the data exposures).

![OWASP Category Relationships]({{ site.baseurl }}{{ '/assets/images/OWASPTopTen-Cat-Relationships.jpg' }})
_Interrelationships and Overlap among OWASP Top Ten Categories_

Updates for the 2021 Top Ten list also reflected more categories of vulnerabilities, and fewer specifics (but we still see you SSRF). Cross-Site Scripting (XSS) got folded into the Injection category, Insecure Deserialization folded into a new, larger Software and Data Integrity Failures category, and XML External Entities (XXE) merged with the Security Misconfiguration category. The updates seemed largely to add some degree of clarity to how we conceptualize application security vulnerabilities as well as how frequently we expect to come across specific security flaws in Redpoint's testing/services work. From a training perspective, the benefit of additional clarity stemming from the changes holds true as well.

## What the changes have meant for our APPSEC-101 training

In Redpoint's course re-design, the new Top Ten inspired some changes with how we approach students and dev teams who take the course. The most apparent alterations were in prioritization. Broken Access Control's ascension to the first place meant we teach about IDOR (Insecure Direct Object Reference) early and often. It's realistically helped quickly initiate students unfamiliar with hacking techniques to understand the nature of the course more immediately. Nothing like force-browsing in [Vulnerable Task Manager](https://github.com/redpointsec/vtm) (our custom-developed intentionally vulnerable application) onto another user's private profile page to illustrate for hacking novices what a security vulnerability looks like and how it could be potentially devastating. 

![SDLC in Waterfall]({{ site.baseurl }}{{ '/assets/images/SDLC-RDPT.png' }})
_Using the Insecure Design category to highlight security decisions in the Software Development Lifecycle_

The addition of the Insecure Design category being in the Top Ten helps frame security as a concept that really benefits from an early-as-possible inclusion in design considerations. The fact that this is OWASP's first category that fully implicates bringing in security experts to train your developers is a kind of incentive we like because it encourages even more teaching opportunities for our team, but generally it is certainly true that an application development team that never trains devs in security fundamentals risks the introduction of security flaws into their production application, risking both a company's reputation and its customers' data.

Honestly speaking, the training re-design made some of our modules top heavy. Broken Access Control and its component vulnerabilities are explored through a variety of authorization exercises and folding XSS into injection has led to a slightly overloaded injection module. We plunge into the deep end on all the classes of injection vulnerabilities because Vulnerable Task Manager clearly demonstratess the hacking methods and security problems presented by these categories. The application similarly illustrates credential stuffing attacks and mass assignment vulnerabilities (Insecure Deserialization (2017) --> Software and Data Integrity Failures (2017)). Overall, the OWASP Top Ten necessitates a full day of exercises and discussions moving at a healthy clip to cover the material, but new students uniformly leave the course with a much more advanced idea of what security means and a handy toolset for considering security early in their dev team's design decisions. 

Peppered throughout the hands-on training are tales from consulting or stories of exploits (shhhhhh) that our trainers may have discovered in the wild. Contact Redpoint here (info@redpointsecurity.com) if you have any questions about how our APPSEC-101 training works or if you're interested in any of our other course offerings: Mobile Security 101 (iOS and Android), TM-101 (Threat Modeling 101), APPSEC-102 (Advanced OWASP Top Ten and Exploits), or Practical Secure-Code Review for Advanced Students.