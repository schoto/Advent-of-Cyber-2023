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

