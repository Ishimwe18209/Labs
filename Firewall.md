---
title: "Lab 06"
author: "Constantine Macris"
date: "01OCT2020"
subject: "Firewall Lab"
subtitle: "Firewalls -- Get Ready!"
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
# Lab 06 Firewall Lab -- Building a Firewall

## Credit

This lab was derived (at times directly) from the Seed Security Labs found at:

[Firewall Exploration](https://seedsecuritylabs.org/Labs_16.04/PDF/Firewall_New.pdf)

All Credit should be given to Dr. Wenliang Du

## Learning Outcomes

* Evaluate network architecture and identify potential vulnerabilities (IAA)
* Explain how network defense tools are used to defend against attacks and mitigate vulnerabilities (NDF)
* Design and implement a secure architecture for provided application (IAA)


## Definitions

## Resources

* [CNS-Build repo](https://github.com/macee/cns-build)
* [Seed Lab Code](https://github.com/kevin-w-du/BookCode)

## Introduction

The learning objective of this lab is for students to gain the insights on how firewalls work by playing with firewall software and implement a simplified packet filtering firewall. Firewalls have several types; in this lab, we focus on packet filter. Packet filters inspect packets, and decide whether to drop or forward a packet based on firewall rules. Packet filters are usually stateless; they filter each packet based only on the information contained in that packet, without paying attention to whether a packet is part of an existing stream of traffic. Packet filters often use a combination of a packet’s source and destination address, its protocol, and, for TCP and UDP traffic, port numbers. In this lab, students will play with this type of firewall, and also through the implementation of some of the key functionalities, they can understand how firewalls work. Moreover, students will learn how to use SSH tunnels to bypass firewalls. This lab covers the following topics:

* Firewall
* Netfilter
* Loadable kernel module
* Bypassing firewalls using SSH tunnel
* How to bypassing firewalls using VPN is covered in a separate lab.

For this lab you can begin using the provided vagrant file at the [CNS-Build repo](https://github.com/macee/cns-build) under the firewall directory. Run the `Vagrantfile` to build **node1** and **node2**. As you move through the lab you can decide to make a new directory to build fresh versions of the environment or destroy and rebuild using `vagrant destory` and `vagrant up`. You will also be asked to modify this code to support **node3**

NOTE: **This lab will cover two weeks. Evaluation criteria will be released during lab for next week. Document your work by tracking your progress and answers with screen shots and saving code produced.**

## Lab

### Task 1 -- Using Firewall

Linux has a tool called iptables, which is essentially a firewall. In this task, the objective is to use iptables to set up some firewall policies, and observe the behaviors of your system after the policies become effective. You need to set up at least two VMs, one called **node1**, and other called **node2**. You run the firewall on your **node1**. Basically, we use iptables as a personal firewall. Optionally, if you have more VMs, you can set up a firewall at your router, so it can protect a network, instead of just one single computer. 

One can access the VMs using `vagrant ssh` through PowerShell as opposed to using the VirtualBox console. To perform these tasks after navigating to the directory containing your `Vagrantfile`:

```bash
// Boot the VMs
$ vagrant up
// Optional: check the status of your two VMs (node1 and node 2)
$ vagrant status
// ssh into the virtual machine labeled node1
$ vagrant ssh node1
// Open a new window of PowerShell, navigate to the directory containing your Vagrantfile, and ssh into the VM node 2
$ vagrant ssh node2
```

After you set up the two VMs, you should perform the following tasks:

1) Determine the topology of the network
2) Show you can `telnet` from **node1** to **node2**
2) Install `nmap` and configure a scan to find open ports on **node2**
2) Enable a firewall (`iptables` or `ufw`)to prevent **node1** from being able `telnet` to **node2** showing that one can still `telnet` from **node2** to **node1**
3) Prevent **node2** from being able `telnet` to **node1**
4) Prevent **node1** from visiting an external web site. You can choose any web site that you like to block, but
keep in mind, some web servers have multiple IP addresses. Hint: `ping` and `lynx`
5) Disable the firewall and test the function of `telnet`

