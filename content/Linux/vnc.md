---
title: "vnc"
date: 2018-09-27 15:05
---


[TOC]

# vnc 



## CentOS

```
sudo yum groupinstall -y "GNOME Desktop"
sudo yum install -y tigervnc-server
sudo cp /lib/systemd/system/vncserver@.service /etc/systemd/system/vncserver@:1.service

vim /etc/systemd/system/vncserver@:1.service


ExecStartPre=/bin/sh -c '/usr/bin/vncserver -kill %i > /dev/null 2>&1 || :'
ExecStart=/sbin/runuser -l vnc -c "/usr/bin/vncserver %i -geometry 1280x1024" 
PIDFile=/home/vnc/.vnc/%H%i.pid
ExecStop=/bin/sh -c '/usr/bin/vncserver -kill %i > /dev/null 2>&1 || :'


useradd vnc
su - vnc
vncserver
```



## Ubuntu 



### 18.04

```
sudo apt update  && apt upgrade -y && \
sudo apt install -y gnome-session gdm3 && \
sudo apt install -y tightvncserver ubuntu-gnome-desktop lxde && \
sudo apt install -y gnome-panel gnome-settings-daemon metacity nautilus gnome-terminal && \
sudo apt install -y tasksel && \
sudo tasksel install kubuntu-desktop
```



fixing when facing errors, using `aptitude `

```
sudo apt-get install aptitude   # press Enter to execute the command.
sudo aptitude install PACKAGENAME  # where PACKAGENAME is the package you’re installing, and press Enter to 																		execute it. This will try to install the package via aptitude instead of 																		apt-get, which should potentially fix the unmet dependencies issue.
```




```
adduser vnc

echo "%vnc     ALL=(ALL)       NOPASSWD: ALL" >> /etc/sudoers

sudo su - vnc



mkdir .vnc
tee  ~/.vnc/xstartup <<-'EOF'
#!/bin/sh
  
export XKL_XMODMAP_DISABLE=1
unset SESSION_MANAGER
unset DBUS_SESSION_BUS_ADDRESS

[ -x /etc/vnc/xstartup ] && exec /etc/vnc/xstartup
[ -r $HOME/.Xresources ] && xrdb $HOME/.Xresources
xsetroot -solid grey
vncconfig -iconic &
x-terminal-emulator -geometry 80x24+10+10 -ls -title "$VNCDESKTOP Desktop" &
x-window-manager &

gnome-panel &
gnome-settings-daemon &
metacity &
nautilus &
gnome-terminal &
gnome-shell &
EOF

sudo chmod +x  ~/.vnc/xstartup

vncserver

sudo chown vnc.vnc .vnc #if you need
vncserver -kill :1
```



Latest vnc xstartup and works

```
#!/bin/sh

unset SESSION_MANAGER
unset DBUS_SESSION_BUS_ADDRESS

export XKL_XMODMAP_DISABLE=1
export XDG_CURRENT_DESKTOP="GNOME-Flashback:GNOME"
export XDG_MENU_PREFIX="gnome-flashback-"


[ -x /etc/vnc/xstartup ] && exec /etc/vnc/xstartup
[ -r $HOME/.Xresources ] && xrdb $HOME/.Xresources
xsetroot -solid grey
vncconfig -iconic &

gnome-session --builtin --session=gnome-flashback-metacity --disable-acceleration-check --debug &
nautilus &
gnome-terminal &              

```





* VNC Service File

```
tee > /etc/systemd/system/vncserver@.service  <<EOF
[Unit]
Description=Start TightVNC server at startup
After=syslog.target network.target

[Service]
Type=forking
User=vnc
PAMName=login
PIDFile=/home/vnc/.vnc/%H:%i.pid
ExecStartPre=-/usr/bin/vncserver -kill :%i > /dev/null 2>&1
ExecStart=/usr/bin/vncserver -depth 24 -geometry 1280x800 :%i
ExecStop=/usr/bin/vncserver -kill :%i

[Install]
WantedBy=multi-user.target
EOF
```

```
sudo systemctl daemon-reload
sudo systemctl enable vncserver@1.service
sudo systemctl start vncserver@1.service
```





## novnc (vnc client)

**Installation from Snap Package**

Running the command below will install the latest release of noVNC from Snap:

```
sudo snap install novnc
```



**Running noVNC**

You can run the Snap-package installed novnc directly with, for example:

```
novnc --listen 6081 --vnc localhost:5901
```





## Debian 8

