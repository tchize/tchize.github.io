---
layout: single
title: Disclosure Methodology
permalink: /security/full-disclosure/
toc: true
author_profile: true
---

## tl;dr

[Coordinated vulnerability disclosure](https://en.wikipedia.org/wiki/Coordinated_vulnerability_disclosure)
  1. I find a security hole by whatever way
  2. I contact you on a confidential layer (typically mail + [pgp](/about/))
  3. You have plenty of time (def. 3 months) to fix your security
  4. I disclose information in a responsible way
  
## A sensitive subject

Our modern software have become more and more complex. The good old
days where you controlled the whole pipeline and you could be oblivious
of volontary bad usage of your software are gone.

Let's face it. Software have bugs. Libraries have bugs. And people who want 
to exploit those for their own profit exists.

This makes finding exploitable bugs or mistakes in live software a triple problem.
This is obviously a problem for the software owner. This is a problem for the user,
who trust the software owner to manage his personal and private data.
And It become also my problem as I have to decide what to do with the findings.
I am now the recipient of a dangerous knowledge and could be held responsible
for the use or divulgation of that knowledge.

## Now what?

Once you make a discovery, you find yourself needing the answer to one single simple question:

    Now what?

Personal data can be sold. According to [Privacy Australia](https://privacyaustralia.net/dark-web-personal-data),
this ranges from a few cents to thousands of euros. 
Very specific leaks of data could be sold to targeted buyers 
at very good value. 

Should you keep all those users in the dark? They will be the one impacted and have
a right to know.

0-day exploits in widely used libraries, especially if they allow remote control, 
[can be sold for tens of thousands](https://www.newsbtc.com/news/bitcoin-buy-zero-day-exploit-dark-net/).  

Blackmailing can also be quite lucrative. Ransomware keeps targeting enterprises and individuals
with quite a good amount of success.

Or you can be a good citizen and follow that IT security standard: 
[Coordinated vulnerability disclosure](https://en.wikipedia.org/wiki/Coordinated_vulnerability_disclosure)

## What I will do

If you store personal data about me or handle financial service for me, I may fiddle with your APIs
to ensure my personal data are safe. Especially if I find an obviously spurious behavior of that service.
I have learned in the past to not trust blindly companies. Some implement good security, 
a few of them are excellent citizens, most do work on a "need to work, no budget".

If I find a security issue, I will try to contact you and give you a report. My only
interest is that you fix it.

I will make everything possible to ensure the confidentiality of that report when transmitting it.
 I will always try to initiate a discussion canal where I am sure you are the legitimate 
 recipient for this report and ask you for an encryption key to transmit the report.

If you have a published vulnerability disclosure procedure in place, I will do my best to follow it.

If my findings give me access to places I should not have access to, I will refrain from abusing 
the access and limit my actions to the gathering of evidence.

I will publish a full disclosure of the findings on this website. No discussion possible.
However I will do my best to ensure you had plenty of time to fix the issue.
I will try to find common agreement on a reasonable delay. By default, 3 months minimum 
after initial notification.

Published documents may include evidences and proof of concept code to reproduce. 

I will ensure my publication respect European GDPR laws and don't include personal data.


## What I won't do

I won't run disruptive attacks on your services, attempt to destroy data or prevent your site
to run normally.

I won't accept any kind of payment or contractual IT relation with your company. 
This could legally be seen as black mailing. This implies I will not audit your security.

I won't sell this information or derived exploits to third parties.

I will not provide recommendation or affiliation to any security company. You have to
do your own shopping.

I will not handle the mandatory GDPR violation declaration. If you user personal data
is exposed, it's your responsability.

