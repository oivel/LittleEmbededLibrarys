<p align="center">
<img src="https://i.ibb.co/8D7gPLY/Screenshot-2024-06-12-153900-1.jpg" border="0"></a>
</p>

# Introduction
LEL (Little Embedded Libraries) is a fully open-source project that allows anybody to connect to a router and access information. LEL was made to allow any person to find a little library, connect to a network, and learn about the surroundings of the little library.
LEL works with no internet and allows you to keep your network secure.

Want to try it? Go below and follow the steps to set it up!

## Before You Start
Before you start building, you need the correct software and hardware. All of the details will get you started on your journey.

IMPORTANT: Please note that by installing OpenWRT you could do undoable damage to your router or even brick it. LEL is not responsible for bricked routers. Please only install if you understand the risk you are taking.

### Hardware
LEL can run on any router. LEL runs an open-source router firmware called OpenWRT. 
Below is a button to see if your router is compatible. Please search for your router inside the link.

[Click Here](https://openwrt.org/toh/views/toh_fwdownload?dataflt%5B0%5D=supported%20current%20rel_%3D23.05.3)

### Software
Before you start anything, you will need to download and install these softwares to your computer.

- [PuTTY](https://putty.org)
- [FilesRemote](https://github.com/allanrbo/filesremote/releases/)
- [Microsoft Visual Studio Code](https://code.visualstudio.com)

## Installing OpenWRT
To install OpenWRT on your router, [Click Here](https://openwrt.org/toh/views/toh_fwdownload?dataflt%5B0%5D=supported%20current%20rel_%3D23.05.3) and search for your router in the table. Then, click on the blue Factory Image link that is next to your routers name.
Make sure you click on the Factory Image download or else it will not work. Also, we highly recommend only downloading it if the Support version is higher than 19.0.0. If it isn't, this guide may not work and may brick your router.


Once on your computer, connect to your router via an ethernet cable for higher connectivity speeds. Wifi will also work for this. Then once connected, go to your router's admin panel in your browser which is mostly 192.168.1.1. If it doesn't work check your routers manual. Once you have reached the admin panel, type in your username and password which is usually admin and password. If not, please refer back to your router's manual.

### Actually Installing
Once inside the panel, find a page called Firmware Update. Once inside that page, you should be able to automatically update or upload your own firmware. Select Upload your own firmware and drag and drop the OpenWRT file you downloaded earlier and wait for the installation to complete. DO NOT UNPLUG YOUR ROUTER FROM ANYTHING DURING THIS PROCESS AS THIS MAY RESULT IN A BRICK OF YOUR ROUTER MAKING IT A PAPERWEIGHT. 

## Configuring OpenWRT
First, navigate to 192.168.1.1 in your browser. Once in, you will be prompted to create a password. Create a password and log in. If you get signed out your username default is root.

To get devices connected to the router go to networks then wireless at the top of the page. Once in the Wireless Overview page you should have a section that says SSID: OpenWRT. There should be a button in the section that says disable. If it says enable, click the button and enable it. Then click the Edit button and scroll down to General Setup. Make sure Mode is set to Access Point and Network is set to LAN. For ESSID, that will be the name somebody sees when they want to connect to your network. You can set it as anything you want.

Then click on Wireless Security and in the Encryption dropdown select No Encryption (open network). OpenWRT is now configured!

## Installing NoDogSplash and DropBear
In this step, we will use a program called NoDogSplash which is an open-source captive portal app made for OpenWRT. To install it, open PuTTY and type 192.168.1.1 for the hostname. Make sure the port is set to 22. Once inside the username is root and the password is the password you set earlier. 

Copy and Paste this line of code into the Putty Command Line

```bash
opkg update
```

Make sure your router has an internet connection for this or else it will not work.


Next paste this line of code into the Command Line


```bash
opkg install nodogsplash
```

To start NoDogSplash paste this
```bash
/etc/init.d/nodogsplash stop
```


Boom, now you have a captive portal on your router!

Now its time for dropbear

Paste these lines into putty:

```bash
opkg install dropbear
```

```bash
/etc/init.d/dropbear start
```
```bash
opkg install nano
```
```bash
opkg install openssh-sftp-server
```


## Network Config
Before connecting to the router on FilesRemote we need to allow access from port 22. Simply go to your admin pannel and go to network > firewall. In the web interface: Go to the Traffic Rules tab.
Click on Add under the Open Ports on Router section.
Fill in the details:
Name: Allow-SSH
Protocol: TCP
External Port: 22
Internal IP Address: Leave blank (default is any)
Internal Port: Leave blank (default is any)
Click Save & Apply.

Now, before you can access your captive portal on any device we need to configure the network settings on OpenWRT. Luckily I've made it easy. Open FilesRemote and type root@192.168.1.1 for the host name. The password is the password you set for OpenWRT. Once inside navigate to /etc/config and find the nodogsplash file. Go ahead and delete it. Find the nodogsplash file in the config repository folder above and drag and drop it into the folder to replace it.

Next, let's configure the firewall so that when you connect it leads you to the captive portal. In the same config folder, find the firewall file. Then delete it and replace it with the firewall file that is in the config folder in our repository.
Do the same thing for the dropbear file.

## Custom Website
Go ahead and test LEL by connecting to the network you set up. You should be brought to a website or be shown that you need to take action to connect. If nothing works go to our help page and we will help troubleshoot it. Now that it works let's add a custom website.

### Building and Uploading the site
For this example, we made a google site. Go to [sites.google.com](https://sites.google.com) and create a new site. You may make your site however you would like it. Then publish it. Next, go to your Google Drive and create a folder named website. Make sure the folder is above all of the other files inside your drive or else it won't work. Then, go through your drive and find your Google site that you made, drag and drop it into the new folder you made.

### Downloading HTML
Now, go to [takeout.google.com](https://takeout.google.com). Once in Google Takeout, Diselect all of the products scroll down, and select Google Drive. Then under the selection button click the All Drive Data Included button. In the menu, click the deselect all button and select the folder you made in Google Drive a few moments ago. Scroll down and go to the next step by clicking the next button. Then click the Create Archive button. Wait about 1-3 minutes and refresh the page. Go to your Gmail or the email you use for your Google account and you will see an email from Google that contains a link. Click it and download your data.

### Loading the Website
Once downloaded, head over to your file manager and un-zip the folder. Next go through all the folders until you reach the name of the folder you made in google drive and click on it. Make a new folder in your downloads section and drag and drop all of the contents of the Google Takeout folder you downloaded into the new folder.

Now comes the fun part, loading it on. Go back to FilesRemote and log in. Navigate to /etc/nodogsplash/htdocs and inside you will find multiple files. Delete everything inside except for Splash.html and Status.html. Delete Splash.html and replace it with the Splash.html inside our repository by clicking the upload button in FilesRemote. Open the file Splash.html that you just uploaded on FilesRemote. Then find the name of the main page you want to be the captive portal home screen in your Google takeout folder you made. It should be named something like Home.html and if it's not that's fine. Find where it says "Rural Cemetery.html" and change it to your home html page. (ex: window.location.href = "home.html";). Now drag and drop every file thats inside your google takeout folder you made into the htdocs folder on FilesRemote. You can only do one at a time. If you have folders in your Google Takeout folder, you will have to create them in FilesRemote. Name the Folders the same thing in FilesRemote because it's case sensitive. drag and drop every file that is inside the folders in your Google Takeout folder to the duplicate folders in FilesRemote.

Please note that downloading a website takes different steps on a different website builder.

# Final Steps
In PuTTY run all of these commands line by line
```bash
/etc/init.d/nodogsplash enable
```
```bash
/etc/init.d/firewall enable
```
```bash
/etc/init.d/dropbear enable
```
```bash
/etc/init.d/nodogsplash start
```
```bash
/etc/init.d/firewall start
```
```bash
/etc/init.d/dropbear start
```
```bash
/etc/init.d/nodogsplash restart
```
```bash
/etc/init.d/firewall restart
```
```bash
/etc/init.d/dropbear restart
```
# Congrats
Yay, you installed LEL onto your router! Now simply connect the router to power and place it anywhere. All you need to do is connect to the router's network and you can view the website. If your having trouble please go the the issues page on this GitHub repository to troubleshoot your problem.

## To access the admin panel or to add USB Drive support go to the Wiki page








