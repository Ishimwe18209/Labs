---
title: "Lab 01"
author: "Constantine Macris"
date: "28AUG2020"
subject: "Vagrant Terraform O-My"
subtitle: "With our powers combined!!!"
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

# Lab 01 Vagrant Terraform GitHub SSH and so much more

## Learning Outcomes

## Definitions

## Objectives

- Setup WSL
- Setup and test Vagrant
- Setup and verify Terraform
- Install Ansible
- Setup GitHub
- Setup VS Code
- Pull **CNS** Repo, fork repo, make an improvement and issue pull request

## Introduction

* Take a moment to watch the [Intro Video]

This lab we are going to be setting up various tools that we will be using during the Computer Network Security (CNS) course. The first is called Vagrant which enables us to work off a common template online as well as write custom scripting to define aspects of our Virtual Machine (VM). Vagrant will require we install another virtualization platform called VirtualBox. Vagrant can work with VMware Fusion but the default for most VMs is VirtualBox. After VirtualBox is installed you will need to install the application. I would suggest if you have a Windows host you use Vagrant in Windows interacting with it through Powershell. If you are on a Linux host (which I assume most are not) you would install the Linux version and interact via the shell. **Do not install vagrant in the WSL** you would be nesting hypervisors and you do not have a program to render the VirtualBox window which will mean you need to use the shell on the VM for all your commands.

The next piece of software we are going to install is Terraform. It doesn't matter where you install Terraform as it will mostly be using API calls to a Type 1 Hypervisor. The lab will have two options, you can do both or either to install in Windows or Linux. Terraform allows you describe your infrastructure as code. As we build secure systems this makes your process repeatable and deploying assets a breeze! 

Next we will be setting up your account in GitHub which we will use for various aspects of our class. This includes making an SSH key pair and uploading your public key online. You will also make a GPG key that you can use for signing your code commits as well as encrypting and decrypting files! You will then log into GitHub from the WSL, clone the CNS repo, and finally setup VS Code to make some improvements and push them back to the remote and issue a pull request!

The assessment for this lab is in 2 parts, your pull request and a D2L quiz:

### D2L Quiz

- **No Collaboration**
- 20 Minutes to complete
- Due by next lab period
- You can continue to work the lab until next lab just complete the quiz by Thursday 0900
- Solution will be released when all labs are submitted
- The D2L quiz is worth 12 points

### Submitted Pull Request

- You may work with classmates to complete the Pull Request
- Your pull request is worth 8 points
- You will be graded on the quality of your edits, and the fact you completed all the steps to submit the request.

## Finish WSL Setup

If you have not setup your WSL please reach out to peers or Mr. Davis or myself on **Teams** for help. Please come prepared with your troubleshooting steps already completed. *if WSL2 doesn't want to become the default, WSL1 should work just fine, or you can maually install the WSL2 Linux Kernal which allows for the WSL version 2 to become the default*

**Note: You will need the WSL functional prior to moving forward**

## Setup Vagrant

### Vagrant Intro

From the Vagrant documentation Vagrant is "The command line utility for managing the lifecycle of virtual machines" ([Vagrant Docs])

We will be using Vagrant as another tool to build and deploy virtual machines. It is a very lightweight and easy way to interact with VMs once you get familiar with the command line interface.

Vagrant can work with various different providers for desktop virtualization. In this case we are going to use VirtualBox because it is by far the standard but don't be surprised if you see Docker, VMware or even HyperV images on the vagrant cloud. You can explicitly request a provider using the `--provider` tag in the software.

### Install VirtualBox

1) Go to [https://www.virtualbox.org/wiki/Downloads] and select "Windows hosts" and version 6.1.12.
2) Download and run the binary and follow the installation wizard. 
3) From the same site download the VirtualBox 6.1.12 Extension Pack. This will help with compatibility and features. Read about the pack and install using instructions here: [https://www.virtualbox.org/manual/ch01.html#intro-installing]

### Download and Install Vagrant

1) Go to[https://www.vagrantup.com/downloads] and download the Windows executable
2) Install the executable and follow the wizard

### Your first VM

