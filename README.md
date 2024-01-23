# YACardEmuRaspi
This repo is to keep track of what i needed to do to use YACardEmu with a Raspberry Pi 3+ in headless mode.

here is the official repo of YACardEmu https://github.com/GXTX/YACardEmu

At any point after the operating system is up and running, you can install YACardEmu

In my fresh install of YACardEmu, i was missing a font, i copied it in the YACardEmu directory. The needed font is here :
https://github.com/indico/indico-fonts/blob/master/indico_fonts/kochi-gothic-subst.ttf

The goal is to have a Raspberri Pi3+ stand alone , no screen, no kb, only 1 button to power up and power down to stanby mode.

Operating system :
---------
After few struggle with RaspiOs it appear that the best os to build YACardEmu is Ubuntu, so i installed UBUNTU SERVER 22.04.3 LTS

Autologin:
---------
To be able to power up and power down only with a button , i must enable autologin. 
Following this https://askubuntu.com/questions/1337658/enabling-autologin-in-ubuntu-20-04-02-lts-server-on-raspi

i use this line
```
systemctl edit getty@.service to change the ExecStart=- line to /sbin/agetty -a $DESIRED_USERNAME --noclear %I $TERM
```
of course you replace DESIRED_USERNAME with yours.

Power Button :
---------
For the power button i followed this : https://embeddedcomputing.com/technology/open-source/development-kits/raspberry-pi-power-up-and-shutdown-with-a-physical-button

Be aware that under Ubuntu the config.txt file is or must be created here : /boot/firmware/config.txt

i use these lines
```
[all]
# Enable the audio output, I2C and SPI interfaces on the GPIO header. As these
# parameters related to the base device-tree they must appear *before* any
# other dtoverlay= specification
dtparam=audio=on
#dtparam=i2c_arm=on
dtparam=spi=on

# shutdown button and led
dtoverlay=gpio-shutdown
gpio=15=op,dh
```
With Ubuntu the line #dtparam=i2c_arm=on must be commented, as it won't accept it as powerdown switch . Reason being that it would be allocated to the i2c.

Autostart
---------
Now we need to have YACardEmu autostarted after bootup. This is done via rc.local.
This file must be created in this directory : /etc/rc.local
Put this code in rc.local
```
#!/bin/sh -e
#
# rc.local
#
# This script is executed at the end of each multiuser runlevel.
# Make sure that the script will "exit 0" on success or any other
# value on error.
#
# In order to enable or disable this script just change the execution
# bits.
#
# By default this script does nothing.

cd home/bsl88k/YACardEmu/build
./YACardEmu &

exit 0
```
At this point we need rc.local to be executed at bootup.
I did follow this : https://www.linuxbabe.com/linux-server/how-to-enable-etcrc-local-with-systemd

Add execute permission to /etc/rc.local file.
```
sudo chmod +x /etc/rc.local
```
After that, enable the service on system boot:
```
sudo systemctl enable rc-local
```
Almost Done
---------

My system had a really slow boot cause by a wait for network, i was getting this message "A start job is running for wait for network to be configured" that stays for about 2 minutes

the solution to this is here : https://askubuntu.com/questions/972215/a-start-job-is-running-for-wait-for-network-to-be-configured-ubuntu-server-17-1

these lines stopped it 

Use
```
systemctl disable systemd-networkd-wait-online.service
```
to disable the wait-online service to prevent the system from waiting on a network connection, and use
```
systemctl mask systemd-networkd-wait-online.service
```
to prevent the service from starting if requested by another service (the service is symlinked to /dev/null).

# NOW we have a standalone system in a small box control by 1 push button.
