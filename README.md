# JENKINS ON EC2 : SETTING UP MASTER AND SLAVE NODES ON UBUNTU 18.04

At First to manage our Jenkins installation we need to create a sudo user.
## 1. Creating a Sudo User on Ubuntu
For creating a sudo user follow the steps in [How To Create a Sudo User on Ubuntu](https://linuxize.com/post/how-to-create-a-sudo-user-on-ubuntu/).

## 2. Installing Jenkins Master on Ubuntu 18.04
To install Jenkings on ubuntu 18.04 follow the steps in [Install Jenkins on Ubuntu](https://linuxize.com/post/how-to-install-jenkins-on-ubuntu-18-04/).

**Note**: _If the firewall is inactive, the following commands will allow OpenSSH and enable the firewall:_

```
sudo ufw allow OpenSSH
sudo ufw enable
```
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