1) Open Powershell and type `vagrant`. You should get a list of help to get going. Read these commands and focus in on how to get help and read the help for commands like `init`,`box`, `ssh` and `status`
2) Make a directory for your your VM to live. `mkdir kali`
3) Change directory into that folder with `cd kali`
4) Initialize the directory with the VM you want to use, in our case we will use the [Kali Linux Rolling VM] by typing `vagrant init kalilinux/rolling`.
5) Explore the directory and note new files that were created and explore the function
6) At this point you can either explicitly tell vagrant to download the box template with `vagrant box add kalilinux/rolling`. This will tell vagrant to go to the vagrant cloud and download the vagrant box by user **kalilinux** and the image **rolling**. The tool will give you progress on download status. Wait until the download is complete.
7) You can now start up your VM with `vagrant up` this will download the box from Vagrant Cloud (if you don't already have it) and then start up the machine.
8) The default username and password are **vagrant:vagrant** in order to allow the software to provision and configure the system lets log into that system using the command vagrant ssh:
```bash
vagrant ssh
```
This allows you to enter a shell with your VM through Vagrant. You can simply type `exit` to return to your Powershell terminal.
9) Open the GUI (Start Menu and VirtualBox). Most of the time the GUI will launch itself when the VM starts with `vagrant up`. You will see that you can also interact with the VM using the GUI Desktop

### Destroy it all

1) Lets stop our VM back in our VM directory on Powershell with `vagrant halt`. This will turn off the VM but leave that instance instance of the VM on your hard disk, keep in mind you have that instance and the template box.
2) Lets burn this instance with fire by issuing the `vagrant destroy` command which will delete the instance of the VM from your machines. Notice in the GUI that VM is no longer there. If you wanted you could spin the machine up again since the template and Vagrant files are still present on the system
3) Lets clear off more room by issuing `vagrant box remove kalilinux/rolling`. This command will finally delete the box template off your machine. 

## Setup Terraform

### Terra What?

We will be using another HashiCorp tool called Terraform. Terraform is "a tool for building, changing and versioning infrastructure safely and efficiently". In short it is a way to manage your infrastructure as code. This enables a system to be defined as code, committed to version control and easy return to a known good state. 

It may help to watch the following video:
[Terraform Intro] 18:38

We will not be using Terraform this week but the install will assure the system is setup properly and ready for follow on labs.

### Terrafrom Install

1) Download Terraform at [https://www.terraform.io/downloads.html] Note: You can install Terraform on either Windows or the WSL. I personally prefer the WSL but either will work. Take a moment to look at the resources HashiCorp has on [Installing Terraform] (Info on both Windows and Linux, I would suggest you use the **Manual** method for Windows and the **Linux** method for Linux; the best is manual for Linux!)
2) Extract the download and add to your `PATH`. In the above [Installing Terraform] there is documenation. Why do we need to do this?  (You may need to install "unzip" and/or other commands first)
3) Install Terraform in the unbuntu WSL terminal for it to work best. 
4) Edit the '.profile' with the command vim ~/.profile.
5) Test that Terraform is working by opening up a **Terminal** in Linux or **Powershell** in Windows and running `terraform --help` (If you use "ls" now you will still see the zip file, this is ok! the extracted file will still be there and you do not need to keep trying to unzip).
6) If you get the help info CONGRATS! It works STOP THERE! If not try to read the troubleshooting and run through the instructions again on [Installing Terraform]. Read, Google, Ask Classmates, Ask Instructors -- WIN!

## Setup Ansible

If Terraform is designed to manage infrastructure as code, imagine doing the same thing for the operating system and applications. Terraform can manage some of those things but a tool specifically designed to manage configuration is Ansible. Ansible is another open source project with a large corporate backer, Red Hat (owned by IBM). Take a moment to review the [Ansible Architecture] 

Jeff Geerling has a great [Intro to Ansible] which I think is worth the watch if you are not comfortable with the concept but otherwise only **suggested**

### Install Ansible

We are also going to installing Ansible in the WSL so start with the WSL terminal open

1) You will use the `sudo` command to elevate your look at the manual for sudo with `man sudo`
2) The program (in Ubuntu) to install software is called `apt` it is a "Package Manager", take a look at the manual and install **Ansible** with 

```bash
sudo apt update # needed before installing ansible to update repos
sudo apt install ansible
```
*after installing all of these new tools, make sure to close and reopen your PowerShell and/or restart your computer so that all of the changes are implemented thoughtout because the tools won't work if it was open before the updates*

3) Verify that Ansible is installed by displaying the version number.

## Setup GitHub

If you do not already have a GitHub account we are going to take some time in lab to not only make an account but also create an SSH public/private key set and upload the **public** key to use with the class. We will learn more about these keys as we move through the class but it will provide a mechanism to allow the owner of the key to authenticate to a server.

### Create GitHub Login

1) If you already have a GitHub Login and you have been able to successfully log in send your skip to the next section [Create SSH Key]
2) Go to [www.github.com] and select "Sign-up"
3) You can use your CGA email or a personal one depending on how long you want to keep the account
4) Most things on GitHub are based on your 
5) Check your email and verify your account
6) Congrats you have a fully active GitHub account, now to make some keys!

