---
icon: windows
---

# Active Directory Basics

**Date:** 19.03.2026 **Room Category:**&#x20;

Walkthrough **Core Concept:** Centralized identity management, authentication protocols (Kerberos/NTLM), and the AD hierarchy (OUs, Trees, and Forests).

Active Directory (AD) is a sophisticated directory database and service that stores information about objects on a network such as users, groups, machines, and printers. In a large enterprise environment, it allows for centralized administration of thousands of endpoints from a single cluster of Domain Controllers (DCs).&#x20;

Without AD, managing permissions and security policies would require manual configuration on every individual machine, which is impossible at scale. Mastering these authentication flows is a core requirement for any high-level network or security role.

***

## Task 1: Introduction

Active Directory is effectively the phonebook of the Windows network. It provides a structured way to organize and manage all the resources in a business under a single administrative boundary. It uses the Lightweight Directory Access Protocol (LDAP) to talk to the database and query information about users or assets.

Question:Click and continue learning!

> **Answer:** No answer needed

***

## Task 2: Windows Domains

A Windows Domain centralizes common components of a computer network into a single repository. The server that runs the Active Directory services is known as a Domain Controller (DC). This is the "brain" of the network that handles all requests for authentication and authorization. The DC holds the NTDS.dit file, which is the heart of the AD database containing all usernames and password hashes.

<figure><img src="../.gitbook/assets/image (56).png" alt="" width="339"><figcaption></figcaption></figure>

Question: In a Windows domain, credentials are stored in a centralized repository called…

> **Answer:** Active Directory

Question: The server in charge of running the Active Directory services is called…

> **Answer:** Domain Controller

***

## Task 3: Active Directory

The core of any Windows Domain is the Active Directory Domain Service (AD DS). This service acts as a catalog for all objects that exist on the network.

* **Users:** These are security principals representing people or services. Service accounts are particularly important as they often have higher privileges to run background tasks.
* **Machine Accounts:** Every computer joining the domain gets its own account in the database. For example, a machine named TOM-PC has an account named TOM-PC$. These accounts have their own passwords that are rotated automatically by the system.
* **Security Groups:** These allow for the assignment of permissions over resources to entire groups instead of single users. Default groups like "Domain Admins" have total control over the domain, while "Enterprise Admins" have control over the entire forest.
* **Organizational Units (OUs):** These are logical containers used to group objects so that specific Group Policy Objects (GPOs) can be applied consistently across a specific department or geographic location.

<figure><img src="../.gitbook/assets/image (57).png" alt="" width="563"><figcaption></figcaption></figure>

Question: Which group normally administrates all computers and resources in a domain?

> **Answer:** Domain Admin

Question: What would be the name of the machine account associated with a machine named TOM-PC?

> **Answer:** TOM-PC$

Question: Suppose our company creates a new department for Quality Assurance. What type of containers should we use to group all Quality Assurance users so that policies can be applied consistently to them?

> **Answer:** Organizational Units

***

## Task 4: Managing Users in AD

One of the most powerful features of AD is Delegation. This allows for the assignment of specific administrative rights to non-admin users. For instance, an IT helpdesk worker can be granted the right to reset passwords for a specific OU without being a full Domain Admin. This adheres to the principle of least privilege, reducing the attack surface by ensuring users only have the permissions necessary for their specific job function.

<figure><img src="../.gitbook/assets/image (58).png" alt="" width="398"><figcaption></figcaption></figure>

Practical: Sophie's Password Reset

1. Log in as phillip (Password: Claire2008).
2. Open PowerShell.
3. Run the reset command: Set-ADAccountPassword sophie -Reset -NewPassword (Read-Host -AsSecureString -Prompt 'New Password') -Verbose
4. Force the user to reset on login: Set-ADUser -ChangePasswordAtLogon $true -Identity sophie -Verbose

Question: What was the flag found on Sophie’s desktop?

> **Answer:** THM{thanks\_for\_contacting\_support}

Question: The process of granting privileges to a user over some OU or other AD Object is called…

> **Answer:** delegation

***

