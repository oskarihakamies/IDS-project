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

Tcpreplay
```
sudo apt install build-essential libpcap-dev
wget https://github.com/appneta/tcpreplay/releases/download/v4.4.3/tcpreplay-4.4.3.tar.gz
tar -xzf tcpreplay-4.4.3.tar.gz
cd tcpreplay-4.4.3
./configure
make
sudo make install
```

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

### Kibana

```
wget https://artifacts.elastic.co/downloads/kibana/kibana-9.3.0-amd64.deb
shasum -a 512 kibana-9.3.0-amd64.deb
sudo dpkg -i kibana-9.3.0-amd64.deb
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

```
sudo systemctl enable filebeat
sudo systemctl restart filebeat
sudo systemctl status filebeat
```

Didn't work, took everything out and replaced with:
```
filebeat.inputs:
  - type: filestream
    id: suricata-eve
    enabled: true
    paths:
      - /var/log/suricata/eve.json
    parsers:
      - ndjson:
          target: ""

  - type: filestream
    id: zeek-logs
    enabled: true
    paths:
      - /opt/zeek/logs/current/*.log

output.elasticsearch:
  hosts: ["http://localhost:9200"]
```
Works!

<img width="1205" height="463" alt="kuva" src="https://github.com/user-attachments/assets/81d5ede4-b55e-483e-a3a9-e455efd55a60" />

Kibana:

<img width="530" height="206" alt="kuva" src="https://github.com/user-attachments/assets/ba39619a-d51e-4524-a8b3-3449defb6b68" />

Currently having trouble installing it. 
<img width="641" height="878" alt="kuva" src="https://github.com/user-attachments/assets/9931146b-8f15-42f6-9d20-406aac5f8d40" />

Tried force removing and purging 15 different times, no luck so far.

```curl -fsSL https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elastic.gpg```

```
wget https://artifacts.elastic.co/downloads/kibana/kibana-9.3.0-linux-x86_64.tar.gz
tar -xzf kibana-9.3.0-linux-x86_64.tar.gz
cd kibana-9.3.0-linux-x86_64
./bin/kibana
```
Correcting certificates:
```
sudo /usr/share/elasticsearch/bin/elasticsearch-reset-password -u elastic -i
sudo /usr/share/elasticsearch/bin/elasticsearch-create-enrollment-token -s kibana
```

I got an answer from GPT that this is ok to remove from Lab environment and should be removed since there's  a conflict between Debian 13 and Kibana/Elasticsearch

In elasticsearch.yml:

```
xpack.security.enabled: false
xpack.security.http.ssl.enabled: false
xpack.security.transport.ssl.enabled: false
```

<img width="722" height="484" alt="kuva" src="https://github.com/user-attachments/assets/e258f7d0-c7da-469f-a77c-5bf844503ce1" />
Woohooo! It works!


### Testing

```sudo tcpreplay --intf1=dummy0 test.pcap``` which I downloaded from netresec.com
<img width="1128" height="294" alt="kuva" src="https://github.com/user-attachments/assets/ed083a37-366a-426c-8702-f7a836a06ff3" />

<img width="1155" height="530" alt="kuva" src="https://github.com/user-attachments/assets/810d7269-ac66-4890-928a-897e151e2e9c" />

<img width="1145" height="149" alt="kuva" src="https://github.com/user-attachments/assets/ef7e8713-301f-4bb3-a8f9-c67c76f771bf" />

### Kibana

<img width="1902" height="933" alt="kuva" src="https://github.com/user-attachments/assets/4a6eb9ea-d38b-4e00-ae36-04b193491cd9" />

Test successfull! First demo completed.

I used 

```sudo tcpreplay --intf1=dummy0 test.pcap``` to "shoot" the pcap - file in
```sudo suricata -i dummy0 -D``` to activate Suricata (doesn't function normally when using dummy0)
```sudo /opt/zeek/bin/zeek -i dummy0```to listen on NIC dummy0
```tail f /var/log/suricata/eve.json``` Suricata eve - file
```./bin/kibana``` To start Kibana
2nd attempt:

<img width="1148" height="293" alt="kuva" src="https://github.com/user-attachments/assets/86d8b78c-f1d8-4790-a1f8-2c919da7145a" />

<img width="1709" height="945" alt="kuva" src="https://github.com/user-attachments/assets/c31b48ca-f670-446f-8e20-2153ce72a909" />


This is where I'm stuck at 24/02/2026 time 0:39 Helsinki
