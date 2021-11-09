---
title: "Lab 07"
author: "Constantine Macris"
date: "03MARCH2020"
subject: "Project, lets get going"
titlepage: true
titlepage-color: "3C9F53"
titlepage-text-color: "FFFFFF"
titlepage-rule-color: "FFFFFF"
titlepage-rule-height: 2
book: true
classoption: oneside
code-block-font-size: \scriptsize
top-level-division: chapter
---

# Lab 07 Start deploying stuff and things

## Learning Outcomes

* Design and implement a secure architecture for provided application (IAA)
* Evaluate network architecture and identify potential vulnerabilities (IAA)
* Apply tools and techniques for identifying vulnerabilities (VLA)
* Explore web application technologies and security issues associated with them (WAS)
* Describe approaches used in the development and deployment of secure web applications (WAS)

## Tasking (Subject to refinement)

### Infrastructure

The **Infrastructure Team** will be responsible for building the infrastructure in a sustainable repeatable manner. They will be given requirements from the customer and internal customers on specifications needed and develop a method to build those assets and networking to support.

1) Develop Infrastructure overview
2) Outline deployment method to deploy infrastructure
3) Assist other groups in deployment needs

* Deploy assets and networking to Digital Ocean
* Setup deployment to VMware and Azure

### Configuration Management

The **Configuration Management Team** will be responsible for creating all the configuration's playbooks/templates for the machines to support the current configurations by the customer and internal customers to support both application operation, security and compliance.

1) Work with Infrastructure and Security Operations team to develop playbooks and templates to assure

* Harden OS
* Work with Security Operations Team to configure firewall
* Work with Pen Test and compliance to develop and document configurations
* Deploy configurations

### Security Operations

The **Security Operations Team** will be responsible for developing the security architecture for the deployment, managing security requirements, developing specifications for security systems like SIEM logging, Vuln mgmt and system hardening and configuration. They are responsible to work with the Configuration Management team by building the firewall and Pen Test and Compliance team by giving them our firewall to STIG.

1) Log into the DISA Portal: [DISA Patch Repository](https://patches.csd.disa.mil/CollectionInfo.aspx?id=535) and get the build requirements for the ACAS Security Center and Nessus Scanner
2) Develop strategy for establish STIG Baselines for all Operating systems
3) Work with the Infrastructure and Configuration Management Team to establish deployment documents for OS, firewall and application
4) Work with pen test/compliance team to document settings
5) Install and configure ACAS
6) Install and configure IDS/IPS and firewall

### Pen Test and Compliance

The **Pen Test and Compliance Team** will be responsible for planning and executing a pen test of the web application and host system. This is including a code review and full white box test. Prior to the testing deployment the team will also be responsible for compiling an accreditation package that can be used to outline the security features of the system and explain the system and controls in place to support security of the system.

1) Review security plan documentation [NIST 800-18](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-18r1.pdf)
2) Work with teams to assist in documenting system
3) Develop draft security plan

### Integration

The **Integration Team** will be responsible to work with the application developer (Customer) to get necessary specification and requirements. They will be acting at the deployment team as well as overall management of the entire project. The Integration team will be responsible for bringing all teams together to develop an execution plan where security along the CIA principals are upheld.

1) Coordinate interactions between all groups
2) Develop final requirements document
3) Keep open lines of communication between the development team and DevOps Team (CNS Class)
4) Maintain GitHub as central collaboration point

## Project Groups

### Infrastructure


### Configuration


### Security


### Pen-Test and Compliance


### Integration


## Report

1) Rewrite the description of your team, please be detailed in the project.md file in the **cns** repo
2) Document progress made during lab period and any outstanding questions
3) Develop timeline for deliverables (tasking) and completion dates (GitHub)
4) Propose enterprise architecture (from your team's perspective), what settings and technologies will you use?
