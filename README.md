### Steps of privilege escalation

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

Services running and user-context:

`Ps aux`

Versions of services: 

`Ps -ef`

Applications that are installed:

`ls -alh /usr/bin/`