## Task 5: Managing Computers in AD

Computers are treated as objects in the same way users are. Organizing them into separate OUs for Servers, Workstations, and Domain Controllers is vital for security. This allows for the application of restrictive security policies on sensitive servers while maintaining more flexible policies for standard user workstations. Proper organization makes the environment significantly easier to audit during a security review.

<figure><img src="../.gitbook/assets/image (60).png" alt="" width="563"><figcaption></figcaption></figure>

Question: After organizing the available computers, how many ended up in the Workstations OU?

> **Answer:** 7

Question: Is it recommendable to create separate OUs for Servers and Workstations? (yay/nay)

> **Answer:** yay

***

## Task 6: Group Policies

Group Policy Objects (GPOs) allow for the configuration of security settings, software installation, and registry tweaks for users and computers across the network from one central location. These settings are stored on the Domain Controller and distributed via the SYSVOL share. When a computer boots up or a user logs in, the system checks the SYSVOL folder for any applicable policies and applies them locally.

<figure><img src="../.gitbook/assets/image (61).png" alt="" width="563"><figcaption></figcaption></figure>

Question: What is the name of the network share used to distribute GPOs to domain machines?

> **Answer:** SYSVOL

Question: Can a GPO be used to apply settings to users and computers? (yay/nay)

> **Answer:** Yay

## Task 7: Authentication Methods

Windows uses two primary protocols for network authentication, each with different security implications.

1. **Kerberos:** This is the default and preferred system for modern Windows environments. It uses a ticketing system involving a Key Distribution Center (KDC). A user proves their identity once to get a Ticket Granting Ticket (TGT), which they then use to request Service Tickets (TGS) for specific resources. This is much more secure than sending hashes across the wire.

<figure><img src="../.gitbook/assets/image (62).png" alt="" width="563"><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (63).png" alt="" width="563"><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (64).png" alt="" width="563"><figcaption></figcaption></figure>

2. **NetNTLM:** This is a legacy challenge-response protocol kept for backward compatibility. It is significantly less secure because it is vulnerable to NTLM relay attacks and "pass-the-hash" techniques. In a hardened environment, NTLM should be disabled in favor of Kerberos wherever possible.

Question: Will a current version of Windows use NetNTLM as the preferred authentication protocol by default? (yay/nay)

> **Answer:** nay

Question: When referring to Kerberos, what type of ticket allows us to request further tickets known as TGS?

> **Answer:** Ticket Granting Ticket

Question: When using NetNTLM, is a user’s password transmitted over the network at any point? (yay/nay)

> **Answer:** nay

***

## Task 8: Trees, Forests, and Trusts

Active Directory scales using a hierarchical structure.

* **Tree:** This is a collection of domains that share a contiguous namespace. For example, https://www.google.com/search?q=dev.company.com and https://www.google.com/search?q=sales.company.com would be part of the same tree under company.com.

<figure><img src="../.gitbook/assets/image (65).png" alt="" width="375"><figcaption></figcaption></figure>

* **Forest:** This is the highest level of the hierarchy. A forest is a collection of one or more trees that share a common schema (the rules for what objects can exist) and a global catalog.

<figure><img src="../.gitbook/assets/image (66).png" alt="" width="563"><figcaption></figcaption></figure>

* **Trusts:** These are logical relationships that allow users in one domain to be authenticated by a Domain Controller in another. Trusts can be one-way or two-way and are essential for mergers and acquisitions where two separate companies need to share resources.

<figure><img src="../.gitbook/assets/image (67).png" alt="" width="563"><figcaption></figcaption></figure>

Question: What is a group of Windows domains that share the same namespace called?

> **Answer:** tree

Question: What should be configured between two domains for a user in Domain A to access a resource in Domain B?

> **Answer:** trust relationship

***

## Conclusion

This walkthrough covers the fundamental components of Active Directory. From the way objects are organized in OUs to the complex ticketing systems used by Kerberos, these concepts form the backbone of modern corporate security. Understanding how these pieces fit together is a prerequisite for moving into more advanced topics like domain escalation and threat hunting.
