# GuacamoleOnAwsAmi2
How to setup Guacamole in Aws Ami Linux 2 Ec2

## Update your instance

`sudo yum update`

Reboot

`sudo reboot now`

Install git

`sudo yum install git`

## Install graphical interface
`sudo amazon-linux-extras install mate-desktop1.x`

Define MATE as the default desktop

`sudo bash -c 'echo PREFERRED=/usr/bin/mate-session > /etc/sysconfig/desktop'`

## Install TigerVNC
`sudo yum install tigervnc-server`

Configure a VNC-specific password containing 6 to 8 characters for this user. Do not configure a secondary view-only password in this step

`vncpasswd`

### Configure to always start the VNC Server at boot time:

Create a new systemd unit

`sudo cp /lib/systemd/system/vncserver@.service /etc/systemd/system/vncserver@.service`

Replace all occurrences of <USER> in the new unit with ec2-user

`sudo sed -i 's/<USER>/ec2-user/' /etc/systemd/system/vncserver@.service`

Reload the systemd manager configuration

`sudo systemctl daemon-reload`

Enable the service

`sudo systemctl enable vncserver@:1`

Start the service

`sudo systemctl start vncserver@:1`

Add a firewall rule to open 5901 port

`sudo iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 5901 -j ACCEPT`
## Install Guacamole

`sudo amazon-linux-extras install epel`

`sudo yum install epel-release`

`sudo yum install guacamole guacd`

`sudo yum install libguac-client-vnc libguac-client-ssh libguac-client-rdp`

## Install Apache Maven

`sudo wget http://ftp.meisei-u.ac.jp/mirror/apache/dist/maven/maven-3/3.6.3/binaries/apache-maven-3.6.3-bin.tar.gz`

`sudo tar -xzvf apache-maven-3.6.3-bin.tar.gz -C /opt`

`cd /opt/`

$ sudo ln -s /opt/apache-maven-3.6.3 maven

`sudo nano /etc/profile.d/maven.sh`


4. Verify the installation
Conclusion
SHARE:


Apache Maven is a free and open-source project management and comprehension tool used primarily for Java projects. Maven uses a Project Object Model (POM), which is essentially an XML file containing information about the project, configuration details, the project’s dependencies, and so on.

In this tutorial, we will show you two different ways to install Apache Maven on Ubuntu 18.04. The same instructions apply for Ubuntu 16.04 and any Ubuntu-based distribution, including Linux Mint, Kubuntu, and Elementary OS.

The official Ubuntu repositories contain Maven packages that can be installed with the apt package manager. This is the easiest way to install Maven on Ubuntu. However the version included in the repositories may lag behind the latest version of Maven.

To install the latest version of Maven follow the instructions provided in the second part of this article where we will be downloading Maven from their official website.

Choose the installation method that is most appropriate for your environment.
Prerequisites
In order to be able to install packages on your Ubuntu system, you must be logged in as a user with sudo privileges.

Installing Apache Maven on Ubuntu with Apt
Installing Maven on Ubuntu using apt is a simple, straightforward process.
Start by updating the package index:

sudo apt update
Next, install Maven by typing the following command:

sudo apt install maven
Verify the installation by running the mvn -version command:

mvn -version
The output should look something like this:

Apache Maven 3.5.2
Maven home: /usr/share/maven
Java version: 10.0.2, vendor: Oracle Corporation
Java home: /usr/lib/jvm/java-11-openjdk-amd64
Default locale: en_US, platform encoding: ISO-8859-1
OS name: "linux", version: "4.15.0-36-generic", arch: "amd64", family: "unix"
That’s it. Maven is now installed on your system, and you can start using it.

Install the Latest Release of Apache Maven
The following sections provide a step by step instructions about how to install the latest Apache Maven version on Ubuntu 18.04. We’ll be downloading the latest release of Apache Maven from their official website.

1. Install OpenJDK
Maven 3.3+ requires JDK 1.7 or above to be installed. We’ll install OpenJDK, which is the default Java development and runtime in Ubuntu 18.04.

The installation of Java is pretty simple. Start by updating the package index:

sudo apt update
Install the OpenJDK package by typing:
sudo apt install default-jdk
Verify the installation by running the following command:

java -version
The output should look something like this:

openjdk version "10.0.2" 2018-07-17
OpenJDK Runtime Environment (build 10.0.2+13-Ubuntu-1ubuntu0.18.04.2)
OpenJDK 64-Bit Server VM (build 10.0.2+13-Ubuntu-1ubuntu0.18.04.2, mixed mode)
2. Download Apache Maven
At the time of writing this article, the latest version of Apache Maven is 3.6.0. Before continuing with the next step, check the Maven download page to see if a newer version is available.

Start by downloading the Apache Maven in the /tmp directory using the following wget command:
wget https://www-us.apache.org/dist/maven/maven-3/3.6.0/binaries/apache-maven-3.6.0-bin.tar.gz -P /tmp
Once the download is completed, extract the archive in the /opt directory:

sudo tar xf /tmp/apache-maven-*.tar.gz -C /opt
To have more control over Maven versions and updates, we will create a symbolic link maven that will point to the Maven installation directory:

sudo ln -s /opt/apache-maven-3.6.0 /opt/maven
Later if you want to upgrade your Maven installation you can simply unpack the newer version and change the symlink to point to the latest version.

3. Setup environment variables
Next, we’ll need to set up the environment variables. To do so, open your text editor and create a new file named mavenenv.sh inside of the /etc/profile.d/ directory.

sudo nano /etc/profile.d/maven.sh

Paste the following configuration:

`export JAVA_HOME=/usr/lib/jvm/java`

`export M2_HOME=/opt/maven`

`export MAVEN_HOME=/opt/maven`

`export PATH=${M2_HOME}/bin:${PATH}`

Save and close the file. This script will be sourced at shell startup.

Make the script executable with chmod

`sudo chmod +x /etc/profile.d/maven.sh`

Load the environment variables

`source /etc/profile.d/maven.sh`

## Install Tomcat

`sudo amazon-linux-extras install tomcat8.5`

Start Tomcat

`sudo systemctl start tomcat`

## Install Guacamole Client

`sudo su -`

`ldconfig`

wget https://www.apache.org/dist/guacamole/1.0.0/source/guacamole-client-1.0.0.tar.gz

tar -xzf guacamole-client-1.0.0.tar.gz

cd guacamole-client-1.0.0/

git clone git://github.com/apache/guacamole-client.git

`mvn package`

`cp guacamole.war /var/lib/tomcat/webapps`

cp guacamole/target/guacamole-1.0.0.war /var/lib/tomcat/webapps/guacamole.war
