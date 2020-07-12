# NeeoDriversInBrain
Set of instructions/helpers in order to load drivers directly in the NEEO Brain (no need for secondary server).

### Disclaimer
Please note that these instructions come without any guarantee of you not bricking your NEEO Brain. It is also your responsibility to check if what you do with the NEEO Brain is part of the usage authorized by Neeo or Control4 company.

# Instructions
## Rooting the device.
The first thing you need to do is root your NEEO Brain in order to be able to access it. In order to root it you just have to use the excellent tool provided here (huge shout out to Dennis Giese for his amazing work on this image builder):
- https://builder.dontvacuum.me/neeo.html

You just have to follow the instructions, give your email address and submit. You can perfectly "Let DustBuilder generate a SSH Keypair" for you. The rest of this tutorial will assume you are choosing this option as if you chose the other one that means you are a more advanced user and probably don't need this part of the tutorial.

After you submit, you should receive a first email from iotstuff@mit.edu a few minutes later stating that the job has started. Another two emails will follow (10-15 minutes usually) after with further instructions.

When you receive the second email starting with "your firmware image is ready", you can start following the instructions. Make sure to safely save the keys file provided to you and that you will need to unzip using the provided password. This key file is what you will use to connect to your brain over the SSH protocol.

The most complex part of the instructions is to find your brain IP address when you are in recovery. There are a few ways to do this:

- Router method: Google the brand/model of your router and see instructions on how to connect to it (don't panic if you don't have user/password, that means that you probably used the default provided one and are the same for everybody, you should find them on google easily (usually user:admin password:1234 or blank). Once you are connected to your router (it should be a webpage or a mobile app), go to the attached devices pages and search for neeo-recovery and then the IP address (`192.168.xxx.xxx` is a common household subnet).
- Angry IP Scanner: Download the application for mac or for windows here: https://angryip.org/. Once installed, open the application, and click scan. The appropriate IP address range should already be selected and it will scan your entire subnet looking for connected devices. Look for `neeo-recovery`. That is the ip address assigned to your brain.

On step 3 of the tutorial, don't be alarmed if you don't have the same version of firmware. If you are up to date, it will most likely look like:
```bash
Firmware package available on server:
`0.53.8-20180424-05eb8e2-0201-092014`
```
This isn't an issue.
When downloading the new firmware, you may be stuck for a while at 0%, it is not a problem. Just go have a coffee.
After a few minutes you should reach this status:
```bash
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
If you are using a Mac or Linux you first need to get the private SSH key that was produced for you. You can find the appropriate key file from the zip you extracted with the provided password in the emails sent by iotstuff@mit.ed. The file name is `id_rsa`.

Then you will have an SSH capable program called Terminal. Launch it and type (making sure to substitute your ip address and key file path below appropriately)
```bash
ssh -i /path/to/id_rsa neeo@192.168.xxx.xxx
```

You should now be looking at an active SSH session with your NEEO brain.

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
```bash
cd /var
sudo mkdir NeeoShare
```
This will make a folder in the `/var` directory and not mess with other parts of the brain. Now you need to make sure the `neeo` user has access to read and write to the directory.
```bash
sudo chown neeo:wheel NeeoShare
sudo chmod -R 775 NeeoShare
```
5. Now, using WinSCP, you should be able to browse inside NeeoShare and copy files into it.
  sudo pacman -Fy
  upgrade pacman database
  TO BE FINALISED



