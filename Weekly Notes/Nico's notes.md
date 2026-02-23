## Weeks 5 & 6

I was supposed to be installing the whole idea of ours, 
test out the first working model and figure out how to Mount the ISO so that we can share it when the project is finished...

- I ran into a few problems during the installation - phase.
1st was finding the right installers for the actual software, f.ex Suricata.
After searching for a while, managed to find them. Install them, test out that they work individually. It was time to see if they work together but I ran into another trouble.

2nd issue was with the VM itself - which is what I'm troubleshooting now with a good friend of mine, Copilot.

![onkelma](https://github.com/user-attachments/assets/378f402f-7c77-4cc7-b165-e3edebb6f638)

Could be a lack of memory.
I believe this was caused when I started to install Wazuh, and while it was installing, it got stuck and the computer decided that it's time to recharge and shut down completely.

Even the GUI won't start, so time to start debugging with CLI.

Decided not worth the time, doing a fresh install even thought I decided to do a fresh one with installation guides

### Installing

Suricata:

```sudo apt-get install suricata```

Zeek based on this source:

https://github.com/zeek/zeek/wiki/Binary-Packages

For Debian 13:

```
echo 'deb http://download.opensuse.org/repositories/security:/zeek/Debian_13/ /' | sudo tee /etc/apt/sources.list.d/security:zeek.list
curl -fsSL https://download.opensuse.org/repositories/security:zeek/Debian_13/Release.key | gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/security_zeek.gpg > /dev/null
sudo apt update
sudo apt install zeek
```