You can find the manual of iptables by typing "man iptables" or search it online. We list some
commonly used commands in the following:

```bash
// List all the rules in the filter table
$ sudo iptables -L
$ sudo iptables -L --line-numbers
// Delete all the rules in the filter table
$ sudo iptables -F
// Delete the 2nd rule in the INPUT chain of the filter table
$ sudo iptables -D INPUT 2
// Drop all the incoming packets that satisfy the <rule>
$ sudo iptables -A INPUT <rule> -j DROP
```

### Task 2 -- Implementing a Simple Firewall

The firewall you used in the previous task is a packet filtering type of firewall. The main part of this type of firewall is the filtering part, which inspects each incoming and outgoing packets, and enforces the firewall policies set by the administrator. Since the packet processing is done within the kernel, the filtering must also be done within the kernel. Therefore, it seems that implementing such a firewall requires us to modify the Linux kernel. In the past, this had to be done by modifying and rebuilding the kernel. The modern Linux operating systems provide several new mechanisms to facilitate the manipulation of packets without rebuilding the kernel image. These two mechanisms are Loadable Kernel Module (LKM) and Netfilter.

LKM allows us to add a new module to the kernel at the runtime. This new module enables us to extend the functionalities of the kernel, without rebuilding the kernel or even rebooting the computer. The packet filtering part of a firewall can be implemented as an LKM. However, this is not enough. In order for the filtering module to block incoming/outgoing packets, the module must be inserted into the packet processing path. This cannot be easily done in the past before the `Netfilter` was introduced into the Linux.

`Netfilter` is designed to facilitate the manipulation of packets by authorized users. `Netfilter` achieves this goal by implementing a number of hooks in the Linux kernel. These hooks are inserted into various places, including the packet incoming and outgoing paths. If we want to manipulate the incoming packets, we simply need to connect our own programs (within LKM) to the corresponding hooks. Once an incoming packet arrives, our program will be invoked. Our program can decide whether this packet should be blocked or not; moreover, we can also modify the packets in the program.

In this task, you need to use LKM and Netfilter to implement the packet filtering module. This module will fetch the firewall policies from a data structure, and use the policies to decide whether packets should be blocked or not. To make your life easier, so you can focus on the filtering part, the core of firewalls, we allow you to hardcode your firewall policies in the program. You should support at least the ability block `telnet` with the reach goal of supporting five different rules, including the ones specified in the previous task. Guidelines on how to use Netfilter can be found in Section 3.

You may find the code from the book helpful: https://github.com/kevin-w-du/BookCode

1) clone the above repo to your **node1**
2) Compile and troubleshoot the c code found in `telnetFilter.c`
3) Test the firewall
4) Implement additional rules

### Task 3 -- Evading Egress Filtering

Many companies and schools enforce egress filtering, which blocks users inside of their networks from reaching out to certain web sites or Internet services. They do allow users to access other web sites. In many cases, this type of firewalls inspect the destination IP address and port number in the outgoing packets. If a packet matches the restrictions, it will be dropped. They usually do not conduct deep packet inspections (i.e., looking into the data part of packets) due to the performance reason. In this task, we show how such egress filtering can be bypassed using the tunnel mechanism. There are many ways to establish tunnels; in this task, we only focus on SSH tunnels.

You need two VMs **node1** and **node2** for this task (three will be better). **node1** is running behind a firewall
(i.e., inside the company or school’s network), and **node2** is outside of the firewall. Typically, there
is a dedicated machine that runs the firewall, but in this task, for the sake of convenience, you can run the
firewall on **node1**. You can use iptables to set up the following two firewall rules:

* Block all the outgoing traffic to external telnet servers. In reality, the servers that are blocked are usually game servers  or other types of servers that may affect the productivity of employees. In this task, we use the telnet server for demonstration purposes. You can run the telnet server on **node2**. If you have a third VM, **node3**, you can run the telnet server on **node3**.

