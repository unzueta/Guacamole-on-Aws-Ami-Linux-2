# GuacamoleOnAwsAmi2
How to setup Guacamole in Aws Ami 2 Ec2

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

Configure a VNC-specific password containing 6 to 8 characters for this user. You also have the option to configure a secondary view-only password in this step

`vncpasswd`
