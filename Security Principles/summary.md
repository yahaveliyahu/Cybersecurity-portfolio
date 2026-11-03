# Security Principles – Summary

## Overview
In this room, I learned the core principles and concepts that security is built on.  
Before choosing any protection, you need to know **who the adversary is** – protecting a laptop from a toddler is very different from protecting it from industrial espionage.

No system is 100% secure. The goal is to improve the **security posture** so attacks become harder, slower, and less likely to succeed.

---

## Key Terms
- **Asset** – Anything of value we want to protect (data, devices, services)
- **Vulnerability** – A weakness an attacker could exploit
- **Threat** – A potential danger that could exploit a vulnerability
- **Risk** – How likely a threat is to exploit a vulnerability, and how much damage it would cause
- **Security control** – Any measure that reduces risk (a lock, a password, a firewall)

---

## The CIA Triad
Security is judged through three security functions:

- **Confidentiality** – Only the intended people can access the data
- **Integrity** – Data cannot be altered, and any alteration can be detected
- **Availability** – The system or service is available when needed

Example – online shopping:
- Confidentiality: the credit card number is disclosed only to the payment processor
- Integrity: an intruder cannot change the shipping address
- Availability: the website or app is up when you want to order

The emphasis doesn't have to be equal. A university announcement is not confidential, but its integrity is critical.

### Practice scenarios I solved
| Scenario | Pillar |
|---|---|
| A bank teller verifies the signature on a cheque | Integrity |
| Troops are told not to share their location | Confidentiality |
| A police officer suspects registration papers are fake | Integrity |
| Two companies keep the details of an agreement secret | Confidentiality |
| A hotel requires its WiFi to work 24/7 | Availability |

Quick rule: *secret / private* → Confidentiality, *fake / forged / altered / verify* → Integrity, *down / unreachable / 24/7* → Availability.

---

## Beyond CIA
- **Authenticity** – The data really comes from the claimed source (not fraudulent or counterfeit)
- **Nonrepudiation** – The source cannot deny being the source of the data

Example: a company receiving an order for 1,000 cars must confirm the customer really placed it (authenticity), and that the customer can't later deny it (nonrepudiation).

### Parkerian Hexad
Six security elements: **Availability, Utility, Integrity, Authenticity, Confidentiality, Possession**.
- **Utility** – The information must be useful. An encrypted disk whose key was lost is still available, but has no utility.
- **Possession** – Protecting information from unauthorised taking, copying, or controlling (e.g. a stolen backup drive, or data encrypted by ransomware).

---

## The DAD Triad
DAD is the opposite of CIA – it describes how attacks break each pillar:

| Attack | Breaks | Example |
|---|---|---|
| **Disclosure** | Confidentiality | Customer records stolen and dumped online |
| **Alteration** | Integrity | Patient medical records modified, leading to wrong treatment |
| **Destruction / Denial** | Availability | Main and backup power switched off, shutting down the network |

Pushing confidentiality and integrity to an extreme can restrict availability, and maximising availability can weaken confidentiality and integrity. Good security requires **balance** between the three.

---

## Security Models
Security models describe how to build a system that enforces one or more security functions.

### Bell-LaPadula – Confidentiality
- **Simple Security Property** – *No read up*: a subject can't read objects above its clearance
- **Star Security Property** – *No write down*: a subject can't write to objects at a lower level (prevents leaks)
- **Discretionary-Security Property** – An access matrix defines read/write permissions
- Short form: **write up, read down**
- Limitation: not designed to handle file sharing

### Biba – Integrity
- **Simple Integrity Property** – *No read down*: a high-integrity subject can't read lower-integrity data
- **Star Integrity Property** – *No write up*: a low-integrity subject can't write to higher-integrity data
- Short form: **read up, write down** – the mirror image of Bell-LaPadula
- Limitation: does not handle insider threats

### Clark-Wilson – Integrity
- **CDI (Constrained Data Item)** – Data whose integrity we protect
- **UDI (Unconstrained Data Item)** – Everything else, such as user and system input
- **TP (Transformation Procedure)** – Approved operations that change CDIs while keeping them valid
- **IVP (Integrity Verification Procedure)** – Checks that CDIs are valid
- Example: a bank teller changes balances only through the bank's transaction software

