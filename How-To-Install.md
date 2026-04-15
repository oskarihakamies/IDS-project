## Please Note. The product isn't fully finished, is in development and for testing only

Our tests have been ran on different Windows hosts and Arm64 by using Virtual Machines with Debian 13

## Feedback
Thank you for stopping by and showing interest.
You can send any feedback related to this project to: osiris-t-form@protonmail.com

Our main goal for this guide and the project as a whole is to make it as easy as possible for the newbies and veterans to start experimenting and learning on their own terms.

That's why we mainly focus to keep it simple and straightforward without spending the already limited hours in installing and researching every aspect separately.


After installation, this will be the working pipeline and you got:

Suricata -> Wazuh Agent -> Wazuh Manager -> Wazuh Alerts Shipper -> Wazuh Indexer

## Pre-requirements and troubleshooting

**Req**

- Debian 13 running on a Virtual Machine (recommended) - Not tested in other versions or distros.
- At least 30GB of disk space +8GB RAM preferred as well

**Troubleshooting**

1. Make sure your Debian is well set beforehand.

2. User not in sudo group
 ```su``` -> ```sudo adduser (youruser) sudo``` -> Reboot

3. If got any more issues, please inform us at osiris-t-form@protonmail.com

4. Something failed during installation?
   - Run again
   - Failed again?
   - Run again
   - Failed? Send us mail or try to troubleshoot and mail us after!

# How to install?
**For beginners, keep on reading. Full setup guide.**

