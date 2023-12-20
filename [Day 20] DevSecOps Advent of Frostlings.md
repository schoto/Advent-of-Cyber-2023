One of the main reasons the Best Festival Company acquired AntarctiCrafts was their excellent automation for building, wrapping, and crafting. Their new automation pipelines make it a much easier, faster, scalable, and effective process. However, someone has tampered with the source control system, and something weird is happening! It's suspected that McGreedy has impersonated some accounts or teamed up with rogue Frostlings. Who knows what will happen if a malicious user gains access to the pipeline?

In this task, you will explore the concept of poisoned pipeline execution (PPE) in a GitLab CI/CD environment and learn how to protect against it. You will be tasked with identifying and mitigating a potential PPE attack.

A GitLab instance for AntarctiCrafts' CI/CD automates everything from sending signals and processing Best Festival Company services to building and updating software. However, someone has tampered with the configuration files, and the logs show unusual behaviour. Some suspect the Frostlings have bypassed and gained access to our build processes.

Learning Objectives

In today's task, you will:

- Learn about poisoned pipeline execution.
- Understand how to secure CI/CD pipelines.
- Get an introduction to secure software development lifecycles (SSDLC) & DevSecOps.
- Learn about CI/CD best practices.

GitLab and SDLC Concepts

GitLab is a platform that enables collaboration and automation throughout the software development lifecycle, which is the framework structure that describes the stages that code goes through, from design and development to deployment. GitLab is built around Git, a distributed version control system (VCS) where code is managed.

Here are the key components of GitLab:

- Version control system: A VCS is the environment where you manage and track changes made in the codebase. It makes it easier to collaborate with others and maintain the history and versioning of a project.

- CI/CD pipelines: Pipelines automate the building, testing, and deployment processes. Pipelines ensure the code is consistently integrated, tested, and delivered to the specified environment (production or staging).

- Security scanning: GitLab has a few scanning features, like incorporating static application security testing (SAST), dynamic application security testing (DAST), container scanning, and dependency scanning. All these tools help identify and mitigate security threats in code and infrastructure.

CI/CD
We mentioned CI/CD earlier in the context of pipelines. CI/CD stands for continuous integration and continuous delivery.

- Continuous integration: CI refers to integrating code changes from multiple contributors into a shared repository (where code is stored in a VCS; you can think of it as a folder structure). In GitLab, CI allows developers and engineers to commit code frequently, triggering automations that lead to builds and tests. This is what CI is all about: ensuring that code changes and updates are continuously validated, which reduces the likelihood of vulnerabilities when introducing security scans and tests as part of the validation process (here, we start entering the remit of DevSecOps).
- Continuous deployment: CD automates code deployment to different environments. During SDLC, code travels to environments like sandbox and staging, where the tests and validations are performed before they go into the production environment. The production environment is where the final version of an app or service lives, which is what we, as users, tend to see. CD pipelines ensure the code is securely deployed consistently and as part of DevSecOps. Integrating security checks before deployment to production is key.

DevSecOps

We have mentioned that integrating security into CI/CD ensures consistency and threat reduction when integrating it into the SDLC. This is what DevSecOps aims to achieve. Everything we have seen so far is part of a cultural and technical approach that aims to improve collaboration, automation, and CI/CD. It's what we call developer operations, or DevOps for short. DevSecOps was born from DevOps and is an extension specialising in security for DevOps practices.

CI/CD Attacks: PPE

In today's AoC, you will learn about poisoned pipeline execution. This type of attack involves compromising a component or stage in the SDLC. For this attack to work, it takes advantage of the trust boundaries established within the supply chain, which is extremely common in CI/CD, where automation is everywhere.

When an attacker has access to version control systems and can manipulate the build process by injecting malicious code into the pipeline, they don't need access to the build environment. This is where the "poisoned" pipelines come into play. It's crucial to have effective, secure gates and guardrails to prevent malicious code from getting far if there is an account compromise.

Scenario

'Tis the season of giving, but the Frostlings have invaded the AntarctiCrafts GitLab CI/CD pipeline. They have found a way to poison the pipeline, orchestrating the Advent Calendar build process for this holiday season. Your mission as a DevSecOps engineer is to uncover and mitigate this attack to ensure the calendar doesn't suffer from any malicious alterations.

After logging in, if you see a warning that specifies adding an SSH key, you can ignore it, as we will be using the web editor. If you have used Git and GitLab before, and you prefer to interact with GitLab programmatically, feel free to add your key!

Upon login, you should see the AoC DevSecOps / Advent-Calendar-BFC.

<img width="1000" alt="requently" src="https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/ece264ac-6a37-4c64-bf9c-d6c5ee84ab16">

![advent](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/76dab8c3-1257-4bc5-b9b4-60f40673e5e8)


Let's take a look at the project. It is a workflow for the Advent Calendar site by the Best Festival Company built by AntarctiCrafts. If we check the repository we see it uses Apache to host an index.html file. 

The configuration file gitlab-ci.yml is written in YAML format for GitLab CI/CD. It defines a series of jobs and stages that will be executed automatically when code changes are pushed to the Advent-Calendar-BFC Repository. Let's break down what it does:

![lets](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/281ee64c-41f1-4a75-b3a2-f3715136ec39)

- ```Workflow```: Describes a CI/CD workflow for the value assigned to the commit branch.
- ```Install_dependencies``` Stage: If the pipeline is triggered on any branch, it installs dependencies if there are any. In this case, it echoes a message indicating the installation step.
- ```Before_script``` Stage: Checks for an existing Docker container with a specific name, stops and removes it if found. This way, whenever a new job runs, there won't be clashes with previously running containers.
- ```Test``` Stage: 1) Executes in the "test" stage of the pipeline. 2) Runs a Docker container named "affectionate_saha" based on the httpd:latest image. 3) Binds a volume from the local directory to the container's web server directory. 4) Maps port 9080 on the host to port 80 on the container.
- ```Artifacts```: Specifies that the contents of the "public/" directory.
- ```Rules```: The "test" stage runs only if the pipeline is triggered on the "main" branch.

Investigation

Detective Frost-eau received reports that the Advent Calendar site has been stuck in a testing phase for a few weeks. However, the team is acting strangely, and the site has been acting up. As a DevSecOps engineer, you must help Detective Frost-eau understand what's happening.

We can start by checking if there are any Merge requests or attempts. Merge requests appear when someone has new code or an updated project version and wants to update the codebase with the new changes. In other words, they want to merge it.

Let's take a look at the merge requests! Click on the "Merge requests" tab on the left-hand dropdown. Changes can be seen on the "Merged tab"; look at the "Update .gitlab-ci.yml" changes.

<img width="119" alt="merge" src="https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/4bb7037f-4a5d-4695-a939-6efc539c9fcd">

There is some activity made for testing. It looks like Frostlino has opened a merge request with some code updates, explaining it is for testing purposes. Delf Lead approved and merged the changes. It seems no code review was done, and it was merged directly!

![frost](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/04cf3c44-995b-40a2-92f9-54c87e96194b)

What Is Going On Here?

Let's check the job logs. Job logs show all the workflows triggered and jobs that have run or are running. On the same menu on the left-hand side, select "Jobs" from the dropdown menu in CI/CD:

<img width="234" alt="jobs" src="https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/1fccb424-5b73-487d-9aaf-63ea52858e5f">
