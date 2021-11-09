---
title: "Lab 08"
author: "Constantine Macris"
date: "29OCT2020"
subject: "To the CLOUD"
subtitle: "To The CLOUD"
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

# Lab 08 IAM Lab -- To The CLOUD!

## Practical Experience

* Create, update, delete, undelete custom roles
* Create and manage service accounts.
* Create a virtual machine and associate it with a service account.
* Use client libraries to access BigQuery from a service account.
* Run a query on a BigQuery public dataset from a Compute Engine instance.
* Configure VPC Network Peering
* Setting up a secure Cloud Storage bucket
* Managing keys and encrypted data using Key Management Storage
* Viewing Cloud Storage audit logs
* Create a Private Kubernetes Cluster

## Leaning Outcomes

* Explore web application technologies and security issues associated with them (WAS)
* Design and implement a secure architecture for provided application (IAA)

## Definitions

* GCP -- Google Cloud Platform (aka Google Cloud)
* K8S -- Kubernetes (K********S)
* Terraform -- An open source infrastructure automation tool made by [HashiCorp](https://www.terraform.io/) HashiCorp also makes Vagrant

## Introduction

Welcome to Lab #08! If you have not noticed, I am very excited! This lab has lots of my favorite things and will build on what we talked about with ZTN. You will have the opportunity to learn a bit about access controls in a cloud environment and identity aware proxies. There are 8 parts to this lab (the User Authentication: Identity-Aware Proxy is currently under maintenance) and you will need to complete each section and register your completion using their automated checks throughout the lab!

* Take your time completing
* Before clicking "Start Lab" read the steps to become familiar
* Take notes on commands and what they do
* Attempt to piece together the new terms for GCP (not a small task)
* Don't blindly follow directions, attempt to understand the tools and processes

## Lab

### Quicklab Login and Setup

1) Go to https://www.qwiklabs.com/(https://www.qwiklabs.com)
2) "Join" with your @uscga.edu email address (that will get you access to the credits I provided)
3) Feel free to try out a lab for a credit to make sure everything works for you!
4) Please let me know if you have any issues getting in or getting access or credits

### Enroll in the Quest

In the top search bar enter `Ensure Access & Identity in Google Cloud` and hit enter. The quest should show up and you will be asked to enroll in this quest. Follow the "Quest Outline" and complete the following labs:

* Cloud IAM: Qwik Start
* IAM Custom Roles
* Service Accounts and Roles: Fundamentals (Note: install pyarrow with `pip install pyarrow`)
* VPC Network Peering
* User Authentication: Identity-Aware Proxy
* Getting Started with Cloud KMS
* Setting up a Private Kubernetes Cluster
* Ensure Access & Identity in Google Cloud: Challenge Lab

### Work labs

Take you time to complete the labs. The instructor will monitor the status of the one lab under maintenance and hope it will come back online soon.

See the points above in the introduction and let the instructor know if you encounter any issues. Many times chances in the cloud environment take a moment to register with the QuikLabs evaluation check, if you do not get a green check mark give it a minute then double check all the steps and outputs of your work.

### Challenge

The final lab is a challenge quest where not all the steps are provided for you. This is your work so please work alone to complete this challenge. The notes you took should be able to help you here! The challenge will require:

* Create a custom security role.
* Create a service account.
* Bind IAM security roles to a service account.
* Create a private Kubernetes Engine cluster in a custom subnet.
* Deploy an application to a private Kubernetes Engine cluster

Take your time and pay attention in the prior labs to gain the experience necessary to complete the challenge.

### ZTN

While completing the lab take some time to research each piece of new technology you are using and what you are actually doing. Consider how you might implement the same technology using tools like Vagrant or Terraform and Ansible using resources we have access to.

### Evaluation

You will be evaluated on your completion of the labs and of the challenge. If you have all of the green check marks then you are good to go! You will be responsible for the material and may see it again! The lab is due prior 5NOV2020.