```
apt-get update
apt-get -y upgrade
apt-get install xfce4 xfce4-goodies gnome-icon-theme tightvncserver xfonts-base iceweasel
```



```
adduser vnc
apt-get install sudo
gpasswd -a vnc sudo
su - vnc
vncserver
vncserver -kill :1
sudo nano /usr/local/bin/myvncserver
```

Add these contents exactly. This script provides VNC with a few parameters for startup.

/usr/local/bin/myvncserver

```
#!/bin/bash
PATH="$PATH:/usr/bin/"
DISPLAY="1"
DEPTH="16"
GEOMETRY="1024x768"
OPTIONS="-depth ${DEPTH} -geometry ${GEOMETRY} :${DISPLAY}"

case "$1" in
start)
/usr/bin/vncserver ${OPTIONS}
;;

stop)
/usr/bin/vncserver -kill :${DISPLAY}
;;

restart)
$0 stop
$0 start
;;
esac
exit 0
```

Make the file executable:

```
sudo chmod +x /usr/local/bin/myvncserver
```

Our script will help us to modify settings and start/stop VNC Server easily.

If you'd like, you can call the script manually to start/stop VNC Server on port 5901 with your desired configuration.`sudo /usr/local/bin/myvncserver start sudo /usr/local/bin/myvncserver stop sudo /usr/local/bin/myvncserver restart`



Copy these commands to the service file. Our service will simply call the startup script above with the user vnc

/lib/systemd/system/myvncserver.service

```
[Unit]
Description=Manage VNC Server on this droplet

[Service]
Type=forking
ExecStart=/usr/local/bin/myvncserver start
ExecStop=/usr/local/bin/myvncserver stop
ExecReload=/usr/local/bin/myvncserver restart
User=vnc

[Install]
WantedBy=multi-user.target
```

Now we can reload `systemctl` and enable our service:

```
sudo systemctl daemon-reload
sudo systemctl enable myvncserver.service
```

You've enabled your new service now. Use these commands to start, stop or restart the service using the `systemctl` command:

```
sudo systemctl start myvncserver.service
sudo systemctl stop myvncserver.service
sudo systemctl restart myvncserver.service
```



* Securing Your VNC Server with SSH Tunneling

First, stop the VNC server:

```
sudo systemctl stop myvncserver.service
```

Edit your configuration script:

```
sudo nano /usr/local/bin/myvncserver
```

Change this line:

/usr/local/bin/myvncserver

```
OPTIONS="-depth ${DEPTH} -geometry ${GEOMETRY} :${DISPLAY}"
```

Replace it with:

/usr/local/bin/myvncserver

```
OPTIONS="-depth ${DEPTH} -geometry ${GEOMETRY} :${DISPLAY} -localhost"
```

Restart the VNC server:

```
sudo systemctl start myvncserver.service
```



* Windows client

We will use PuTTY to create an SSH Tunnel and then connect through the tunnel we have created.

Open PuTTY.

From the left menu, go to the **Connection->SSH->Tunnels** section.

In the **Add New Forwarded Port** section, enter `5901` as **Source port** and `localhost:5901` as **Destination**.

Click the **Add** button.

![PuTTY SSH Tunnel Configuration](https://assets.digitalocean.com/articles/vnc-debian8/jWDVCt9.png)

You can now go to the **Session** section in the left menu.

Enter your Droplet's IP address in the **Host Name (or IP address)** field.

Click the **Open** button to connect. You can also save these options for later use.

![PuTTY SSH Connection](https://assets.digitalocean.com/articles/vnc-debian8/zvIl1fJ.png)

Log in with your **vnc** user.

Keep the PuTTY window open while you make your VNC connection.

Now you can use your VNC viewer as usual. Just enter **localhost::5901** as the address, and keep your SSH connection live in the background.

![UltraVNC Viewer: localhost::5901](https://assets.digitalocean.com/articles/vnc-debian8/FZWF3UH.png)

 

## OS X

* To establish an SSH tunnel, use the following line in Terminal

```
ssh vnc@your_server_ip -L 5901:localhost:5901
```

Authenticate as normal for the **vnc** user for SSH. Then, in the Screen Sharing app, use **localhost:5901**.







# Appendix

https://github.com/novnc/noVNC/tree/89f9ac00166f1106e03d46562ffeaa3d8032f399

https://askubuntu.com/questions/1285420/how-to-properly-configure-xstartup-file-for-tightvnc-with-ubuntu-20-04-lts-gnome

