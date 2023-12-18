McGreedy is very greedy and doesn't let go of any chance to earn some extra elf bucks. During the investigation of an insider threat, the Blue Team found a production server that was using unexpectedly high resources. It might be a cryptominer. They narrowed it down to a single unapproved suspicious process. It has to be eliminated to ensure that company resources are not misused. For this, they must find all the nooks and crannies where the process might have embedded itself and remove it.

**Learning Objectives**

In this task, we will:

Identify the CPU and memory usage of processes in Linux.

Kill unwanted processes in Linux.

Find ways a process can persist beyond termination.

Remove persistent processes permanently.

Identifying the Process

Linux gives us various options for monitoring a system's performance. Using these, we can identify the resource usage of processes. One option is the top command. This command shows us a list of processes in real time with their usage. It's a dynamic list, meaning it changes with the resource usage of each process.

Let's start by running this command in the attached VM. We can type top in a terminal and press enter. It will show a similar output to the one below:

```
top - 03:40:19 up 32 min,  0 users,  load average: 1.02, 1.08, 1.11
Tasks: 187 total,   2 running, 183 sleeping,   0 stopped,   2 zombie
%Cpu(s): 50.7 us,  0.3 sy,  0.0 ni, 48.8 id,  0.0 wa,  0.0 hi,  0.0 si,  0.2 st
MiB Mem :   3933.8 total,   2111.3 free,    619.7 used,   1202.8 buff/cache
MiB Swap:      0.0 total,      0.0 free,      0.0 used.   3000.4 avail Mem 

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND   
   2062 root      20   0    2488   1532   1440 R 100.0   0.0  18:22.15 a         
    941 ubuntu    20   0  339800 116280  57168 S   1.0   2.9   0:08.27 Xtigervnc 
   1965 root      20   0  123408  27700   7844 S   1.0   0.7   0:02.83 python3   
   1179 lightdm   20   0  565972  44756  37252 S   0.3   1.1   0:02.25 slick-gr+ 
   1261 ubuntu    20   0 1073796  38692  30588 S   0.3   1.0   0:01.10 mate-set+ 
      1 root      20   0  104360  12052   8596 S   0.0   0.3   0:04.52 systemd   
      2 root      20   0       0      0      0 S   0.0   0.0   0:00.00 kthreadd  
      3 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 rcu_gp    
      4 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 rcu_par_+ 
      5 root      20   0       0      0      0 I   0.0   0.0   0:00.43 kworker/+ 
      6 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/+ 
      9 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 mm_percp+ 
     10 root      20   0       0      0      0 S   0.0   0.0   0:00.12 ksoftirq+ 
     11 root      20   0       0      0      0 I   0.0   0.0   0:00.50 rcu_sched 
     12 root      rt   0       0      0      0 S   0.0   0.0   0:00.01 migratio+ 
     13 root      20   0       0      0      0 S   0.0   0.0   0:00.00 cpuhp/0   
     14 root      20   0       0      0      0 S   0.0   0.0   0:00.00 cpuhp/1   
     15 root      rt   0       0      0      0 S   0.0   0.0   0:00.31 migratio+ 
     16 root      20   0       0      0      0 S   0.0   0.0   0:00.13 ksoftirq+ 
     18 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/+ 
     19 root      20   0       0      0      0 S   0.0   0.0   0:00.00 kdevtmpfs 
     20 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 netns     
     21 root      20   0       0      0      0 S   0.0   0.0   0:00.00 rcu_task+ 
     22 root      20   0       0      0      0 S   0.0   0.0   0:00.00 kauditd   
     23 root      20   0       0      0      0 S   0.0   0.0   0:00.00 xenbus    
     24 root      20   0       0      0      0 S   0.0   0.0   0:00.03 xenwatch  
     25 root      20   0       0      0      0 S   0.0   0.0   0:00.00 khungtas+ 
     26 root      20   0       0      0      0 S   0.0   0.0   0:00.00 oom_reap+ 
```

In the terminal, the output changes dynamically with the resource usage of the different processes, similar to what we see in the Task Manager in Windows. It also shows important information such as PID (process ID), user, CPU usage, memory usage, and the command or process name.

In the above terminal output, we can see that the topmost entry in the output is a process that uses 100% CPU. We will return to it later, but for now, we can see that our shell is not interactive and only shows this command's result.

To exit from this view, press the q key.


Killing the Culprit
At the top of the output of the top command, we find our culprit. It's the process named a, which uses unusually high CPU resources. In normal circumstances, we shouldn't have processes consistently using very high amounts of CPU resources. However, certain processes might do this for a short time for intense processing.