**For advanced users:**
- Install Debian 13 on a virtual machine, set +30GB disc space and +8GB RAM. 
- After that, head to [Step 2](https://github.com/oskarihakamies/IDS-project/blob/main/How-To-Install.md#step-2---download-unzip-run-enjoy)

## Step 1 - Environment - Full guide
### If you have Debian 13 installed on a clean Virtual machine or know how to install, Skip to [Step 2](https://github.com/oskarihakamies/IDS-project/blob/main/How-To-Install.md#step-2---download-unzip-run-enjoy)

#### 1. Managing machines
- For Windows hosts, the most common way is to download https://www.virtualbox.org/wiki/Downloads which will be the "manager" for your Virtual Machines
- For Arm64 chech here: https://github.com/oskarihakamies/IDS-project/blob/main/How-To_Install_arm64.md
- Choose your host OS
- Download and follow the installation process


####  2. Getting Debian
- Navigate to https://www.debian.org/distrib/
- You can basically choose any Debian 13 - version, but this is the most straighforward without the need to tinker that much

<img width="1876" height="859" alt="kuva" src="https://github.com/user-attachments/assets/5a9fac78-a57d-41bd-9b11-0b46a791f53d" />

- After clicking, it should start download

- When download has finished, open up Oracle VirtualBox

- Press "new" <img width="802" height="118" alt="kuva" src="https://github.com/user-attachments/assets/7c0669d9-dbdf-48c1-849f-bfb27656f5af" />

- Name your VM as you wish
- Find the correct ISO - file that you downloaded
- Make sure the OS is Linux and Debian (64-bit)

<img width="954" height="778" alt="kuva" src="https://github.com/user-attachments/assets/a6b8fa36-d814-4b73-b17c-43e8357a0ca0" />

- Move onto "Specify Virtual hardware"
- Choose +8000MB Base memory if your computer has enough
- Number of CPUs -> experiment as long as it's not on the red part, you should be fine.

<img width="1177" height="813" alt="kuva" src="https://github.com/user-attachments/assets/823427dc-3231-4b47-a852-8309e3cbf3f2" />

- Virtual Hard disk is the size of the disk for the VM
- +35GB recommended
- Finish

- It should appear to the list
- Choose it and press "start"
- A lot of ways, but choose "Graphical Install"

<img width="621" height="561" alt="kuva" src="https://github.com/user-attachments/assets/862b5217-1d15-43ab-8ee9-0c52101eca1b" />

Your preferred language

<img width="804" height="727" alt="kuva" src="https://github.com/user-attachments/assets/1103e53c-0f27-442d-947c-3503e34bc2f2" />

Location is important for timezone, but do as you wish

<img width="787" height="559" alt="kuva" src="https://github.com/user-attachments/assets/62de7994-e5c5-4359-b2dd-78f528a1fbf7" />

Keyboard.. Once again choose your own

<img width="793" height="690" alt="kuva" src="https://github.com/user-attachments/assets/c0d01b57-678a-4afc-985c-6dca070ffbbf" />

After you continue, it will start the install. Grab a coffee and wait. Will take around 2 minutes ☕

<img width="799" height="684" alt="kuva" src="https://github.com/user-attachments/assets/6dac26a7-f2f2-4230-a218-466deab3fef9" />

It will stop and ask for a hostname. This will be visible for other machines in your network.

<img width="793" height="696" alt="kuva" src="https://github.com/user-attachments/assets/13dcd578-11a6-485f-937d-e3e030b56154" />

Domain name can be left blank in home environments

<img width="799" height="691" alt="kuva" src="https://github.com/user-attachments/assets/02bd5c53-5b17-496c-92cf-f347cb7f7d0b" />

Set up a Root password or read through the instructions

<img width="781" height="523" alt="kuva" src="https://github.com/user-attachments/assets/582bb8da-bb91-46e6-9b0c-b926f6b0e3d7" />

Name

<img width="787" height="294" alt="kuva" src="https://github.com/user-attachments/assets/10823ad2-cf53-4962-bcab-cbf4b03ef605" />

Username. Not recommended to be too complicated nor long.

<img width="784" height="193" alt="kuva" src="https://github.com/user-attachments/assets/11f0190f-be41-4219-9eba-e7500455f269" />

Password for the user

<img width="787" height="351" alt="kuva" src="https://github.com/user-attachments/assets/e3ce979c-914b-4d6d-828b-c846319ad4c0" />

After that the installation will continue, this will take 1-3 minutes, jump around for a bit

It will stop and ask for partitions - Use entire disk
<img width="790" height="300" alt="kuva" src="https://github.com/user-attachments/assets/04f9ae31-cc14-4fc0-8af8-90e2ddee785a" />

One might have more than one depending on previous setup, but choose the one we made

<img width="778" height="241" alt="kuva" src="https://github.com/user-attachments/assets/31b53025-7cb8-4186-bd94-75353d71dd00" />

This part is optional

<img width="793" height="465" alt="kuva" src="https://github.com/user-attachments/assets/398b4ac8-4312-409d-9ee4-c5697c8ce61f" />

Keep pressing Continue

<img width="801" height="415" alt="kuva" src="https://github.com/user-attachments/assets/893fde55-3246-4774-8061-87d661752548" />

<img width="787" height="474" alt="kuva" src="https://github.com/user-attachments/assets/6e95a8bb-3298-4d20-bff3-304927409350" />

After pressing continue, it will run for 2 minutes again.

<img width="789" height="453" alt="kuva" src="https://github.com/user-attachments/assets/ab9dd99d-36a5-4534-a095-8c02f9dbdc93" />

Choose "Yes" 

<img width="796" height="297" alt="kuva" src="https://github.com/user-attachments/assets/89180391-f553-4e85-a88a-8c07f8441fce" />

Choose the one closest

<img width="795" height="547" alt="kuva" src="https://github.com/user-attachments/assets/a3395dc8-10d8-45fe-b53e-237d342a05d6" />

And 

<img width="772" height="319" alt="kuva" src="https://github.com/user-attachments/assets/a2124afe-4afe-4549-b216-d49cb07891aa" />

Can be left blank

<img width="787" height="448" alt="kuva" src="https://github.com/user-attachments/assets/0162c26f-3513-462a-aaa3-2bdb47f2fdda" />

The next installation will take around 1 minute.

Optional

<img width="787" height="298" alt="kuva" src="https://github.com/user-attachments/assets/70a7eb8b-05ca-47a6-9642-1df0503759d8" />

Next.. lighter is Xfce, most commonly used GNOME. This part is optional as well, feel free to explore

<img width="787" height="457" alt="kuva" src="https://github.com/user-attachments/assets/162b2e02-2af0-4a5d-9cef-a2d1f16ad058" />

Now, finally - go get some fresh air. This installation should take around 5-15 minutes.

It will once again ask for questions. If you run this inside a vm, no need to worry - if not, read carefully before choosing

<img width="796" height="253" alt="kuva" src="https://github.com/user-attachments/assets/92a51339-ea45-4380-b42e-9f84faaf7b12" />

<img width="796" height="274" alt="kuva" src="https://github.com/user-attachments/assets/95966380-884e-4970-b441-c49b4fe3f3bf" />

Rest of the install 2 - minutes

<img width="781" height="166" alt="kuva" src="https://github.com/user-attachments/assets/a4263c44-2702-445e-bb47-a17eb2ed2c95" />

Reboot and you should have yourself a Debian 13 running on a Virtual Machine

Log in

<img width="1279" height="883" alt="kuva" src="https://github.com/user-attachments/assets/b69cc7aa-220d-4cce-8b63-aa8f00996d3b" />

Check ```sudo nano /etc/apt/sources.list``` that this line cdrom line is # commented

<img width="814" height="525" alt="kuva" src="https://github.com/user-attachments/assets/209f6a49-d981-4348-8c9e-ba89d65334c7" />

If not, put # in front and save

If you get a notification that user is not in sudoers file

> su

> log in using sudo or the root password you set earlier

> sudo adduser (replace with your user) sudo

> reboot the Virtual Machine and try again

Follow to Step 2

## Step 2 - Download, Unzip, Run, Enjoy

1. Download the zip file:

[soc-project-v4.zip](https://github.com/user-attachments/files/26421526/soc-project-v4.zip)

or ```wget https://github.com/user-attachments/files/26421526/soc-project-v4.zip```

2. Navigate to Downloads and look for the file

<img width="735" height="301" alt="kuva" src="https://github.com/user-attachments/assets/640f9480-8673-4f41-8bcc-154deb6fc857" />


3. Download zip ```sudo apt-get install zip```

4. Unzip the file ```unzip soc-project.zip```

5. Navigate to the unzipped file ```cd soc-project```, ```ls``` to list the files

6. Use ```sudo bash install.sh``` to launch it

7. Let it do its magic. This takes around 2-10 minutes. Grab a coffee.

 <img width="741" height="484" alt="kuva" src="https://github.com/user-attachments/assets/d328d9b8-a918-4048-b2df-b04cb162458d" />

8. You should get this once it's finished. If errors, please report them!
   
<img width="1311" height="169" alt="kuva" src="https://github.com/user-attachments/assets/a22330e2-3e34-403f-89ec-3f60bf242e53" />

9. When it's done, navigate to Firefox or w.e Browser

10. Type into the URL: localhost

<img width="1501" height="198" alt="kuva" src="https://github.com/user-attachments/assets/cc7f5f88-c062-4912-9127-3272325f499a" />

Accept the risks

<img width="924" height="730" alt="kuva" src="https://github.com/user-attachments/assets/93b5b4b3-e649-43c4-a87d-a1c90b6816a6" />

11. user ```admin``` pass ```admin```

12. NOTE: IF Wazuh sends an error, change the address to /app/home/ - this tends to happen.

<img width="1660" height="991" alt="kuva" src="https://github.com/user-attachments/assets/8d1f1653-a383-4fea-a97f-04f70b9e106e" />

<img width="1620" height="859" alt="kuva" src="https://github.com/user-attachments/assets/d2d65d77-b345-40b1-962a-6f5590f8d773" />

13. Navigate here:

<img width="1657" height="646" alt="kuva" src="https://github.com/user-attachments/assets/5f5bf856-6dcf-460a-8536-4afe20673e8b" />

14. Navigate to the Security Events or Threat Hunting tab. Some initial traffic should be visible. If not, refresh the page a couple of times

15. If you don't see any alerts, let's trigger one manually. Open your terminal and run this curl command, which is widely used for testing IDS systems:
```curl http://testmynids.org/uid/index.html```

(read more [here](https://github.com/3CORESec/testmynids.org))

16. Now check your Wazuh dashboard again. You should see that something has happened. If not, every single field will be 0 which means that the setup is not fully working or you haven't been fully successfull in creating any alerts.

<img width="1650" height="445" alt="kuva" src="https://github.com/user-attachments/assets/87d019e8-88e4-48e4-b100-14de7d542638" />

17. Your setup should now work and function properly, hooray!

18. Be creative and safe! The fastest test is to use this command ```curl http://testmynids.org/uid/index.html```which is widely used for testing IDS - systems. This SHOULD come up in Wazuh

## . Turn off your internet for testing
- Go to Oracle VirtualBox Manager
- Choose your VM
- Click on "Settings"

<img width="1117" height="742" alt="kuva" src="https://github.com/user-attachments/assets/cfc1f4bc-749a-4189-9afa-1c61d07277fb" />


- Go to "Network"

<img width="966" height="630" alt="kuva" src="https://github.com/user-attachments/assets/68287604-eb93-4aa6-a8a8-10f7b10340da" />

- Click "Virtual Cable Connected" to unmark it

<img width="1120" height="741" alt="kuva" src="https://github.com/user-attachments/assets/05e7b1c6-fabb-48d2-9d8b-ee12948278a8" />

- Ping 8.8.8.8 on your VM and/or go to the browser and do a search
- Should be "unreachable"

<img width="651" height="69" alt="kuva" src="https://github.com/user-attachments/assets/80e28649-6b2b-4387-8184-8b6e0ce5fcf6" />

<img width="1113" height="747" alt="kuva" src="https://github.com/user-attachments/assets/7a2ba2df-4802-43c6-b8c5-176feca0e101" />



- You can also test the system using ```tcpreplay``` and a portscan PCAP file from Practical Packet Analysis. Instructions for this will be added soon.
 https://github.com/markofu/pcaps/blob/master/PracticalPacketAnalysis/ppa-capture-files/portscan.pcap.




## Task for the lesson - shoot a packet with tcpreplay - analyze

## Instructions for tcpreplay

### What is tcpreplay and a PCAP file? 

Tcpreplay is like a recordplayer for the wanted capture packet. Tcpreplay replays the recording onto a network interface. Suricata processes the packets as if the attack is happening live, even though it is just a historical capture.

PCAP file which is used is a recorded packet capture so to speak. Basically a captured snapshot of real traffic such as a port scan or brute-force attempt.

1. Download a PCAP file - We have listed few sites, where you can download them from.

2. Check your interface name

 ``` ip a  ```

3. Replay the PCAP

```sudo tcpreplay --intf1=enp0s3 ~/Downloads/portscan.pcap```


(Command is an example. You have to check the correct interface and pcap files name)
   

5. Open the Dashboard
   

```https://127.0.0.1/app/home```


6. Analyze
   

*Did an alert appear in Wazuh?*

*What rule fired? What severity?*

Imagine the possibilities - you can record your own attacks and see how the system tracks it, what are the delays, how to read different attacks or just an overall network safety tool.


### Thank you

Remember to send any feedback to: osiris-t-form@protonmail.com

~ Make installation and configuration easy, Open-Source and let the open-minded explore their own path ~

