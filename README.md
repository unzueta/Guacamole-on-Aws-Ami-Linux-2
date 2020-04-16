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

Paste the following configuration

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
