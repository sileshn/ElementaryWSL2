# ElementaryWSL2
ElementaryOS on WSL2 (Windows 10 FCU or later) based on [wsldl](https://github.com/yuk7/wsldl).

[![Screenshot-2022-07-26-062453.png](https://i.postimg.cc/JzrWpkcS/Screenshot-2022-07-26-062453.png)](https://postimg.cc/SJv5sjPG)
[![Github All Releases](https://img.shields.io/github/downloads/sileshn/ElementaryWSL2/total.svg?style=flat-square)](https://github.com/sileshn/ElementaryWSL2/releases)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square)](http://makeapullrequest.com) [![License](https://img.shields.io/github/license/sileshn/ElementaryWSL2.svg?style=flat-square)](https://github.com/sileshn/ElementaryWSL2/blob/main/LICENSE)

## Features and important information
ElementaryWSL2 has the following features during the installation stage.
* Increase virtual disk size from the default 256GB
* Create a new user and set the user as default
* ElementaryWSL2 Supports systemd natively if you are running wsl v0.67.6 (more details [here](https://devblogs.microsoft.com/commandline/systemd-support-is-now-available-in-wsl/)) and above. For earlier versions of wsl, systemd is supported using diddledani's [one-script-wsl2-systemd](https://github.com/diddledani/one-script-wsl2-systemd). This is done automatically during initial setup.
* ElementaryWSL2 includes a wsl.conf file which only has section headers. Users can use this to configure the distro to their liking. You can read more about wsl.conf and its configuration settings [here](https://docs.microsoft.com/en-us/windows/wsl/wsl-config).

## Requirements
* For x64 systems: Version 1903 or higher, with Build 18362 or higher.
* For ARM64 systems: Version 2004 or higher, with Build 19041 or higher.
* Builds lower than 18362 do not support WSL 2.
* If you are running Windows 10 version 2004 or higher, you can install all components required to run wsl2 with a single command. This will install ubuntu by default. More details are available [here](https://devblogs.microsoft.com/commandline/install-wsl-with-a-single-command-now-available-in-windows-10-version-2004-and-higher/).
	```cmd
	wsl.exe --install
	```
* If you are running Windows 10 lower then version 2004, follow the steps below. For more details, check [this](https://docs.microsoft.com/en-us/windows/wsl/install-manual) microsoft document.
	* Enable Windows Subsystem for Linux feature.
	```cmd
	dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
	```
	* Enable Virtual Machine feature
	```cmd
	dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
	```
	* Download and install the latest Linux kernel update package from [here](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi).

## Install
* Make sure all the steps mentioned under "Requirements" are completed.
* [Download](https://github.com/sileshn/ElementaryWSL2/releases/latest) installer zip
* Extract all files in zip file to same directory
* Set version 2 as default. Note that this step is required only for manual installation.
  ```dos
  wsl --set-default-version 2
  ```
* Run Elementary.exe to extract rootfs and register to WSL

**Note:**
Exe filename is using the instance name to register. If you rename it you can register with a diffrent name and have multiple installs.

## How-to-Use(for Installed Instance)
#### exe Usage
```
Usage :
    <no args>
      - Open a new shell with your default settings.
        Inherit current directory (with exception that %%USERPROFILE%% is changed to $HOME).

    run <command line>
      - Run the given command line in that instance. Inherit current directory.

    runp <command line (includes windows path)>
      - Run the given command line in that instance after converting its path.

    config [setting [value]]
      - `--default-user <user>`: Set the default user of this instance to <user>.
      - `--default-uid <uid>`: Set the default user uid of this instance to <uid>.
      - `--append-path <true|false>`: Switch of Append Windows PATH to $PATH
      - `--mount-drive <true|false>`: Switch of Mount drives
      - `--wsl-version <1|2>`: Set the WSL version of this instance to <1 or 2>
      - `--default-term <default|wt|flute>`: Set default type of terminal window.

    get [setting [value]]
      - `--default-uid`: Get the default user uid in this instance.
      - `--append-path`: Get true/false status of Append Windows PATH to $PATH.
      - `--mount-drive`: Get true/false status of Mount drives.
      - `--wsl-version`: Get the version os the WSL (1/2) of this instance.
      - `--default-term`: Get Default Terminal type of this instance launcher.
      - `--wt-profile-name`: Get Profile Name from Windows Terminal
      - `--lxguid`: Get WSL GUID key for this instance.

    backup [file name]
      - `*.tar`: Output backup tar file.
      - `*.tar.gz`: Output backup tar.gz file.
      - `*.ext4.vhdx`: Output backup ext4.vhdx file. (WSL2 only)
      - `*.ext4.vhdx.gz`: Output backup ext4.vhdx.gz file. (WSL2 only)
      - `*.reg`: Output settings registry file.

    clean
      - Uninstall that instance.

    help
      - Print this usage message.
```

#### Just Run exe
```cmd
>Elementary.exe
[root@PC-NAME user]#
```

#### Run with command line
```cmd
>Elementary.exe run uname -r
4.4.0-43-Microsoft
```

#### Run with command line with path translation
```cmd
>Elementary.exe runp echo C:\Windows\System32\cmd.exe
/mnt/c/Windows/System32/cmd.exe
```

#### Change Default User(id command required)
```cmd
>Elementary.exe config --default-user user

>Elementary.exe
[user@PC-NAME dir]$
```

#### Set "Windows Terminal" as default terminal
```cmd
>Elementary.exe config --default-term wt
```

## How to setup

ElementaryWSL2 will ask you to create a new user during its first run. If you chose to create a new user during initial setup, the steps below are not required unless you want to create additional users.
```dos
passwd
useradd -m -g users -G sudo -s /bin/bash <username>
echo "%sudo ALL=(ALL) ALL" >/etc/sudoers.d/sudo
passwd <username>
exit
```

You can set the user you created as default user using 2 methods.

Open Elementary.exe, run the following command (replace username with the actual username you created).
```dos
sed -i '/\[user\]/a default = username' /etc/wsl.conf
```

Shutdown and restart the distro (this step is important).

(or)

Execute the command below in a windows cmd terminal from the directory where Elementary.exe is installed.
```dos
>Elementary.exe config --default-user <username>
```

## How to uninstall instance
```dos
>Elementary.exe clean

```

## How to backup instance
export to backup.tar.gz (WSL1 or 2)
```cmd
>Elementary.exe backup backup.tar.gz
```
export to backup.ext4.vhdx.gz  (WSL2 only)
```cmd
>Elementary.exe backup backup.ext4.vhdx.gz
```

## How to restore instance

There are 2 ways to do it. 

Rename the backup to rootfs.tar.gz and run Elementary.exe

(or)

.tar(.gz)
```cmd
>Elementary.exe install backup.tar.gz
```
.ext4.vhdx(.gz)
```cmd
>Elementary.exe install backup.ext4.vhdx.gz
```

You may need to run the command below in some circumstances.
```cmd
>Elementary.exe --default-uid 1000
```

## How to build

#### Prerequisites

Docker, tar, zip, unzip, bsdtar need to be installed.

```dos
git clone git@gitlab.com:sileshn/ElementaryWSL2.git
cd ElementaryWSL2
make

```
Copy the ElementaryWSL2.zip file to a safe location and run the command below to clean.
```dos
make clean

```

## How to run docker in ElementaryWSL2 without using docker desktop.

Delete older versions of docker if installed.
```dos
sudo apt-get remove docker docker-engine docker.io containerd runc

```

Execute the commands below to install docker.
```dos
sudo apt -y install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(. /etc/os-release; echo "$UBUNTU_CODENAME") stable"
sudo apt update
sudo apt install docker-ce
sudo usermod -aG docker $USER
```

Follow [this](https://blog.nillsf.com/index.php/2020/06/29/how-to-automatically-start-the-docker-daemon-on-wsl2/) blog post for further details on how to set it up. Alternatively, if using systemd, use the commands below to setup and reboot.
```dos
sudo systemctl start docker.service
sudo systemctl enable docker.service
sudo usermod -aG docker $USER
```
