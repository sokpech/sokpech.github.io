---
title: Execute-Assembly VS Traditional Execution
description: Execute-Assembly is a technique that allows operators to run a .NET assembly (C# executable) directly in memory, without writing it to disk.
date: 2025-08-24
categories:
  - Post_Exploit
tags:
  - RedTeam
math: true
mermaid: true
---


> In this blog post, we won’t discuss about AV/EDR bypass techniques. Instead, we’ll dive straight into post-exploitation enumeration, focusing on gathering information about the user and environment via Inline Execute-Assembly.

## Overview
Imagine you have already compromised a user account or gained initial access in an Assume Breach scenario within a hardened environment. The next step is to perform post-exploitation enumeration to identify potential attack path for privilege escalation, lateral movement, and to map out user privileges and environment structure [Active Directory].

Manual enumeration in such environments is time-consuming. Automation tools such as **Seatbelt**, **SharpUp**, **BloodHound**, and others can significantly speed up this process. However, in a hardened environment, you cannot rely on these tools, as they are often detected by IOA/IOC signatures. Effective post-exploitation enumeration in this context requires careful planning, stealth techniques, and possibly bypassing detection mechanisms before leveraging automation.


## The Problem with Traditional Approaches
- PowerShell’s golden era and why defenders now heavily monitor it.
- Limitations of dropping binaries (AV/EDR signatures, file I/O).
- The native Windows binaries (LOLbins) aren’t always enough.

> For example, **net.exe** cannot enumerate nested group memberships, meaning if a group contains other groups, the tool will not expand and list those indirect members.
  

## What is Execute-Assembly?
**Execute-Assembly** is a post-exploitation capability that allows operators to run .NET assemblies (compiled C# executables) directly in memory, without writing them to disk.

Instead of dropping an executable and running it — which leaves forensic artifacts and risks of AV/EDR detection — the assembly is injected into a process and executed through the .NET Common Language Runtime (CLR). This makes the execution stealthier and more flexible for the operator.


## Why Do Use It?
The advantage of this technique is that it does not require transferring or uploading any additional tools to the target victim machine. The benefit:
- **Stealth**: Avoids leaving binaries on disk, reducing the chance of file-based detection.
- **Flexibility**: Assemblies can be executed on demand, only when needed.
- **Speed**: No need for uploading or cleanup of executables.
- **Detection Testing**: Provides a realistic way to test how well defenders can spot in-memory attacks.

> For example, you can compile C# .NET assembly code on your attacker machine and execute the .NET assemblies directly within an existing process, without spawning a new child process. This allows for in-memory execution, reducing the chance of detection by process monitoring tools.
  

## Common Use Cases
### #1 Nested Group
> I wanted to enumerate nested groups in the target environment. To achieve this, I built a C# .NET assembly on my attacker machine and executed it through the current session in-memory execution without creating a new process.

![Description of image](assets/post/2025-08-24-Execute-Assembly/1_NestedGroup.png)

### #2 User Privileges
> I wanted to determine what privileges the compromised user has and whether there are any misconfigurations in permission controls that could be exploited

![Description of image](assets/post/2025-08-24-Execute-Assembly/2_ACL.png)

### #3 Kerberoast_Delegation
>  I wanted to enumerate accounts configured with Kerberos delegation, as this information can be valuable for identifying potential attack opportunities.

![Description of image](assets/post/2025-08-24-Execute-Assembly/3_Kerberoast_Delegation.png)

### #4 Dumping The Tickett From Memory
I attempted to dump the Kerberos ticket from memory using Rubeus in order to capture and analyze the authentication artifacts present within the system.

![Description of image](assets/post/2025-08-24-Execute-Assembly/4_Rubeus.png)

## Conclusion

**Execute-Assembly** remains one of the most versatile and stealthy techniques in modern post-exploitation. By enabling inline execution of .NET assemblies, it allows operators to run powerful tools directly in memory without touching disk, minimizing detection risk and maximizing operational efficiency. Its flexibility makes it suitable for a wide range of tasks—from credential harvesting to lateral movement—while its fileless nature keeps defenders on their toes.