The process we see here consistently uses 100% of the CPU resources, which can signify a hard-working malicious process, like a cryptominer. We see that the root user runs this process. The process' name and resource usage gives a suspicious vibe, and assuming this is the process unnecessarily hogging our resources, we would like to kill it. (Disclaimer: In actual production servers, don't try to kill processes unless you are sure what you are doing.)

If we wanted to perform forensics, we would take a memory dump of the process to analyse it further before killing it, as killing it would cause us to lose that information. However, taking a memory dump is out of scope here. We will assume that we have already done that and move on to termination.

We can use the kill command to kill this process. However, since the process is running as root, it's a good idea to use sudo to elevate privileges for killing this process. Let's try to kill the process. Note that you will have to replace 2062 with the PID that is shown in your top command's output.

```
ubuntu@tryhackme:~$ sudo kill 2062
ubuntu@tryhackme:~$ 
```

Here, we have given the process's PID as the parameter to the kill command. We don't get any error as the output, so we believe the process has been killed successfully. Let's check again with the top command.

```
Tasks: 187 total,   2 running, 183 sleeping,   0 stopped,   2 zombie
%Cpu(s): 34.6 us,  3.8 sy,  0.0 ni, 53.8 id,  0.0 wa,  0.0 hi,  0.0 si,  7.7 st
MiB Mem :   3933.8 total,   2094.9 free,    632.6 used,   1206.2 buff/cache
MiB Swap:      0.0 total,      0.0 free,      0.0 used.   2983.9 avail Mem 

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND   
   2257 root      20   0    2488   1424   1332 R  93.8   0.0   1:59.16 a         
      1 root      20   0  104360  12052   8596 S   0.0   0.3   0:04.53 systemd   
      2 root      20   0       0      0      0 S   0.0   0.0   0:00.00 kthreadd  
      3 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 rcu_gp    
      4 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 rcu_par_+ 
      5 root      20   0       0      0      0 I   0.0   0.0   0:00.56 kworker/+ 
      6 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/+ 
      9 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 mm_percp+ 
     10 root      20   0       0      0      0 S   0.0   0.0   0:00.12 ksoftirq+ 
     11 root      20   0       0      0      0 I   0.0   0.0   0:00.63 rcu_sched 
     12 root      rt   0       0      0      0 S   0.0   0.0   0:00.01 migratio+ 
     13 root      20   0       0      0      0 S   0.0   0.0   0:00.00 cpuhp/0   
     14 root      20   0       0      0      0 S   0.0   0.0   0:00.00 cpuhp/1   
     15 root      rt   0       0      0      0 S   0.0   0.0   0:00.32 migratio+ 
     16 root      20   0       0      0      0 S   0.0   0.0   0:00.14 ksoftirq+ 
     18 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/+ 
     19 root      20   0       0      0      0 S   0.0   0.0   0:00.00 kdevtmpfs 
     20 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 netns     
     21 root      20   0       0      0      0 S   0.0   0.0   0:00.00 rcu_task+ 
     22 root      20   0       0      0      0 S   0.0   0.0   0:00.00 kauditd   
     23 root      20   0       0      0      0 S   0.0   0.0   0:00.00 xenbus    
     24 root      20   0       0      0      0 S   0.0   0.0   0:00.03 xenwatch  
     25 root      20   0       0      0      0 S   0.0   0.0   0:00.00 khungtas+ 
     26 root      20   0       0      0      0 S   0.0   0.0   0:00.00 oom_reap+ 
     27 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 writeback 
     28 root      20   0       0      0      0 S   0.0   0.0   0:00.00 kcompact+ 
     29 root      25   5       0      0      0 S   0.0   0.0   0:00.00 ksmd      
     30 root      39  19       0      0      0 S   0.0   0.0   0:00.00 khugepag+ 
```

Woah! The process is still there. Did our command not work or what? Wait, the PID has changed, and so has the TIME. It looks like we successfully killed the process, but it has been resurrected somehow. Let's see what happened.

Checking the Cronjobs

Our first hint of what happened with the process will be in the cronjobs. Cronjobs are tasks that we ask the computer to perform on our behalf at a fixed interval. Often, that's where we can find traces of auto-starting processes.

