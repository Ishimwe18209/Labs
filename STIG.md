---
title: "Lab 02"
author: "Constantine Macris"
date: "03SEP2020"
subject: "STIG What?"
subtitle: "Making the world secure, one OS at a time!"
titlepage: true
titlepage-color: "3C9F53"
titlepage-text-color: "FFFFFF"
titlepage-rule-color: "FFFFFF"
titlepage-rule-height: 2
book: true
classoption: oneside
code-block-font-size: \scriptsize
top-level-division: chapter
description: |
    Lab setting up VM and working with STIG Viewer and SCAP Tools
---

# Lab 02 STIG and Virtual Machines

## Learning Outcomes

* Install and run STIG Viewer on two test OS's
* Become familiar with various test methods and desktop virtualization platforms (OSH)
* Exhibit ability to identify hardening controls on various operating systems (OSH)
* Create and manage local users and groups on various operating systems (OSH)
* Identify and implement password standards (OSH)

## Introduction

The second lab lab will continue on our crash course in setting up VMs and becoming familiar with compliance management and checking tools we discussed in class. If you have already completed some of these steps you can move on to the next ones. The following instructions are provided for both Windows and Linux **Hosts**. You can also use another virtualization platform if you prefer like VMware Workstation to accomplish the same goals but the lab will describe the process for Vagrant and Virtual Box.  

You will have some down time during this lab while things are downloading, use the time to go through controls and start preparing your report. 

You will be preparing a [Lab Report] for this lab and the contents of the report is described at the end of the lab. **Take a moment now to review the [Lab Report] and take your time collecting data so you only need to do this once**. You will have the choice to submit your report on GitHub using markdown or on D2L as a **PDF**. The choice is yours Padawan.

