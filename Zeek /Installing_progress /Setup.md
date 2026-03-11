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

### Zeek from source

Firstly you need to install the packages required to run Zeek corrcetly, without these dependencies, the build would fail because essential components would be missing.

```
sudo apt update

sudo apt install -y \
cmake make gcc g++ flex bison \
libpcap-dev libssl-dev python3-dev \
swig zlib1g-dev
```
After you got those packages you can download Zeek from source code

```
wget https://download.zeek.org/zeek-6.0.0.tar.gz
```

Then you need to unpack the source code and move it into a folder so you can build it

Extract the archive:

```
tar -xvf zeek-6.0.0.tar.gz
cd zeek-6.0.0
```

Then run the configure script:

```
./configure
```

or 

```
./configure --prefix=/opt/zeek
```

It is installed by default in /usr/local/zeek

Then you can compile it by using (it takes time)

```
make -j$(nproc)
```

After it is complete

```
sudo make install
```

Last thing is optional, because you need to add Zeek to yout PATH (if only needed)

```
echo 'export PATH=/usr/local/zeek/bin:$PATH' >> ~/.bashrc
source ~/.bashrc
```

Verify it by using zeek --version


<img width="331" height="38" alt="Näyttökuva 2026-03-08 kello 22 27 43" src="https://github.com/user-attachments/assets/49d053b1-1996-4376-bb9f-71f4d2f01975" />


## Configuartion

After installation we need to configure Zeek so it works correctly.

```
sudo nano /opt/zeek/etc/networks.cfg
```
And add your IP there 

<img width="600" height="47" alt="Näyttökuva 2026-03-08 kello 23 02 17" src="https://github.com/user-attachments/assets/91b774b0-b22d-4e3e-99fd-993cb756ac0e" />

Next step is to configure node.cfg

```
sudo nano /opt/zeek/etc/node.cfg
```

<img width="607" height="123" alt="Näyttökuva 2026-03-08 kello 23 07 33" src="https://github.com/user-attachments/assets/54c4a1da-8f64-4f55-9811-d95fded11d1f" />

After you saved your configurations you can deploy it by using this command

```
sudo /opt/zeek/bin/zeekctl deploy
```

You should see something like this:

<img width="859" height="313" alt="Näyttökuva 2026-03-11 kello 13 15 13" src="https://github.com/user-attachments/assets/356f7005-6875-4d8b-aa0e-37f71291d48c" />

There is also a zeek status command, which tells you if everything is ok.

```
sudo /opt/zeek/bin/zeekctl status
```

<img width="748" height="68" alt="Näyttökuva 2026-03-11 kello 13 17 16" src="https://github.com/user-attachments/assets/0c90ea08-c017-4d4f-a420-dc73acf4249c" />

How you can check your logs?

```
ls /opt/zeek/logs/current
```

<img width="1289" height="65" alt="Näyttökuva 2026-03-11 kello 13 28 32" src="https://github.com/user-attachments/assets/4ae2e4d1-38d2-40dc-ba0a-e1328b597f9a" />

If you see this kind of files, Zeek is active and writing logs


### Aftermath

Your IDS now has:

- Zeek installed
- Interface configured
- Local network defined
- Sensor running
- Logs being generated

So the sensor part of your IDS is operational.

