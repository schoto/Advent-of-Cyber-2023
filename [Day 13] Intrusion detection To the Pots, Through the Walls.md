The proposed merger and suspicious activities have kept all teams busy and engaged. So that the Best Festival Company's systems are safeguarded in the future against malicious attacks, McSkidy assigns The B Team, led by McHoneyBell, to research and investigate mitigation and proactive security.

The team's efforts will be channelled into the company's defensive security process. You are part of the team – a security researcher tasked with gathering information on defence and mitigation efforts.

**Learning Objectives**

In today's task, you will:

- Learn to understand incident analysis through the Diamond Model.
- Identify defensive strategies that can be applied to the Diamond Model.
- Learn to set up firewall rules and a honeypot as defensive strategies.

**Introduction**

Intrusion detection and prevention is a critical component of cyber security aimed at identifying and mitigating threats. When set up early, intrusion detection becomes a proactive security measure. However, in our story, the Best Festival Company has to develop ways to improve their security, given the magnitude of the recent breaches.

In this epic task, we'll embark on a thrilling journey through fundamental concepts, detection strategies, and the application of the Diamond Model of Intrusion Analysis in defensive security.

Incident Analysis

Consider the cyber threat events that have recently taken place within the Best Festival Company and AntarctiCrafts. We have identified clues and artefacts, but we're yet to piece them together to lead us to the attacker. We need a framework to profile the attacker, understand their moves, and help us strengthen our defences.

The DiamondModel is a security analysis framework that seasoned professionals use to unravel the mysteries of adversary operations and identify the elements used in an intrusion. It comprises four core facets, interconnected to form a well-orchestrated blueprint of the attacker's plans:

- Adversary
- Victim
- Infrastructure
- Capability

We'll wield the knowledge we gained from the previous days of Advent of Cyber to unlock the secrets hidden within these core features.

**Adversary**

In our exciting storyline, we have discovered a suspected insider threat causing trouble within the Best Festival Company and interfering with the proposed merger with AntarctiCrafts. This individual, who we'll call the adversary operator, is not just an ordinary troublemaker. They are the clever attackers or malicious threat actors responsible for cyberattacks or intrusions. Adversary operators can be an individual or an entire organisation aiming to disrupt the operations of another.

That's not the only type of adversary. The adversary customer is another intriguing player in this grand scheme. They are the one who reaps the rewards from the cyberattack and can consolidate the efforts of various adversary operators.

Picture this: a collection of adversaries working together to orchestrate widespread security breaches, just like the enigmatic advanced persistent threat (APT) groups.

Victim

This is none other than the target of the adversary's wicked intentions. It could be a single individual or domain or an entire organisation with multiple network and data assets. The Best Festival Company finds itself at the mercy of these adversaries, and we must shield them from further harm.

Infrastructure

Every adversary needs tools. They require software or hardware to execute their malicious objectives. Infrastructure represents the physical and logical interconnections that an adversary employs. Our story takes an interesting twist as we uncover the USB drive that Tracy McGreedy cunningly plugged in, disrupting Santa's meticulously crafted plans.

But beware. Adversarial infrastructure can be owned and controlled by adversaries or even intermediaries like service providers.

Capability

Ah, what capabilities these adversaries have; what skills, tools, and techniques they employ!

Here, we shine a light on the tactics, techniques, and procedures (TTPs) that shape adversaries' devious endeavours. Intruders or adversaries may employ various tactics, techniques, and procedures for malicious activities. Some examples include:

- Phishing: Adversaries may use deceptive emails or messages to trick individuals into revealing sensitive information or clicking on malicious links.
- Exploiting vulnerabilities: Adversaries can exploit weaknesses or vulnerabilities in software, systems, or networks to gain unauthorised access or perform malicious actions. This was very well showcased on AOC Day 10, where we covered SQL injection as one of the techniques used.
- Social engineering: This involves manipulating individuals through psychological tactics to gain unauthorised access or obtain confidential information.
- Malware attacks: Adversaries may deploy malicious software, such as viruses, worms, or ransomware, to gain control over systems or steal data.
- Insider threat: This refers to individuals within an organisation who misuse their access privileges to compromise systems, steal data, or disrupt operations.
- Denial–of–service (DoS) Attacks: Adversaries may overwhelm a target system or network with excessive traffic or requests, causing it to become unresponsive or crash.

Defensive Diamond Model

But fear not, for we shall not be mere observers in this cosmic battle! We will harness the power of the Diamond Model's components, particularly capability and infrastructure, for our defensive endeavours. We will forge The Best Festival Company into a formidable defender – no longer a hapless victim.

Defensive Capability

It is said that defence is the best offence. In the quest for protection against adversaries, the Best Festival Company must equip itself with powerful defensive capabilities. Two key elements of this are threat hunting and vulnerability management.

