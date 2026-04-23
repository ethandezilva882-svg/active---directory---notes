<img width="1650" height="906" alt="Screenshot 2026-04-23 205518" src="https://github.com/user-attachments/assets/4f639f4a-cb15-480f-bcda-6392e9d560f6" />
 Active Directory Basics — TryHackMe

> Documentation of my progress through the Active Directory Basics 
> room on TryHackMe. Written in my own words as I work through each task.

 
## Task 1 — Windows Domains

Imagine you're the IT guy for a small business with 5 computers and 
5 employees. Easy right? You can walk over to each machine, set up 
accounts manually, fix issues on the spot. No big deal.

Now imagine that same business blows up overnight — 157 computers, 
320 users, spread across 4 different offices. You can't manually 
configure every machine anymore. You can't fly to every office to 
fix one person's account. That approach just doesn't scale.

That's exactly the problem Windows Domains solve.




### So What is  Windows Domain?


A Windows Domain is basically a way to group all your users and 
computers under one roof and manage them from a single place. 
That central place is called **Active Directory (AD)**.

The server that hosts and runs Active Directory is called a 
**Domain Controller (DC)**. Think of it as the brain of the network 
— everything goes through it.

<img width="1912" height="878" alt="Screenshot 2026-04-14 220725" src="https://github.com/user-attachments/assets/d1901f57-9ca1-4921-b639-8a9b98e4a9f5" />



### Why Does This Matter?

Two big reasons:

- **Centralised identity management** — Instead of creating a user 
account on every single machine, you create it once in AD and it 
works everywhere on the network.

- **Security policy management** — Admins can push rules and 
restrictions to every computer on the network from one place. 
No going machine by machine.



### Real World Example

You've probably already used this without knowing it.

Remember at school or university — you had one username and password 
that worked on any computer in the building? That's Active Directory 
doing its job. Your credentials weren't saved on each machine. 
When you logged in, the computer checked with the Domain Controller 
to verify who you are.

Also remember how you couldn't open the Control Panel or install 
anything on school computers? That's AD enforcing group policies 
across the whole network.



### Lab Setup

For this room I'm working as the IT admin for THM Inc., managing 
the domain **THM.local** through a pre-configured Domain Controller. 
Connected via RDP using admin credentials provided by the lab.

<img width="1911" height="908" alt="Screenshot 2026-04-14 220609" src="https://github.com/user-attachments/assets/d0de26d0-85e8-4385-b21f-2936d53d83f5" />





## Task 2 — Active Directory Objects

The core of any Windows Domain is **Active Directory Domain Services (AD DS)**.
It acts as a central catalogue holding information on every object that exists
on the network. These objects include users, groups, machines, printers,
shared folders and more. Everything in your domain is represented as an object
inside AD DS.

### Users

Users are the most common object type in Active Directory. They fall under a
category called **security principals** — meaning they can be authenticated
by the domain and assigned permissions over resources like files or printers.
Basically, a security principal is any object that can interact with and act
upon resources in the network.

Two types of users exist:

- **People** — regular employees or anyone in the organisation who needs
  access to the network and its resources
- **Service accounts** — accounts created specifically to run services like
  IIS or MSSQL. These only have the minimum permissions needed to run that
  specific service and are not meant to be used by actual people

  <img width="1706" height="937" alt="Screenshot 2026-04-15 111947" src="https://github.com/user-attachments/assets/8f731acf-a460-4067-92f6-679b09981ef0" />


### Machines

Every computer that joins the Active Directory domain automatically gets its
own machine object created in AD. Like users, machines are also considered
security principals, meaning they have their own accounts and can be assigned
permissions.

Key things to know about machine accounts:
- They act as local administrators on their assigned computer
- They are not meant to be accessed by people — only the computer itself uses them
- Passwords are automatically rotated and consist of 120 random characters,
  making them practically impossible to guess
- Naming format always follows: computer name + dollar sign
  (e.g. a computer named DC01 will have the machine account **DC01$**)

  <img width="1919" height="869" alt="Screenshot 2026-04-15 114734" src="https://github.com/user-attachments/assets/36d8c5b5-5307-4a74-bef8-4e9e95172bf6" />


### Security Groups

Instead of assigning permissions to users one by one, security groups let
you manage access at scale. You add users to a group and they automatically
inherit all the permissions that group has. Groups can contain users,
machines, and even other groups.