* Block all the outgoing traffic to www.facebook.com, so employees (or school kids) are not distracted during their work/school hours. Social network sites are commonly blocked by companies and schools. After you set up the firewall, launch your Firefox browser, and try to connect to Facebook, and report what happens. If you have already visited Facebook before using this browser, you need to clear all the caches using Firefox’s menu: Edit -> Preferences -> Privacy & Security (left pane) -> Clear History (Button on right); otherwise, the cached pages may be displayed. If everything is set up properly, you should not be able to see Facebook pages. It should be noted that Facebook has many IP addresses, it can change over the time. Remember to check whether the address is still the same by using ping or dig command. If the address has changed, you need to update your firewall rules. You can also choose web sites with static IP addresses, instead of using Facebook. For example, most universities’ web servers use static IP addresses (e.g. www.uscga.edu); for demonstration purposes, you can try block these IPs, instead of Facebook. **Note: you may need to install both Firefox and a desktop GUI for this task**

#### Task 3.a: Telnet to node2 through the firewall

![SSH Tunnel](https://cga.sfo2.digitaloceanspaces.com/cns/images/lab_firewall.png)

To bypass the firewall, we can establish an SSH tunnel between **node1** and **node2**, so all the telnet traffic will go through this tunnel (encrypted), evading the inspection. Figure 1 illustrates how the tunnel works. The following command establishes an SSH tunnel between the `localhost` (port 8000) and **node2** (using the default port 22); when packets come out of **node2**’s end, it will be forwarded to **node3**’s port 23 (telnet port). If you only have two VMs, you can use one VM for both **node2** and **node3**

```bash
$ ssh -L 8000:node3_IP:23 seed@node2_IP
// We can now telnet to node3 via the tunnel:
$ telnet localhost 8000
```

When we `telnet` to `localhost`’s port `8000`, SSH will transfer all our TCP packets from one end of the tunnel on `localhost:8000` to the other end of the tunnel on **node2**; from there, the packets will be forwarded to **node3**:23. Replies from **node3** will take a reverse path, and eventually reach our telnet client. Essentially, we are able to telnet to **node3**. Please describe your observation and explain how you are able to bypass the egress filtering. You should use Wireshark to see what exactly is happening on the wire.

(Hint: Make sure you use two terminals in order to traverse the tunnel, use this link for more help: https://www.linuxschoolonline.com/tunnel-telnet-traffic-inside-ssh-using-port-forwarding/)

#### Task 3.b: Connect to Facebook using SSH Tunnel

To achieve this goal, we can use the approach similar to that in Task 3.a, i.e., establishing a tunnel between your localhost:port and **node2**, and ask **node2** to forward packets to Facebook. To do that, you can use the following command to set up the tunnel: "`ssh -L 8000:FacebookIP:80 ...`". We will not use this approach, and instead, we use a more generic
approach, called dynamic port forwarding, instead of a static one like that in Task 3.a. To do that, we only
specify the local port number, not the final destination. When **node3** receives a packet from the tunnel,
it will dynamically decide where it should forward the packet to based on the destination information of the
packet.

```bash
$ ssh -D 9000 -C seed@node2
```

Similar to the `telnet` program, which connects `localhost:9000`, we need to ask Firefox to connect to `localhost:9000` every time it needs to connect to a web server, so the traffic can go through our SSH tunnel. To achieve that, we can tell Firefox to use localhost:9000 as its proxy. To support dynamic port forwarding, we need a special type of proxy called SOCKS proxy, which is supported by most browsers. To set up the proxy in Firefox, go to the menu bar, click `Edit -> Preferences`, scroll down to `Network Proxy`, and click the Settings button. Then follow Figure 2. After the setup is done, please do the following:

1. Run Firefox and go visit the Facebook page. Can you see the Facebook page? Please describe your observation.
2. After you get the Facebook page, break the SSH tunnel, clear the Firefox cache, and try the connection again. Please describe your observation.
3. Establish the SSH tunnel again and connect to Facebook. Describe your observation.
4. Please explain what you have observed, especially on why the SSH tunnel can help bypass the egress filtering. You should use `tshark` to see what exactly is happening on the wire. Please describe your observations and explain them using the packets that you have captured.

[SOCKS Config](https://cga.sfo2.digitaloceanspaces.com/cns/images/SOCKS.png)

### Task 4: Evading Ingress Filtering

**node1** runs a web server behind a firewall; so only the machines in the internal network can access this web server. You are working from home and needs to access this internal web server. You do not have  VPN, but you have SSH, which is considered as a poor man’s VPN. You do have an account on **node1** (or another internal machine behind the firewall), but the firewall also blocks incoming SSH connection, so you cannot SSH into any machine on the internal network. Otherwise, you can use the same technique from Task 3 to access the web server. The firewall, however, does not block outgoing SSH connection, i.e., if you
want to connect to an outside SSH server, you can still do that.

The objective of this task is to be able to access the web server on **node1** from outside. We will use
two machines to emulate the setup. **node1** is the internal computer, running the protected web server;
**node2** is the outside machine at home. On **node1**, we block **node2** from accessing its port 80
(web server) and 22 (SSH server). You need to set up a reverse SSH tunnel on **node2**, so once you get
home, you can still access the protected web server on A from home.

1) Write a `Vagrantfile` that sets up the infrastructure for this environment
2) Write an Ansible playbook or shell script that configures the firewall and services
3) Develop a method using an SSH tunnel to evade the ingress filtering to access the internal web server
4) Develop the same architecture using a stand alone firewall like `pfsense` or `vyos` (also in Vagrant) configure manually and export the config or provide an Ansible Playbook to support your configuration.