### Create SSH Key

1) Open up the Linux Subsystem  
2) Look at the manual for the command `ssh-keygen`
3) Generate your SSH key pair after reading about it! (you can use default settings by just hitting enter at the options)
4) You will need to upload your public key GitHub because that is how we will be authenticating into the server using SSH
  * Click in the upper right corner of GitHub once you are logged in. It will be an Icon of your user and drop down. Select **Settings**
  * Within Settings select the **SSH and GPG Keys** on the left navigation bar
  * You should see a green button labeled **New SSH Key**, click that
5) You will need to copy and paste your SSH Public Key. I will provide my way and the GitHub way
  * My Way -- Open up the Linux Subsystem and issue the following commands and highlight the contents and copy and paste it in (everything under the command):

  ```bash
  cat ~/.ssh/id_rsa.pub
  ```
  
  * [The GitHub Way] -- Follow the linked instructions. You will need to install some new software but it basically outputs the public key to your clipboard
6) Add some title that makes sense to you. Like "Cadet Lappy WSL Key"
7) **Send your username to the instructor to become added to the class repo if you have not already** Until you get added to the repo you will not be able to proceed.

## Setup VS Code

I like using Visual Studio Code for text editing. It is how I have written most of the course. It makes having the terminal open and editing a breeze. They have numerous plugins and the ability to use the WSL from your host without much effort. They have a good way to manage git workflows and helps check syntax error in code. You don't need to use VS Code, you could use the text editor of your choice, however, I would think you will find most day to day tasks a bit easier with VS Code.

### Download and Install Code

1) Go to [https://code.visualstudio.com/download](https://code.visualstudio.com/download) and download the 64 bit version for Windows
2) Click your way through the installer. I have made a video that how VS Code recognizes that the WSL is installed and adding and configuring the plugin for it. [WSL Plugin]
3) Once in VS Code you should be prompted to install the WSL Plugin (As per the video above) Please install and open up a new remote connection to confirm that the plugin in working (as seen in the video)

In this case the "remote connection" is actually remote to the WSL... I wonder how else that can be used.

## Fork and Download the Class Content

To have your own copy, and make changes to the course content we are going to work through what is known as forking the repository. 	

**You need to send the instructor your GitHub user name in order to be added to the repository**

1) Login to GitHub
2) Select your user icon drop-down in the upper right
3) Select your profile
4) Select the Electrical Engineering Icon under your Organizations
5) Find the Repo labeled **CNS**. Note the path to the repo when you select it, it should be **macee/cns**
6) Spend some time getting familiar with the user interface. From here you can **fork** the repository, or make a copy that is associated with and linked to the main repo but will not affect the main repo unless you issue a **pull request** to have your changed pulled back into the main repo.
7) Fork the repo into your personal GitHub account. (Usually is the top option)
8) Once you have forked the you will notice the path (top left) it now should read **<username>/cns** That is how you know it is your copy of the repo. You can edit files in the browser but we are going to pull the repo down to our local machine.
9) To get the repo to your machine you need to pull down your cloned repo. To do this click the green "Code" button with the download arrow. From here it gives you a few options. You can copy the SSH link, use HTTPS or download the files in a Zip. We are going to set up SSH since we already configured our keys with our account.
10) Copy the SSH clone information it should look like `git@github.com:<username>/cns.git`
11) Enter your WSL terminal (Usually a circular orange icon called "Ubuntu") and issue the command to clone, first access the help with:

```bash
sudo apt install git
git clone --help
# Also
man git-clone
# Finally lets clone!

git clone git@github.com:<username>/cns.git

## UMMMM Mr. Macris this isn't working... Take a moment to think about why this may not be working
```

12) It looks like something may not be working correctly, that is a bummer. I wonder why?
13) Return to the web console and get the cloning information for HTTPS this time. It should look something like this `https://github.com/<username>/cns.git` and lets try that again:

```bash
git clone https://github.com/<username>/cns.git
# ahhhh, much better... what is my password again?
```
    (in order to complete this step, you can not be on cadet wifi, use a personal hotspot or another internet option.)
14) Lets open up VS code and try to connect to the WSL to open up this folder!

## Edit Class Content

You will need to be able to edit documents in a text editor. Take what you read about Markdown and find some errors or improve something in some of the documents from class. This could be fixing a typo, correcting formatting or adding additional content that was covered in class or in some of our readings. 

### VS Code Remote Connection to WSL