Key default groups that come with any domain:

| Group | What it does |
|---|---|
| Domain Admins | Full administrative rights over the entire domain including all DCs |
| Server Operators | Can manage Domain Controllers but cannot change admin group memberships |
| Backup Operators | Can access any file on the domain regardless of permissions — used for backups |
| Account Operators | Can create and modify user accounts in the domain |
| Domain Users | Automatically includes every user account in the domain |
| Domain Computers | Automatically includes every computer joined to the domain |
| Domain Controllers | Contains all Domain Controllers in the domain |


<img width="952" height="813" alt="Screenshot 2026-04-15 115006" src="https://github.com/user-attachments/assets/93e70ec1-f7d3-412f-aa69-f40296cb4d50" />


### Active Directory Users and Computers

To actually manage all of these objects — users, groups, machines — you open
a tool called **Active Directory Users and Computers** from the start menu
on the Domain Controller. This gives you a visual interface showing the full
hierarchy of everything in your domain.

Objects inside AD are organised into **Organisational Units (OUs)**.
OUs are basically containers that group users and machines together,
usually based on their role or department in the organisation. This makes
it much easier to apply specific policies to specific groups of people.

Important: a user can only belong to **one OU at a time**.

The domain I'm working on (THM.local) already has a THM OU set up with
five child OUs underneath it — IT, Management, Marketing, R&D, and Sales —
each representing a different department in the business.

<img width="255" height="138" alt="Screenshot 2026-04-15 115220" src="https://github.com/user-attachments/assets/ec77ab70-b59c-44ab-840a-472ef7016c0f" />


Windows also automatically creates several default containers in every domain:

- **Builtin** — contains default groups available to any Windows host
- **Computers** — where any new machine joining the domain gets placed by default
- **Domain Controllers** — the default OU holding all DCs in the network
- **Users** — holds default user accounts and groups that apply domain-wide
- **Managed Service Accounts** — stores accounts used by services running
  on the domain

  <img width="256" height="137" alt="Screenshot 2026-04-15 115327" src="https://github.com/user-attachments/assets/6a67c39f-7a1a-4ab0-acba-7a245f5ca7f2" />



### Security Groups vs OUs — What is the Difference?

Both are used to organise users and computers but they serve completely
different purposes and this is a common point of confusion:

- **OUs** exist to apply policies and configurations to sets of users based
  on their role. Since policies are role-specific, a user can only be in
  one OU at a time — you wouldn't want two conflicting policy sets applied
  to the same person.

- **Security Groups** exist to grant access to resources like shared folders
  or network printers. A user can belong to many groups at once because
  they may need access to multiple different resources across the domain.


## Task 3 — Managing OUs, Users and Delegation

### The Scenario

As the new domain administrator for THM Inc., the first job was to audit
the existing AD structure and bring it in line with the company's current
organisational chart. This meant cleaning up outdated OUs, adjusting users
across departments, and setting up delegation for the IT support team.

---

### Deleting OUs

The first thing I noticed was an extra department OU in AD that no longer
exists in the business — removed due to budget cuts. Deleting it wasn't
straightforward though.

By default, OUs are protected against accidental deletion. Trying to
right-click and delete it throws an error. To get around this:

1. Go to **View → Enable Advanced Features** in AD Users and Computers
2. Right-click the OU → **Properties → Object tab**
3. Uncheck **"Protect object from accidental deletion"**
4. Now you can delete the OU — along with everything inside it


<img width="499" height="198" alt="Screenshot 2026-04-16 214647" src="https://github.com/user-attachments/assets/45bfb100-dcb3-4c38-8820-c52409a6c591" />

<img width="935" height="516" alt="Screenshot 2026-04-16 214701" src="https://github.com/user-attachments/assets/6b7b0219-1281-4475-9b20-e16ba8349238" />

<img width="492" height="570" alt="Screenshot 2026-04-16 214717" src="https://github.com/user-attachments/assets/bcd94a7a-6c61-4703-a651-7c1bc6ad9db6" />


### Adjusting Users

After removing the extra OU, the next step was making sure the users
inside each department OU matched the organisational chart. This involved
creating new users where they were missing and deleting ones that no
longer belonged.

