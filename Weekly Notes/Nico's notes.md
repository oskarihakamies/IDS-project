## Weeks 5 & 6

I was supposed to be installing the whole idea of ours, 
test out the first working model and figure out how to Mount the ISO so that we can share it when the project is finished...

- I ran into a few problems during the installation - phase.
1st was finding the right installers for the actual software, f.ex Zeek.
After searching for a while, managed to find them. Install them, test out that they work individually.

 It was time to see if they work together but I ran into another trouble, which was with the VM itself - which is what I'm troubleshooting now with a good friend of mine, Copilot.

![onkelma](https://github.com/user-attachments/assets/378f402f-7c77-4cc7-b165-e3edebb6f638)

Could be a lack of memory.
I believe this was caused when I started to install Wazuh, and while it was installing, it got stuck and the computer decided that it's time to recharge and shut down completely.

Even the GUI won't start, so time to start debugging with CLI.

Decided not worth the time, doing a fresh install even thought I decided to do a fresh one with installation guides after this "Hello World" - would've been successful.

### Installing and QuickStart

Suricata:

```sudo apt-get install suricata```
```sudo systemctl status suricata```
<img width="743" height="352" alt="kuva" src="https://github.com/user-attachments/assets/07c8bbb7-80e3-462f-bbe6-00cf3f04ee78" />

Zeek based on this source:

https://github.com/zeek/zeek/wiki/Binary-Packages

For Debian 13:

```
echo 'deb http://download.opensuse.org/repositories/security:/zeek/Debian_13/ /' | sudo tee /etc/apt/sources.list.d/security:zeek.list
curl -fsSL https://download.opensuse.org/repositories/security:zeek/Debian_13/Release.key | gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/security_zeek.gpg > /dev/null
sudo apt update
sudo apt install zeek
```

## ELK - Stack
From [here](https://www.elastic.co/docs/deploy-manage/deploy/self-managed/install-elasticsearch-with-debian-package) Under "Download and install the Debian package manually"

Dependencies: 
```sudo apt-get install wget```
```
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-9.3.0-amd64.deb
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-9.3.0-amd64.deb.sha512
shasum -a 512 -c elasticsearch-9.3.0-amd64.deb.sha512
sudo dpkg -i elasticsearch-9.3.0-amd64.deb
```

<img width="735" height="150" alt="kuva" src="https://github.com/user-attachments/assets/e78fb11f-e337-410a-9cf2-a99d06a53238" />

### Filebeat

I was a bit lost on this one for a bit but found it [here](https://www.elastic.co/docs/reference/beats/filebeat/filebeat-installation-configuration)

```
curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-9.3.0-amd64.deb
sudo dpkg -i filebeat-9.3.0-amd64.deb
```


### Dummy-interface

- I went into this whole thing by just figuring out an alternative way to test TCPREPLAY so that I can isolate the network not to leak anything out.
- I found a thread discussing about dummy interfaces which is built into the Linux Kernel from [here](https://superuser.com/questions/1789157/black-hole-output-interface-for-tcpreplay).
- I fell into the wormhole of digging deeper, chatting with Copilot and searching online and figured this might be worth the shot.

Because it doesn't have any physical connections and Linux creates a virtual network card by using it, the packets will be dropped instead of jumping into the NIC that is actually being used. It is widely used for testing is what I gathered.

Built in, no need to install
```
sudo ip link add dummy0 type dummy
sudo ip addr add 192.0.2.0/24 dev dummy0
sudo ip link set dummy0 up
```

### Testing and setting everything up

1st the dummy which we already set up

<img width="752" height="150" alt="kuva" src="https://github.com/user-attachments/assets/30825238-6060-4471-b271-25f287be6ac4" />

Running Suricata

```sudo suricata -i dummy0 -c /etc/suricata/suricata.yaml -D```
Listens to dummy0

Zeek to listen

```sudo zeek -i dummy0```

"Command not found". Didn't somehow have it installed, let's try again

<img width="655" height="146" alt="kuva" src="https://github.com/user-attachments/assets/67324a17-7dec-4207-9f2d-5be8729df77a" />

Forgot path, so ```sudo /opt/zeek/bin/zeek -i dummy0```

Configuring Filebeat with Copilot (didn't have enough time to figure this on my own)

```
sudo nano /etc/filebeat/filebeat.yml
```
Suricata eve.json
```
filebeat.inputs:
  - type: filestream
    id: suricata-eve
    paths:
      - /var/log/suricata/eve.json
    parsers:
      - ndjson:
          target: ""
```
Zeek-logs
```
  - type: filestream
    id: zeek-logs
    paths:
      - /opt/zeek/logs/current/*.log
```

Output to Elasticsearch
```
output.elasticsearch:
  hosts: ["http://localhost:9200"]
```
Save and close.