1) Open VS Code and select the "extensions" on the left bar (the four squares icon)
2) Search for the Extension "Remote - WSL" and find the Microsoft extension with the same title.
3) Install the extension by clicking the "Install Button"
4) Click the small green box in the bottom left of the VS Code window
5) In the drop-down that appears select "Remote - WSL Getting Started" and read that information
6) Click the green button again this time selecting "Remote - WSL New Window" (it may take a moment to connect)
7) Select: File, Open Folder and navigate to the "cns" directory on your WSL directory structure, it should be somewhere like `/home/<username>/cns`
8) When you open the folder you should see all the class files. Now, use the top bar to open a new terminal in VS Code.
9) Find out what branch you are in with `git branch`
10) **If your branch is not 2020f:** Checkout the branch we are using for the course with

```bash
 git fetch origin 2020f
 git checkout origin 2020f
 git branch 
 ```

11) You should now see the current branch data in VS Code

### Make some fixes

Take some time to read the README.md and look at build.py
The classes are in the class directory, the lab is in labs. Feel free to change whatever you like, hopefully improvements! When you are done you will be ready to package up your changes and commit them! Take your time and make some meaningful improvements. 

### Package and push

1) Once you are done save all the files you have edited with CTRL-S (the white dot should go away)
2) Return to a terminal and enter the repo directory `cd /home/<username>/cns`
3) Make sure you are in the right place by looking at the files in the directory by typing `ls`
4) If you are you can try `git status` and git should tell you about all the changes you made and what to do next! Try to get your files staged and committed
5) Once your files are staged and committed you will be ready to push your changes to the remote. Do so with `git push origin 2020f`

### Finally the Pull Request

The last thing you need to do is ask to have your changes put back into the main repository not your fork. You will be issuing what is known as a pull request. You are asking the lead developer to include your work into the main source code, merge your code into the original repo. You need to do this from the GitHub GUI.

1) Log back into GitHub if you exited.
2) Find your version of the `cns` repo and go into it
3) You should see some notification above your files that notices you are ahead of the **macee:cns** and prompts you to issue a "pull request". It is called this because you are asking to have changed **pulled** from your project.
4) Fill in the details of the pull request with meaningful documentation to let me know what you worked on and what changes you made.
5) Send in your first pull request!!!

## Closing out

You will find a quiz in D2L for this lab with the questions below. Please treat the lab as a quiz and **Do not collaborate** however you have until next week to work on the lab and take the quiz. You will have 20 minutes to take the online quiz. Do not discuss the quiz as other will take it at different times. Once everyone is complete I will provide a walk through as a solution!

You will also be graded on your ability to make a pull request as well as the quality of your pull request.

I hope you had the opportunity to set up your computer with some new tools and gain some more exposure to GitHub and a workflow for writing pretty much anything! Now you will be able to contribute to projects, make repositories and fix errors you find in class and labs, I am sure a few are hiding in this very document. I hope this was fun but also challenging and provided some good experience working through some issues!

## Critical Questions

- What is the command to find your version of Vagrant
- What version of vagrant do you have installed?
- How would you get help in Ansible?
- What is the `PATH` and why did we need to add Terraform to it?
- Why didn't we need to add Terraform to the `PATH` with the non-manual Linux install?
- What is placed in the directory after `vagrant init`
- Where are VM templates stored after a box is downloaded?
- What is one function that VirtualBox Extension Pack brings?
- Why didn't SSH work to clone the repository and HTTPS did?
- What branch did you switch to?
- What command will list the full name and path to your remote?
- In the command `git push origin 2020f` what is origin referencing?

[Intro Video]:https://youtu.be/gkNItUOWuFA
[Vagrant Docs]:https://www.vagrantup.com/docs
[https://www.vagrantup.com/downloads]:https://www.vagrantup.com/downloads
[Kali Linux Rolling VM]:https://app.vagrantup.com/kalilinux/boxes/rolling
[https://www.virtualbox.org/wiki/Downloads]:https://www.virtualbox.org/wiki/Downloads
[https://www.virtualbox.org/manual/ch01.html#intro-installing]:https://www.virtualbox.org/manual/ch01.html#intro-installing
[Terraform Intro]:https://www.youtube.com/watch?v=h970ZBgKINg&feature=emb_logo
[https://www.terraform.io/downloads.html]:https://www.terraform.io/downloads.html
[The GitHub Way]:https://docs.github.com/en/github/authenticating-to-github/adding-a-new-ssh-key-to-your-github-account
[WSL Plugin]:https://youtu.be/JwX9d8a1Huo
[Installing Terraform]:https://learn.hashicorp.com/tutorials/terraform/install-cli
[Intro to Ansible]:https://youtu.be/goclfp6a2IQ
[Ansible Architecture]:https://docs.ansible.com/ansible/latest/dev_guide/overview_architecture.html
