---
title: "Lab 04"
author: "Constantine Macris"
date: "16SEPT2020"
subject: "OS/Application Hardening"
subtitle: "OS and Application Hardening"
titlepage: true
titlepage-color: "3C9F53"
titlepage-text-color: "FFFFFF"
titlepage-rule-color: "FFFFFF"
titlepage-rule-height: 2
book: true
classoption: oneside
code-block-font-size: \scriptsize
description: |
    Lab setting up VM and working with STIG Viewer and SCAP Tools
---
# Lab 02 OS/Application Hardening

## Learning Outcomes

* Practice the steps necessary for hardening the OS with respect to DISA standards (OSH1)
* Practice the necessary steps for hardening applications with respect to DISA standards
* Understand additional OS and Application hardening tools and standards
* Securely configure system, remove or shutdown unnecessary components and services, close unnecessary ports and ensure that all patches and updates are applied (OSH2)
* Apply principals of security engineering in documentation and process control

## Introduction

We have spent some time looking at why and what we spend time hardening. In Lab 02 we were introduced to some of the tools used by the the DoD and USG to configure secure systems (DISA STIGs, STIG Viewer). Today we are going to look at applying these settings to a machine, application or system of your choice. Anything that has a STIG listed can be chosen for this lab. Many of the controls can be broken into technical controls and administrative controls. Technical controls are things that you can apply to a system like configuring a firewall or setting password life. Administrative controls are things like a policy that mandate that the system can only be used in a particular way; these are sometimes referred to as "Paper Controls". Before you start the lab I wanted to practice one last piece of the puzzle that was presented in class: the SCAP (Security Content Automation Protocol).

## SCAP Demo

There are various tools we discussed that can take SCAP content. The SCAP is an automated way to assess the controls. The SCAP content can be loaded into various different platforms as we discussed in class (see Class 05). Let's take a moment to install the SCC tool on our Windows 10 VM and run the SCAP on the VM.
**NOTE: You can choose not to run the SCC tool on your Windows 10 VM or run it host on your laptop instead if you feel that will run better**

1) Download the SCC Tool [SCC Tool Download](https://cga.sfo2.digitaloceanspaces.com/cns/SCAP/scc-5.2.1_Windows_bundle.zip)
2) Run the SCC Installer and install the program
3) Start the SCC Tool
NOTE: What is the default Windows 10 content version that is installed? Make a note for the questions at the end.
4) Run the SCC Tool on Windows 10 by clicking "Start Scan" in the Analyze section of the tool.
5) Access the results using the "Results" Tab and "View Results"
6) Quickly view the report in HTML format by selecting "All Settings" from the report panel.
NOTE: What is the score of the system? Make a note for the questions at the end of lab.
6) Select the "XML" tab and right click on the "XCCDF Results" and select "Show in Directory"
7) Save this file to a location that will be easy to reference later.
8) Import the SCC results in to STIG Viewer by opening up the checklist you created in Lab 1, or make a new one if you no longer have that checklist.
9) Once in the Checklist view, select "Import" and "XCCDF Data" and select the XML file you saved from #7 above.
10) How many CAT1 controls are still Open? How does that compare with your evaluation from Lab 01?

The same process can be followed for applications like IE10 (Internet Explorer) or IE11. Take a moment to view the other preloaded STIGS that the SCC can test against. At this point it is possible to start the process of configuring your system.

## Select OS or Application to Harden