Before we get going lets watch a little bit about the [STIG Viewer Tool](https://youtu.be/LdBfJZ7aK9w) -- 9 minutes

## Vagrant

Last lab we tried to install Vagrant. As a tool to manage and deploy virtual machines it is a good resource to have. It enables one to build systems quickly and reliably and store the configuration information as code. There are a few important things to remember when using Vagrant.

It is not a Hypervisor -- Vagrant works with different hypervisors to spin up and configure virtual machines on your desktop host
It is not **needed** -- You will find, after some pain, that you do not need to use Vagrant but you may want to because it makes things repeatable and easy to test.

Generally you will find a box you want to work with at the [Vagrant Cloud](https://app.vagrantup.com/boxes/search). Here you can find a box someone already made that does what you want or one that you can build off of. In this case we are using one made by [Microsoft to test Edge Browser](https://app.vagrantup.com/Microsoft/boxes/EdgeOnWindows10) and the official [CentOS 7](https://app.vagrantup.com/centos/boxes/7) machine as our new flavor of Linux. You will notice he way to reference these is by the organization name and box in this format `name/box` in the case of Microsoft it is `Microsoft/EdgeOnWindows10` and CentOS 7 it is `centos/7`. Take a moment to search a few different boxes, keep in mind a box is only as good as the person who posted it, try to stick with people you trust and people who are highly rated!

Two important concepts are
* Provisioners -- Things that work on the Vagrant Boxes (think Ansible)
* Providers -- Things that run the Vagrant Boxes (think VirtualBox or VMware workstation)

### How to use Vagrant

Vagrant is a command line tool that has a number of commands you can view with `vagrant --help`. The general workflow is that you will need to get a Vagrant box with `vagrant box add <name/box>`, make a directory for the instance of that box to live `mkdir somedir && cd somedir`, initiate the directory you made with `vagrant init <name/box>` and then tell Vagrant to start the box with `vagrant up`. Once the box is up the VM is running (as an instance of it) on your machine. You can go into your Type 2 Hypervisor and see it running as well as opening up the VM console (the VM screen). Alternatively for *nix derivatives you can remote in with `vagrant ssh` which uses the vagrant software to set up a remote shell on the system via SSH. When you want to stop the instance (VM) you can stop it with `vagrant halt` but the instance will still be installed on disk. To remove that instance you will use `vagrant destroy`. Keep in mind at this point the box (the template you downloaded) is still installed and located in (on Linux) `~/.vagrant.d/boxes`. If you want to see which boxes are on your system you can use the `vagrant box list` command and to remove a box you can use the `vagrant box remove <name/box>` command.

The summary of those commands is below. 

| Action        | Command                  |
| ----          | ----                     |
| Add box       | `vagrant box add <name>` |
| Access a box  | `vagrant up`             |
| ssh into box  | `vagrant ssh`            |
| exit box      | `exit`                   |
| shut down box | `vagrant halt`           |
| remove VM     | `vagrant destroy`        |
| remove box    | `vagant box remove`      |

So right now it seems like Vagrant is a fancy overly complex remote control for VMs. It saves a little time not needing to download an install image, install the software on a virtual disk and set up resources... But we are really just getting started. After you run `vagrant init` you will find that there are now a number of files that get placed in the directory. You will find a `Vagrantfile` and a `.vagrant` directory. The Vagrantfile is what describes the vagrant box and any deltas between what you want and the current state. You can view more on the Vagrantfile in the [Vagrant Documentation](https://www.vagrantup.com/docs/vagrantfile) or this [Quick Tutorial](https://phoenixnap.com/kb/vagrant-beginner-tutorial)

You can learn a little more with this quick video: [Vagrant in 5 minutes](https://youtu.be/cx79jOpZVE8)

## Lab Procedure

Many of you have had issues using VirtualBox with Vagrant. If you prefer to use VMware Workstation or just VirtualBox without Vagrant you can get the [Windows VM](https://developer.microsoft.com/en-us/microsoft-edge/tools/vms/) which is prebuilt and [CentOS Installer](https://www.centos.org/download/). As you can imagine this may be a little more involved.


### Download and install STIG Viewer

Windows: [*Windows 10 STIG - Ver 1, Rel 23*](https://dl.dod.cyber.mil/wp-content/uploads/stigs/zip/U_MS_Windows_10_V1R23_STIG.zip)  
Linux: [*Red Hat Enterprise Linux 7 STIG - Ver2, Rel 5*](https://dl.dod.cyber.mil/wp-content/uploads/stigs/zip/U_Red_Hat_Enterprise_Linux_7_V2R5_STIG.zip)  

**NOTE: It is suggested you run STIG Viewer on your Host Computer NOT THE VM**


1. Get the correct zip file from [DOD Cyber Exchange](https://public.cyber.mil/stigs/srg-stig-tools/) for Windows.
2. Download and extract the file, read the documentation to install and install the STIG Viewer on your **HOST**
3. Open up STIG Viewer once it is installed and confirm it is working

### Download and install STIGs

1. Download the required STIGs from the [DOD Cyber Exchange STIGs](https://public.cyber.mil/stigs/downloads/). Use the filters on the right to find the correct operating system or use the links below.  NOTE: Version and Rel may have changed so get the latest!
  * Windows: [*Windows 10 STIG - Ver 1, Rel 19*](https://dl.dod.cyber.mil/wp-content/uploads/stigs/zip/U_MS_Windows_10_V1R19_STIG.zip) 
  * CentOS7: [*Red Hat Enterprise Linux 7 STIG - Ver2, Rel 5*](https://dl.dod.cyber.mil/wp-content/uploads/stigs/zip/U_Red_Hat_Enterprise_Linux_7_V2R5_STIG.zip)   
3. Import STIG into STIG Viewer  
Run STIG Viewer and go to `File > Import STIG`. Choose the STIG for Windows 10.
4. Make a Checklist for each OS
Check, one at a time, the STIG for a OS. Save the checklist by `Checklist > Create Checklist - Check Marked STIG(s)`. Save your checklists with the format "2020_1{{your_github_name}}_{{OS}}"


### Setup Windows Box box Vagrant


1. Download and install [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
2. Download and install [Vagrant](https://www.vagrantup.com/downloads.html)
3. Enter Powershell and run

```powershell
mkdir lab02/windows
cd lab02/windows
vagrant box add Microsoft/EdgeOnWindows10 #takes a while
vagrant init Microsoft/EdgeOnWindows10
vagrant up
```

While you are are waiting for the box to download you can move onto [Windows 10 Box Checklist Test Procedure] and start selecting your additional controls (#3)

4. Open Desktop
   1. Open Virtual Box on the host:  ```virtualbox &```  
   2. Double Click the CentOS VM in VirtualBox  
   3. Click inside the VM screen  

### Windows 10 Box Checklist Test Procedure 

NOTE: this is done on the Edge VM

We now have a nice brand new computer ready for evaluation. This is a stock version of Windows 10 with a short term license for testing. You will be using the checklists you downloaded and evaluating if controls are in place and then actually fixing controls and making notes to that effect in the STIG Viewer tool. Before you do that you are going to set up some local users on your system, one user will be a regular user, the other will be a privileged user. 

1. Open the VM in VirtualBox

2. Create new users  
    1. Add two new users: one with first initial and last name (e.g. cmacris) and one as an admin account (cmacris-admin).  
    Follow this link on [making users in Windows 10](https://support.microsoft.com/en-us/help/4026923/windows-10-create-a-local-user-or-administrator-account) or follow the next steps.
    2. Open Settings > Family & other users > Add someone else to this account
    3. Select `I don't have this person's sign-in information` > `I don't have this person's sign-in information`
    4. Enter account information
    5. Add superuser account by selecting the account in the `Family & other users` menu and change account to `Administrator`

3. Evaluate Controls  

When you are evaluating the controls you are going into the system to see if the control is in place already. At this stage think of yourself as an auditor evaluating a system. Go ahead, put on your auditor hat, yea are an auditor now, your family now likes you a little less... You are now checking if the technical control is enabled and determining if it is **Open** -- not implemented, **Not a Finding** -- Implemented or **N/A** -- Not Applicable.

  * Evaluate the control V-63319 using the **Check Test** Section, assuming the computer is a "Domain Joined" Computer. Make Comments as appropriate.
  * Evaluate the control V-63321 using the *Check Text*. To execute the *Check Text*, use regedit (See [here](https://www.computerhope.com/issues/ch001348.htm) for more info)
  * Select 20 additional technical controls you can evaluate in a similar manner. Challenge yourself to learn a bit about the controls in this period! Keep track of your work and make sure to make notes in the findings!

4. Implement Controls  

Take off your auditor hat and put on your security engineer hat, WOW you must feel so much cooler already! When you are implementing controls you are actually fixing the issues using the **fix text**. You will be making some fixes, as this is open ended you will realize there are some easier and some more difficult fixes!

  * Follow the *fix text* to fix the V-63319 control. Refresh the registry editor and verify the change occurred by executing the *check text*. This should have changed. Make notes in the tool to indicate what you have done
  * Select 10 additional technical controls that you can evaluated and attempt to fix them. Record your findings in the tool 

5. Review the questions at the end and make sure you have obtained all the data you need from the VM. If you do not have a lot of space on your device, once you have gathered all the information you need for the report, you can destroy the VM and remove the box with the following:

**NOTE: You may want to hold onto the box if you can but I know most cadet machines are low on space. AGAIN GET ALL THE DATA YOU NEED FOR YOUR REPORT FIRST!!! YOU HAVE BEEN WARNED**

```powershell
vagrant halt
vagrant destroy
vagrant box remove Microsoft/EdgeOnWindows10
```

### Setup CentOS 7 Box box Vagrant

While you are are waiting for the box to download you can move onto [Windows 10 Box Checklist Test Procedure] and start selecting your additional controls (#3)

1. Enter Powershell and run

```powershell
mkdir lab02/centos7
cd lab02/centos7
vagrant box add centos/7 #takes a while
vagrant init centos/7
vagrant up
```
While you are are waiting for the box to download you can move onto [CentOS 7 Box Checklist Test Procedure] and start selecting your additional controls (#6)

We need both a Windows and Centos box
1. Make a directory to hold lab one  
```mkdir lab01```  
2. Make two directories for each Windows and Centos  
```mkdir lab01/windows```  
```mkdir lab01/centos7```  
3. Install the particular box in the *corresponding directory*
For Windows (CAUTION: Takes forever):  
```vagrant init Microsoft/EdgeOnWindows10```  
For Centos7:  
```vagrant init centos/7```  

### Download and Load STIGs in STIGViewer

1. Get the correct zip file from [DoD Cyber Exchange](https://public.cyber.mil/stigs/srg-stig-tools/) for Windows.

2. Download STIGs  
Download the required STIGs from the [DoD Cyber Exchange](https://public.cyber.mil/stigs/downloads/). Use the filters on the right to find the correct operating system or use the links below.  
Windows: [*Windows 10 STIG - Ver 1, Rel 23*](https://dl.dod.cyber.mil/wp-content/uploads/stigs/zip/U_MS_Windows_10_V1R23_STIG.zip)  
Linux: [*Red Hat Enterprise Linux 7 STIG - Ver2, Rel 5*](https://dl.dod.cyber.mil/wp-content/uploads/stigs/zip/U_Red_Hat_Enterprise_Linux_7_V2R5_STIG.zip)  

3. Import STIG into STIG Viewer  
Run STIG Viewer (`./STIGViewer`) and go to `File > Import STIG`. Choose the STIG.  

4. Make a Checklist for each OS
Check, one at a time, the STIG for a OS. Save the checklist by `Checklist > Create Checklist - Check Marked STIG(s)`. Save your checklists with the format "2020_1{{ Group# }}_{{ OS }}"

### Windows 10 Virtual Machine Procedure

1. Open the VM in VirtualBox

2. Create new users  
    1. Add two new users: one with first initial and last name (e.g. cmacris) and one as an admin account (cmacris-admin).  
    Follow this link on [making users in Windows 10](https://support.microsoft.com/en-us/help/4026923/windows-10-create-a-local-user-or-administrator-account) or follow the next steps.
    2. Open Settings > Family & other users > Add someone else to this account
    3. Select `I don't have this person's sign-in information` > `I don't have this person's sign-in information`
    4. Enter account information
    5. Add superuser account by selecting the account in the `Family & other users` menu and change account to `Administrator`

3. Evaluate Controls  
    * Evaluate the control V-63319 using the *Check Test* Section, assuming the computer is a "Domain Joined" Computer. Make Comments as appropriate.
    * Evaluate the control V-63321 using the *Check Text*. To execute the *Check Text*, use regedit (See [here](https://www.computerhope.com/issues/ch001348.htm) for more info)

4. Implement Controls  
    * Follow the *fix text* to fix the V-63319 control. Refresh the registry editor and verify the change occurred by executing the *check text*


### CentOS 7 Box Checklist Test Procedure 

1. Access the VM  

```bash
vagrant ssh
```

2. Update the OS  

```bash
sudo yum update
```

3. Install a desktop  

```bash
sudo yum -y groups install "GNOME Desktop"  
sudo yum install kernel-devel
```  

4. Open Desktop
   1. Open Virtual Box on the host:  ```virtualbox &```  
   2. Double Click the CentOS VM in VirtualBox  
   3. Click inside the VM screen  
   *NOTE: If the message to "Capture" pops up, allow the capture. This puts the mouse inside the VM. To escape the VM, press right CTRL to return to the host.*  
   4. Enter in the username and password. Both are `vagrant`  
   5. Run the command `startx`

5. Setup users  
    1. Add two new users: one with first initial and last name (e.g. cmacris) and one as an admin account (cmacris-admin).  
    Follow this link on [how to add users in CentOS](https://www.digitalocean.com/community/tutorials/how-to-create-a-sudo-user-on-centos-quickstart) or use the commands below (make sure to replace `$(username)` with your new username).  
    2. Add root (superuser) privileges to the admin by adding them to the `wheel` group.  
    3. Switch to the admin user.
    4. Install a package call `rsh-server`

```bash
# add user and set their password
sudo adduser username
sudo passwd username

# give root privilege
sudo usermod -aG wheel username

# switch user
su - username

# install rsh-server
sudo yum install rsh-server
```

6. Evaluate Controls  

Much like Windows we are going to evaluate some controls in the CentOS 7 machine. Again, put on your auditor hat and pretend you are simply evaluating if the control is in place or not using the  **Open** -- not implemented, **Not a Finding** -- Implemented or **N/A** -- Not Applicable drop downs in the STIG Viewer Tool. My My, Look at all that RED

  * Evaluate the control V-71939 using the *Check Text* Section -- Record your findings
  * Evaluate the control V-71967 using the *Check Text* Section -- Record your findings
  * Select 20 other technical controls you can check and record your findings.

7. Implement Controls

Time to put on your security engineer hat again! In much the same manner you can fix issues and make those bad red marks turn to nice green marks!

  * Implement the fix for the control V-71939 using the *Fix Text* Section -- Record your findings
  * Implement the fix for the control V-71967 using the *Fix Text* Section -- Record your findings
  * Select 10 controls that you had evaluated and fix them and record your findings.

## Lab Report

Complete a Lab Report as described below.
The important information follows:

Format: Markdown or PDF (not MS Word please)
Submission: Markdown = GitHub or D2L Dropbox, PDF = D2L Dropbox
Due: 0900 10 September 2020

You going to prepare a report as if you were a consultant that had been brought in to an organization to evaluate controls of two systems in that organization. You are provided with the purpose of:

`Evaluating Operating System Compliance using the Security Technical Implmentation Guide`

You are contracted to first go onto a Windows 10 and CentOS 7 system and evaluate 22 controls (the ones specified and that you selected during the lab) and discuss your findings. Your contract states that you will describe in detail the importance of the 5 highest impact controls that you feel will increase security of the systems the most. You will provide screenshots and commands you used to back up your findings.

You are also contracted to remediate 12 of these controls on each system by implementing the fixes and showing they are in place and working. You may encounter some issues so make sure you explain which controls you had not been able to fully remediate and what actions may be needed to provide long term fixes.

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


## Install for those without Vagrant

If you did not get Vagrant working this should help you deploy the VMs.

### Virtual Environment Setup Download from web on Windows or Linux for Windows VM guest

1. Download the MSEDGE on Windows 10 Image -- [Temp Windows Image Here](https://developer.microsoft.com/en-us/microsoft-edge/tools/vms/)
2. Select the virtualization type you will be using, normally VMware if you have VMware Workstation
3. Once complete downloading unzip the file and import the VM into your virtualization client. [VMware Workstation](https://www.youtube.com/watch?v=I6WfFLQwoPg), [VirtualBox](https://www.youtube.com/watch?v=fVYwt1Tluug)
4. Start your VM to make sure it works!
**NOTE: The password to your VM is "Passw0rd!"**

### Virtual Environment Setup Download from web on Windows or Linux for CentOS VM guest

1. Download the [Pre-Packaged VM](https://www.osboxes.org/centos/#centos-1908-vbox)
2. Select the virtualization type you will be using, normally VMware 64bit if you have VMware Workstation, VirtualBox if you are using that.
3. Once the download is complete import the VM into your virtualization client [VMware Workstation](https://www.youtube.com/watch?v=I6WfFLQwoPg), [VirtualBox](https://www.youtube.com/watch?v=fVYwt1Tluug)

