# YACardEmuRaspi
This repo is to keep track of what i needed to do to use YACardEmu with a Raspberry Pi 3+ in headless mode.

here is the official repo of YACardEmu https://github.com/GXTX/YACardEmu

The goal is to have a Raspberri Pi3+ stand alone , no screen, no kb, only 1 button to power up and power down to stanby mode.

Operating system :
After few struggle with RaspiOs it appear that the best os to build YACardEmu is Ubuntu, so i installed UBUNTU SERVER 22.04.3 LTS

Autologin:
To be able to power up and power down only with a button , i must enable autologin. 
Following this https://askubuntu.com/questions/1337658/enabling-autologin-in-ubuntu-20-04-02-lts-server-on-raspi

i use this line
```
systemctl edit getty@.service to change the ExecStart=- line to /sbin/agetty -a $DESIRED_USERNAME --noclear %I $TERM
```
of course you replace DESIRED_USERNAME with yous.
