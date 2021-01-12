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
 - Copy the "Public IPv4 address" of the Jenkins-Slave instance to create the New Node in Jenkins where Jenkins_Master configured.
