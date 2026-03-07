# Identity & Access Management System – NorthBridge Solutions  
### A practical IAM project built around a mid-sized fictional company

This repository contains a full Identity & Access Management (IAM) project designed for *NorthBridge Solutions*, a fictional company with around 120 employees.  
The goal of this project is to show how an IT team could structure and manage identities, access, security policies, and daily operations in a real environment.

Everything here is based on common situations you would find in a growing company: organizing Active Directory, improving security, automating repetitive tasks, and creating clear processes for user management.

---

## Project Goals

- Build a clean and scalable Active Directory structure.  
- Apply security policies that make sense for a real company.  
- Delegate basic tasks to Helpdesk, HR, and Managers.  
- Standardize onboarding, offboarding, and role changes.  
- Use PowerShell to automate routine work.  
- Improve visibility and auditing across the environment.  

---

## Repository Structure

Each folder represents a part of the IAM project, similar to how a real IT department would organize documentation and technical work.

01-Project-Overview/        → Summary, scope, and architecture
02-ActiveDirectory-Design/  → OU structure, RBAC model, groups and roles
03-Security-Policies/       → GPOs, hardening, auditing setup
04-Delegation-Model/        → Delegation for Helpdesk, HR, and Managers
05-Identity-Lifecycle/      → Onboarding, offboarding, and role change processes
06-Automation/              → PowerShell scripts and automated reports
07-Logs-and-Auditing/       → Audit configuration and sample logs
08-Documentation/           → SOPs, runbooks, glossary, diagrams


---

## Technologies Used

- Active Directory (On-Prem)  
- Group Policy Objects (GPOs)  
- PowerShell  
- Windows Server  
- RBAC (Role-Based Access Control)  
- Event Auditing  

---

## Key Results

- Onboarding time reduced by about **70%** thanks to automation.  
- More consistent and predictable permission assignments.  
- Clear separation of responsibilities through delegation.  
- Better visibility into inactive accounts and access changes.  
- Stronger security posture with documented processes.  

---

## How to Explore This Project

- Start with **01-Project-Overview** to understand the full picture.  
- Go to **02-ActiveDirectory-Design** for the technical structure.  
- Check **03-Security-Policies** for hardening and GPO work.  
- Look at **06-Automation** to see the PowerShell scripts.  
- Visit **05-Identity-Lifecycle** for the operational workflows.  

---

## Project Status

Completed  
Last updated: *[add date]*  

---

## License

This project is for learning and demonstration purposes.  
Feel free to use it as inspiration for your own IAM projects.
