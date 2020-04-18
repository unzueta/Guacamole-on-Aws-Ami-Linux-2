# Guacamole on Aws Ami Linux 2
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

`sudo systemctl start guacd`

# Download Guacamole client

`wget https://downloads.apache.org/guacamole/1.1.0/binary/guacamole-1.1.0.war`

`sudo mv guacamole-1.1.0.war /var/lib/tomcat/webapps/guacamole.war`

## Install Tomcat

`sudo amazon-linux-extras install tomcat8.5`

Start Tomcat

`sudo systemctl start tomcat`

## Configure Guacamole

`sudo mkdir /usr/share/tomcat/.guacamole`

#Create the file guacamole.properties

`sudo nano /usr/share/tomcat/.guacamole/guacamole.properties`

Paste the bollowing lines

```# Hostname and port of guacamole proxy
guacd-hostname:      localhost
guacd-port:          4822
```

#Create the file user-mapping.xml

`sudo mkdir /usr/share/tomcat/.guacamole`

`sudo nano /usr/share/tomcat/.guacamole/user-mapping.xml`

```
<user-mapping>
  <authorize username="USERNAME" password="PASSWORD">
    <connection name="Windows: RDP Connection">
      <protocol>rdp</protocol>
      <param name="hostname">192.168.10.1</param>
      <param name="port">3389</param>
      <param name="username">User</param>
      <param name="password">PassUsr</param>
      <param name="ignore-cert">true</param>
      <param name="console">true</param>
    </connection>
 
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
```
Change premissions and Owner

`sudo chmod 600 usr/share/tomcat/.guacamole/user-mapping.xml`

`sudo chown tomcat:tomcat usr/share/tomcat/.guacamole/user-mapping.xml`

## Start guacamole

 `sudo service guacd stop`
 
 `sudo service guacd start`
 
 # Running gucad in Debug mode
 
 `sudo service gucad stop`
 
 `sudo gucad -L debug -f'
 
 ## Restart Tomcat
 
 `sudo service tomcat restart`
