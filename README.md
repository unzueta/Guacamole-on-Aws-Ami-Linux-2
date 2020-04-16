# GuacamoleOnAwsAmi2
How to setup Guacamole in Aws Ami Linux 2 Ec2

## Update your instance

`sudo yum update`

Reboot

`sudo reboot now`

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

## Install Tomcat

`sudo amazon-linux-extras install tomcat8.5`

Start Tomcat

`sudo systemctl start tomcat`


