====== Erik's default RPi image ======

What has been added on top of the base image? How do you use it?

It's based off of Raspbian's Buster lite image but there are tools that I use commonly (git, golang, vim), tools for wifi connecting (comitup), and web IDE's (cloud9 and theia). It's currently only tested with a Raspberry Pi Zero W.
Also, the camera is enabled and ffmpeg is installed.

There are a couple options to get started:
1. Power up the RPi, find the comitup-## wireless network (the default password is 'raspberry'). It will pop up a wifi connect page to choose which wifi network the RPi should connect to (optional if it doesn't need internet). You can ssh into it (default user is 'pi', password is 'raspberry') (find it's ip address or try raspberrypi.local), or go to https://raspberrypi.local:8443 to go into the cloud9 IDE (same default login as ssh).
2. Connect with the OTG port on the Pi Zero to a computer, it will connect through RNDIS/Ethernet Gadget (tested on mac - go to System Preferences, Network). Again you can ssh or use cloud9.

To use Theia:
It seems to be less stable than cloud9 but I haven't tested much. Theia (maybe also cloud9) could possibly use up all the memory (I've seen it use up half of it). You may want to increase the swap size.
You probably don't want to run them both at once, so with a carefully crafted bash command you can get from one to the other without having a separate ssh.
Get into ssh or cloud9 (see above), run 'sudo systemctl start theia; sudo systemctl stop c9sdk'. You could also set which one is started on boot (disable instead of stop, enable instead of start). Theia runs on port 8182.

To make it secure do all of the following:
1. Change the system user password (use passwd)
2. Change the comitup network password (in /etc/comitup.conf change ap_password)
3. Change the cloud9 access password (in /opt/c9/start-c9.bash change the field after --auth to have your username:password)
4. Don't use theia (there's a TODO below to fix that)
5. Reboot

You Might want to:
Make the comitup network ssid unique (/etc/comitup.conf)
Make the hostname unique (raspi-config)
Set localization

How did I make the image?

Start with Raspbian Buster lite image
Flash it to an RPi Zero, enable SSH, usb OTG SSH
Give it internet (wpa supplicant)
Install git, vim, golang
Install comitup (with Dave Steele's PPA repository to get the latest version)
Clone cloud9 repository, run scripts/install.sh
Setup cloud9 systemd service
Make cloud9 and comitup secure
Install nvm (use 10), Install yarn, make the theia package.json
Run 'yarn --network-timeout 1000000' (wait 1 hour)
Run 'yarn theia build' (wait 1 hour)
Setup theia systemd service
TODO: Make theia secure (Apache reverse proxy with basic auth)
Remove connection from comitup
clear /tmp files, anything unnecessary, apt clean
Add ssh-keygen -A to /etc/rc.local (for next step to work)
Delete /etc/ssh/ssh_host* files (so that all devices have different identities)
Clear history: 'history -c', 'rm ~/.bash_history'

