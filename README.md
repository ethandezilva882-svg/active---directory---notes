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

> 📸 Add screenshot of the deletion error here

> 📸 Add screenshot of the Object tab with the checkbox here

---

### Adjusting Users

After removing the extra OU, the next step was making sure the users
inside each department OU matched the organisational chart. This involved
creating new users where they were missing and deleting ones that no
longer belonged.

> 📸 Add screenshot of the updated OU structure here

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

> 📸 Add screenshot of the Delegate Control window here

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

> 📸 Add screenshot of the PowerShell commands here

> 📸 Add screenshot of Sophie's desktop flag here


  


