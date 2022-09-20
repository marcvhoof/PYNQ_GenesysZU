![pynq_logo](https://github.com/Xilinx/PYNQ/raw/master/logo.png)

![python](https://github.com/Xilinx/PYNQ/workflows/Python/badge.svg)

PYNQ is an open-source project from Xilinx that makes it easy to design embedded systems with Zynq All Programmable Systems on Chips (APSoCs). Using the Python language and libraries, designers can exploit the benefits of programmable logic and microprocessors in Zynq to build more capable and exciting embedded systems.
PYNQ users can now create high performance embedded applications with
-	parallel hardware execution
-	high frame-rate video processing
-	hardware accelerated algorithms
-	real-time signal processing
-	high bandwidth IO
-	low latency control

See the <a href="http://www.pynq.io/" target="_blank">PYNQ webpage</a> for an overview of the project, and find <a href="http://pynq.readthedocs.io" target="_blank">documentation on ReadTheDocs</a> to get started. 

## Before  you start
First off, realise compiling is going to take a long time (can be days, depending on your setup). The Virtualbox setup is very slow. It sometimes randomly locks up; needing a restart. However - it does work with the fixes applied in this fork. Building these images with new(er) tools, runs you into a lot of compatibility issues - I tried. I have modified the Vagrant file to include more memory (16G), use 4 cores and have a bigger workspace drive (180GB). Also a swapon file is created by default (4G). 

## Installing Vagrant and Virtualbox on a recent Ubuntu
```
sudo apt-get install vagrant virtualbox
vagrant plugin install vagrant-vbguest
mkdir <PYNQ repository>
cd <PYNQ repository>
git clone https://github.com/marcvhoof/PYNQ_GenesysZU
cd PYNQ_GenesysZU
vagrant up bionic
```
Wait until vagrant is completely ready with executing scripts, ignore the virtual box in the mean time. When 'bionic: Running: inline script' is reached, close the virtual box screen (by shutting down) and: 
```
vagrant reload bionic
```
Login with ' vagrant'  as the password. A few more scripted actions will start. Wait untill 'bionic: flag to force provisioning. Provisioners marked to run always will still run.' is reached. 

From this point on, start using the reload command to reboot the machine - when necessary.

## Some advice
Good to know: 
- You can change the screen size and resolution in Ubuntu itself simply by going to Settings, searching for 'Displays'. 
- Enabling 'Shared Clipboard' in Virtualbox is quite convenient for copy/paste across machines. The system reboots to make it possible.
- gnome-system-monitor (```sudo apt-get install gnome-system-monitor```) helps you conveniently track issues with memory/swap file in the VM. 

## Download Petalinux 2020.2 and Vitis 2020.2
I recommend downloading Petalinux seperate and the webinstaller for Vitis 2020.2. Create a directory under /workspace, e.g. /tools/Xilinx/Petalinux. Petalinux is finished downloading earlier - so start installing that one first. Install Vitis. I only selected the MPSOC ultrascale+ boards. I also left in DocNav. Install the y2k22_patch-1.2.zip fix, by unzipping it in the /workspace/tools/Xilinx directory, and running "python3 '/workspace/tools/Xilinx/y2k22_patch/patch.py'" in /workspace/tools/Xilinx.

## Within the VM
### Ensure that the swap file works. 
You can see it in the gnome-system-monitor if it already works. If not, you can manually enable it by running
```
sudo swapon /swapfile 
```
and add it automatically to boot up by 
```
sudo echo "/swapfile swap swap defaults 0 0" | sudo tee -a /etc/fstab
```

### Clone the repository again in workspace
```
cd /workspace
git clone https://github.com/marcvhoof/PYNQ_GenesysZU
cd PYNQ_GenesysZU
export PATH="/opt/crosstool-ng/bin:/opt/qemu/bin:$PATH"
source /workspace/tools/Xilinx/Vitis/2020.2/settings64.sh
source /workspace/tools/Xilinx/Petalinux/settings.sh
petalinux-util --webtalk off
cd sdbuild
```
## Fix the broken links
```
mkdir -p build/gcc-mb/.build/tarballs
wget -P /workspace/PYNQ_GenesysZU/sdbuild/build/gcc-mb/.build/tarballs/ http://mirror.sobukus.de/files/src/isl/isl-0.20.tar.gz
wget -P /workspace/PYNQ_GenesysZU/sdbuild/build/gcc-mb/.build/tarballs/ http://mirror.sobukus.de/files/src/isl/isl-0.20.tar.xz
wget -P /workspace/PYNQ_GenesysZU/sdbuild/build/gcc-mb/.build/tarballs/ https://github.com/libexpat/libexpat/releases/download/R_2_4_1/expat-2.4.1.tar.gz
wget -P /workspace/PYNQ_GenesysZU/sdbuild/build/gcc-mb/.build/tarballs/ https://github.com/libexpat/libexpat/releases/download/R_2_4_1/expat-2.4.1.tar.bz2
```

## Start making
```
make
```
Once it fails due to download errors because of the broken tar links, just clean the ' gcc-mb' manually by deleting all other files and directories under 'PYNQ_GenesysZU/sdbuild/build/gcc-mb' until only the '.build' directory is left over and restart make. Note that .build is hidden, so enable showing hidden files.  

## A few things that might be relevant to you
- make -j is not viable
- Only Pynq-Z2 is necessary for building the toolchain. It is also necessary for other boards. So leave it in. 
- If you need the HDMI license, make sure you enable it beforehand - as it stops the building process.
- I use sudo make clean to clean
- Somehow, sometimes the wrong qemu is used, giving a variety of errors (e.g. recipe for target 'qemu_check_arm' failed / recipe for target ‘checkenv’ failed). This fixes it: export PATH="/opt/crosstool-ng/bin:/opt/qemu/bin:$PATH". 

## Support

Please ask questions on the <a href="https://discuss.pynq.io" target="_blank">PYNQ support forum</a>.

## Licenses

**PYNQ** License: [BSD 3-Clause License](https://github.com/Xilinx/PYNQ/blob/master/LICENSE)

**Xilinx Embedded SW** License: [Multiple License File](https://github.com/Xilinx/embeddedsw/blob/master/license.txt)

**Digilent IP** License: [MIT License](https://github.com/Xilinx/PYNQ/blob/master/THIRD_PARTY_LIC)

## SDBuild Open Source Components

**License and Copyrights Info** [TAR/GZIP](https://bit.ly/pynq_license_2_7)

**Open Components Source Code** [TAR/GZIP](https://bit.ly/pynq_opencomponents_2_7)
