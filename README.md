# JENKINS ON EC2 : SETTING UP MASTER AND SLAVE NODES ON UBUNTU 18.04

## ***To setting up Jenkins Master/Slave configuration we need at least two AWS EC2 instances with Ubuntu OS installed***.

At First to manage our Jenkins installation we need to create a sudo user.
## 1. Creating a Sudo User on Ubuntu for Jenkins
Log in to your system as the root user. Then run the following commands(Preferred username is **jenkins**. If you want any other username please modify the commands accordingly)

_Command:_
```
sudo adduser jenkins
```
_Output:_
```
Adding user `jenkins' ...
Adding new group `jenkins' (1001) ...
Adding new user `jenkins' (1001) with group `jenkins' ...
Creating home directory `/home/jenkins' ...
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
Is the information correct? [Y/n] Y
```
Add the new user to the sudo group

_Command:_
```
sudo usermod -aG sudo jenkins
```
Test the sudo access
Switch to the newly created user:
```
su - jenkins
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
[sudo] password for jenkins:
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
**Note**: _Also add and open HTTP port at 8080 in EC2 Security group._

_If the firewall is inactive, the following commands will allow OpenSSH and enable the firewall:_
```
sudo ufw allow OpenSSH
sudo ufw enable
```

When you first access a new Jenkins instance, you are asked to unlock it using an automatically-generated password.

Browse to http://localhost:8080 (or whichever port you configured for Jenkins when installing it) and wait until the Unlock Jenkins page appears.

**Unlock Jenkins**
To ensure Jenkins is securely set up by the administrator, a password has been written to the log (not sure where to find it?) and this file on the server:

_/var/lib/jenkins/secrets/initialAdminPassword_

The command: ```sudo cat /var/lib/jenkins/secrets/initialAdminPassword``` will print the password at console.
Please copy the password from either location and paste it.

From the Jenkins console log output, copy the automatically-generated alphanumeric password.

**Customizing Jenkins with plugins**

After unlocking Jenkins, the Customize Jenkins page appears. Here you can install any number of useful plugins as part of your initial setup.

Click one of the two options shown:

_Install suggested plugins_ - to install the recommended set of plugins, which are based on most common use cases.

_Select plugins to install_ - to choose which set of plugins to initially install. When you first access the plugin selection page, the suggested plugins are selected by default.

If you are not sure what plugins you need, choose Install suggested plugins. You can install (or remove) additional Jenkins plugins at a later point in time via the Manage Jenkins > Manage Plugins page in Jenkins

**Creating the first administrator user**

Finally, after customizing Jenkins with plugins, Jenkins asks you to create your first administrator user.

When the Create First Admin User page appears, specify the details for your administrator user in the respective fields and click Save and Finish.

When the Jenkins is ready page appears, click Start using Jenkins.
Notes:

This page may indicate Jenkins is almost ready! instead and if so, click Restart.

If the page does not automatically refresh after a minute, use your web browser to refresh the page manually.

If required, log in to Jenkins with the credentials of the user you just created and you are ready to start using Jenkins!

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

For testing the Jenkins Master/Slave configuration we need to create two or three Jenkins jobs and build it concurrently. then the jobs will be distributed between Master and Slave.

1. Go to Jenkins Dashboard and create two new jobs.

**Dashboard** >> **New Item** >> Give **Item name** (Example: Job1) >> Choose **FreeStyle project** >> Press **OK** 
Give Description >> 

We're going to run a very simple job: sleep 30 seconds:
under **build** section add shell script ***sleep 30***, then save the job.

Create another one for job **Job2** doing the same thing: sleep 30s.

Then run build for **Job1** and **Job2**.
Then we ca see that one job will run in the slave#1 and other will be in the slave#2.