<img width="1902" height="886" alt="Screenshot 2026-04-16 123452" src="https://github.com/user-attachments/assets/312a9e8b-f978-46a0-aa94-87094700af34" />


---

### Delegation

Delegation in AD lets you give a specific user control over an OU without
making them a full Domain Admin. This is useful for things like letting
IT support reset passwords without having access to everything else.

In this case, Phillip from IT support was delegated control over the
Sales, Marketing and Management OUs — specifically the ability to reset
passwords.

**Steps to delegate control:**
1. Right-click the OU → **Delegate Control**
2. Add the user (type the name and hit **Check Names** to autocomplete)
3. Select the task to delegate — in this case **Reset user passwords**
4. Confirm and finish

<img width="1884" height="898" alt="Screenshot 2026-04-16 123027" src="https://github.com/user-attachments/assets/ed6b9717-d924-4ac7-a99d-8dbe89aa6e6a" />


---

### Testing Delegation via PowerShell

Since Phillip doesn't have access to AD Users and Computers, password
resets have to be done through PowerShell. Used the following commands
logged in as Phillip via RDP:

**Reset Sophie's password:**
```powershell
Set-ADAccountPassword sophie -Reset -NewPassword (Read-Host -AsSecureString -Prompt 'New Password') -Verbose
```

**Force password change on next login:**
```powershell
Set-ADUser -ChangePasswordAtLogon $true -Identity sophie -Verbose
```

After resetting the password, logged into Sophie's account via RDP
and retrieved the flag from her desktop.


<img width="1857" height="784" alt="Screenshot 2026-04-16 131722" src="https://github.com/user-attachments/assets/2696fc82-0e9f-4e61-8e1d-3610cf621740" />

<img width="1912" height="902" alt="Screenshot 2026-04-16 214402" src="https://github.com/user-attachments/assets/354cad75-4d6f-40a1-92d1-6a09b8e0fc12" />




## Task 4 — Managing Computers in AD

### The Problem with Default Computer Placement

By default, every machine that joins the domain (except Domain Controllers)
gets dumped into a container called **Computers**. This works fine at small
scale but becomes a problem when you need to apply different policies to
different types of machines — you can't do that if everything is sitting
in the same container.

<img width="1804" height="861" alt="Screenshot 2026-04-22 131824" src="https://github.com/user-attachments/assets/ebcef270-44f9-4040-91af-8ec7f51e5c1f" />


---

### The Three Categories of Machines

**Workstations**
The most common device type in any domain. These are the day-to-day
machines that regular users log into for work and browsing. No privileged
accounts should ever be signed into a workstation.

**Servers**
The second most common device type. Servers exist to provide services
to users or other servers on the network. They require different policies
compared to regular workstations.

**Domain Controllers**
The most sensitive machines in the entire network. DCs store hashed
passwords for every user account in the domain, making them the highest
priority target for attackers. Windows automatically creates an OU for
these — they don't go in the Computers container.

---

### Organising the Computers

To apply proper policies to each device type, two new OUs were created
directly under the **thm.local** domain:

- **Workstations** — for all personal computers and laptops
- **Servers** — for all server machines

Domain Controllers already have their own default OU created by Windows.

<img width="1827" height="914" alt="Screenshot 2026-04-22 132746" src="https://github.com/user-attachments/assets/6d7d8c2a-5fca-4a70-9acf-2bc3d4a91115" />


After creating the OUs, all machines were moved out of the default
Computers container and into their appropriate OU:

- Personal computers and laptops → **Workstations OU**
- Servers → **Servers OU**

This separation means policies can now be configured independently
for each device type without affecting the others.

<img width="907" height="825" alt="Screenshot 2026-04-22 133746" src="https://github.com/user-attachments/assets/756d8389-bedb-40ca-8498-5056a371e8ec" />



<img width="915" height="919" alt="Screenshot 2026-04-22 133730" src="https://github.com/user-attachments/assets/fc355830-948f-4c42-9f66-fa7ac1f73d8a" />




## Task 6<img width="816" height="913" alt="Screenshot 2026-04-23 204557" src="https://github.com/user-attachments/assets/96bd8d81-86f8-43a5-ac9a-e98bd14fdee3" />
 — Group Policy Objects (GPOs)

### What are GPOs?

Up until now, OUs have just been used to organise users and machines.
The real purpose of OUs is to allow different policies to be applied
to different groups of people and devices. This is done through
**Group Policy Objects (GPOs)**.

