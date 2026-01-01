# 🏥 Azure Secure Infrastructure Modernisation Lab  
**Existing State → Secure Target State (Enterprise / Healthcare Scenario)**

---

## 📌 Project Overview

This lab documents a **realistic enterprise scenario** where a healthcare company’s Azure infrastructure has grown organically and now suffers from **security, access, and operational risks**.

The project is split into two clear parts:

1. **Existing Infrastructure (As-Is)** – what the company currently has and why it is a problem  
2. **Target Infrastructure (To-Be)** – a redesigned, secure, scalable, and auditable Azure architecture  
3. **Step-by-Step Implementation** – how the new design was built  
4. **Validation & Testing** – how improvements were verified  

This format mirrors how **real cloud security / infrastructure projects are documented in real companies**.

---

# 1️⃣ Existing Infrastructure (As-Is State)

## 1.1 Business Context

The organization is a **healthcare company** hosting a patient-facing web application with backend services. Availability is important, but **security and compliance** are critical due to sensitive data.

---

## 1.2 Existing Technical Design

### Network
- Single Azure Virtual Network
- Flat subnet design
- No clear separation between tiers

### Compute
- Web and App VMs deployed directly into the same network
- Each VM has:
  - A **Public IP**
  - Direct RDP access from the Internet

### Access & Identity
- Local administrator accounts used on VMs
- Multiple engineers share admin credentials
- No RBAC governance
- No audit trail for privileged access

### Security Controls
- NSGs exist but are overly permissive
- RDP (3389) open to the Internet
- No Just-In-Time access
- No Privileged Identity Management

---

## 1.3 Problems & Risks Identified

### 🔴 Security Risks
- Public IPs expose VMs directly to the Internet
- RDP brute-force risk
- Credential reuse across admins
- No MFA enforcement for admin actions

### 🔴 Operational Risks
- No separation of duties
- Difficult to prove compliance or audits
- Hard to track “who did what and when”

### 🔴 Scalability & Reliability Issues
- No load balancing
- Manual VM management
- No defined traffic flow between tiers

---

# 2️⃣ Target Infrastructure (To-Be State)

## 2.1 Design Goals

The new design focuses on:

- ✅ **No Public IPs on workloads**
- ✅ **Least privilege access**
- ✅ **Just-In-Time admin elevation**
- ✅ **Clear network segmentation**
- ✅ **High availability**
- ✅ **Audit readiness**

---

## 2.2 Target Architecture Overview

### Network Segmentation
- One Virtual Network with multiple subnets:
  - Web subnet
  - App subnet
  - Azure Bastion subnet

### Traffic Flow
- Internet → **Public Load Balancer** → Web tier
- Web tier → **Internal Load Balancer** → App tier
- No direct Internet access to App tier

### Access Model
- No RDP/SSH exposed to the Internet
- Azure Bastion for secure VM access
- Azure RBAC for permissions
- Azure PIM for temporary admin access

---

# 3️⃣ Solution Design Components

## 3.1 Network Design

| Component | Purpose |
|---|---|
| Virtual Network | Isolated enterprise network |
| Web Subnet | Hosts public-facing web VMs |
| App Subnet | Hosts private backend services |
| AzureBastionSubnet | Secure management access |

---

## 3.2 Load Balancing

### Public Load Balancer
- Exposes only HTTP/HTTPS
- Distributes traffic across WEB1 and WEB2
- Only public entry point

### Internal Load Balancer
- Private IP only
- Used for App tier traffic
- Accessible only from Web subnet

---

## 3.3 Access & Identity Controls

### RBAC
- Group-based role assignments
- No shared local admin accounts
- VM access controlled via Entra ID roles

### PIM (Privileged Identity Management)
- Standing admin access removed
- Admin roles are **Eligible**, not Active
- Requires:
  - MFA
  - Justification
  - Time-bound activation

---

# 4️⃣ Step-by-Step Implementation

## 4.1 Create Resource Group
- Central container for all resources
- Enables easy cleanup and lifecycle management

---

## 4.2 Build Virtual Network & Subnets
- Create VNet with /16 CIDR
- Create separate subnets for Web, App, Bastion
- Enforces traffic isolation

---

## 4.3 Configure Network Security Groups

### Web NSG
- Allow HTTP/HTTPS from Internet
- Deny all other inbound traffic

### App NSG
- Allow only Web subnet traffic on app port (e.g. 8080)
- Block Internet traffic entirely

---

## 4.4 Deploy Azure Bastion
- Enables browser-based secure VM access
- Removes need for public IPs on VMs
- Centralised admin entry point

---

## 4.5 Deploy Virtual Machines
- WEB1 & WEB2 in Web subnet
- APP1 & APP2 in App subnet
- **No public IPs assigned**

---

## 4.6 Configure Load Balancers

### Public Load Balancer
- Frontend: Public IP
- Backend pool: Web VM NICs
- Health probe: HTTP

### Internal Load Balancer
- Frontend: Static private IP
- Backend pool: App VM NICs
- Health probe: App port

---

## 4.7 Configure Application Services
- Web tier serves basic web content
- App tier listens on internal port
- Enables end-to-end traffic testing

---

## 4.8 Implement RBAC
- Create Entra ID groups
- Assign VM login roles at resource scope
- Remove local admin dependency

---

## 4.9 Implement PIM
- Enable PIM for Azure resources
- Convert permanent admin roles to Eligible
- Enforce MFA, justification, time limits

---

# 5️⃣ Validation & Testing

## 5.1 Network Validation
- Web app reachable via Public Load Balancer
- App tier unreachable from Internet
- App tier reachable only via Internal LB

## 5.2 Security Validation
- VMs have no public IPs
- RDP/SSH blocked from Internet
- Access possible only via Bastion

## 5.3 Identity Validation
- Admin access denied without PIM activation
- Access granted only after:
  - MFA
  - Role activation
  - Approval window

## 5.4 Audit Validation
- PIM audit logs show:
  - Who activated access
  - When
  - For how long
  - Why (justification)

---

# 6️⃣ Outcome & Improvements Delivered

## Before
- Public VM exposure
- Permanent admin access
- Weak auditability
- High attack surface

## After
- Zero public VM exposure
- Just-In-Time admin access
- Centralised access control
- Strong audit & compliance posture

---

# 7️⃣ Skills Demonstrated

- Azure network design
- Secure enterprise architectures
- Load balancing (public & internal)
- Zero Trust principles
- Azure RBAC
- Privileged Identity Management
- Infrastructure validation & testing
- Real-world documentation

---

# 8️⃣ Cleanup

To avoid ongoing costs:
- Delete the resource group

---

## ✅ Final Notes

This lab reflects **how real Azure security improvements are planned, implemented, and documented** in regulated environments such as healthcare, finance, and government.

It demonstrates not just *how to deploy*, but **why each control exists** and how it improves the organization’s security posture.
