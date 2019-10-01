# LINUX PRIVILEGE ESCALATION

## Table Of Contents

 - [Steps of privilege escalation](#steps-of-privilege-escalation)
  - [Enumeration of Operating System](#1-enumeration-of-operating-system)
  - [Enumeration of Services and Application](#2-enumeration-of-services-and-application)
  - [Enumeration of File Systems](#3-enumeration-of-file-systems)
  - [Confidential Information](#4-confidential-information)
  - [Preparation and finding Exploit Code](#5-preparation-and-finding-exploit-code)
 - [Reasons for Privilege Escalation](#reasons-for-privilege-escalation)
 - [Techniques for Privilege Escalation](#techniques-for-privilege-escalation)
  - [Kernel Exploits](#1-kernel-exploits)
  - [Exploiting Services that run as root](#2-exploiting-services-that-run-as-root)
  - [Exploiting SUID Executables](#3-exploiting-suid-executables)
  - [Exploiting sudo Rights / User](#4exploiting-sudo-rightsuser)
  - [Exploiting badly configured Cron Jobs](#5exploiting-badly-configured-cron-jobs)
  - [Exploiting Information](#6exploiting-users-with--in-their-path)
 - [Unix Privesc Check](#unix-privesc-check)
 - [Questions](#questions)

### Steps of Privilege Escalation

1. Enumeration
2. Sorting through data, analysis and prioritisation.
3. Knowing where to find exploit code.
4. Customisation and compilation skills.
5. Trial and error.

### 1. Enumeration of Operating System

The main objective is to identify the distribution type and the kernel version of the Operating System.

#### Distribution type command is:

 `cat /etc/issue`
 
#### Kernel version: 

 `cat / proc/version`
 
 
### 2. Enumeration of services and application

The main objective is to identify the services and the applications running with their respective users.

 - Services running and user-context:

`Ps aux`

 - Versions of services: 

`Ps -ef`

 - Applications that are installed:

`ls -alh /usr/bin/`

### 3. Enumeration of file systems

Identify where crucial files have been written:

 - Files read/written in /etc/:

`ls -aRl /etc/`

 - Content found in /var/:

`ls -alh /var/`

### 4. Confidential Information

The aim is to identify confidential information like the users and the passwords:

### 5. Preparation and finding exploit code

The aim is to identify languages supported and how files are uploaded:

 - Python language support:
 
 `find / name python*`

 - Means of uploading files:
 
 `find /name wget`
 
 ### Reasons for Privilege Escalation
 
 But first, a look into why one would use it:
 
  - Read or Write any sensitive file.
  - Persist easily between reboots.
  - Insert a permanent backdoor.
 
 ## TECHNIQUES FOR PRIVILEGE ESCALATION

With many organisations basing their information system structure around Linux Operating Systems, and with the increasing integration of IoT devices and embedded systems, not only are there many potential targets for malicious attacks and hacks, but critical infrastructure components for example the web server, databases and firewalls, are at a great risk as well.
As such, the following lists techniques used by attackers to elevate their privileges on a remote host, and how to secure against such.

### 1. Kernel Exploits

Programs that are designed to gain advantage from kernel vulnerabilities, and as a result cause unintended behaviours i.e, execute arbitrary code with elevated permissions. If successful, attackers will have super user access to systems as a root command prompt. How does it come about?

1. Exploit tricks kernel into running payload in kernel mode.
2. Manipulates kernel data, e.g. process privileges.
3. Launches a shell with new privileges.

#### Conditions for Kernel Exploit

 - Vulnerable Kernel.
 - A matching exploit.
 - Ability to transfer the exploit onto target system.
 - Ability to execute the exploit on target system.

All four must be met for kernel exploit to be successful.

#### Protection against Kernel Exploit

Best way is to update and patch the vulnerable kernel. Otherwise, it falls on the administrator to prevent the introduction and/or execution of the exploit onto the Linux file system. This can be done so by restricting or removing programs that allow file transfer like FTP, wget or curl, or limit them to specific users.

Example of such exploits are like the **Dirtycow** exploit.

### 2. Exploiting Services that run as Root

Examples like the EternalBlue and SambaCry exploits, exploited the smb service which runs as root, and by doing so attacker gained remote code execution and local privilege escalation. This can lead to easily spreading malware, as the above two were used to spread ransomware.

Check if web, mail and database servers are running as root, or other services that run locally but not exposed publicly. Both situations can be exploited.

Even a command injection vulnerability on a web application can lead an attacker to root shell.

#### Solution

Avoid running any service as root unless important or really required, especially the web, database and file servers.

### 3. Exploiting SUID Executables

Set user ID (SUID) allows users to execute a file with the permissions of a specific user such as the ping command that requires root permissions. By marking the ping program as SUID with owner as root, it executes with root privileges anytime with a low privilege user.

However, administrators may unknowingly introduce dangerous SUID configurations with installing third party applications or make logical configuration changes.

#### Solutions for SUID exploit

 - SUID bit should not be set to any program which lets you escape to the shell.
 
 - Never set SUID bit on any file editor/compiler/interpreter as an attacker can easily read or overwrite any files present on the system.

### 4.	Exploiting ‘sudo’ Rights / User 

Attackers may aim at compromising users with sudo access, should they lack direct access to root privileges. By gaining access to sudo users, he gains root privileges as a result. As the administrator allows users to run a few commands through ‘sudo’ rights, they are unbeknownst to them, introducing vulnerabilities leading to privilege escalation.

For Example: assigning sudo rights to the ‘find’ command, allowing users to search for particular files in the system. However, the ‘find’ command contains parameters for command execution, and an attacker can execute commands with root privileges.

#### Solutions to the ‘sudo’ vulnerability

 - No ‘sudo’ rights given to any program allowing access to the shell.
 
 - Disregard giving ‘sudo’ rights to vi, perl, more, less, nmap, python, gdp, ruby etc.

### 5.	Exploiting Badly Configured Cron Jobs

Cron jobs are defined as time-based daemon scheduler that tasks and executes commands at specific dates and times. They are generally run with root privileges. Provided that one can tamper with any scripts or binary which are defined in the cron jobs, then one can execute arbitrary code with root privilege.

#### Solutions

 - Scripts and binaries that are defined in cron jobs, should not be writable.
 
 - The cron file should not be writable by anyone except root.
 
 - The cron.d directory should not be writable by anyone except root.
 
### 6.	Exploiting Users with `.` in their path

This means that the user can execute binaries/scripts from the current directory. To avoid having to input two extra characters every time, one adds ‘.’ to their PATH. By doing so, Linux first searches for the program in the current directory when ‘.’ is added in the PATH at the beginning and then searches anywhere else.

#### Solution

Do not include the **'.'** symbol in your PATH.

## Unix-privesc-check

A script that attempts to find misconfigurations that could potentially allow local unprivileged users to elevate their privileges.

It can be run by either normal user or root with the difference being that the root user can read many files.

NB: It is intended to be a shortcut thus cannot qualify as a replacement for proper audit.

Usage By:  	
 - Security auditors 
 - Penetration testers
 - System administrators

#### Sample vulnerabilities checked ..

 - Writable home directories
 - Readable /etc/shadow-read password hashes and crack them.
 - Weak permissions on Cron jobs
 - Writable configuration files
 - Writable device files
 - Readable files in home directories
 - Running processes correspond to writable programs
 - Accounts with no passwords
 - Sudo configuration

#### Limitations

 - However, it also has its limitations some of which are mentioned below since some file permissions are hard to script. Therefore a manual audit may be required to counter this.

 - It does not check information on open files.
 
 - It does not check permissions of shared objects files for running processes.
  
 - It does not check arguments of currently running processes.
 
 - It does not check the PATHS for users given that insecure programs may be found within those directories.
 
 - It does not poll “ps” to identify processes that don’t run very often and in the event one is run, it has secure file permissions.

#### How to Use unix-privesc-checker

First, download link.

http://pentestmonkey.net/tools/unix-privesc-check/unix-privesc-check-1.4.tar.gz

After download, unzip and navigate to the script path.

Run ./unix-privesc-check standard. Detailed can also be used to gather more info.

The output is dense, thus the best case scenario is to look for the keyword **WARNING** which if found represents the vulnerabilities that were discovered by the script. In the event, no **WARNING** keyword is traced, then there were no vulnerabilities/misconfigurations that were found.

## Questions

1. Why do we need privilege escalation?

2. Enlist 5 ways  on how to perform privilege escalation?

3. Discuss real world example regarding linux privilege escalation?

4. How do you  defend against privilege escalation ?
