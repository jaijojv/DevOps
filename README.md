# JENKINS ON EC2 : SETTING UP MASTER AND SLAVE NODES ON UBUNTU 18.04

## ***To setting up Jenkins Master/Slave configuration we need at least two AWS EC2 instances with Ubuntu OS installed***.

At First to manage our Jenkins installation we need to create a sudo user.
## 1. Creating a Sudo User on Ubuntu
Log in to your system as the root user. Then run the following commands.

_Command:_
```
adduser username
```
_Output:_
```
Adding user `username' ...
Adding new group `username' (1001) ...
Adding new user `username' (1001) with group `username' ...
Creating home directory `/home/username' ...
Copying files from `/etc/skel' ...
New password:
Retype new password:
passwd: password updated successfully
Changing the user information for username
Enter the new value, or press ENTER for the default
    Full Name []:
    Room Number []:
    Work Phone []:
    Home Phone []:
    Other []:
Is the information correct? [Y/n]
```
Add the new user to the sudo group

_Command:_
```
usermod -aG sudo username
```
Test the sudo access
Switch to the newly created user:
```
su - username
```
Use sudo to run the ```whoami```.

_Command:_
```
sudo whoami
```
_Output:_
```
root
```
To use sudo, simply prefix the command with ```sudo``` and space:
```
sudo ls -l /root
```
The first time you use sudo in a session, you will be prompted to enter the user password:

_Output:_
```
[sudo] password for username:
```
For more details: [How To Create a Sudo User on Ubuntu](https://linuxize.com/post/how-to-create-a-sudo-user-on-ubuntu/).

## 2. Installing Jenkins Master on Ubuntu 18.04
**Prerequisites:**

Before starting installation, make sure you are logged in as a user with ```sudo``` privileges.

To install Jenkins on your Ubuntu system, follow these steps:

**1. Install Java.**

First, update the apt package index with:
```
sudo apt update
```
The current version of Jenkins does not support Java 10 (and Java 11) yet. If you have multiple versions of Java installed on your machine make sure Java 8 is the default Java version.
```
sudo apt install openjdk-8-jdk
```
Verify the installation, by running the following command which will print the Java version
```
java -version
```
For more details: [How to Install Java on Ubuntu 18.04](https://linuxize.com/post/install-java-on-ubuntu-18-04/)

**2. Add the Jenkins Debian repository.**

Import the GPG keys of the Jenkins repository using the following ```wget``` command:
```
wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | sudo apt-key add -
```
The command above should output ```OK``` which means that the key has been successfully imported and packages from this repository will be considered trusted.

Next, add the Jenkins repository to the system with:
```
sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
```
**3. Install Jenkins.**

Once the Jenkins repository is enabled, update the apt package list and install the latest version of Jenkins by typing:
```
sudo apt update
sudo apt install jenkins
```
Jenkins service will automatically start after the installation process is complete. You can verify it by printing the service status:
```
systemctl status jenkins
```
You should see something similar to this:

_Output:_
```
jenkins.service - LSB: Start Jenkins at boot time
Loaded: loaded (/etc/init.d/jenkins; generated)
Active: active (exited) since Wed 2018-08-22 13:03:08 PDT; 2min 16s ago
    Docs: man:systemd-sysv-generator(8)
    Tasks: 0 (limit: 2319)
CGroup: /system.slice/jenkins.service
```
**4. Adjusting Firewall.**
If you are installing Jenkins on a remote Ubuntu server that is protected by a firewall you’ll need to open port ```8080```. Assuming you are using ```UFW``` to manage your firewall, you can open the port with the following command:
```
sudo ufw allow 8080
```
Verify the change with:
```
sudo ufw status
```
_Output:_
```
Status: active

