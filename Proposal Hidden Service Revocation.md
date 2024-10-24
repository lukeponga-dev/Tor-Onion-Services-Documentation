# Proposal: Hidden Service Revocation

**Filename**: xxx-rend-revoke.txt  
**Title**: Hidden Service Revocation  
**Author**: Nathaniel Johnson  
**Created**: 2015-03-04  
**Status**: Draft

Hidden service operators need to be able to revoke trust in their hidden service if they know or suspect their hidden service secret key has been compromised.

## Motivation

A hidden service operator can determine that the hidden service secret key is compromised in several ways today. Forensic traces of intrusion may be detected on the hidden service server, or valid signed descriptors for the service may be published by someone other than the legitimate operator in a traffic hijacking attack.

Even though it is possible for the operator to detect a hidden service key is compromised, there is no satisfactory way for a hidden service operator to notify its users of this fact. The hidden service may display a message to its users warning of the compromise and directing them to a new supposedly uncompromised .onion domain, but a user has no way to know whether such a message was posted by the legitimate hidden service operator or a hijacker.

A revocation system where anyone possessing the hidden service secret key can revoke trust in the key solves this problem.

## Overview

This revocation system is built on the hidden service directory system. A revocation takes the form of a hidden service descriptor which provides no way to contact the hidden service (i.e., zero introductory points), and contains a "revoked" line of the following format:

```text
"revoked" NL