### Comparison
| Model | Protects | Rules |
|---|---|---|
| Bell-LaPadula | Confidentiality | No read up, no write down |
| Biba | Integrity | No read down, no write up |
| Clark-Wilson | Integrity | Protected data changes only through approved procedures |

### Goguen-Meseguer
One of the additional models mentioned in the room. It is the basis of **noninterference**: the actions of users in one domain must not affect what users in another domain can observe. Subjects are grouped into predefined **domains** and can act only on predefined objects.

It also addresses **covert channels** and **side channels** – indirect ways information can leak, such as response timing, system load, or shared resources – which read/write rules like Bell-LaPadula and Biba don't cover.

---

## Defence in Depth
Also called **Multi-Level Security**. Instead of relying on one protection, you stack several layers.  
Analogy: a locked drawer, inside a locked room, inside a locked apartment, inside a gated building, with cameras along the way. The layers won't stop every thief, but they block most and slow down the rest.

---

## Trust but Verify vs Zero Trust
- **Trust but Verify** – Trust is given, but behaviour is checked through logs. Since reviewing everything manually isn't feasible, it relies on automated tools such as proxies, IDS, and IPS. The check usually comes **after** access.
- **Zero Trust** – Treats trust as a vulnerability: *"never trust, always verify."* No device is trusted because of its location or ownership. Authentication and authorisation are required **before** accessing any resource, which also helps against insider threats and contains damage after a breach.
- **Microsegmentation** – A Zero Trust implementation where a network segment can be as small as a single host, and moving between segments requires authentication and access control checks.

Example – a contractor's laptop downloads a large amount of data overnight:
| Response | Principle |
|---|---|
| The security team reviews the proxy logs next morning | Trust but Verify |
| The laptop must authenticate before reaching any file server | Zero Trust |
| The network is microsegmented, so the laptop reaches only the one host it needs | Zero Trust |

Zero Trust has practical limits – applying it everywhere can hurt the business – but it should be applied wherever feasible.

---

## ISO/IEC 19249
An international standard listing architectural and design principles for secure products, systems, and applications.

