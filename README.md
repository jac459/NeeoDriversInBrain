# NeeoDriversInBrain
Set of instructions/helpers in order to load drivers directly in the Neeo Brain (no need for raspberry)

### Disclaimer
Please note that these instructions come without any guarantee of you not bricking your Neeo Brain. It is also your responsibility to check if what you do with the Neeo Brain is part of the usage authorized by Neeo or Control4 company.

# Instructions
## Rooting the device.
The first thing you need to do is root your Neeo brain in order to be able to access it. The protocole you will use to access it is called 'SSH'. In order to do that, you just have to use the excellent tool provided here:
https://builder.dontvacuum.me/neeo.html
You will have to follow te instructions, give your email address and submit. You should receive a first email from iotstuff@mit.edu, and a few minutes later, you will receive a another email with further instructions.
You can perfectly "Let DustBuilder generate a SSH Keypair" for you. The rest of this tutorial will assume you are choosing this option as if you chose the other one that means you are a more advanced user and probably don't need this part of the tutorial.

When you receive the second email starting with "your firmware image is ready", you can start follow the instructions. Make sure to safely save the keys file provided to you and that you will need to unzip using the provided password.
The most complex part of the instructions is to find your brain IP address when you are in recovery. To do that, you will need to google the brand/model of your rooter and see for instructions to connect to it (don't panic if you don't have user/password, that means that you probably used the default provided one and are the same for everybody, you should find them on google easily (usually user:admin password:1234 or blank). Once you are connected to your router (it should be a webpage or a mobile app), go to the attached devices pages and search for neeo-recovery and then the IP adress (192.168.x.xxx).

On the step 3 of the tutorial, don't be alarmed if you don't have the same version of firmware. If you are up to date, it will most likely look like:
Firmware package available on server:
0.53.8-20180424-05eb8e2-0201-092014
This isn't an issue.
When downloading the new firmware, you may be stuck for a while at 0%, it is not a problem. Just go have a coffee.
At one point of time, you should reach this status:
Installing firmware
Executing post-install script
Be mindfull to wait your neeo brain stop blinking in red and reboot with white color blinking.
It is time for you to try accessing the device.

## Accessing your neeo brain.
At this point of time,  you should be able to connect your Neeo Brain using the remote or your mobile app. Go to your mobile app, hit the menu button and the 'about' menu in order to get the new IP adress of your neeo brain (under NEEO Brain/LAN IP part of the screen). Note this IP adress, you will need it later.
<<BE CAREFULL: If you plan to connect your brain later to the wifi, take also note of the NEEO Brain/WLAN IP>>
In order to connect to your Neeo Brain, you need a SSH tool. The most commonly used is PUTTY. you can download PUTTY here: https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html
Hence you have installed putty, you can launch it.
1- Copy the IP adress of your Neeo Brain in the 'Host Name (or IP address)' part.
2- On the category part, go to connection/ssh/Auth menu. On the bottom part of the screen, you should see a browse button with "Private key file for authentication". Press it and go find the Key from the zip you extracted with the provided password. It is a file with ppk extension. 
3- Then you can hit the load button and you will get a warning message on which just answer 'yes'.
4- Now you should be in front of a new window with 'login as' prompt. Type neeo and hit 'enter' button.
If you get a new prompt with now error message it means that your brain is now successfully rooted. Congratulation.
5- In the future, when reconnecting, you should repeat the same instructions but before opening, take time to go back to the main page of putty and save the session (giving it a recognizable name). So that you won't have to type the IP or load the file again. Putty will do it for you.

## Installing a driver.
Now you are reaching the meaty part of the tutorial.
Installing a driver directly inside your Neeo brain.
Now you need to download another tool SCP. SCP allows to connect through SSH to your brain but it also allows to copy files on it.
For this tutorial we will assume you are using WinSCP which allows you to copy files directly from your filesystem (or raspberry pi share) to the Neeo Brain.
Good news is WinSCP is reusing the configuration you have done using PUTTY. 
1- Open WinSCP and hit the New Session button. You will see a new modal window with tools on the bottom left.
2- Hit the tools button and choose the import sites feature. Import the settings from putty to connect to the Neeo Brain. User is 'neeo' by default, no password.
3- Now you should be able to browse your brain file system. Go to the /var/ directory and try to create a new folder named 'NeeoShare' (for example). You should get an error message (permission denied).
<<THIS PART COULD BE IMPROVED PLEASE HELP IF LINUX GURU>>
4- In order to be able to copy files to the brain, you need first to connect using PUTTY. Once connected, 
  - Type 'cd /var/' ==> To go to the var directory and not mess with other parts of the brain.
  - Type 'sudo mkdir NeeoShare' to create a directory inside the /var/ path.
  - Type 'sudo chmod 777 NeeoShare' in order to give rights to this folder to copy from winSCP (this is quite a brutal way and can certainly be improved).
5- Now, using WinSCP, you should be able to browse inside NeeoShare and copy files into it.
  sudo pacman -Fy
  upgrade pacman database
  TO BE FINALISED
  
  