To                         Action      From
--                         ------      ----
OpenSSH                    ALLOW       Anywhere
8080                       ALLOW       Anywhere
OpenSSH (v6)               ALLOW       Anywhere (v6)
8080 (v6)                  ALLOW       Anywhere (v6)
```
**Note**: _If the firewall is inactive, the following commands will allow OpenSSH and enable the firewall:_
```
sudo ufw allow OpenSSH
sudo ufw enable
```
For more details: [How to install Jenkins on Ubuntu](https://linuxize.com/post/how-to-install-jenkins-on-ubuntu-18-04/).
## 3. Creating Slave node
 - Create one more EC2 instance as Jenkins-Slave on Ubuntu 18.04 with the same key-pair used for creating Jenkins-Master
 - Install Java on the Server with the same version of the Jenkins-Master [how to install java on ubuntu 18.04](https://linuxize.com/post/install-java-on-ubuntu-18-04/)
 - Create a directory in the server as workspace for slave(Remote directory)
```
     ubuntu@ip-172-31-13-34:~$ mkdir jenkins
     ubuntu@ip-172-31-13-34:~$ cd jenkins
     ubuntu@ip-172-31-13-34:~/jenkins$ pwd
     /home/ubuntu/jenkins

```
 - Copy the "**Public IPv4 address**"(Example: 18.XXX.159.48) of the Jenkins-Slave instance to create the New Node in Jenkins where Jenkins_Master configured.
 - Go to **Jenkins Dashboard**

   1. Click on **Manage Jenkins** >> **Choose Manage Nodes and Clouds**
   2. Click on "**New Node**" to create a slave node
   3. Give name to new slave node(Example: Jenkins-slave-1)
   4. Select "**Permanent Agent**" option
   5. press "**OK**"
- Enter the configuration details for slave node
  1. Name(**Jenkins-slave-1**)
  2. Description(**Jenkins-slave-1**)
 	3. No of executors(**1 or more**)
 	4. Remote root directory(**/home/ubuntu/jenkins**)
     - Remote directory is mandatory
 	5. Labels(**Jenkins-slave-1**)
 	6. Usage	(**Use this node as much as possible**)
 	7. Launch method (**Launch agent via SSH**)
     _- Then do the following_
       - Host	(Public IPv4 address of slave EC2 instance copied before : 18.XXX.159.48). The Host must be specified.
       - Credentials	
         - For adding Credentials Click on **Add** dropdown >> Choose **Jenkins**
         - Then a popup window will appear (**Jenkins Credentials Provider: Jenkins**)
           - **Add Credentials**
              - Domain: **Global credentials (unrestricted)**
              - Kind: **SSH Username with private key**
              - Scope: **Global (Jenkins, nodes, items, all child items, etc)**
              - ID: **Jenkins-slave-1**
              - Description: **Jenkins-slave-1**
              - Username: **ubuntu**
              - Private Key: Select **Enter directly** option
              - Click on **Add** button and enter the SSHPrivate Key(Example : This is the content of **.pem** file)
               ```
              -----BEGIN RSA PRIVATE KEY-----
              MIIEpAIBAAKCAQEAxwpW8iFkMtCIPKNcdVY2DjdPhSf9/EzLgAhhhFZRlij2Vo2X 
              lBV5hTYOOtvRJ8nzJWvDv+bNAeI3HnkU0ve747LFKpX6PizdCFZZ+/b1JmorNDzy
              BNAZkGcIj5ZvttJqVz2gJZzFKD6gymYT0M1Wp9jesBN1dSiwchC+yccbrNIttORn
              3q1yzBcprxLXJDD9mfuiWG1Me75d+I96oY0YYrPV3kyUxwNYrCUaJRCrUcfSjcZb
              -----END RSA PRIVATE KEY-----
              ```
              - Then click on **Add** button
             - Then click on the dropdown showing **-none-** left side of the **Add** button
             - Choose the **ubuntu(Jenkins-slave-1)** option
       - Host Key Verification Strategy: Choose "**Manually trusted key Verification Strategy**".

  8.  	Availability: **Keep this agent online as much as possible**.
  
Then go back to **Dashboard >> Nodes** list then you can see a **X** mark on the slave icon and check the log for "**Agent successfully connected and online**", then go back to the list again - the **X** mark will be removed and the Slave will "**In sync**" with Jenkins-Master.

Do the same to create Slave Node #2. This time we'll use "Copy Existing Node":

## Testing the Jenkins Master/Slave configuration

For testing the Jenkins Master/Slave configuration we need to create two or three Jenkin jobs and build it concurrently. then the jobs will be distributed between Master and Slave.

1. Go to Jenkins Dashboard and create two new jobs.

We're going to run a very simple job: sleep 30 seconds:

Create another one for job 2 doing the same thing: sleep 30s.
