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

