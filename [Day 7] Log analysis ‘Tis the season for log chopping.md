To take revenge for the company demoting him to regional manager during the acquisition, Tracy McGreedy installed the CrypTOYminer, a malware he downloaded from the dark web, on all workstations and servers. Even more worrying and unknown to McGreedy, this malware includes a data-stealing functionality, which the malware author benefits from!

The malware has been executed, and now, a lot of unusual traffic is being generated. What's more, a large bandwidth of data is seen to be leaving the network.

Forensic McBlue assembles a team to analyse the proxy logs and understand the suspicious network traffic.

**Learning Objectives**

In this task, we will focus on the following vital learnings to assist Forensic McBlue in uncovering the potential incident:

- Revisiting log files and their importance.
- Understanding what a proxy is and breaking down the contents of a proxy log.
- Building Linux command-line skills to parse log entries manually.
- Analysing a proxy log based on typical use cases.

**Log Primer**

Before analysing a dataset of proxy logs, let's first revisit what log files are.

A log file is like a digital trail of what's happening behind the scenes in a computer or software application. It records important events, actions, errors, or information as they happen. It helps diagnose problems, monitor performance, and record what a program or application is doing. For clarity, let's look at a quick example.

```
158.32.51.188 - - [25/Oct/2023:09:11:14 +0000] "GET /robots.txt HTTP/1.1" 200 11173 "-" "curl/7.68.0"
```

The example above is an entry from an Apache web server log. We can interpret it easily by breaking down each value into its corresponding purpose.

|FIELD  |VALUE  |DESCRIPTION  |
|---|---|---|
|Source IP Address  |158.32.51.188  | The source (computer) that initiated the HTTP request. |
|Timestamp  | [25/Oct/2023:09:11:14 +0000] |The date and time when the event occurred.   |
|HTTP Request	  | GET /robots.txt HTTP/1.1 | The actual HTTP request made, including the request method, URI path, and HTTP version. |
|Status code  | 200 |The response of the web application.  |
|User Agent  |curl/7.68.0  | The user agent used by the source of the request. It is typically tied up to the application used to invoke the HTTP request. |

Being able to interpret a log entry allows you to contextualise the events, whether for debugging purposes or for hunting potential threat activity.

**What Is a Proxy Server**

Since the data to be analysed is a proxy log, we must understand a proxy server.

A proxy server is an intermediary between your computer or device and the internet. When you request information or access a web page, your device connects to the proxy server instead of connecting directly to the target server. The proxy server then forwards your request to the internet, receives the response, and sends it back to your device. To visualise this, refer to the diagram below.

![proxy](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/75a40ca1-a6e3-4bbe-a379-5000d3aa1d22)

Given that our task is hunting suspicious activity on the proxy log, we need to know what possible malicious activity can be seen inside one. Let's elaborate on a few common examples of malicious activities:

| Attack Technique	  | Potential Indicator |
| -------- | ---------- |
| Download attempt of a malicious binary	   | Connection to a known malicious URL binary (e.g. www[.]evil[.]com/malicious[.]exe)    |
| Data exfiltration	 | High count of outbound bandwidth due to file upload (e.g. outbound connection to OneDrive)   |
| Continuous C2 connection	 | High count of outbound connections to a single domain in regular intervals (e.g. connections every five minutes to a single domain)    |

We'll expand further on these concepts in the following task sections.

We must understand the log contents to work on the dataset provided. To make things fun, let's start playing with it by clicking the Start Machine button in the upper-right corner of the task. The machine will start in a split-screen view. If the virtual machine isn't visible, use the blue Show Split View button at the top-right of the page.

The VM contains a proxy log file in the /home/ubuntu/Desktop/artefacts directory named access.log. You can verify this by clicking the Terminal icon on the desktop and executing the following commands:

```
ubuntu@tryhackme:~$ cd Desktop/artefacts
ubuntu@tryhackme:~/Desktop/artefacts$ ls -lah
total 8.3M
drwxrwxr-x 2 ubuntu ubuntu 4.0K Oct 26 08:09 .
drwxr-xr-x 3 ubuntu ubuntu 4.0K Oct 26 08:09 ..
-rw-r--r-- 1 ubuntu ubuntu 8.3M Oct 26 08:09 access.log
```

