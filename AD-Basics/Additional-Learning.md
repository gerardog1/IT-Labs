# What I Learned About Windows Domains & Active Directory

Through this lab, I learned why Active Directory (AD) exists and the real problem it solves. Managing a very small network (a few users and computers) can be done manually by configuring each machine individually. However, as a business scales to dozens or hundreds of users and computers across multiple locations, this approach quickly becomes impractical.

I learned that a Windows domain solves this scalability problem by centralizing administration. Instead of managing users and settings on each computer, everything is handled from Active Directory, which acts as a central repository for users, computers, and policies. The server running these services is known as a Domain Controller (DC).

Key takeaways for me were:

Centralized identity management: User accounts are created once in Active Directory and can be used to log into any domain-joined computer.

Centralized security and policy enforcement: Administrators can apply security policies (like restricting control panel access or admin privileges) across all users and machines from a single place.

Real-world relevance: This explained how environments like schools or workplaces allow users to log into any computer with the same credentials while still enforcing strict restrictions.

Overall, this helped me understand how Active Directory turns user and computer management from a manual task into a scalable, centralized system, which directly supports what I practiced in the hands-on lab.

# What I Learned About Active Directory Objects & Access Control

I learned that the core service behind a Windows domain is Active Directory Domain Services (AD DS). AD DS functions as a centralized directory (or catalog) that stores and manages all network objects, such as users, computers, groups, printers, and shared resources. Understanding these objects helped me see how Windows domains control identity, access, and permissions at scale.

Users
I learned that Active Directory users are considered security principals, meaning they can be authenticated and assigned permissions to access network resources. Users don’t only represent people (employees), but can also represent services (such as IIS or MSSQL). Service accounts are intentionally restricted and only granted the minimum permissions required to run their specific service, reinforcing the principle of least privilege.

Machines
I learned that every computer joined to a domain automatically creates a machine account in Active Directory. Machine accounts are also security principals and have their own credentials. These accounts:

Act as local administrators on their own machines

Are primarily used by the computer itself, not by users

Have automatically rotated, very long passwords (typically ~120 characters)

I also learned how to identify machine accounts by their naming convention, which uses the computer name followed by a dollar sign (e.g., DC01$).

Security Groups
I learned that security groups are used to manage access efficiently by assigning permissions to groups instead of individual users. Users and machines can be added to groups and automatically inherit the group’s permissions. Groups can also be nested inside other groups.

I became familiar with several default domain groups and their importance, such as:

Domain Admins (full control over the domain)

Server Operators (administer domain controllers)

Backup Operators (bypass file permissions for backups)

Account Operators (manage user accounts)

Domain Users, Domain Computers, and Domain Controllers

Understanding these groups helped me see how privilege separation is enforced in enterprise environments.

Security Groups vs Organizational Units (OUs)
A key distinction I learned is that OUs and security groups serve different purposes:

Organizational Units (OUs) are used to apply policies (like Group Policy) to users or computers based on their role. An object can only belong to one OU at a time.

Security Groups are used to grant access to resources such as shared folders or printers, and a user can belong to many groups at once.

This clarified how policy management and permission management are intentionally separated in Active Directory, making large environments both scalable and secure.

# dkdkd

Once a change has been made to any GPOs, it might take up to 2 hours for computers to catch up. If you want to force any particular computer to sync its GPOs immediately, you can always run the following command on the desired computer:
```bash
gpupdate /force
```

# What I Learned About Windows Domain Authentication (Kerberos vs NetNTLM)

I learned that in a Windows domain, all user credentials are centrally stored on Domain Controllers (DCs). Any time a user attempts to access a domain resource (such as a file share, website, or database), the service must verify that user’s credentials with the Domain Controller. This verification is handled using domain authentication protocols.

Windows domains primarily use two authentication protocols:

Kerberos (default for modern Windows domains)

NetNTLM (legacy protocol retained for compatibility)

Although NetNTLM is considered outdated, I learned that many real-world environments still support both protocols.

Kerberos Authentication

