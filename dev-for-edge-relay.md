# OS
Ubuntu 16.04 Desktop, **DON'T** take 18.04 which is not compatible with Openwrt development.

I recommend to setup a virtual machine for this exclusive development purpose.

This artical is based on the assumption that you are running Ubuntu 16.04 from scratch in VirtualBox.

# Dev essentials
```
sudo apt-get update
sudo apt-get install subversion build-essential git-core libncurses5-dev zlib1g-dev gawk flex quilt libssl-dev xsltproc libxml-parser-perl mercurial bzr ecj cvs unzip git wget texinfo
```
Now make a folder for all the work
```
mkdir -p ~/devhome
cd ~/devhome
```
`devhome` would be the working directory or `${WORK_DIR}` in this article. 

# Download BlueCats Luci(which is a private repo)
```
git clone -b dev https://github.com/bluecats/luci-bluecats.git
```

# Download prebuilt SDK
Once you are on branch `dev` of repo `luci-bluecats.git`, you'll see the SDK download link in `README.md`, now download it into `${WORK_DIR}` and unzip
```
tar -zxvf sdk_cc.tar.bz2
```

# Download prebuilt ImageBuilder(private repo)
```
git clone https://github.com/bluecats/openwrt-imagebuilder-chaos-calmer.git ic_bluecats
```

Now `${WORK_DIR}` looks like this:
```
~/devhome 
        /sdk_cc 
        /luci_bluecats 
        /ic_bluecats 
```

# Update Configurations for the SDK
```
cd sdk_cc
nano feeds.conf.default
```
Change the line `src-link luci /home/chq/dev/luci-bluecats` to your own path to `luci-bluecats`, then update feeds:
```
./scripts/feeds update -a
./scripts/feeds install -a
```

# Update Configuration for the ImageBuilder
```
mkdir -p ~/code
cd ~/code
ln -s ../devhome/sdk_cc OpenWrt-SDK-Chaos
cd ~/devhome
```

# Build luci-app-bluecats-core
```
make package/luci-app-bluecats-core/clean
make package/luci-app-bluecats-core/compile
```

# Build all related apps
Since our firmware includes not only bluecats apps, but also luci, luci-admin, luci-theme, we must rebuild all related apps for building a image file.
Take a look at the shell script `sdk_cc/runedge` that explans the process.

You may call `./runedge all` to get an entire rebuild.
```
cd ~/devhome/sdk_cc
./runedge all
```

# Update firmware UID and version string
Before an entire rebuild, there are 3 places of code need to be changed:

 - The firmware UID `deviceFWUID` in `luci-bluecats/applications/luci-app-bluecats-core/src/config.c`, 
 - The version string `deviceFwVersion` in `luci-bluecats/applications/luci-app-bluecats-core/src/config.c`
 - `luciname` in `luci-bluecats/modules/luci-base/src/mkversion.sh`

*important*
- Find the next avaliable Firmware UID on Animalcontrol by click `Add Firmware` in `Device Config`, you don't need to add any file at this stage, just write down the `Fireware UID`, and click `Cancel`
- Keep in mind that the building system will read the current git commit hash as one part of the version dispayed on any LuCI web page, so before starting to rebuild all, make sure all changes have been committed or reset, otherwise the version string is not trackable.

# Build image
```
cd ~/devhome/ib_bluecats
./build_defaut.sh
```
There are some `.sh` shell scripts to make the work easier.

| script name | description |
| ----------- | ----------- |
|build_default.sh|build image with bluecats-core app|
|build_default_le.sh|build image with bluecats-core and bluecats-core-le app|
|build_hs.sh|build image with bluecats-core app and HS's configurations|
build_mifi.sh|build image as GL-MIFI with bluecats-core app|

# Validate the image file
## re-calculate md5
Once building completed, check the md5sum of the binary file:
```
cd bin/ar71xx
cat md5sum
md5sum openwrt-ar71xx-generic-gl-mifi-squashfs-sysupgrade.bin
```

## flash into EdgeRelay
 - Open LuCI web `http://192.168.8.1`
 - Go to `System` -> `Backup/Flash Firmware`
 - Uncheck `Keep settings`, choose the newly built firmware bin file, click `Flash image...`, then start to flash
 
Once flash finished or after 3 minutes, reopen `http://192.168.8.1` to check the version string on the homepage, it looks like this:
```
OpenWrt Chaos Calmer 15.05.1 r48532 / BlueCats Edge-0.16.48 LuCI core-forwards branch (git-18.269.42020-7ff99c3)
```
Please notice that the last segment(`7ff99c3`) in the above version string is the git commit hash which should be same with the git HEAD of the branch of your luci code `luci-bluecats`

# Upload fireware to the AnimalControl
 - Make sure the firmware's fwUID is matched with the one displayed in `Add firmware` screen.
 - In the `Description` text field, add `git-1111111;md5-2222222222222222222` where includes git commit hash and the md5 summary.
 - Choose the bin file and upload
 
 # Verify md5 sum
  - Download the bin file from AnimalControl
  - Run `md5 0.5.8.bin` and compare the output with the md5 sum in `Description`