Note: You can skip the following section if you are familiar with the following Linux commands: cat, less, head, tail, wc, nl.

**Chopping Down the Proxy Log**

Log McBlue.Log McBlue tells us that he has configured the Squid proxy server to use the following log format:

```
timestamp - source_ip - domain:port - http_method - http_uri - status_code - response_size - user_agent
```

Let's use one of the log entries as an example and compare it to the format above.

```
[2023/10/25:16:17:14] 10.10.140.96 storage.live.com:443 GET / 400 630 "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/118.0.0.0 Safari/537.36"
```

| POSITION |FIELD  |VALUE  |
|---|---|---|
| 1 |Timestamp  | [2023/10/25:16:17:14] |
|  2|Source IP  | 10.10.140.96 |
|  3| Domain and Port | storage.live.com:443 |
|  4| HTTP Method | GET |
|  5| HTTP URI | / |
|  6| Status Code | 400 |
|  7| Response Size	 | 630 |
|  8| User Agent | "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/118.0.0.0 Safari/537.36" |

As you can see in the table above, we can break the log entry down and assign a position to each value so that it can be easily interpreted. Now, let's continue by using another Linux command-line tool to split the log entries per column. This is the cut command.

The cut command allows you to extract specific sections (columns) of lines from a file or input stream by "cutting" the line into columns based on a delimiter and selecting which columns to display. This can be done using the -d option (for delimiter) and the -f for position. The example below uses space (' ') as its delimiter and only displays the timestamp (column #1 after cutting the log with space).

```
ubuntu@tryhackme:~/Desktop/artefacts$ cut -d ' ' -f1 access.log
[2023/10/25:15:42:02]
[2023/10/25:15:42:02]
--- REDACTED FOR BREVITY ---
```

It's also possible to select multiple columns, just like in the example below, which chooses the timestamp (column #1), domain, port (column #3), and status code (column #6).

```
ubuntu@tryhackme:~/Desktop/artefacts$ cut -d ' ' -f1,3,6 access.log
[2023/10/25:15:42:02] sway.com:443 200
[2023/10/25:15:42:02] sway.com:443 301
[2023/10/25:15:42:02] sway.office.com:443 200
--- REDACTED FOR BREVITY ---
```

Lastly, the space delimiter won't work if you plan to get the User-Agent column since its value may contain a space, just like in the example log:

```"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/118.0.0.0 Safari/537.36"```

Given this, you must change the delimiter and select column #2 because the User-Agent is enclosed with double quotes.

```
ubuntu@tryhackme:~/Desktop/artefacts$ cut -d '"' -f2 access.log
-
Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/118.0.0.0 Safari/537.36
-
--- REDACTED FOR BREVITY ---
```

In the example above, we used column #2 since column #1 will provide the contents before the first use of double quotes ("). Try executing ```cut -d '"' -f1 access.log``` and see how the output differs from the space delimiter.

**Linux Pipes**

In the previous section, we introduced some Linux commands that will be useful for investigation. To utilise all these commands and produce an output that can provide meaningful information, we can use Linux Pipes.

In Linux or Unix-like operating systems, a pipe (or the "|" character) is a way to connect two or more commands to make them work together seamlessly. It allows you to take the output of one command and use it as the input for another command. We'll introduce more commands by going through some use cases.

Get the first five connections made by 10.10.140.96.

To do this, we'll combine the grep command with the head command.

Grep is a command in Linux that is used for searching text within files or input streams. It typically follows the syntax: grep OPTIONS STRING_TO_SEARCH FILE_NAME.

Let's use the command to focus on the connections made by the specific IP by executing grep 10.10.140.96 access.log. To limit the display to the first five entries, we can append | head -n 5 to that command to achieve our goal.

```
ubuntu@tryhackme:~/Desktop/artefacts$ grep 10.10.140.96 access.log
[2023/10/25:15:46:20] 10.10.140.96 flow.microsoft.com:443 CONNECT - 200 0 "-"
--- REDACTED FOR BREVITY ---

ubuntu@tryhackme:~/Desktop/artefacts$ grep 10.10.140.96 access.log | head -n 5
[2023/10/25:15:46:20] 10.10.140.96 flow.microsoft.com:443 CONNECT - 200 0 "-"
[2023/10/25:15:46:20] 10.10.140.96 flow.microsoft.com:443 GET / 307 488 "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/118.0.0.0 Safari/537.36"
[2023/10/25:15:46:20] 10.10.140.96 make.powerautomate.com:443 CONNECT - 200 0 "-"
[2023/10/25:15:46:20] 10.10.140.96 make.powerautomate.com:443 GET / 200 3870 "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/118.0.0.0 Safari/537.36"
[2023/10/25:15:46:21] 10.10.140.96 o15.officeredir.microsoft.com:443 CONNECT - 200 0 "-"
```

The first command's output may have been a little too overwhelming since it provides every connection made by the specific IP. Meanwhile, appending a pipe with a head command limited the results to five.

Get the list of unique domains accessed by all workstations.

To do this, we'll combine the sort and uniq commands with the cut command.

Sort is a Linux command used to sort the lines of text files or input streams in ascending or descending order, while the uniq command allows you to filter out and display unique lines from a sorted file or input stream. 

Note: The uniq command requires a sorted list to be effective because it only compares the adjacent lines.

To achieve our goal, we will start by getting the domain column and removing the port. When we have the list of domains, we'll sort it and get the unique list using the sort and uniq commands.

```
# The first use of the cut command retrieves the column of the domain:port, and the second one removes the port by splitting it with a colon.

ubuntu@tryhackme:~/Desktop/artefacts$ cut -d ' ' -f3 access.log | cut -d ':' -f1
sway.com
sway.com
sway.office.com
--- REDACTED FOR BREVITY ---

# After retrieving the domains, the sort command arranges the list in alphabetical order

ubuntu@tryhackme:~/Desktop/artefacts$ cut -d ' ' -f3 access.log | cut -d ':' -f1 | sort
account.activedirectory.windowsazure.com
account.activedirectory.windowsazure.com
account.activedirectory.windowsazure.com
--- REDACTED FOR BREVITY ---

# Lastly, the uniq command removes all the duplicates

ubuntu@tryhackme:~/Desktop/artefacts$ cut -d ' ' -f3 access.log | cut -d ':' -f1 | sort | uniq
account.activedirectory.windowsazure.com
activity.windows.com
admin.microsoft.com
--- REDACTED FOR BREVITY ---
```

You can try to execute the commands one at a time to see their results before adding a piped command.

Display the connection count made on each domain.

We already have the list of unique domains based on our previous use case. Now, we only need to add some parameters to our commands to get the count of each domain accessed. This can be done by adding the -c option to the uniq command.

```
ubuntu@tryhackme:~/Desktop/artefacts$ cut -d ' ' -f3 access.log | cut -d ':' -f1 | sort | uniq -c
    423 account.activedirectory.windowsazure.com
    184 activity.windows.com
    680 admin.microsoft.com
    272 admin.onedrive.com
    304 adminwebservice.microsoftonline.com
```

Moreover, the result can be sorted again based on the count of each domain by using the -n option of the sort command.

```
ubuntu@tryhackme:~/Desktop/artefacts$ cut -d ' ' -f3 access.log | cut -d ':' -f1 | sort | uniq -c | sort -n
     78 partnerservices.getmicrosoftkey.com
    113 **REDACTED**
    118 ocsp.digicert.com
    123 officeclient.microsoft.com
--- REDACTED FOR BREVITY ---
```

Based on the result, you can see that the count of connections made for each domain is sorted in ascending order. If you want to make the output appear in descending order,  use the -r option. Note that it can also be combined with the -n option (-nr if written together).

```
ubuntu@tryhackme:~/Desktop/artefacts$ cut -d ' ' -f3 access.log | cut -d ':' -f1 | sort | uniq -c | sort -nr
   4992 www.office.com
   4695 login.microsoftonline.com
   1860 www.globalsign.com
   1581 **REDACTED**
   1554 learn.microsoft.com
--- REDACTED FOR BREVITY ---
```

You can play with all the above commands to test your capabilities in combining Linux commands using pipes.

**Hunting Down the Malicious Traffic**

Now that we have developed the skills needed to assist Forensic McBlue, let's get down to business!

To start hunting for suspicious traffic, let's try to list the top domains accessed by the users and see if the users accessed any unusual domains. You can do this by reusing the previous command to retrieve the connection count for each domain and | tail -n 10 to get the last 10 items.

```
ubuntu@tryhackme:~/Desktop/artefacts$ cut -d ' ' -f3 access.log | cut -d ':' -f1 | sort | uniq -c | sort -n | tail -n 10
    606 docs.microsoft.com
    622 smtp.office365.com
    680 admin.microsoft.com
    850 c.bing.com
    878 outlook.office365.com
   1554 learn.microsoft.com
   1581 **REDACTED***
   1860 www.globalsign.com
   4695 login.microsoftonline.com
   4992 www.office.com
```

Note: We used the command tail -n 10 since the list is sorted in ascending order, and because of this, the domains with a high connection count are positioned at the end of the list.

Check the list of domains and you'll see that Microsoft owns most of them. Out of the 10 domains we can see, one seems unusual. Let's use that domain with the grep and head commands to retrieve the first 10 connections made to it.

```
ubuntu@tryhackme:~/Desktop/artefacts$ grep **SUSPICIOUS DOMAIN** access.log | head -n 5 [2023/10/25:15:56:29] REDACTED_IP REDACTED_DOMAIN:80 GET /storage.php?goodies=aWQscmVjaXBpZW50LGdp 200 362 "Go-http-client/1.1"
[2023/10/25:15:56:29] REDACTED_IP REDACTED_DOMAIN:80 GET /storage.php?goodies=ZnQKZGRiZTlmMDI1OGE4 200 362 "Go-http-client/1.1"
[2023/10/25:15:56:29] REDACTED_IP REDACTED_DOMAIN:80 GET /storage.php?goodies=MDRjOGExNWNmNTI0ZTMy 200 362 "Go-http-client/1.1"
[2023/10/25:15:56:30] REDACTED_IP REDACTED_DOMAIN:80 GET /storage.php?goodies=ZTE3ODUsTm9haCxQbGF5 200 362 "Go-http-client/1.1"
[2023/10/25:15:56:30] REDACTED_IP REDACTED_DOMAIN:80 GET /storage.php?goodies=IENhc2ggUmVnaXN0ZXIK 200 362 "Go-http-client/1.1"
```

Upon checking the list of requests made to the **REDACTED** domain, we see something unusual with the string passed to the goodies parameter. Let's try to retrieve the data by cutting the request URI with equals (=) as its delimiter.

```
ubuntu@tryhackme:~/Desktop/artefacts$ grep **SUSPICIOUS DOMAIN** access.log | cut -d ' ' -f5 | cut -d '=' -f2
aWQscmVjaXBpZW50LGdp
ZnQKZGRiZTlmMDI1OGE4
MDRjOGExNWNmNTI0ZTMy
ZTE3ODUsTm9haCxQbGF5
--- REDACTED FOR BREVITY ---
```

Based on the format, the data sent seems to be encoded with Base64. Using this theory, we can try to decode the strings by piping the output to a base64 command.

```
ubuntu@tryhackme:~/Desktop/artefacts$ grep **SUSPICIOUS DOMAIN** access.log | cut -d ' ' -f5 | cut -d '=' -f2 | base64 -d
id,recipient,gift
ddbe9f0258a804c8a15cf524e32e1785,Noah,Play Cash Register
cb597d69d83f24c75b2a2d7298705ed7,William,Toy Pirate Hat
4824fb68fe63146aabc3587f8e12fb90,Charlotte,Play-Doh Bakery Set
f619a90e1fdedc23e515c7d6804a0811,Benjamin,Soccer Ball
ce6b67dee0f69a384076e74b922cd46b,Isabella,DIY Jewelry Kit
939481085d8ac019f79d5bd7307ab008,Lucas,Building Construction Blocks
f706a56dd55c1f2d1d24fbebf3990905,Amelia,Play-Doh Kitchen
2e43ccd9aa080cbc807f30938e244091,Ava,Toy Pirate Map
--- REDACTED FOR BREVITY --- 
```


Did you notice that the decoded data seems to be sensitive data for AntarctiCrafts? This might be a case of data exfiltration!

**Conclusion**

Congratulations! You have completed the investigation through log analysis and uncovered the stolen data. The next step for Forensic McBlue's team in this incident is to apply mitigation steps like blocking the malicious domain to prevent any further impact.

**Q/A**

![thm bid](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/eacc64f2-b921-4401-b95f-41de2a49873b)
