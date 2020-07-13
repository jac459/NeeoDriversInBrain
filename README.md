# NeeoDriversInBrain
Set of instructions/helpers in order to load drivers directly in the NEEO Brain (no need for secondary server).

### Disclaimer
Please note that these instructions come without any guarantee of you not bricking your NEEO Brain. It is also your responsibility to check if what you do with the NEEO Brain is part of the usage authorized by Neeo or Control4 company.

# Instructions
## Rooting the device.
The first thing you need to do is root your NEEO Brain in order to be able to access it. In order to root it you just have to use the excellent tool provided here (huge shout out to [Dennis Giese](https://dontvacuum.me/) for his amazing work on this image builder):
- https://builder.dontvacuum.me/neeo.html

You just have to follow the instructions, give your email address and submit. You can perfectly "Let DustBuilder generate a SSH Keypair" for you. The rest of this tutorial will assume you are choosing this option as if you chose the other one that means you are a more advanced user and probably don't need this part of the tutorial.

After you submit, you should receive a first email from iotstuff@mit.edu a few minutes later stating that the job has started. Another two emails will follow (10-15 minutes usually) after with further instructions.

When you receive the second email starting with "your firmware image is ready", you can start following the instructions. Make sure to safely save the keys file provided to you and that you will need to unzip using the provided password. This key file is what you will use to connect to your brain over the SSH protocol.

The most complex part of the instructions is to find your brain IP address when you are in recovery. There are a few ways to do this:

- Router method: Google the brand/model of your router and see instructions on how to connect to it (don't panic if you don't have user/password, that means that you probably used the default provided one and are the same for everybody, you should find them on google easily (usually user:admin password:1234 or blank). Once you are connected to your router (it should be a webpage or a mobile app), go to the attached devices pages and search for neeo-recovery and then the IP address (`192.168.xxx.xxx` is a common household subnet).
- Angry IP Scanner: Download the application for mac or for windows here: https://angryip.org/. Once installed, open the application, and click scan. The appropriate IP address range should already be selected and it will scan your entire subnet looking for connected devices. Look for `neeo-recovery` as that is the hostname given to a NEEO brain in recovery mode. That is the ip address assigned to your brain.

On step 3 of the tutorial, don't be alarmed if you don't have the same version of firmware. If you are up to date, it will most likely look like:
```shell
Firmware package available on server:
`0.53.8-20180424-05eb8e2-0201-092014`
```
This isn't an issue.
When downloading the new firmware, you may be stuck for a while at 0%, it is not a problem. Just go have a coffee.
After a few minutes you should reach this status:
```shell
Installing firmware
Executing post-install script
```
Be mindful to wait for your NEEO Brain's led to stop blinking red and reboot with white color blinking.
It is time for you to try accessing the device.

## Accessing your NEEO Brain.
At this point in time, you should be able to connect to your NEEO Brain using the remote or your mobile app just like normal. Go to your mobile app, hit the menu button and the 'about' menu in order to get the new IP address of your NEEO Brain (under NEEO Brain/LAN IP part of the screen). Note this IP address, you will need it later.

> BE CAREFUL: If you plan to connect your brain later to the wifi, take also note of the NEEO Brain/WLAN IP

In order to connect to your NEEO Brain, you need a SSH tool.

#### Windows
If you are running Windows the most commonly used application for SSH is PuTTY. you can download PuTTY here: https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html
After you have installed putty, launch it.
1. Copy the IP address of your NEEO Brain in the 'Host Name (or IP address)' part.
2. On the category part, go to connection/ssh/Auth menu. On the bottom part of the screen, you should see a browse button with "Private key file for authentication". Press it and go find the key from the zip you extracted with the provided password. It is a file with ppk extension.
3. Then you can hit the load button and you will get a warning message on which just answer 'yes'.
4. Now you should be in front of a new window with 'login as' prompt. Type neeo and hit 'enter' button.
If you get a new prompt with now error message it means that your brain is now successfully rooted. Congratulation.
5. In the future, when reconnecting, you should repeat the same instructions but before opening, take time to go back to the main page of putty and save the session (giving it a recognizable name). So that you won't have to type the IP or load the file again. Putty will do it for you.

#### Mac or Linux
If you are using a Mac or Linux you first need to get the private SSH key that was produced for you. You can find the appropriate key file from the zip you extracted with the provided password in the emails sent by iotstuff@mit.edu. The file name is `id_rsa`.

Then you will have an SSH capable program called Terminal. Launch it and type (making sure to substitute your ip address and key file path below appropriately)
```shell
ssh -i /path/to/id_rsa neeo@192.168.xxx.xxx
```

Congratulations, you should now be looking at an active SSH session with your NEEO brain.

## Preparing your NEEO Brain.
Now that you have accessed your Neeo brain, you need to do a few transformation in order to make it easy to run drivers in the brain, without disturbing the brain normal operations. To do that you will have to type a few commands in the ssh capable program you are using (putty on windows, the Terminal on mac).

### Create a driver dedicated folder
The first thing you need to do is to make your file system read-write. By default it is read-only as a protection implemented by neeo company. This way you don't risk to corrupt anything. SO in order to change something in the brain, you need to type that:
```shell
sudo mount -o remount,rw /
```
> After a reboot, your file system will be again read-only. So you will need to type that again for any change in the brain like installing a driver. If you run into a problem like 'disk full', you can assume you forgot to mount read-write (or you installed already an awful number of drivers and should not be reading this tuto anymore ;-)).
Now we will create a special place in order to install your drivers. To do that we will create a directory inside your user's place. 
Your user name is neeo, to get there you need to type:
```shell
cd ~
```
or
```shell
cd /home/neeo/
```
Then create a 'drivers' directory:
```shell
sudo mkdir drivers
```
```shell
sudo chown neeo:wheel drivers
sudo chmod -R 775 drivers
```
The second and third commands allow you to copy files from SCP.

### Install a recent version of node.js
Now you need to install node.js. Node.js is the underlying technology used by the Neeo brain. It is already installed but an older version. In order to be able to install modern and efficient drivers, you need to also install the last version of node.js. The node used by neeo is version 8, the most recent version of node at the time of writting this tutorial is 12.

The tool that will help us load node is named nvm. The tool that will help us install nvm is ca-certificates and the tool that will help us install ca-certifcates is pacman (simple, isn't it?).
This gets pacman up and running again by refreshing local database files.
```shell
sudo pacman -Sy
```
(if you have a disc full error, it is because you are not mounted read-write, read the tuto again :-)).

In order to be able to download the nvm tool, we need to be sure we can trust the site, this is done by refreshing the certificate this way:
Install ca-certificates
```shell
sudo pacman -S --force ca-certificates
```
(press Y on the prompt)
Now you can install nvm.

This installs nvm in the `/home/neeo/` directory (based on the current version of nvm available you might need to update the version in the url of the command below).
```shell
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.3/install.sh | bash
```
> after this step, you need to close the command window (terminal or putty) and reopen it in order for your changes to take effect.

FINALLY, you can install the last version of node without overwritting the one used by Neeo.
Install nodejs v12 latest. This should also install npm 6.14.5 (a tool helping to quickly install drivers).
```shell
nvm install --lts=erbium
```
AND THAT'S IT ! (at least for this step).


## Installing a driver.
Now you are reaching the meaty part of the tutorial.
Installing a driver directly inside your NEEO Brain.
Now you need to download another tool SCP (Secure Copy Protocol). SCP allows to copy files through an SSH connection.

#### For Windows Users
For this tutorial we will assume you are using WinSCP which allows you to copy files directly from your filesystem (or raspberry pi share) to the NEEO Brain.
The good news is WinSCP is reusing the configuration you have done using PuTTY.
1. Open WinSCP and hit the New Session button. You will see a new modal window with tools on the bottom left.
2. Hit the tools button and choose the import sites feature. Import the settings from PuTTY to connect to the NEEO Brain. User is 'neeo' by default, no password.
3. Now you should be able to browse your brain file system. Go to the /var/ directory and try to create a new folder named 'NeeoShare' (for example). You should get an error message (permission denied).
4. In order to be able to copy files to the brain, you need first to connect using PuTTY. Once connected, create a folder inside the var folder:
```shell
cd /var
sudo mkdir NeeoShare
```
This will make a folder in the `/var` directory and not mess with other parts of the brain. Now you need to make sure the `neeo` user has access to read and write to the directory.
```shell
sudo chown neeo:wheel NeeoShare
sudo chmod -R 775 NeeoShare
```
5. Now, using WinSCP, you should be able to browse inside NeeoShare and copy files into it.

# COMMANDS THAT NEED TO BE INTEGRATED INTO HOWTO

This gets pacman up and running again by refreshing local database files
```shell
sudo pacman -Sy
```
In order to be able to download the nvm tool, we need to be sure we can trust the site, this is done by refreshing the certifcate this way:
Install ca-certificates
```shell
sudo pacman -S --force ca-certificates
```
(press Y on the prompt)


This installs the latest version of pacman (v5.2.1 as of when this was written)
```shell
### DONT RUN THIS RIGHT NOW
#sudo pacman -S --force pacman
```

Install ca-certificates
```shell
pacman -S --force ca-certificates
```

This installs nvm in the `/home/neeo/` directory (based on the current version of nvm available you might need to update the version in the url of the command below).
```shell
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.3/install.sh | bash
```

Install nodejs v12 latest. This should also install npm 6.14.5.
```shell
nvm install --lts=erbium
```

In order to switch back and forth between system installed nodejs and nvm installed you need to use the `nvm use` command. You can see some commands and responses below to illustrate operation.
```shell
$ nvm use system
Now using system version of node: v8.11.2
$ nvm use lts/erbium
Now using node v12.18.2 (npm v6.14.5)
```

sudo pacman -Fy
  upgrade pacman database
  TO BE FINALISED
  >> just quick notes before I forgot:
  To install nvm (needed for side by side node versions ==> needed because brain has issue running with newer versions of nodes and most libraries to construct drivers are using latest versions of node ... obviously).
  Download the content of this GIT repositiory: https://github.com/nvm-sh/nvm
  To do that, simplest is to press the green code button in the website and choose download zip.
  Unzip somewhere. It will create a folder nvm-master.
  go to your putty/neeo
```
cd ~
mount -o remount,rw /
sudo mkdir tempo
sudo mkdir .nvm
sudo chmod 777 tempo
sudo chmod 777 .nvm
```

Going to your /home/neeo on winscp, you should be able to see the tempo directory.
using winscp, copy the content of svn-master (not svn-master itself) inside tempo.
then in your putty/neeo
```
cd~/tempo
sudo mv ./* ../.nvm
cd ~/.nvm
sudo chmod 777 install.sh      ////(maybe not usefull and gives error messages)
sudo chmod 777 nvm.sh
sudo ./install.sh        ////(maybe not usefull and gives error messages)
sudo ./nvm.sh
cd ~
sudo nano .bashrc
```
  This list command will open an editor to edit your .bashrc
  copy these line at the end of the file. Save by typing usign Ctrl+s then Ctrl+x keys

```
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # This loads nvm
```
Then you should reboot the brain.
```shell
sudo reboot
```
  On reboot, log with putty/neeo, type nvm, you should have the command installed.








