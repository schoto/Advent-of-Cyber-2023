As the elves try to recover the compromised servers, McSkidy's SOC team identify abnormal activity and notice that a massive amount of data is being sent to an unknown server (already identified on Day 9). An insider has likely created a malicious backdoor. McSkidy has contacted Detective Frost-eau from law enforcement to help them. Can you assist Detective Frost-eau in taking down the command and control server?

**Learning Objectives**

- Understanding server-side request forgery (SSRF)
- Which different types of SSRF are used to exploit the vulnerability
- Prerequisites for exploiting the vulnerability
- How the attack works
- How to exploit the vulnerability
- Mitigation measures for protection

What Is SSRF?

SSRF, or server-side request forgery, is a security vulnerability that occurs when an attacker tricks a web application into making unauthorised requests to internal or external resources on the server's behalf. This can allow an attacker to interact with internal systems, potentially leading to data exposure or unauthorised actions. Leaving web applications vulnerable to SSRF can have profound security implications, potentially leading to unauthorised access to internal systems, remote code execution (RCE), data breaches, or the application being further compromised.

Types of SSRF Attack

- Basic: In a basic SSRF attack, the attacker sends a crafted request from the vulnerable server to internal or external resources. For example, they might attempt to access files on the local file system, internal services, or databases that are not intended to be publicly accessible.
- Blind SSRF: In a blind SSRF attack, the attacker doesn't directly see the response to the request. Instead, they may infer information about the internal network by measuring the time it takes for the server to respond or observing error message changes.
- Semi-blind SSRF: In semi-blind SSRF, again, the attacker does not receive direct responses in their browser or application. However, they rely on indirect clues, side-channel information, or observable effects within the application to determine the success or failure of their SSRF requests. This might involve monitoring changes in application behaviour, response times, error messages, and other signs.

Prerequisites for Exploitation

- Vulnerable input points: Web applications must have input fields susceptible to manipulation, such as URLs or file upload functionalities.
- Lack of input validation: The application should have adequate input validation or effective sanitisation mechanisms, allowing an attacker to craft malicious requests.

How Does SSRF Work?

- Identifying vulnerable input: The attacker locates an input field within the application that can be manipulated to trigger server-side requests. This could be a URL parameter in a web form, an API endpoint, or request parameter input such as the referrer.
- Manipulating the input: The attacker inputs a malicious URL or other payloads that cause the application to make unintended requests. This input could be a URL pointing to an internal server, a loopback address, or an external server under the attacker's control.
- Requesting unauthorised resources: The application server, unaware of the malicious input, makes a request to the specified URL or resource. This request could target internal resources, sensitive services, or external systems.
- Exploiting the response: Depending on the application's behaviour and the attacker's payload, the response from the malicious request may provide valuable information, such as internal server data, credentials, system credentials/information, or pathways for further exploitation.

Using SSRF To Hack the Command and Control Server

Disclaimer: Hacking a command and control server is ethically unacceptable and illegal. Any suspected C2 activity should be reported to the appropriate incident response team for investigation and mitigation. This knowledge is provided solely for educational purposes.

Detective Frost-eau checked the C2 against known vulnerabilities, but none worked; he decided to give SSRF a shot. Now that we know how the SSRF works, can you help Detective Frost-eau take down the C2 server?

Launch the virtual machine by clicking Start Machine at the top right of this task. Wait for 1-2 minutes for the machine to load completely. You can access the C2 server by visiting the URL http://mcgreedysecretc2.thm but first, you need to add the hostname in your OS or AttackBox.

```
How to add hostname (click to read)
If you are connected via VPN or AttackBox, you can add the hostname mcgreedysecretc2.thm by first opening the host file, depending on your host operating system.
Windows                       :  C:\Windows\System32\drivers\etc\hosts
Ubuntu or AttackBox: /etc/hosts
Open the host file and add a new line at the end of the file in the format: 10.10.20.151 mcgreedysecretc2.thm
Save the file and type http://mcgreedysecretc2.thm in the browser to access the website.
```

- Identify vulnerable input: Once we visit the URL for the command and control server, we'll see that it's protected by a login panel. McSkidy's pentester team have launched different types of automated and manual scans to gain access – but all in vain. For a target to be exploitable through SSRF, we need to use some vulnerable input to forge the request to the server. Sometimes, these requests can be found through scanning, viewing source code, or other documentation logs.

![mcgreedy](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/99e03880-b1c6-478a-9314-b313aa50fb84)

Manipulating the input: McSkidy noticed a link to the documentation at the bottom of the page. Once we click on the URL, it redirects us to the endpoint's API. Now that we have some URLs, we can try SSRF attacks against them.

![api](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/97563bc4-5034-4129-a9ac-7d4627f58c1c)

- Requesting the unauthorised resources:  We can see that one of the endpoints http://10.10.20.151/getClientData.php?url=http://IP_OF_CLIENT/NAME_OF_FILE_YOU_WANT_TO_ACCESS takes the URL as a parameter. If an infected agent URL is provided to it, it will fetch all files from the infected agent. But what if we change the URL parameter to a different IP and try to access any other file?
- Exploiting the response:  We noticed that if we change the URL parameter to any other file on the host, we can still fetch the file like http://10.10.20.151/getClientData.php?url=file:////var/www/html/index.php will fetch the contents of  index.php.

```
<?php
session_start();
include('config.php');

// Check if the form was submitted
if ($_SERVER["REQUEST_METHOD"] == "POST") {
    // Retrieve the submitted username and password
  
  $uname = $_POST["username"];
    $pwd = $_POST["password"];

    if ($uname === $username && $pwd === $password) {
...
```

The file: scheme, when used in a URL, typically references local files on a computer or file system. For example, a URL like file:///path/to/any/file is often used to access a file located on your local file system. Usually, an attacker can access sensitive files like /etc/passwd and connection strings (config.php, connection.php, etc.) to take control of the C2 panel.

![assets](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/25c86118-ee0e-4305-8f49-65196a3b5eeb)

We can get the C2 panel's credentials by accessing the file containing the password. Then we can log in successfully to the C2 panel.

Mitigation Measures

To prevent SSRF exploitation, the following mitigations are suggested:

- Employing strict input validation and sanitisation to prevent malicious input.
- Using allow lists to control which domains and IPs the application can access.
- Applying network segmentation to restrict requests to authorised resources.
- Following the principle of least privilege, granting the minimum permissions required for system operations.

**Q/A**

