
# install packages from repo or remote server

For Edge(or OpenWRT), the packages are in format of `.ipk` file

## update the package list

Navigate to main menu -> `System` -> `Software`, click button `Update lists` if present

It will take a while to download a package list from Internet, so make sure the Edge has the internet access via either WAN or WIFI

## install packages by providing package name

Now just type a package name in `Download and install package` text box, click OK


## install packages by providing URL link

Paste the package link in `Download and install package` text box, click OK.

**Important** I've tested `Http` and `Https`, but it seems only `http` urls are accepted

Here is an example: https://s3.amazonaws.com/bc-globe/packagetest/luci-app-bluecats-core_git-18.283.19260-87ffb4b-1_ar71xx.ipk, by applying this url, the luci-app-bluecats-core package will be installed or updated.