Threat hunting is a proactive and iterative process, led by skilled security professionals, to actively search for signs of malicious activities or security weaknesses within the organisation's network and systems. Organisations can detect adversaries early in their attack lifecycle by conducting regular threat hunts. Threat hunters analyse behavioural patterns, identify advanced threats, and improve incident response. Developing predefined hunting playbooks and fostering collaboration among teams ensures a systematic and efficient approach to threat hunting.

Vulnerability management is a structured process of identifying, assessing, prioritising, mitigating, and monitoring vulnerabilities in an organisation's systems and applications. Regular vulnerability scanning helps identify weaknesses that adversaries could exploit. Prioritising vulnerabilities based on their severity and potential impact, promptly patching or remediating vulnerabilities, and maintaining an up–to–date asset inventory is essential. Continuous monitoring, integration with threat intelligence feeds, and periodic penetration testing further strengthen the organisation's security posture. Meanwhile, reporting and accountability provide visibility into security efforts.

By integrating threat hunting and vulnerability management, organisations can proactively defend against adversaries, detect threats early, and reduce the attack surface. These defensive capabilities form a solid foundation for incident response and ensure the best possible defence for the Best Festival Company.

Defensive Infrastructure

The Best Festival Company will construct their bastion of defence, fortified with tools and infrastructure to repel cyber-attacks. Layer upon layer of hardware and software will be deployed, ranging from intrusion defence and prevention systems to robust anti-malware solutions. The objective is to impede attackers by limiting their options to predetermined paths and disrupting their malicious actions with increased noise.

This strategy serves as a deterrent to the attacker, making it more difficult for them to carry out their intended activities and providing an opportunity for detection and response. By implementing this approach, organisations can strengthen their cyber security posture and reduce the risk of successful attacks.

In this section, we'll guide Van Twinkle on her quest to understand two essential components of defence infrastructure: mighty firewalls and cunning honeypots.

Firewall

The mighty firewall is a guardian of networks and a sentinel of cyber security! This network security device stands vigilant, monitoring and controlling the ebb and flow of incoming and outgoing network traffic. With its predetermined security rules, a firewall can repel a wide range of threats, from unauthorised access to malicious traffic and even attempts to breach sensitive data.

Firewalls come in many forms, including hardware, software, or a combination. Their presence is vital, a cornerstone of any cyber security defence strategy. The following are the common types of firewalls that exist:

- Stateless/packet-filtering: This firewall provides the most straightforward functionality by inspecting and filtering individual network packets based on a set of rules that would point to a source or destination IP address, ports and protocols. The firewall doesn’t consider any context of each connection when making decisions and effectively blocks denial–of–service attacks and port scans.
- Stateful inspection: This firewall is more sophisticated. It is used to track the state of network connections and use this information to make filtering decisions. For example, if a packet being channelled to the network is part of an established connection, the stateful firewall will let it pass through. However, the packet will be blocked if it is not part of an established connection.
- Proxy service: This firewall protects the network by filtering messages at the application layer, providing deep packet inspection and more granular control over traffic content. The firewall can block access to certain websites or block the transmission of specific types of files.
- Web application firewall (WAF): This firewall is designed to protect web applications. WAFs block common web attacks such as SQL injection, cross-site scripting, and denial-of-service attacks.
- Next-generation firewall: This firewall combines the functionalities of the stateless, stateful, and proxy firewalls with features such as intrusion detection and prevention and content filtering.

For the remainder of the task, we shall focus on one application of a stateful inspection firewall in the form of the uncomplicated firewall (ufw).

Configuring Firewalls to Block Traffic

Van Twinkle knows that the uncomplicated firewall is the default firewall configuration tool available on Ubuntu hosts, and she decides to use it for this experiment. Initially, it's turned off by default, so we can check the status by running the command below:

```
vantwinkle@aocday13:~$ sudo ufw status
Status inactive
```

We don't currently have any rules, so we can define default rules to allow or block traffic. These can be set to deny all incoming connections and allow outgoing connections.

```
vantwinkle@aocday13:~$ sudo ufw default allow outgoing
Default outgoing policy changed to 'allow'
(be sure to update your rules accordingly

vantwinkle@aocday13:~$ sudo ufw default deny incoming
Default incoming policy changed to 'deny'
(be sure to update your rules accordingly
```

Additionally, we can add, modify, and delete rules by specifying an IP address, port number, service name, or protocol. In this example, we can add a rule to allow legitimate incoming connections to port 22, which would allow connectivity via SSH. We should get two confirmation messages indicating that the rule has been implemented for IPv4 and IPv6 connections.

```
vantwinkle@aocday13:~$ sudo ufw  allow 22/tcp
Rules updated
Rules updated (v6)
```

Firewall rules can get more complex, incorporating specific IP addresses, subnets or even specific network interfaces.

