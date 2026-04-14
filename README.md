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