I learned that Kerberos is the default and preferred authentication protocol in modern Windows environments. Instead of repeatedly sending credentials, Kerberos uses tickets as proof that a user has already authenticated.

The Kerberos process helped me understand how authentication can be both secure and efficient:

A user initially authenticates to the Key Distribution Center (KDC), which runs on the Domain Controller.

The KDC issues a Ticket Granting Ticket (TGT), which allows the user to request access to other services without re-entering credentials.

The TGT is encrypted using the krbtgt account’s password hash, meaning the user cannot read or modify it.

A session key is shared between the user and the KDC and is reused for future ticket requests.

When accessing a specific service:

The user presents their TGT to request a Ticket Granting Service (TGS) for that specific service.

The KDC returns a TGS encrypted with the service account’s password hash.

The service decrypts the TGS, validates the session key, and grants access.

This showed me how Kerberos avoids password transmission, reduces repeated authentication, and tightly binds access to specific services.

NetNTLM Authentication

I learned that NetNTLM uses a challenge–response mechanism rather than tickets. The key idea is that the user’s password (or hash) is never sent across the network.

The process works as follows:

The server sends a random challenge to the client.

The client uses its NTLM password hash to compute a response.

The server forwards the challenge and response to the Domain Controller.

The Domain Controller verifies the response and confirms whether authentication succeeds.

I also learned that when local accounts are used instead of domain accounts, the server can authenticate the user directly using the locally stored password hash, without contacting the Domain Controller.

Key Takeaways

Through this, I learned:

Why Kerberos is more secure and scalable than NetNTLM

How authentication in a domain relies on centralized trust in the Domain Controller

Why legacy protocols like NetNTLM still exist in enterprise environments despite better alternatives

This knowledge helped me connect authentication theory to how real Active Directory environments actually function in practice.

# What I Learned About Multi-Domain Active Directory (Trees, Forests, and Trusts)

After understanding how a single domain works, I learned how Active Directory scales as organizations grow beyond one simple environment. While a single domain is often enough initially, larger companies eventually require multiple domains to handle legal, administrative, and security boundaries more effectively.

Single Domains and the Need to Scale

I learned that a single domain can technically support a large number of users and computers, but real-world factors—such as different countries, regulations, and IT teams—make a single, complex domain harder to manage and more prone to configuration errors. This is where Active Directory’s multi-domain design becomes important.

Trees

I learned that domains sharing the same namespace can be organized into a tree. Each domain in a tree has its own Active Directory, users, computers, Domain Controllers, and policies, but they are still logically connected.

For example:

A root domain like thm.local

Subdomains such as uk.thm.local and us.thm.local

This structure allows:

Independent administration for each region

Separate Group Policies per domain

Clear separation of authority between IT teams

I also learned about the Enterprise Admins group, which has administrative control across all domains in a tree, while Domain Admins remain limited to their individual domains.

Forests

I learned that a forest is a collection of one or more domain trees that use different namespaces. This becomes especially relevant during mergers or acquisitions.

For example, when two companies with separate domain trees merge, their environments can be joined into a single forest while still maintaining:

Separate namespaces

Independent domain structures

Distinct administrative boundaries

This helped me understand how large enterprises maintain autonomy between business units while still operating within a shared AD environment.

Trust Relationships

I learned that trust relationships are what allow users in one domain to access resources in another. Without trust, cross-domain access is not possible.

Key points I learned about trusts:

One-way trusts allow users from one domain to access resources in another, but not vice versa

Two-way trusts allow mutual access between domains

Domains within the same tree or forest automatically have two-way trusts by default

Most importantly, I learned that trust does not equal access. Even when a trust exists, administrators must explicitly grant permissions to resources. Trust simply allows authorization to be possible—it does not automatically expose resources.

Key Takeaways

From this section, I learned:

How Active Directory scales from a single domain to global enterprise environments

Why trees and forests exist beyond simple OU design

How administrative control and access can be tightly segmented

Why trust relationships are powerful but must be carefully managed

This completed my understanding of how Active Directory is designed to support real-world enterprise growth while maintaining security and control.
