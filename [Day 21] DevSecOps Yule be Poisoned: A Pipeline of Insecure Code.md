One of the main reasons for acquiring AntarctiCrafts was for their crafty automation in gift-giving, wrapping, and crafting. After securing their automation, they discovered other parts of their CI/CD environment that are used to build and extend their pipeline. An attacker can abuse these build systems to indirectly poison the previously secured pipeline.

Learning Objectives

- Understand how a larger CI/CD environment operates.
- Explore indirect poisoned pipeline execution (PPE) and how it can be used to exploit Git.
- Apply CI/CD exploitation knowledge to the larger CI/CD environment.

Deploy the target VM attached to this task by pressing the green Start Machine button. After obtaining the machine's generated IP address, you can either use our AttackBox or your own VM connected to TryHackMe's VPN. We recommend using the AttackBox for this task. To do so, simply click on the Start AttackBox button located at the top-right of the page.

CI/CD Environment

Often, developers or other end-users only see a limited portion of the CI/CD pipeline. Developers interact with Git on a daily basis, so it makes sense that CI/CD is most commonly associated with Git – although it only makes up a quarter of a typical CI/CD pipeline. The diagram below visualises the general segments of a pipeline: development, build, testing, and deployment. While these segments could be expanded and interchanged, all pipelines will follow a similar order.

![;l;;](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/e2c61e8d-d464-415f-b72e-e6c465a4243a)

In the previous task, we looked at a CI/CD environment that was self-contained in Git. In a more formal environment, segments of the pipeline may be separated out onto different platforms. Below is the CI/CD environment we'll be exploring in this room. You will notice the addition of Jenkins, a build platform and automation server. In the next section, we will explore Jenkins and discuss how these components interact and contribute to the pipeline.

![jen](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/66c77bc4-0266-4065-94fd-3e0aca7f3a56)

Automation Platforms


Jenkins, along with many other applications, handles a pipeline's build segment. These platforms can be remote or local. For example, Travis CI is a remote build platform, whereas Jenkins is a local automation server.

These platforms rely on runners or agents to build a project on a pre-configured VM. One advantage of some automation platforms is that they can automatically create and configure build environments on demand. This allows building and testing in different environments without manual configuration or administration.

Indirect Poisoned Pipeline Execution

Let's briefly shift our focus back to the development stage. In the previous task, poisoned pipeline execution was introduced, wherein an attacker has direct write access to a repository pipeline. If an attacker doesn't have direct write access (to a main-protected or branch-protected repository, for example), it's possible they have write access to other repositories that could indirectly modify the behaviour of the pipeline execution. 

If an environment is employing a development pipeline, a configuration file must be defined for the steps the build system must take. If a repository contains all the necessary source and build files, and another repository contains the pipeline files, write permissions could differ between the two, resulting in an indirect PPE vulnerability. In this example, you can assume that the repository containing the source is not write-protected and the repository containing the pipeline is write-protected. 

To exploit this vulnerability, an attacker would need to identify a file or other parameter they can arbitrarily change that the pipeline file will use. Makefiles and other build files are usually exploitable because they are used to build the source and can run any arbitrary commands as defined in the makefile. Below is an example of what this might look like in a pipeline file.

```
stage('make') {
	steps {
		build() {
				sh 'make || true'
		}
	}
}
```

To weaponise this vulnerability or PPE in general, the CI/CD environment as a whole must be taken into consideration. For example, if a build server is used to build artefacts on a pre-configuration virtual machine, an attacker could run arbitrary commands in that environment.

Practical Challenge


Now, let's apply what we have learned in this task to the AntarctiCrafts CI/CD pipeline.

Navigate to http://10.10.10.63:3000, the Gitea platform AntarctiCrafts uses for version control and development. Log in using the credentials guest:password123. When you have logged in successfully, you should see two repositories: gift-wrapper and gift-wrapper-pipeline. Navigate to http://10.10.10.63:8080, the Jenkins platform AntarctiCrafts uses for building and automation. Log in using the credentials admin:admin. Once you have logged in successfully, you should see a project: gift-wrapper-build.


Before looking at the environment's other components, let's dig deeper into the Git repositories.

![repo](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/c6398271-0cfc-4ba0-ba80-e5f46421b4e3)

Looking at the gift-wrapper-pipeline repository, you may notice a Jenkinsfile. If a repository is unprotected, an attacker can modify a pipeline file to execute commands on the build system. For example, an attacker could control the Build stage by modifying make || true to whoami. This is possible because the Jenkinsfile allows you to run shell commands as you can see on line 13. This is an example of PPE as covered by the previous task.

![prepare](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/2ac95155-91be-4a4f-9959-3350d324468d)

To modify the Jenkinsfile, we will use the power of Git. To begin working with a repository, a local copy must be created or "cloned" from the remote repository – in this example, Gitea. Run the command below to clone the gift-wrapper-pipeline repository locally.

git clone http://10.10.10.63:3000/McHoneyBell/gift-wrapper-pipeline.git
Once cloned, we can make any changes we wish, then "commit" the changes. To start, we can exploit PPE by changing line 13 of the Jenkinsfile from sh 'make || true' to sh 'whoami'. When a commit is created, a snapshot of the current state of the project is saved to the local repository. To add our changes to the remote repository, we must "push" our commits. After modifying the Jenkinsfile, run the commands below to add, commit, and push your changes.

- git add .
- git commit -m "<message here>"
- git push

When attempting to push changes to the repository, you'll notice that it's main-protected. You can also try creating a new branch, but you'll notice the repository is branch-protected, too. This means we must find another way to indirectly modify the pipeline.

```
root@ip-10-10-195-97:~/gift-wrapper-pipeline# git push
Username for '': guest
Password for '': 
Counting objects: 3, done.
Delta compression using up to 2 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 306 bytes | 306.00 KiB/s, done.
Total 3 (delta 1), reused 0 (delta 0)
remote: 
remote: Gitea: User permission denied for writing.
To 
 ! [remote rejected] main -> main (pre-receive hook declined)
error: failed to push some refs to ''
```

Looking at how the Jenkinsfile works, you may notice that it uses make. If you recall from the previous section, a makefile can be used to define a set of rules to execute steps, such as commands. The makefile is defined in the gift-wrapper repository, meaning it could have different protections than the pipeline repository, and an attacker could add malicious commands to it.
After also cloning and attempting to push changes to the gift-wrapper repository, we see that our commit is successful. Depending on the configuration of the build system, different actions may initiate a new build. In this example we have access to Jenkins, so a build can be manually scheduled by pressing the green "play" button.

![wrapper](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/b4f7b249-7364-4dfd-8d0b-337cd97f2db5)

We can check the status and output of the build from Jenkins by navigating to http://MACHINE_IP:8080 within the project "gift-wrapper-build" under the gift-wrapper-pipeline repository under the main branch name. If successfully executed, the command we poisoned should appear in the make stage logs.

![console](https://github.com/schoto/Advent-of-Cyber-2023/assets/69323411/300390ca-d25b-41e2-bad1-de730b061a4c)

**Q/A**

