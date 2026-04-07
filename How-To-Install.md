## Note. The product isn't fully finished, not completely ready and everything that happens is at users own risk

This is the pipeline that will be installed and functional after following the steps:

Suricata -> Wazuh Agent -> Wazuh Manager -> Wazuh Alerts Shipper -> Wazuh Indexer

### Pre-requirements and troubleshooting

**Req**

- A computer
- Debian 13 - Not tested in other versions or distros. VM recommended
- Being Sudo
- Able to connect to the Internet (you should be if you're reading this)
- At least 25GB of memory, +8gb RAM preferred as well
- **We will update the memory and RAM requirements after more testing**

**Troubleshooting**

1. Make sure your Debian is well set beforehand:

If some packets are missing, simple Googling will usually do the trick, f.ex the right packages for the apt-manager

```
/etc/apt/sources.list

deb http://deb.debian.org/debian/ trixie main non-free-firmware contrib non-free
deb-src http://deb.debian.org/debian/ trixie main non-free-firmware contrib non-free

deb http://security.debian.org/debian-security trixie-security main non-free-firmware contrib non-free
deb-src http://security.debian.org/debian-security trixie-security main non-free-firmware contrib non-free

deb http://deb.debian.org/debian/ trixie-updates main non-free-firmware contrib non-free
deb-src http://deb.debian.org/debian/ trixie-updates main non-free-firmware contrib non-free
```

2. User not in sudo group
 ```su``` -> ```sudo adduser (youruser) sudo``` -> Reboot

3. No Internet... Get one

4. If got any more issues, please inform us

# How to install?

1. Create a new VM or use an existing one. (Debian 13)

2. Download the zip file

[soc-project-v4.zip](https://github.com/user-attachments/files/26421526/soc-project-v4.zip)




3. Navigate to Downloads and look for the file

<img width="735" height="301" alt="kuva" src="https://github.com/user-attachments/assets/640f9480-8673-4f41-8bcc-154deb6fc857" />


4. Download zip ```sudo apt-get install zip```

5. Unzip the file ```unzip soc-project.zip```

6. Navigate to the unzipped file ```cd soc-project```, ```ls``` to list the files

7. Use ```sudo bash install.sh``` to launch it

8. Let it do its magic. This takes around 2-10 minutes. Grab a coffee.

 <img width="741" height="484" alt="kuva" src="https://github.com/user-attachments/assets/d328d9b8-a918-4048-b2df-b04cb162458d" />

9. You should get this once it's finished. If errors, please report them!
   
<img width="1311" height="169" alt="kuva" src="https://github.com/user-attachments/assets/a22330e2-3e34-403f-89ec-3f60bf242e53" />

11. When it's done, navigate to Firefox or w.e Browser

13. Type into the URL: localhost

<img width="1501" height="198" alt="kuva" src="https://github.com/user-attachments/assets/cc7f5f88-c062-4912-9127-3272325f499a" />

Accept the risks

<img width="924" height="730" alt="kuva" src="https://github.com/user-attachments/assets/93b5b4b3-e649-43c4-a87d-a1c90b6816a6" />

14. user ```admin``` pass ```admin```

15. NOTE: IF Wazuh sends an error, change the address to /app/home/ - this tends to happen.

<img width="1660" height="991" alt="kuva" src="https://github.com/user-attachments/assets/8d1f1653-a383-4fea-a97f-04f70b9e106e" />

<img width="1620" height="859" alt="kuva" src="https://github.com/user-attachments/assets/d2d65d77-b345-40b1-962a-6f5590f8d773" />

16. Navigate here:

<img width="1657" height="646" alt="kuva" src="https://github.com/user-attachments/assets/5f5bf856-6dcf-460a-8536-4afe20673e8b" />

17. Some traffic should be visible, if not reset a couple of times

18. If still not, run this ```curl http://testmynids.org/uid/index.html```

19. Now you should have something in here:

<img width="1650" height="445" alt="kuva" src="https://github.com/user-attachments/assets/87d019e8-88e4-48e4-b100-14de7d542638" />

20. Your setup should now work and function properly, hooray!

21. Be creative and safe! The fastest test is to use this command ```curl http://testmynids.org/uid/index.html```which is widely used for testing IDS - systems. This SHOULD come up in Wazuh

22.  You can use f.ex ```tcpreplay```and a file from https://github.com/markofu/pcaps/blob/master/PracticalPacketAnalysis/ppa-capture-files/portscan.pcap. This will be in upcoming instructions - still working on it
