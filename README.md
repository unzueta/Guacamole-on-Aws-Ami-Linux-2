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
## Install Guacamole Server

`sudo yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm`

`sudo yum-config-manager --enable epel`

`sudo yum install guacamole guacd`

`sudo yum install libguac-client-vnc libguac-client-ssh libguac-client-rdp`

## Install Apache Maven (Optional)

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

# Download Guacamole client

`wget https://downloads.apache.org/guacamole/1.0.0/binary/guacamole-1.0.0.war`

`sudo mv guacamole-1.0.0.war /var/lib/tomcat/webapps/guacamole.war`

## Install Tomcat

`sudo amazon-linux-extras install tomcat8.5`

Start Tomcat

`sudo systemctl start tomcat`

## Configure Guacamole

`sudo mkdir /etc/guacamole`

#Create the file guacamole.properties

`sudo nano /etc/guacamole/guacamole.properties`

Paste the bollowing lines

```# Hostname and port of guacamole proxy
guacd-hostname:      localhost
guacd-port:          4822
available-languages: en, de
auth-provider: net.sourceforge.guacamole.net.basic.BasicFileAuthenticationProvider
basic-user-mapping: /etc/guacamole/user-mapping.xml
```

#Create the file user-mapping.xml

`sudo nano /etc/guacamole/usermapping.xml`

  <authorize username="USERNAME" password="PASSWORD">
    <!--
    <connection name="Debian2: RDP Connection">
      <protocol>rdp</protocol>
      <param name="hostname">localhost</param>
      <param name="port">3389</param>
    </connection>
    -->
 
    <connection name="Debian2: VNC Connection">
      <protocol>vnc</protocol>
      <param name="hostname">localhost</param>
      <param name="port">5901</param>
      <param name="password">vagrant</param>
    </connection>
 
    <connection name="Debian2: SSH Connection">
      <protocol>ssh</protocol>
      <param name="hostname">localhost</param>
      <param name="port">22</param>
      <param name="username">vagrant</param>
    </connection>
 
    <connection name="Debian1: SSH Connection">
      <protocol>ssh</protocol>
      <param name="hostname">192.168.10.5</param>
      <param name="port">22</param>
      <param name="username">vagrant</param>
    </connection>
 
    <connection name="Debian2: Telnet Connection">
      <protocol>telnet</protocol>
      <param name="hostname">localhost</param>
      <param name="port">23</param>
      <param name="username">vagrant</param>
    </connection>
  </authorize>
</user-mapping>

Create a symlink to tomcat

`sudo mkdir /usr/share/tomcat/.guacamole`

`sudo ln -s /etc/guacamole/guacamole.properties /usr/share/tomcat/.guacamole/`

Start guacamole

 `sudo guacd start`