To check the cronjobs, we can run the command crontab -l. A nice description is shown in the comments (lines starting with the # character) in the below terminal that can help us understand the cronjob's format, followed by the cronjobs that are currently active (lines starting without the # character). 

```
ubuntu@tryhackme:~$ crontab -l          
# Edit this file to introduce tasks to be run by cron.
# 
# Each task to run has to be defined through a single line
# indicating with different fields when the task will be run
# and what command to run for the task
# 
# To define the time you can provide concrete values for
# minute (m), hour (h), day of month (dom), month (mon),
# and day of week (dow) or use '*' in these fields (for 'any').
# 
# Notice that tasks will be started based on the cron's system
# daemon's notion of time and timezones.
# 
# Output of the crontab jobs (including errors) is sent through
# email to the user the crontab file belongs to (unless redirected).
# 
# For example, you can run a backup of all your user accounts
# at 5 a.m every week with:
# 0 5 * * 1 tar -zcf /var/backups/home.tgz /home/
# 
# For more information see the manual pages of crontab(5) and cron(8)
# 
# m h  dom mon dow   command
@reboot sudo runuser -l ubuntu -c 'vncserver :1 -depth 24 -geometry 1900x1200'
@reboot sudo python3 -m websockify 80 localhost:5901 -D
```

Well, it looks like we have no luck finding our process here. We see that the only cronjobs run by the user are about running a VNC server.

But wait, the process was running as root, and each user has their own cronjobs, so why don't we check the cronjobs as the root user? Let's switch user to root and see if we find something there. We first switch user using sudo su, which switches our user to root. Then, we check for cronjobs again.

```
ubuntu@tryhackme:~$ sudo su
root@tryhackme:/home/ubuntu# crontab -l
# Edit this file to introduce tasks to be run by cron.
# 
# Each task to run has to be defined through a single line
# indicating with different fields when the task will be run
# and what command to run for the task
# 
# To define the time you can provide concrete values for
# minute (m), hour (h), day of month (dom), month (mon),
# and day of week (dow) or use '*' in these fields (for 'any').
# 
# Notice that tasks will be started based on the cron's system
# daemon's notion of time and timezones.
# 
# Output of the crontab jobs (including errors) is sent through
# email to the user the crontab file belongs to (unless redirected).
# 
# For example, you can run a backup of all your user accounts
# at 5 a.m every week with:
# 0 5 * * 1 tar -zcf /var/backups/home.tgz /home/
# 
# For more information see the manual pages of crontab(5) and cron(8)
# 
# m h  dom mon dow   command
root@tryhackme:/home/ubuntu# 
```

Well, tough luck! No cronjobs running here, either. What else can there be?

Check for Running Services

Maybe we should check for running services that might bring the process back. But the process name is quite generic and doesn't give a good hint. We might be clutching at straws here, but let's see what services are running on the system. 

To do this, we use the systemctl list-unit-files to list all services. Since the service we are looking for must be enabled to respawn the process, we use grep to give us only those services that are enabled.

```
ubuntu@tryhackme:~$ systemctl list-unit-files | grep enabled
proc-sys-fs-binfmt_misc.automount              static          enabled      
-.mount                                        generated       enabled      
dev-hugepages.mount                            static          enabled      
dev-mqueue.mount                               static          enabled      
proc-sys-fs-binfmt_misc.mount                  disabled        enabled      
snap-amazon\x2dssm\x2dagent-2012.mount         enabled         enabled      
snap-amazon\x2dssm\x2dagent-5163.mount         enabled         enabled      
snap-core-16202.mount                          enabled         enabled      
snap-core18-2284.mount                         enabled         enabled      
snap-core18-2790.mount                         enabled         enabled      
snap-core20-1361.mount                         enabled         enabled      
snap-core20-2015.mount                         enabled         enabled      
snap-lxd-22526.mount                           enabled         enabled      
snap-lxd-24061.mount                           enabled         enabled      
sys-fs-fuse-connections.mount                  static          enabled 
.
.
.
. 
[redacted]                                     enabled         enabled      
accounts-daemon.service                        enabled         enabled      
acpid.service                                  disabled        enabled      
alsa-restore.service                           static          enabled      
alsa-state.service                             static          enabled      
alsa-utils.service                             masked          enabled  
.
.
```

We do find something suspicious here. It looks like it has a strange name for a normal service. Let's get more information about this service, starting with checking its status.

```
ubuntu@tryhackme:~$ systemctl status [redacted] 
● [redacted] - Unkillable exe
     Loaded: loaded (/etc/systemd/system/[redacted]; enabled; vendor preset: enabled)
     Active: active (running) since Wed 2023-11-01 03:08:13 UTC; 1h 22min ago
   Main PID: 604 (sudo)
      Tasks: 5 (limit: 4710)
     Memory: 3.5M
     CGroup: /system.slice/[redacted] 
├─ 604 /usr/bin/sudo /etc/systemd/system/a service
             ├─ 672 /etc/systemd/system/a service
             └─2257 unkillable proc

Nov 01 03:08:13 tryhackme systemd[1]: Started Unkillable exe.
Nov 01 03:08:13 tryhackme sudo[604]:     root : TTY=unknown ; PWD=/ ; USER=root ; COMMAND=/etc/systemd/system/a service
Nov 01 03:08:13 tryhackme sudo[604]: pam_unix(sudo:session): session opened for user root by (uid=0)
Nov 01 03:08:13 tryhackme sudo[680]: [redacted] Nov 01 03:21:47 tryhackme sudo[2066]: [redacted] 
Nov 01 03:59:57 tryhackme sudo[2261]: [redacted] 
ubuntu@tryhackme:~$ 
```

Oh, we found the devil in the details! We can see that this service is running the process named a that we couldn't kill. What's more, the service is taunting us with a greeting message. We must kill this service if we are to kill this useless process.

Getting Rid of the Service

So, now that we have identified the service, let's embark on a journey to get rid of it. The first step will be to stop the service. 

We might need root privileges for that, so we will have to switch to the root user.

```
ubuntu@tryhackme:~$ sudo su
root@tryhackme:/home/ubuntu# systemctl stop [redacted] 
root@tryhackme:/home/ubuntu#
```

Let's check the status again.

```
● [redacted] - Unkillable exe
     Loaded: loaded (/etc/systemd/system/[redacted]; enabled; vendor preset: enabled)
     Active: inactive (dead) since Wed 2023-11-01 04:38:06 UTC; 10s ago
    Process: 604 ExecStart=/usr/bin/sudo /etc/systemd/system/a service (code=killed, signal=TERM)
   Main PID: 604 (code=killed, signal=TERM)

Nov 01 03:08:13 tryhackme systemd[1]: Started Unkillable exe.
Nov 01 03:08:13 tryhackme sudo[604]:     root : TTY=unknown ; PWD=/ ; USER=root ; COMMAND=/etc/systemd/system/a service
Nov 01 03:08:13 tryhackme sudo[604]: pam_unix(sudo:session): session opened for user root by (uid=0)
Nov 01 03:08:13 tryhackme sudo[680]: [redacted] 
Nov 01 03:21:47 tryhackme sudo[2066]: [redacted] 
Nov 01 03:59:57 tryhackme sudo[2261]: [redacted] 
Nov 01 04:38:06 tryhackme systemd[1]: Stopping Unkillable exe...
Nov 01 04:38:06 tryhackme sudo[604]: pam_unix(sudo:session): session closed for user root
Nov 01 04:38:06 tryhackme systemd[1]: [redacted]: Succeeded.
Nov 01 04:38:06 tryhackme systemd[1]: Stopped Unkillable exe.
root@tryhackme:/home/ubuntu#
```

Yeah! Not so unkillable now, is it? But let's not stop here. Let's check up on our process. Running the top command, we get the following.

```
Tasks: 185 total,   1 running, 184 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.3 us,  0.0 sy,  0.0 ni, 99.5 id,  0.0 wa,  0.0 hi,  0.2 si,  0.0 st
MiB Mem :   3933.8 total,   2086.3 free,    636.8 used,   1210.7 buff/cache
MiB Swap:      0.0 total,      0.0 free,      0.0 used.   2979.8 avail Mem 

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND                                                                                     
    941 ubuntu    20   0  352660 132948  60792 S   0.7   3.3   0:21.75 Xtigervnc                                                                                   
   2267 root      20   0  124624  28808   7844 S   0.7   0.7   0:04.29 python3                                                                                     
   1179 lightdm   20   0  565972  44756  37252 S   0.3   1.1   0:05.49 slick-greeter                                                                               
      1 root      20   0  104360  12056   8596 S   0.0   0.3   0:09.90 systemd                                                                                     
      2 root      20   0       0      0      0 S   0.0   0.0   0:00.00 kthreadd                                                                                    
      3 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 rcu_gp                                                                                      
      4 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 rcu_par_gp                                                                                  
      5 root      20   0       0      0      0 I   0.0   0.0   0:00.78 kworker/0:0-events                                                                          
      6 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/0:0H-kblockd                                                                        
      9 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 mm_percpu_wq                                                                                
     10 root      20   0       0      0      0 S   0.0   0.0   0:00.12 ksoftirqd/0                                                                                 
     11 root      20   0       0      0      0 I   0.0   0.0   0:00.93 rcu_sched                                                                                   
     12 root      rt   0       0      0      0 S   0.0   0.0   0:00.04 migration/0                                                                                 
     13 root      20   0       0      0      0 S   0.0   0.0   0:00.00 cpuhp/0                                                                                     
     14 root      20   0       0      0      0 S   0.0   0.0   0:00.00 cpuhp/1                                                                                     
     15 root      rt   0       0      0      0 S   0.0   0.0   0:00.33 migration/1                                                                                 
     16 root      20   0       0      0      0 S   0.0   0.0   0:00.18 ksoftirqd/1                                                                                 
     18 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/1:0H-kblockd                                                                        
     19 root      20   0       0      0      0 S   0.0   0.0   0:00.00 kdevtmpfs                                                                                   
     20 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 netns                                                                                       
     21 root      20   0       0      0      0 S   0.0   0.0   0:00.00 rcu_tasks_kthre                                                                             
     22 root      20   0       0      0      0 S   0.0   0.0   0:00.00 kauditd                                                                                     
     23 root      20   0       0      0      0 S   0.0   0.0   0:00.00 xenbus                                                                                      
     24 root      20   0       0      0      0 S   0.0   0.0   0:00.03 xenwatch                                                                                    
     25 root      20   0       0      0      0 S   0.0   0.0   0:00.00 khungtaskd                                                                                  
     26 root      20   0       0      0      0 S   0.0   0.0   0:00.00 oom_reaper                                                                                  
     27 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 writeback                                                                                   
     28 root      20   0       0      0      0 S   0.0   0.0   0:00.00 kcompactd0                                                                                  
     29 root      25   5       0      0      0 S   0.0   0.0   0:00.00 ksmd
```

Yayy! No more unkillable process. Now, let's quickly wrap this up by killing the service as well. We continue by disabling the service.

```
root@tryhackme:/home/ubuntu# systemctl disable [redacted] 
Removed /etc/systemd/system/multi-user.target.wants/[redacted].
root@tryhackme:/home/ubuntu# systemctl status [redacted] 
● [redacted] - Unkillable exe
     Loaded: loaded (/etc/systemd/system/[redacted]; disabled; vendor preset: enabled)
     Active: inactive (dead)

Nov 01 03:08:13 tryhackme systemd[1]: Started Unkillable exe.
Nov 01 03:08:13 tryhackme sudo[604]:     root : TTY=unknown ; PWD=/ ; USER=root ; COMMAND=/etc/systemd/system/a service
Nov 01 03:08:13 tryhackme sudo[604]: pam_unix(sudo:session): session opened for user root by (uid=0)
Nov 01 03:08:13 tryhackme sudo[680]: [redacted] 
Nov 01 03:21:47 tryhackme sudo[2066]: [redacted] 
Nov 01 03:59:57 tryhackme sudo[2261]: [redacted] 
Nov 01 04:38:06 tryhackme systemd[1]: Stopping Unkillable exe...
Nov 01 04:38:06 tryhackme sudo[604]: pam_unix(sudo:session): session closed for user root
Nov 01 04:38:06 tryhackme systemd[1]: [redacted]: Succeeded.
Nov 01 04:38:06 tryhackme systemd[1]: Stopped Unkillable exe.
root@tryhackme:/home/ubuntu#
```

Alright, so we can see that the status is still loaded, but it's disabled. The problem is that the service is still present in the system. To completely eradicate the service, we will have to remove the files from the file system as well. Let's do that. Here, we see the location of the service is /etc/systemd/system/[redacted] and the location of the process is /etc/systemd/system/a. To permanently kill the service, let's delete these two files.

```
root@tryhackme:/home/ubuntu# rm -rf /etc/systemd/system/a
root@tryhackme:/home/ubuntu# rm -rf /etc/systemd/system/[redacted]  
root@tryhackme:/home/ubuntu# systemctl status [redacted] 
Unit [redacted] could not be found.
root@tryhackme:/home/ubuntu# 
```

Finally! We are now rid of the stubborn service that claimed to be unkillable. To wrap it up, we might run the following command to ensure no remnants are left. This will reload all the service configurations and create the whole service dependency tree again, meaning that if there are any remnants left, it will eliminate them.

```
root@tryhackme:/home/ubuntu# systemctl daemon-reload
root@tryhackme:/home/ubuntu# 
```

And that means we can relax. The CPU usage is normal, and the persistent process has been successfully eradicated. However, we still want to know who planted the process and what it did. We have already taken a memory dump of the process so that we can analyse it to uncover further information. Come back tomorrow to find out if our suspicions are confirmed!

**Q/A**

![answers](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/5c74b95a-4db3-489a-9f25-dc1d8ae936e4)