### 5 Architectural Principles
1. **Domain Separation** – Related components are grouped into domains with shared security attributes (e.g. x86 rings: kernel in ring 0, user apps in ring 3). Included in the Goguen-Meseguer model.
2. **Layering** – Structuring a system into layers allows security policies at each level (e.g. the OSI model). Related to Defence in Depth.
3. **Encapsulation** – Hide internal implementation and expose only controlled methods or APIs (e.g. a clock's `increment()` method instead of direct access to the seconds variable).
4. **Redundancy** – Backup components ensure availability and integrity (e.g. dual power supplies, RAID 5 with parity).
5. **Virtualization** – Multiple operating systems share hardware, providing sandboxing for safe detonation and observation of malware.

### 5 Design Principles
1. **Least Privilege** – Give only the permissions needed for the task ("need-to-know")
2. **Attack Surface Minimisation** – Reduce possible entry points, e.g. disable unneeded services
3. **Centralized Parameter Validation** – Validate all input in one shared place, so one fix protects everything
4. **Centralized General Security Services** – Centralise services such as authentication (e.g. Active Directory), while avoiding a single point of failure
5. **Preparing for Error and Exception Handling** – Design systems to **fail safe** (a crashed firewall blocks all traffic) and make sure error messages don't leak confidential data

### Practice scenarios I solved
| Scenario | Design principle |
|---|---|
| Turning off an insecure server that isn't critical to the business | Attack Surface Minimisation |
| A new sales rep gets access only to products and prices | Least Privilege |
| ATM code that handles network disconnection and power failure | Error and Exception Handling |

---

## Vulnerability vs Threat vs Risk
- **Vulnerability** – The weakness
- **Threat** – The potential danger that could exploit it
- **Risk** – The likelihood of exploitation combined with its impact on the business

Example: a web server runs old software with a publicly known flaw, attackers are scanning for it, and the server holds the customer database.
- Vulnerability: the unpatched software
- Threat: the attackers scanning for that flaw
- Risk: high – exploitation is likely and the impact on customer data is severe

---

## Shared Responsibility Model (Cloud)
These are the three primary cloud service models. The difference between them lies in how much of the system the provider manages for you, versus how much remains your responsibility.

The Foundation: What a System Is Built Of
Every computing system is composed of layers, from the bottom up:
Hardware and Data Center: Servers, power, cooling, and physical security.
Network and Virtualization: Connectivity, and the layer that partitions a physical server into multiple virtual machines.
Operating System: Linux, Windows.
Runtime: The environment that executes the code—such as a JVM for Java or a Python interpreter.
Application: The code itself.
Data: The information stored by the application.

As you move through the models from IaaS -> PaaS -> SaaS, the provider takes on more layers, leaving you with fewer responsibilities.

### IaaS – Infrastructure as a Service
What you get: A "blank" virtual machine in the cloud. The provider supplies the hardware, networking, and virtualization; everything else is up to you.
What you do: Choose an operating system, install updates, install software, configure the firewall, and deploy your code and data.
Example: Azure Virtual Machines
When it’s suitable: When you need full control—for instance, to install specialized software or configure the server exactly the way you want.
Regarding security: A significant amount of responsibility rests with you. If you fail to update the operating system and it contains a known vulnerability, that becomes your problem.

### PaaS – Platform as a Service
What you get: A ready-to-use platform for running code. The provider also manages the operating system and the runtime environment.
What you do: You write the code, upload it, configure the application settings, and manage the data. You do not interact with the server itself.
Example: Azure App Service
When it’s suitable: When you want to focus solely on the code. For instance, if you have a Python backend server and simply want it to run—without having to manage a Linux server.
Regarding security: The provider handles operating system updates. You are responsible for ensuring your code is secure, configurations are correct, and data is protected.

### SaaS – Software as a Service
What you get: Software ready for use via a web browser or an application. There is no need for installation or management.
What you do: You simply use the software and manage who has access to it.
Examples: Microsoft 365, Gmail, Google Drive, Zoom.
When it’s suitable: When you need a ready-made solution and do not want to develop or maintain anything yourself.
Regarding security: The provider is responsible for almost everything—but not everything. If a user chooses a weak password, fails to enable two-factor authentication, or accidentally shares a sensitive file publicly, that remains your responsibility.

Comparison of Responsibilities

| Layer | On-Premises | IaaS | PaaS | SaaS |
|---|---|---|---|---|
| **Hardware and Data Center** | You | Provider | Provider | Provider |
| **Network and Virtualization** | You | Provider | Provider | Provider |
| **Operating System** | You | You | Provider | Provider |
| **Runtime** | You | You | Provider | Provider |
| **Application** | You | You | You | Provider |
| **Data and Access** | You | You | You | You |

On-Premises means that the servers are located at your company and you are responsible for everything.\
In every model, the customer stays responsible for their **data** and **who can access it**.

**The Pizza Analogy**\
A common analogy that helps with remembering:\
On-Premises: Making a pizza at home from scratch. You buy the flour, prepare the dough, and use your own oven.\
IaaS: Renting a fully equipped kitchen. The oven and the kitchen belong to someone else, but you prepare the pizza yourself.\
PaaS: Ordering a delivery of pre-made dough and toppings. You simply assemble and bake it.\
SaaS: Going to a pizzeria to eat. Your only responsibility is choosing what to order—and remembering to bring your wallet.

---

## Key Takeaways
- Security is judged through CIA, and attacks are described through its opposite, DAD
- Authenticity, nonrepudiation, utility, and possession extend the CIA triad
- Bell-LaPadula protects confidentiality, Biba and Clark-Wilson protect integrity
- Defence in Depth uses multiple layers so that one failure doesn't break everything
- Zero Trust verifies before access; Trust but Verify checks afterward
- ISO/IEC 19249 provides practical architectural and design principles
- Risk combines how likely a threat is to exploit a vulnerability with its impact
- In the cloud, security is a shared responsibility between provider and customer
