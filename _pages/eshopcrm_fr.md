---
layout: single
title:  Shopping en soldes
permalink: /security/findings/the-eshop-crm-fr/
author_profile: true
toc: true
---

Données personnelles. Tout doit partir :/

[English version available](/security/findings/the-eshop-crm)
## Summary
 
 |       |    |
 | --------------|------------|
 | Découverte | 1/9/2021 | 
 | Date de notification | 15/9/2022 |
 | Status | Divulgation partielle | 
 | Type | Fuite de données personnelles, politique de stockage de mots de passe |
 | Divulgation partielles | 4/2/2023 |  
 | Date de divulgation estimée | Inconnue (En attente de fix du webshop) |
 
## Description

En septembre 2022, de multipes vulnérabilités de sécurité ont été trouvées sur une site belge de gestion de clients.
Une de ces vulnérablités donne à l'attaquant un accès aux données CRM de l'ensemble des clients au cours des trois dernières années.
Les données extractibles sont
* Données personelles (nom, addresse, téléphone, factures) des clients, et un cauchemard RGPD à gérer.
* Trouver les prix et réductions appliquées aux gros clients ou clients professionnels
* Extraction des prospects ainsi que les prix qui leur ont été proposés

Cette société dispose de plusieurs magasins physiques en Belgique et de pas mal de clients.

Après de multiples tentatives pour les contacter par des moyen sécurisés, cette société ne semble pas décidée à fixer les problèmes.
   
## Les soucis

Ce site client souffre de deux problèmes majeurs facilement identifiables:

* L'outil intégré au site permet de consulter les facture et devis de l'entièreté de la société.
* Les mot de passe sont stockés en clair ou d'une façon reversible dans la base de donnée. Ces mot de passe sont aussi recopiés dans les emails.

### Accès en lecture à l'intégralité des factures, devis et tickets de caisse

Le magasin physique à ces clients un moyen de retrouver leurs factures en utilisant leur adresse email.
Ce point d'accès ne fait aucune vérification d'accès aux factures, permettant à n'importe quel client de voir les données
de n'importe qui d'autre.
Ce point d'accès peut facilement être exploité pour accéder aux données suivantes de n'importe qui
* nom complet
* addresse de facturation et de livraison
* email
* phone number

Mais aussi à des secret commerciaux comme
* Les chiffres ventes journalières, mensuelles et annuelles
* Les rabais et remises accordées aux clients professionels
* Les devis en cours

#### En pratique

Lorsque vous vous logguez, vous avez accès à vos factures:

[![Web page showing a list of bills. Name, number, amount and date obfuscated](/assets/images/webshop-2022-obfuscated/billing-page.png)](/assets/images/webshop-2022-obfuscated/billing-page.png)

La petite icône "acrobat" à droite vous amènes sur une page avec une url de la forme

> https://&lt;website&gt;/DynamicScript/Reporting/Index/123456?reportId=197

Où 123456 est le numéro unique de la facture.

[![Web page showing a bill. Name, number, amount, date and company obfuscated](/assets/images/webshop-2022-obfuscated/onebill.png)](/assets/images/webshop-2022-obfuscated/onebill.png)

Si vous remplacez ce nombre dans l'url par un autre, par exemple en ajoutant ou en retranchant 1, vous avez accès à une autre facture,
concernant une personne avec laquelle vous n'avez rien à voir. Ce genre de manipulation est particulièrement facilitée par
le fait que les numéros de facture se suivent. Ils sont incrémentés un à un.
Vous désirez connaitre l'adresse de la personne qui vous suivait dans la file au magasin. 
Récupérez simplement la facture suivante dans le système.

Dans cet example, en changeant le numéro, nous pouvont voir la facture d'un tiers et voir qu'il bénéficie d'une
remise de 18% sur ses factures.

[![Web page showing a bill. Name, number, amount, date and company obfuscated. Bill shows a 18% discount](/assets/images/webshop-2022-obfuscated/strangerbill.png)](/assets/images/webshop-2022-obfuscated/strangerbill.png)

Dans cet autre exemple, nous récupérons un ticket de vente au comptoir.

[![Web page showing a bill. Name, number, amount, date and company obfuscated. Bill shows it was established at counter](/assets/images/webshop-2022-obfuscated/over-the-top.png)](/assets/images/webshop-2022-obfuscated/over-the-top.png)

Pour rajouter au fun, la section factures dispose d'une fonctionnalité "sauver en csv" et "sauver en texte" qui peux facilement
être exploitée pour de l'extraction automatisée de données.

[![Screenshot showing a save as csv option](/assets/images/webshop-2022-obfuscated/as-csv.png)](/assets/images/webshop-2022-obfuscated/as-csv.png)

[![Screenshot showing a parial csv with items, price and total](/assets/images/webshop-2022-obfuscated/csv.png)](/assets/images/webshop-2022-obfuscated/csv.png)

Documentation afférente:
*	[https://owasp.org/www-community/Broken_Access_Control](https://owasp.org/www-community/Broken_Access_Control)
*	[https://owasp.org/Top10/A01_2021-Broken_Access_Control/](https://owasp.org/Top10/A01_2021-Broken_Access_Control/)
*	[https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html](https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html)

### Stockage de mots de passe en clair

Le stockage de mots de passe est très faible sur le site web. Ils sont stockés dans la base de données en clair.
En cas de fuite de données (nooon, mais mon bon monsieur, un site bien sécurisé comme ça, ça n'arriverais pas hein? Pas vrai?)
c'est une nouveau set de mots de passe + emails qui se retrouve dans la nature.

#### En pratique

Si vous vous rendez sur le site web et sélectionnez la fonction "mot de passe perdu", vous avez la surprise de voir
votre mot de passe existant arriver dans votre boite email. Cela signifie que quelque chose d'aussi sensible, secret et privé
que le mot de passe est stocké en clair dans la base de données et envoyé sans aucune forme de sécurité dans un email en clair.

Documentation afférente:
* [https://owasp.org/www-community/vulnerabilities/Password_Plaintext_Storage](https://owasp.org/www-community/vulnerabilities/Password_Plaintext_Storage)
* [https://cwe.mitre.org/data/definitions/256.html](https://cwe.mitre.org/data/definitions/256.html)
* [https://cwe.mitre.org/data/definitions/312.html](https://cwe.mitre.org/data/definitions/312.html)
* [https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html)
 
## Communication

La communication avec l'équipe IT de cet e-shop s'est avérée difficile. Ayant finalement pu identifier un responsable de
la sécurité de cette société sur LinkedIn, un contact a pu être établi via le formulaire "demande de prix".

Au cours de ce contact, on m'a informé que l'équipe était au courant des problèmes de sécurités multiple sur le site
et travaillaient à les corriger. Ils n'ont pas voulu recevoir l'analyse des problèmes et je ne peux les forcer à la lire.
Plusieurs tentatives on été faites après 3 mois pour rétablir la discution, mais ils ne répondent plus à mes demandes de suivi.

A ce jour il est impossible de savoir si la société compte sérieusement résoudre ce problème. Les données personnelles
des clients sont en jeu depuis près de 6 mois sans changement visible de leur part.