During this lab each individual/group will be expected to select one item from the list available STIGs and work towards applying the standards. Follow this link to find the STIGs. [DISA STIG/SRG -- CAC and Public](https://public.cyber.mil/stigs/downloads/)

Select one item from the list STIGS that you feel you can work towards applying for the rest of the class. If it is a major OS like Windows 10, Windows Server, Domain Controller, SQL Server, CentOS, Debian, Ubuntu or other please make sure you can quickly obtain an image in the amount of time for the lab. If you have a major OS you may be asked to continue to utilize that image for future use of the class.

**When you have selected an OS or application please visit the instructor with your preferred method.**

## Identify Technical Hardening Controls

Once the STIG is obtained review the controls that you plan on implementing. Select a number (enough to represent a level of effort commensurate with the time allowed) of technical controls that you can implement during the lab. It is not expected that you apply all of the controls for a STIG like Windows 10, however use the guidelines discussed in class regarding how to prioritize application of controls (start with CAT1 and CAT2 and move to CAT3 as time allows). Be prepared to discuss and explain why you selected the controls you did as well as preparing a POAM (Plan of Action and Milestones) on when the additional controls can be implemented. Please use the STIG Viewer Checklist to document this information.

## Apply Technical Controls

You have the ability to evaluate and remediate the controls however you feel is best. You can manually evaluate the controls or use an automation platform like the SCC tool. In many cases it is faster to manually remediate controls if you have not worked with automation platform but when you remediate controls you can use whatever manner you choose, this decision is up to you.  You will be assessed on the quality of your reporting, documentation and POAM, not on the quantity of controls implemented. For those that use automation it could be very quick; however, it is expected that you work on the system for the entire period. This may mean implementing controls like separating out parts of the OS on separate partitions and implementing an application on your system or apply similar controls to another platform (like Docker if you are applying controls to Debian). It is expected if you have an application that you plan to include your hardened application as part of the team working on your host OS (Win10, Linux, find the individual/team working on your host and plan how you can get your hardened application on that system)

## Document Changes

As you harden your system keep track of what you are completing and how. If you are manually working on applying controls compile the method you used for each control as well as any notes associated to assist anyone who may be working on the project after you. If you are automating your process provide ample documentation on how one can recreate the process.

## Measure Effectiveness

Apply the SCAP (if available) to your OS, appliance or application and add the XCCDF results to your STIG Viewer checklist. If you have no SCAP determine if there is another automated check that can be carried out for a similar standard or format (reference documentation on OpenSCAP, Ansible Hardening etc etc). Use this system to verify the controls you implemented were properly evaluated.

## Lab Report

Complete a Lab Report as described below.
The important information follows:

Format: Markdown or PDF (not MS Word please)
Submission: Markdown = GitHub or D2L Dropbox, PDF = D2L Dropbox
Due: 0900 24 September 2020

You going to prepare a report as if you were a Junior Officer that had been brought in to a Command to evaluate controls of your specific system in that organization. You are provided with the purpose of:

`Evaluatin <insert system> Compliance using the Security Technical Implmentation Guide`

You are tasked to work with the security team (the class) to apply STIG and SRG hardening to various systems in the environment. You are to work with the team to combine all OSs and Applications into one baseline system. Your orders state that you will not only evaluate and remediate as many controls as possible but also describe, in detail, the importance of the highest impact controls that you feel will increase security of the systems the most. You will provide screenshots and commands you used to back up your findings and document your process for follow on teams to reproduce.

You are also contracted to remediate as many controls as possible on your system by implementing the fixes and showing they are in place and working. You may encounter some issues so make sure you explain which controls you had not been able to fully remediate and what actions may be needed to provide long term fixes. Please include any SCAP reports you may create. 

For items you can not fully remediate you will add your open items to a class POAM in Teams -- General -- Files -- POAM

Lastly, you will include the checklist for the STIG Viewer Tool you used for this evaluation with detailed notes on your findings. 

The format should be:

* Executive Summary
* Introduction
* Background of practitioner
* Method -- What standards and tools are being used and why
* Assumptions
* Summary of actions taken including identifications of controls assessed
* System -- Windows 10
  * Evaluation of controls
  * Remediation of controls
* System -- CentOS 7
  * Evaluation of controls
  * Remediation of controls
* Discussion on highest impact controls encountered
* Conclusions

### Uploads

Zip and upload all of the following to Dropbox:

1) STIG Viewer Checklist
2) Report

### OS Image

Be prepared to provide your OS image for use in future classes. We may be looking to use some of these systems in future labs!