A GPO is essentially a collection of settings that gets applied to
an OU. GPOs can target either users or computers — or both — allowing
admins to enforce specific configurations and security baselines
across the domain without touching each machine individually.

---

### Group Policy Management Tool

GPOs are managed through the **Group Policy Management** tool,
accessible from the start menu on the Domain Controller. Opening it
shows the full OU hierarchy and any existing GPOs already in place.

In the THM lab, three GPOs already exist by default:

- **Default Domain Policy** — linked to the entire thm.local domain
- **RDP Policy** — also linked to the entire thm.local domain
- **Default Domain Controllers Policy** — linked only to the Domain Controllers OU

An important thing to note: any GPO applied to an OU automatically
applies to all child OUs underneath it as well. So the Default Domain
Policy affects every single OU in the domain including Sales, Marketing
and so on.

<img width="816" height="913" alt="Screenshot 2026-04-23 204557" src="https://github.com/user-attachments/assets/8b4c5fea-b8a3-4525-b7ac-e6e12129a6e4" />


---

### Inside a GPO

Each GPO has two main tabs worth knowing:

- **Scope** — shows where the GPO is linked in AD and what users
  or computers it applies to. By default GPOs apply to all
  **Authenticated Users**, meaning every user and PC in the linked OU.

- **Settings** — shows the actual configurations the GPO enforces.
  Settings are split into Computer Configurations and User Configurations.


---

### Editing a GPO — Password Policy

To demonstrate how GPO editing works, the minimum password length
policy was changed to require at least 10 characters for all users.

Path to the setting:
**Computer Configuration → Policies → Windows Settings → Security
Settings → Account Policies → Password Policy**

<img width="1650" height="906" alt="Screenshot 2026-04-23 205518" src="https://github.com/user-attachments/assets/33f16159-344b-4adc-a7e2-b0d9432692a5" />

---

### How GPOs Are Distributed

GPOs are pushed out to the network through a shared folder called
**SYSVOL**, stored on the Domain Controller at:
`C:\Windows\SYSVOL\sysvol\`

All domain users have access to this share and sync their GPOs
periodically. Changes can take up to 2 hours to propagate across
the domain. To force an immediate update on any machine, run:

```powershell
gpupdate /force
```

---

### GPOs Created for THM Inc.

Two GPOs were created and configured as part of this task:

---

#### 1. Restrict Control Panel Access

**Goal:** Block all non-IT users from accessing the Control Panel.

**Setting used:**
User Configuration → Policies → Administrative Templates → Control Panel
→ **Prohibit access to Control Panel and PC settings** — set to Enabled

**Linked to:** Sales, Marketing and Management OUs

This means IT users are unaffected and retain full access, while
everyone else gets blocked from changing system settings.

<img width="1660" height="905" alt="Screenshot 2026-04-23 212014" src="https://github.com/user-attachments/assets/a37ba96c-eb60-4a63-8553-5433121b87ae" />


---

#### 2. Auto Lock Screen

**Goal:** Automatically lock workstations and servers after 5 minutes
of inactivity to prevent unsupervised sessions being left open.

**Setting used:**
Computer Configuration → Policies → Windows Settings → Security Settings
→ Local Policies → Security Options
→ **Interactive logon: Machine inactivity limit** — set to 300 seconds

**Linked to:** Root domain (thm.local) so it cascades down to
Workstations, Servers and Domain Controllers OUs automatically.

Note: OUs containing only users like Sales or Marketing will ignore
the Computer Configuration settings, so there's no conflict.

<img width="1615" height="901" alt="Screenshot 2026-04-23 212849" src="https://github.com/user-attachments/assets/976ff0da-436d-4062-9b7f-880161ab4dc0" />


---

### Verification

Logged in as Mark from the Marketing department via RDP using
THM\Mark to verify the GPOs were working correctly.

- Attempting to open the Control Panel returned an access denied
  message — confirming the restriction GPO was applied correctly.
- Screen auto-lock after 5 minutes of inactivity also confirmed
  the Auto Lock Screen GPO was working as expected.

<img width="1652" height="906" alt="Screenshot 2026-04-23 210034" src="https://github.com/user-attachments/assets/f5d3a9dd-36f7-4164-8ad9-57a504b678bc" />
