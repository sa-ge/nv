# nv
Source https://www.reddit.com/r/debian/comments/gantqb/nvidia_driver_installation_problem_in_debian/

Nvidia Driver Installation on Debian Buster


1st: sudo apt remove --purge -y nvidia* libnvidia* bumblebee* primus*

2nd: sudo apt autoremove -y

3rd: sudo apt install nvidia-detect

(that one will show which driver you need)

4th: create file /etc/modprobe.d/blacklist-nvidia-nouveau.conf and append there 2 lines (without quotes):

"

blacklist nouveau

options nouveau modeset=0

"

5th (add i386 if already added then skip): sudo dpkg --add-architecture i386 && sudo apt update && sudo apt upgrade -y nvidia-legacy-390xx-driver

6th: Run: nvidia-detect (response was that I need a nvidia-legacy-390xx-driver so let it be.

7th: sudo apt install nvidia-legacy-390xx-driver

8th: sudo apt -y install bumblebee-nvidia primus primus-libs:i386 mesa-utils

9th: We need to create a virtual card in system that action will fix situation with "can't load secondary GPU driver"

9a: wget https://sourceforge.net/projects/virtualgl/files/2.6.2/virtualgl_2.6.2_amd64.deb

9b: sudo dpkg -i ./virtualgl_*.deb

9c (optional): if you have some pkgs pending that install 'em: sudo apt -f install

10th: Make a symlink for VirtualGL: sudo ln -s /opt/VirtualGL/bin/glxspheres64 /usr/local/bin/

11th: sudo adduser $USER bumblebee

12th: Restart system,

13th: (Optional) if errors on bumbleblee start "sudo apt-get install xserver-xorg-input-mouse"

14th: Make sure in /etc/bumblebee/bumblebee.conf

"

PMMethod=none

AlwaysUnloadKernelDriver=false

"

15th: Make sure in /etc/bumblebee/xorg.conf.nvidia

Append to the file

"

Section "Screen"

Identifier "Default Screen"

Device "DiscreteNvidia"

EndSection

"

and uncomment

BusID "PCI:01:00:0"


16th: Restart Bumbleblee service

/etc/init.d/bumblebeed restart

/etc/init.d/bumblebeed status


17th: Add in cat /usr/share/applications/nvidia-settings.desktop

"

Exec=optirun nvidia-settings -c :8

"

and logout and login to restart X server


18th: Run the below ( prepend optirun or primusrun to get graphic card details )


glxinfo | grep "OpenGL renderer string"

glxinfo|egrep "OpenGL vendor|OpenGL renderer"

optirun glxinfo|egrep "OpenGL vendor|OpenGL renderer"

glxheads


19th: Use primusrun rather than optirun. ( Reason Below )

Bumblebee is the current workaround for using Nvidia optimus with linux.

At the heart of bumblebee has been VirtualGL which is now being superseded by Primus.

If you have Bumblebee you'll know that to use your discrete card you need to invoke optirun application from the command line and then after a short wait your application runs using the VirtualGL backend.

Unfortunately the VirtualGL backend does not play nice with some applications, particularly Wine and Crossover.

Primus is the replacement of optirun and it all it means to the end user is that instead of using optirun you now invoke an app with primusrun application.

The developers of Bumbleebee highly recommend primus over VirtualGL because there are a host of benefits.

One benefit is that it works perfectly with Wine and Crossover meaning You can play your Windows games on your discrete card instead of having to use your integrated one and has substantially better performance too.

20th: Enjoy!!

Installing steam - https://wiki.debian.org/Steam

# dpkg --add-architecture i386

# apt update

# apt install steam

#sudo apt install nvidia-vulkan-common nvidia-vulkan-icd nvidia-vulkan-icd:i386

From steam Library - select the game ---> manage ---> properties ---> set launch options to "primusrun %command%" 


