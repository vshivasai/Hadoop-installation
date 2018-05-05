Welcome to the Hadoop-installation wiki!<br>
How to Install Hadoop-1.2.1 (Single-Node Cluster) on Ubuntu-12.04, with JDK 8 
In this DIY we will see how to set up a single-node Hadoop cluster backed by the Hadoop Distributed File System (HDFS), running on Ubuntu-12.04. The main goal of this tutorial is to get a simple Hadoop installation up and running so that you can play around with the software and learn more about it.(system with 4 gb of ram is highly recommended.)

This tutorial has been tested with the following software versions:<br>
•	Ubuntu -12.04 (LTS)<br>
•	Hadoop- 1.2.1, released Aug, 2013<br>
•	JDK- 8 update 5


### **STEP 1: Prerequisites**
***


### 1. Download Hadoop
Hadoop-1.2.1 can be downloaded from here(Apache hadoop site). Select a mirror, then select 'hadoop-1.2.1/' directory and download hadoop-1.2.1.tar.gz. I assume you have downloaded it into your '/home/user_name/Downloads' directory.                           Or download it in /usr/local as below(image 1). <br>
                                     ![](https://2.bp.blogspot.com/-fL-pZkTUlcE/Wkm3u04g3HI/AAAAAAAAFos/LAhs299ZYOwZTyAyNM-UyhkMk7hPN312wCLcBGAs/s1600/Capture%2B5.PNG)
### 2. Install JDK 8
Hadoop requires a working Java installation. So, open up a terminal and run the following<br>
1. ` 	sudo add-apt-repository ppa:webupd8team/java`<br>
2. ` 	sudo apt-get update && sudo apt-get install oracle-java8-installer`

It will take some time to download and install so sit back and wait. Once it's done then we have to add the JAVA_HOME to the Ubuntu environment. Run the following in a terminal to open up the /etc/environment file.<br>
1. ` 	sudo gedit /etc/environment`

Now, append the following at the end of the file and save it:<br>
  > JAVA_HOME="/usr/lib/jvm/java-8-oracle"

### 3. Adding a dedicated Hadoop system user
We will use a dedicated Hadoop user account for running Hadoop. While that’s not required it is recommended because it helps to separate the Hadoop installation from other software applications and user accounts running on the same machine. Following commands are used for our purpose, image 2 shows a typical output screen of the same.<br>
1. ` 	sudo addgroup hadoop`
2. ` 	sudo adduser --ingroup hadoop hduser`<br>
          ![](https://3.bp.blogspot.com/-D1Qb7BR1-ME/Wkm4XjfTrcI/AAAAAAAAFow/3uF0VGe9VLgueaEA9V2bRycC5MUSDNG8ACLcBGAs/s1600/Capture.PNG)<br>
                        Image 2. Adding a Hadoop System User.<br>
### 4.Giving Root Permissions to hduser<br>

By running the following command <br>
1. ` 	sudo visudo`<br>Add hduser below root .The output will look like this.<br>
                       ![](https://3.bp.blogspot.com/-C2DU5LYppGM/Wkm4zDTjJaI/AAAAAAAAFo4/uvalF9MK7044CFO873qo2J_ATbCYvcJVwCLcBGAs/s1600/capture%2B13.PNG)           
                                           
### 5. Installing and configuring SSH Server
Hadoop requires SSH access to manage its nodes, i.e. remote machines plus your local machine if you want to use Hadoop on it (which is what we want to do in this short tutorial). For our single-node setup of Hadoop, we therefore need to configure SSH access to localhost for the hduser user we created in the previous section. Install openssh server as:
 1. `	sudo apt-get install openssh-server`

Now, open a new terminal and switch to hduser then we have to generate an SSH key for the hduser user
 1. `su - hduser`
 2. `ssh-keygen -t rsa -P ""`

The second line will create an RSA key pair with an empty password. Generally, using an empty password is not recommended, but in this case it is needed to unlock the key without your interaction (you don’t want to enter the pass-phrase every time Hadoop interacts with its nodes). The output will look like this:<br>

 ![](https://2.bp.blogspot.com/-G3YetLqc6pQ/Wkm5D39k57I/AAAAAAAAFpA/m6aYnXMXpzkFKKeiBmJFofmxn4j8FYxwACLcBGAs/s1600/Capture%2B2.PNG)<br>
    Image 3. Creating an RSA Key Pair.

Now we have to enable SSH access to your local machine with this newly created key.
 1. `	cat $HOME/.ssh/id_rsa.pub >> $HOME/.ssh/authorized_keys`

The final step is to test the SSH setup by connecting to our local machine with the hduser user. The step is also needed to save our local machine’s host key fingerprint to the hduser user's known_hosts file.
1. `1 	ssh localhost`

The output will look like this:

![](https://2.bp.blogspot.com/-wBDTmTYfcYk/Wkm5ZDBL1FI/AAAAAAAAFpE/fJNyGe07JKcjcnHofKa0nSckDSD76zwRQCLcBGAs/s1600/Capture%2B3.PNG)<br>
 Image 4. Testing the ssh Setup.

NOTE: If you get an error saying “ssh: connect to host localhost port 22: Connection refused”, then you have not installed the ‘openssh-server’ properly, install it first.

### 6. Disabe IPv6
Close the current terminal by repeatedly typing "exit" until it doesn't get closed. Open a new terminal and open the file /etc/sysctl.conf 
1. ` 	sudo gedit /etc/sysctl.conf`

Now append the following lines at the end of the file and save it.
> net.ipv6.conf.all.disable_ipv6 = 1<br>
> net.ipv6.conf.default.disable_ipv6 = 1<br>
> net.ipv6.conf.lo.disable_ipv6 = 1<br>

You have to restart your system in order to make the changes take effect. You can check whether IPv6 is enabled on your machine with the following command:
1. ` 	cat /proc/sys/net/ipv6/conf/all/disable_ipv6`

A return value of 0 means IPv6 is enabled, a value of 1 means disabled (image 5, that’s what we want).


After restart

 ![](https://1.bp.blogspot.com/-tyzdy8x-x2w/Wkm56gNW-yI/AAAAAAAAFpM/dzPDViNIf4wPFgNmNVTikhI7eF5QnyGAACLcBGAs/s1600/Capture%2B4.PNG)<br>
Image 5. IPv6 Disabled.


