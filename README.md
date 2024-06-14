# build-a-custom-rom
Guide To Build a Custom Rom from scatch for Realme 7 device Common (MT6785) By using Lineage OS 21 Android 14


# Building a Custom ROM

This is a very beginner friendly tutorial on how to build a Custom ROM for your device. For this tutorial, I will be taking LineageOS ROM (Easy to Build and Beginner Friendly Rom ) for Realme 7  as an example.
## Requirements

- Your device with USB cable (Realme 7 for example)
- x64-bit powerful Linux PC or server. Recommended: 4 cores - 8 threads or more with 64GB RAM, (32GB RAM + 16GB or more ZRAM should also do the job). Just 32GB RAM for Android 14 onwards will lead to the build being killed. Storage of 350GB or more. I will be considering Ubuntu (mentioned by google ) to build in this tutorial.
- Unlimited internet connection
- Basic Git and GitHub knowledge. You will need some advanced knowledge too which you can learn along with time.

## Let's begin
## 1. Download and install platform-tools

Download the platform-tools for Linux from here by using the terminal.
```bash
cd ~/
wget https://dl.google.com/android/repository/platform-tools-latest-linux.zip
```
Use this command to unzip it:
```bash
unzip platform-tools-latest-linux.zip -d ~
```
Now you have to add adb and fastboot to your PATH. In the same terminal enter this:
```bash
cd ~/
nano ~/.profile
```
Just enter the following text at the bottom of the file that opens up, save it and close.
```bash
# add Android SDK platform tools to path
if [ -d "$HOME/platform-tools" ] ; then
    PATH="$HOME/platform-tools:$PATH"
fi
```
Key combination to save file and exit in nano editor is: (Ctrl + O) + (Enter) + (Ctrl + X). 
Then, run this to update your environment.
```bash
source ~/.profile
```
## 2. Install the build packages
Several packages are needed to build a Custom ROM and all those can be installed easily by using Akhil Narang's scripts. Run this:
```bash
cd ~/
git clone https://github.com/akhilnarang/scripts
cd scripts
./setup/android_build_env.sh
```
## 3. Create the directories
You’ll need to set up some directories in your build environment
To create them:
```bash
mkdir -p ~/bin
mkdir -p ~/android
```
The ~/bin directory will contain the git-repo tool (commonly named “repo”) and the ~/android directory will contain the source code of the Custom ROM


## 4. Install the repo command
Enter the following to download the repo binary and make it executable (runnable):
```bash
curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
chmod a+x ~/bin/repo
```
## 5. Configure git
Given that repo requires you to identify yourself to sync Android, run the following commands to configure your git identity:
```bash
git config --global user.email "you@example.com"
git config --global user.name "Your Name"
```
## 6. Initialize the source repository and download
Get into the android directory that we created and initialize the branch of repo that you wish to build. You can find the link to initialize the repo in the manifest of the particular ROM. As I am considering LineageOS ROM for example, you can look at their manifest from [here](https://github.com/LineageOS/android/tree/lineage-21.0). LineageOS calls this particular repo as "android" but for other ROMs it is usually called as "manifest". Just for reference you can look at the manifest of Pixel Experience from [here](https://github.com/PixelExperience/manifest).
```bash
cd ~/android
repo init -u https://github.com/LineageOS/android.git -b lineage-21.0 --git-lfs
```
Download the source code by this command:
```bash
repo sync -c -j$(nproc --all) --force-sync --no-clone-bundle --no-tags
```
Note that repo sync will take time depending on your internet connection.

## 7. Prepare the device-specific code
To build a ROM for a specific device, we usually need three device trees i.e., device trees, kernel and vendor trees. 
For Realme  7 we have 2, 1 and 2 directories respectively in device trees, kernel and vendor tree and Oplus Hardware . The number of directories might vary from device to device. You can easily find that by checking your device specific trees from Lineage OS GitHub (if available). Note that device codename for Realme 7 is salaa.  You need to clone all the directories below from GitHub into your source code. For Realme  7 the directories are:
## Device trees
- [android/device/realme/salaa](https://github.com/realme-mt6785-devs/android_device_realme_salaa.git)
- [android/device/realme/RM6785-common](https://github.com/realme-mt6785-devs/android_device_realme_RM6785-common.git) <br /> <br />
Clone them to the respective directories. For example, clone "android_device_realme_salaa" in "android/device/realme/salaa" directory. The command for this example would be:
```bash
git clone https://github.com/realme-mt6785-devs/android_device_realme_salaa.git android/device/realme/salaa
```

## Kernel
- [android/kernel/realme/mt6785](https://github.com/realme-mt6785-devs/android_kernel_realme_mt6785.git)
## Vendor Trees
- [android/vendor/realme/salaa](https://github.com/realme-mt6785-devs/android_vendor_realme_salaa.git)
- [android/vendor/realme/RM6785-common](https://github.com/realme-mt6785-devs/proprietary_vendor_realme_RM6785-common.git)
## Other necessary repositories
There might be other repositories required to build a ROM. For example in LineageOS 21 (Android 14) for Realme 7, couple of extra repositories are used, which are: [android/hardware/oplus](https://github.com/LineageOS/android_hardware_oplus.git) 
## 8.Building the ROM

Before Building We have to make changes in device tree In AndroidProducts.mk and overlay if you want to make changes in color
Next we can start building the ROM by using the following commands. These commands might be different for different ROMs. Check the ROM manifest for exact commands.
The format for LineageOS is:
```bash
source build/envsetup.sh
breakfast device_codename
export USE_CCACHE=1
export CCACHE_EXEC=/usr/bin/ccache
ccache -M 50G
croot
brunch device_codename | tee log.txt
```
Above mentioned 3rd, 4th and 5th lines are only for first build. It will set ccache for building ROMs."50G" is to metion the amount of ccache you are allocating to build the ROM. 30 to 50 GB should be enough for building ROM for one device. ccache will increase the speed of building the ROM.
For Realme  7 the following commands to be used for first build.
```bash
source build/envsetup.sh
breakfast salaa
export USE_CCACHE=1
export CCACHE_EXEC=/usr/bin/ccache
ccache -M 50G
croot
brunch salaa | tee log.txt
```
For second build onwards:
```bash
source build/envsetup.sh
breakfast salaa
croot
brunch salaa | tee log.txt
```
Now the building time might vary depending on your raw PC power. First build usually takes long time. Second buid onwards, time will significantly get reduced. To clean build a ROM for later builds use this command after calling the "lunch" command:
```bash
make clobber
```
For a dirty build, use:
```bash
make installclean
```
Dirty build is recommended for faster builds (unless no major change is done).

## Credits
- [Lineage OS](https://github.com/LineageOS)
- [MT6785-DEvs](https://github.com/realme-mt6785-devs)
- [Akhil Narang](https://github.com/akhilnarang)