## Guide

### Loadable Kernel Modules

The following is a simple loadable kernel module. It prints out "Hello World!" when the module is loaded; when the module is removed from the kernel, it prints out "Bye-bye World!". The messages are not printed out on the screen; they are actually printed into the /var/log/syslog file. You can use dmesg | tail -10 to read the last 10 lines of message

```c
#include <linux/module.h>
#include <linux/kernel.h>
int init_module(void)
{
printk(KERN_INFO "Hello World!\n");
return 0;
}
void cleanup_module(void)
{
printk(KERN_INFO "Bye-bye World!.\n");
}
```

}
We now need to create Makefile, which includes the following contents (the above program is named hello.c). Then just type make, and the above program will be compiled into a loadable kernel module.

```c
obj-m += hello.o
all:
make -C /lib/modules/$(shell uname -r)/build M=$(PWD) modules
clean:
make -C /lib/modules/$(shell uname -r)/build M=$(PWD) clean
```

Once the module is built by typing make, you can use the following commands to load the module, list all modules, and remove the module:

```bash
$ sudo insmod mymod.ko  (inserting a module)
$ lsmod                 (list all modules)
$ sudo rmmod mymod.ko   (remove the module)
```

Also, you can use modinfo mymod.ko to show information about a Linux Kernel module

### A Simple Program that Uses Netfilter

Using Netfilter is quite straightforward. All we need to do is to hook our functions (in the kernel module) to the corresponding Netfilter hooks. Here we show an example:

```c
#include <linux/module.h>
#include <linux/kernel.h>
#include <linux/netfilter.h>
#include <linux/netfilter_ipv4.h>
/* This is the structure we shall use to register our function */
static struct nf_hook_ops nfho;
/* This is the hook function itself */
unsigned int hook_func(void *priv, struct sk_buff *skb,
const struct nf_hook_state *state)
{
/* This is where you can inspect the packet contained in
the structure pointed by skb, and decide whether to accept
or drop it. You can even modify the packet */
// In this example, we simply drop all packets
return NF_DROP; /* Drop ALL packets */
}
/* Initialization routine */
int init_module()
{ /* Fill in our hook structure */
nfho.hook = hook_func; /* Handler function */
nfho.hooknum = NF_INET_PRE_ROUTING; /* First hook for IPv4 */
nfho.pf = PF_INET;
nfho.priority = NF_IP_PRI_FIRST; /* Make our function first */
nf_register_hook(&nfho);
return 0;
}
/* Cleanup routine */
void cleanup_module()
{
nf_unregister_hook(&nfho);
}
```

## Evaluation

NOTE: **This lab will cover two weeks. Evaluation criteria will be released during lab for next week. Document your work by tracking your progress and answers with screen shots and saving code produced.**
