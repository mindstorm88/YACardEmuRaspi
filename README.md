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
of course you replace DESIRED_USERNAME with yours.

Power Button :
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
With Ubuntu the line #dtparam=i2c_arm=on must be commented, as it won't accept it as powerdown switch . Reason being that it would be allocated to the i2c
