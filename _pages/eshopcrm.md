---
layout: single
title: Wild e-shopping
permalink: /security/findings/the-eshop-crm/
author_profile: true
toc: true
---

All personal data must go, help yourself :/


[Version française](/security/findings/the-eshop-crm-fr)
## Summary
 
 |       |    |
 | --------------|------------|
 | Discovery | 2022-09-01 | 
 | Notification date | 2022-09-15 |
 | Status | Partial disclosure | 
 | Type | Personal data leak, password storage policy |
 | Partial disclosure | 2023-02-04 |  
 | Est. Disclose date | Unknown (Waiting for webshop to fix it) |
 
## Description

On September 2022 multiple security vulnerabilities were found in some a belgian company customer web site. 
One vulnerability gave attackers access to crm data of all customers during the last 2 and a half year. Exploitation of those
data include:
* Leak personal data (name, address, phone, billing) related customers and a potential GDPR nightmare
* Potentially find prices and reduction applied to big or professional customers
* Extraction of prospect

This company has several physical shop in Belgium and quite a lot of customers.

After multiple attempts at contacting them, the company doesn't seem keen to fix the issue.
   
## The problems

The webshop suffer two major problems:
* The embedded tool to view bills and estimates give access to complete company database of customer documents.
* The passwords are stored in clear text or in a reversible encoding. They are also sent "as is" in the email

### Complete read access to invoice, bills and estimate documents


This physical shop provides people with an access to view their bills and estimates using their email address.
This endpoint has no access control and give access to anyone's data
This endpoint can easily be exploited to extract personal information on customer including
* full name
* address
* email
* phone number

But also company secret commercial information like
* daily, monthly or yearly sales
* discount given to professional customers
* current estimates

Once you log in, you have access to your bills:

[![Web page showing a list of bills. Name, number, amount and date obfuscated](/assets/images/webshop-2022-obfuscated/billing-page.png)](/assets/images/webshop-2022-obfuscated/billing-page.png)

The little "acrobat" icon on the right bring you to a URL in the form

> https://&lt;website&gt;/DynamicScript/Reporting/Index/123456?reportId=197

Where 123456 is the unique bill number.

[![Web page showing a bill. Name, number, amount, date and company obfuscated](/assets/images/webshop-2022-obfuscated/onebill.png)](/assets/images/webshop-2022-obfuscated/onebill.png)

If you replace this number in the url by another one, like adding or substracting 1, you get access to a complete stranger's bill. It's particularily easy to do
as the number are directly following each other, it represents the bill number, as present in document. They keep increasing one by one.
Want to know where this person who was in line behind you live? Just get the next bill in their system.


In this example, by changing numbers, I can see a bill from a complete stranger, and a see he got a discount of 18%

[![Web page showing a bill. Name, number, amount, date and company obfuscated. Bill shows a 18% discount](/assets/images/webshop-2022-obfuscated/strangerbill.png)](/assets/images/webshop-2022-obfuscated/strangerbill.png)

In this other example, One can easily access over the top sales.

[![Web page showing a bill. Name, number, amount, date and company obfuscated. Bill shows it was established at counter](/assets/images/webshop-2022-obfuscated/over-the-top.png)](/assets/images/webshop-2022-obfuscated/over-the-top.png)

Last but not least, the billing section has a "save as csv" and a "save as text" feature, which could easily be used for
automated data extraction.

[![Screenshot showing a save as csv option](/assets/images/webshop-2022-obfuscated/as-csv.png)](/assets/images/webshop-2022-obfuscated/as-csv.png)

[![Screenshot showing a parial csv with items, price and total](/assets/images/webshop-2022-obfuscated/csv.png)](/assets/images/webshop-2022-obfuscated/csv.png)

Related documentation:
*	[https://owasp.org/www-community/Broken_Access_Control](https://owasp.org/www-community/Broken_Access_Control)
*	[https://owasp.org/Top10/A01_2021-Broken_Access_Control/](https://owasp.org/Top10/A01_2021-Broken_Access_Control/)
*	[https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html](https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html)

### Clear text password storage

The password storage is very weak on this website. They are stored in the database in clear text.
In case of data leak (yeah yeah, it doesn't happen a lot, you have to have 
some kind of endpoint to extract information, right? Right?), a new set of password and emails 
woes in the wild.

If you go to the website and select the "lost password", surprise, surprise, your original password is sent by mail.
This mean something private, secret and sensitive is sent in plain text by email and stored in plain text in the database.

* [https://owasp.org/www-community/vulnerabilities/Password_Plaintext_Storage](https://owasp.org/www-community/vulnerabilities/Password_Plaintext_Storage)
* [https://cwe.mitre.org/data/definitions/256.html](https://cwe.mitre.org/data/definitions/256.html)
* [https://cwe.mitre.org/data/definitions/312.html](https://cwe.mitre.org/data/definitions/312.html)
* [https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html)
 

## Communication

Communication with e-shop IT team has proved difficult. In the end, having identified an IT security
responsible party on LinkedIn, contact was made using the "request quote" form.

Company said they are aware of issue and are fixing it. They did not accept to receive the analysis. Well, I can't force them.
I attempted multiple time to restablish discussion after 3 months for updates, but the company don't reply anymore

To this day, it's impossible to know if company is doing serious attempt at fixing the issue or just ignoring it. Personal
data are at stake since nearly 6 month without any visible change.