```
vantwinkle@aocday13:~$ sudo ufw deny from 192.168.100.25
Rule added

vantwinkle@aocday13:~$ sudo ufw deny in on eth0 from 192.168.100.26
Rule added
```

Once we have added our rules, we can enable the service and check the rules set.

```
vantwinkle@aocday13:~$ sudo ufw enable
Firewall is active and enabled on system startup

vantwinkle@aocday13:~$ sudo ufw status verbose
Status: active
Logging: on (low)
Default: deny (incoming), allow (outgoing), disabled (routed)
New profiles: skip
           
To                  Action        From
--                  -------       ----
22/tcp              ALLOW IN    Anywhere
22/tcp (v6)         ALLOW IN    Anywhere (v6)
Anywhere            DENY        192.168.100.25
Anywhere on eth0    DENY IN     192.168.100.26
```

What happens if the rules are incorrectly configured? We can reset the firewall and, revert to its default state and be able to configure the rules fresh.

```
vantwinkle@aocday13:~$ sudo ufw reset
Resetting all rules to installed defaults. This may disrupt existing ssh
connections. Proceed with operation (y|n)? y
Backing up 'user.rules' to '/etc/ufw/user.rules.20231105_130227'
Backing up 'before.rules' to '/etc/ufw/before.rules.20231105_130227'
Backing up 'after.rules' to '/etc/ufw/after.rules.20231105_130227'
Backing up 'user6.rules' to '/etc/ufw/user6.rules.20231105_130227'
Backing up 'before6.rules' to '/etc/ufw/before6.rules.20231105_130227'
Backing up 'after6.rules' to '/etc/ufw/after6.rules.20231105_130227'
```

At this point, Van Twinkle has a much deeper understanding of how to set up and configure firewall rules to help McHoneyBell implement Santa's defences.

Honeypot

This is another intriguing piece of infrastructure in the world of defensive security. Picture a trap laid for the attackers, a mirage of vulnerability tempting them away from the true treasures. Behold, the honeypot!

A honeypot is a cyber security mechanism – a masterful deception. It presents itself as an alluring target to the adversaries, drawing them away from the true prizes. Honeypots come in various forms: software applications, servers, or entire networks. They are designed to mimic legitimate targets, yet they are under the watchful control of the defender. For the Best Festival Company, envision a honeypot masquerading as Santa's website – a perfect replica of the real one.

Honeypots can be classified into two main types:

- Low–interaction honeypots: These honeypots artfully mimic simple systems like web servers or databases. They gather intelligence on attacker behaviour and detect new attack techniques.
- High–interaction honeypots: These honeypots take deception to new heights, emulating complex systems like operating systems and networks. They collect meticulous details on attacker behaviour and study their techniques to exploit vulnerabilities.

To demonstrate how to set up a honeypot, we'll use a tool called PenTBox, which has already been installed on the VM under ```/home/vantwinkle/pentbox/pentbox-1.8```. Launch the tool via the directory demonstrated below, select option 2 for network tools, and follow with option 3 to install the honeypot.

```
vantwinkle@aocday13:~/pentbox/pentbox-1.8$ sudo ./pentbox.rb
           
PenTBox 1.8
           
------- Menu         ruby2.7.0 @ x86_64-linux-gnu
1 - Cryptography tools
2 - Network tools
3 - Web
           
----Redacted---
-> 2
           
1 - Net DoS Tester
2 - TCP port scanner
3 - Honeypot
--- Redacted---
```
When we select the option to set up the honeypot, we can choose to set up an auto-configuration or a manual configuration. The manual configuration offers more options to allocate which port to open and a custom message for the honeypot to display. Accompanying these options, log data will be collected and displayed on the terminal for every intrusion encountered.

With the active honeypot, we can attempt to connect to the VM by navigating to on the AttackBox browser. You should see the custom message crafted from the honeypot. Once connected, the intrusion will trigger an alert on the honeypot, and a log will be created showing the attacking IP and port.

```
1- Fast Auto Configuration
2- Manual Configuration
           
-> 2
           
Insert port to Open
-> 8080
Insert false message to show
-> Santa has gone for the Holidays. Tough luck.
           
---Redacted---
HONEYPOT ACTIVATED ON PORT 8080

INTRUSION ATTEMPT DETECTED! from 10.0.2.5:49852 (2023-11-01 22:56:15 +0000)
```

**Van Twinkle's Challenge**

After learning about firewalls and honeypots, Van Twinkle puts his knowledge into practice and sets up a simple website to be hidden behind some firewall rules. You can deploy the firewall rules by executing the Van_Twinkle_rules.sh script within the/home/vantwinkle directory. Your task is to update the firewall rules to expose the website to the public and find a hidden flag.

**Q/A**

![thmhghghg](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/cac11718-c8d1-4f2b-892f-3949f1f2ce29)


