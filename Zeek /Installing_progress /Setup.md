# Zeek Installation

## Quick Introduction

Zeek (formerly Bro) is a free and open-source platform for network security monitoring. It is a powerful passive network traffic analyzer that investigates suspicious or malicious activity

## Setup

Zeek is a monitoring tool that can be installed in many ways. Here are two of those


### APT Package

First thing you need to do is add Zeek repository to your Ubuntu system by using this command (Ubuntu 24.04):


```
echo 'deb http://download.opensuse.org/repositories/security:/zeek/xUbuntu_24.04/ /' | sudo tee /etc/apt/sources.list.d/security:zeek.list
```

Next thing you need to do is download and add a GPG key for your Zeek repository ->

```
curl -fsSL https://download.opensuse.org/repositories/security:zeek/xUbuntu_24.04/Release.key | \
gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/security_zeek.gpg > /dev/null
```

After this just run sudo apt update and you should see multiple zeek options

```
sudo apt install zeek -y
```
After the installation completes, add the directory /opt/zeek/bin to your system’s PATH by updating your ~/.bashrc file. Once you’ve made the change, reload the ~/.bashrc file so the new PATH takes effect. You`ll then be able to run Zeek commands directly from your terminal.

```
echo "export PATH=$PATH:/opt/zeek/bin" >> ~/.bashrc
source ~/.bashrc
```

