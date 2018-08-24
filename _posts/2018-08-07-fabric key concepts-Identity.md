---
layout: post
title:  "fabric key concepts-Identity"
date:   2018-08-06 23:59:31 +0800
categories: fabric 
tags: Identity MSP PKI
---

* content
{:toc}

## What is an Identity?
The different actors in a blockchain network include peers, orderers, client applications, administrators and more. Each of these actors — active elements inside or outside a network able to consume services — has a digital identity encapsulated in an X.509 digital certificate. **These identities really matter because they determine the exact permissions over resources and access to information that actors have in a blockchain network.**

## A Simple Scenario to Explain the Use of an Identity
PKI certificate authorities and MSPs provide a similar combination of functionalities. A PKI is like a card provider — it dispenses many different types of verifiable identities. An MSP, on the other hand, is like the list of card providers accepted by the store, determining which identities are the trusted members (actors) of the store payment network. MSPs turn verifiable identities into the members of a blockchain network.
![Identity][1]

**MSPs turn verifiable identities into the members of a blockchain network.**

## What are PKIs?
The elements of Public Key Infrastructure (PKI). A PKI is comprised of Certificate Authorities who issue digital certificates to parties (e.g., users of a service, service provider), who then use them to authenticate themselves in the messages they exchange with their environment.
![PKIs][2]

There are four key elements to PKI:

 - Digital Certificates 
 - Public and Private Keys 
 - Certificate Authorities
 - Certificate Revocation Lists

### Digital Certificates
A digital certificate is a document which holds a set of attributes relating to the holder of the certificate. The most common type of certificate is the one compliant with the X.509 standard, which allows the encoding of a party’s identifying details in its structure.
![此处输入图片的描述][3]

### Authentication, Public keys, and Private Keys
Authentication and message integrity are important concepts in secure communications. Authentication requires that parties who exchange messages are assured of the identity that created a specific message. For a message to have “integrity” means that cannot have been modified during its transmission. For example, you might want to be sure you’re communicating with the real Mary Morris rather than an impersonator. Or if Mary has sent you a message, you might want to be sure that it hasn’t been tampered with by anyone else during transmission.

Technically speaking, digital signature mechanisms require each party to hold two cryptographically connected keys: a public key that is made widely available and acts as authentication anchor, and a private key that is used to produce digital signatures on messages. Recipients of digitally signed messages can verify the origin and integrity of a received message by checking that the attached signature is valid under the public key of the expected sender.

**Technically speaking, digital signature mechanisms require each party to hold two cryptographically connected keys: a public key that is made widely available and acts as authentication anchor, and a private key that is used to produce digital signatures on messages.** Recipients of digitally signed messages can verify the origin and integrity of a received message by checking that the attached signature is valid under the public key of the expected sender.
![此处输入图片的描述][4]

### Certificate Authorities
As you’ve seen, an actor or a node is able to participate in the blockchain network, via the means of a **digital identity** issued for it by an authority trusted by the system. In the most common case, digital identities (or simply identities) have the form of cryptographically validated digital certificates that comply with X.509 standard and are issued by a Certificate Authority (CA).

In a blockchain setting, every actor who wishes to interact with the network needs an identity. In this setting, you might say that **one or more CAs** can be used to **define the members of an organization’s from a digital perspective.** It’s the CA that provides the basis for an organization’s actors to have a verifiable digital identity.
![此处输入图片的描述][5]

### Root CAs, Intermediate CAs and Chains of Trust
CAs come in two flavors: Root CAs and Intermediate CAs. Because Root CAs (Symantec, Geotrust, etc) have to securely distribute hundreds of millions of certificates to internet users, it makes sense to spread this process out across what are called Intermediate CAs. These Intermediate CAs have their certificates issued by the root CA or another intermediate authority, allowing the establishment of a “chain of trust” for any certificate that is issued by any CA in the chain. 
![此处输入图片的描述][6]








 


  [1]: http://hyperledger-fabric.readthedocs.io/en/latest/_images/identity.diagram.6.png
  [2]: http://hyperledger-fabric.readthedocs.io/en/latest/_images/identity.diagram.7.png
  [3]: http://hyperledger-fabric.readthedocs.io/en/latest/_images/identity.diagram.8.png
  [4]: http://hyperledger-fabric.readthedocs.io/en/latest/_images/identity.diagram.9.png
  [5]: http://hyperledger-fabric.readthedocs.io/en/latest/_images/identity.diagram.11.png
  [6]: http://hyperledger-fabric.readthedocs.io/en/latest/_images/identity.diagram.1.png