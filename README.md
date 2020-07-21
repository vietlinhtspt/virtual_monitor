# Xorg.conf config for dummy video driver
# For usage with for example TeamViewer on a machine without a monitor attached
# and you wanted more then just 1024x768 ;)
#
# Use at own risk, loosly based on info scattered around but these links really helped
# http://arachnoid.com/modelines/ for the modelines (lot of trial and error to figure out which worked over Teamviewer and Xorg)
# https://www.xpra.org/xorg.conf sample config from xpra who seems to use the dummy driver a lot (thanks guys!)
1) Login to your google cloud console
2) Click on Resources - Computer Engine
3) Click on Create Instance
4) Click on SSH button to remote into the ubuntu instance
5) change the default user password by typing :
   sudo passwd username (your username)
   (we will need password to login to GUI later)
6) type: sudo su - to become root
7) change the root user password as well type:
   passwd
8) enable password for SSH login by edit the file: (not sure if this needed, but i just do that)
nano /etc/ssh/sshd_config
change PasswordAuthentication no
to PasswordAuthentication yes
add: PermitRootLogin yes
service ssh restart

9) type following command 1 by 1:
apt update
apt-get upgrade
apt-get install ubuntu-desktop ( require some time, please wait )
apt-get install xserver-xorg-video-dummy

10) edit the xorg conf:
#==========================
Section "Device"
	Identifier "dummy_videocard"
	Option "NoDDC" "true"
	Option "IgnoreEDID" "true"

	# Debian: apt-get install xserver-xorg-video-dummy
	Driver "dummy"

	# You could lower this to suit your needs, however you will need
	# a quite high amount to run such crazy resolutions
	VideoRam 524288
EndSection

Section "Monitor"
	Identifier "dummy_monitor"

	# 16:9 4k
	Modeline "3840x2160_20.00" 218.15 3840 4016 4416 4992 2160 2161 2164 2185

	# 21:9 "4k"
	Modeline "3440x1440_20.00" 124.95 3440 3520 3864 4288 1440 1441 1444 1457

	# Usual 27" suspect before 4k era
	Modeline "2560x1440" 42.12 2560 2592 2752 2784 1440 1475 1478 1513

	# Oddball 1920 resolution
	Modeline "1920x1440" 69.47 1920 1960 2152 2384 1440 1441 1444 1457

	# 16:10 "Full-HD"
	Modeline "1920x1200" 26.28 1920 1952 2048 2080 1200 1229 1231 1261

	# 16:9 Full HD
	Modeline "1920x1080" 23.53 1920 1952 2040 2072 1080 1106 1108 1135

	# These are just crazy high to ensure stuff works
	HorizSync   5.0 - 1000.0
	VertRefresh 5.0 - 1000.0
EndSection

Section "Screen"
	Identifier "dummy_screen"
	Device "dummy_videocard"
	Monitor "dummy_monitor"
	DefaultDepth 24
	SubSection "Display"
		Depth 24
		Modes "3840x2160_20.00" "3440x1440_20.00" "2650x1440" "1920x1440" "1920x1200" "1920x1080"

		# Not sure why, but 3440x1440 won't work when the Virtual is set to "3840 2160"
		# However it will complain in the Xorg.log when you didn't comment out the 3840x2160 resolution at the top
		#
		# Uncomment this for 21:9 4k
		Virtual 3440 1440

		# Uncomment this for 16:9 4k
		#Virtual 3840 2160
	EndSubSection
EndSection
#==========
11) reboot the server:
reboot
12) login as root again using sudo su -
13) install teamviewer:
apt install gdebi -y
cd /tmp
wget https://download.teamviewer.com/downl...
gdebi teamviewer_amd64.deb
teamviewer license accept
teamviewer daemon enable
teamviewer daemon start
teamviewer passwd thenewpassword
teamviewer info

on the teamviewer info you should able to get the teamviewer ID
and password use the password you set above.
